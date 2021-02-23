# socket编程

内存中的多字节数据相对于内存地址有大端和小端之分，磁盘中的的多字节数据相对于文件中的偏移地址也有大端小端之分。TCP/IP协议规定，网络数据流应该采用大端字节，即低地址高字节。例如UDP段格式，地址0-1是16位的源端口号，如果这个端口号是1000(0x3e8)，则地址0是0x03，地址1是0xe8，也就是先发0x03，再发0xe8，这16位在发送主机的缓冲区中也应该是低地址存0x03，高地址存0xe8.但是，如果发送主机是小端字节的，这16位就被解释称0xe803，而不是1000.因此，发送主机把1000发送到缓冲区之前需要做字节序的转换。同样的，接收主机如果是小端字节序的，接到16位的源端口号也要做字节序的转换。如果主机是大端字节序的，发送和接收都不需要做转换。同理，32位的IP地址也要考虑网络字节序和主机字节序的问题。

可以用以下库函数做网络字节序和主机字节序的转换：

```
#include <arpa/inet.h>

uint32_t htonl(uint32_t hostlong);
uint16_t htons(uint16_t hostshort);
uint32_t ntohl(uint32_t netlong);
uint16_t ntohs(uint16_t netshort);
```

h表示host，n表示network，l表示32位长整数，s表示16位短整数。例如htonl表示将32位的长整数从主机字节序转换为网络字节序，例如将IP地址转换后准备发送。如果主机是小端字节序，这些函数参数做相应的大小端转换后返回，如果主机是大端字节序，这些函数不做转换，将参数原封不动返回。

### socket地址的数据类型和相关函数

socket API是一层抽象的网络接口编程，适用于各种底层网络协议，如IPv4、IPv6，以及后面要讲的UNIX Domain Socket。然而，各种网络协议的地址格式并不相同，如下图所示：

sockaddr数据结构：

![](..\picture\微信截图_20201115141123.png)

IPv4和IPv6的地址格式定义在`netinet/in.h`中，IPv4地址用`sockaddr_in`结构体表示，包括16位端口号和32位IP地址，IPv6地址用sockaddr_in6结构体表示，包括16位端口号、128位IP地址和一些控制字段。UNIX Domain Socket的地址格式定义在sys/un.h中，用sockaddr_un结构体表示。各种socket结构体的开头都是相同的，前16位表示整个结构体的长度，后16位表示地址类型。IPv4和IPv6和UNIX Domain Socket的地址类型分别定义为常数AF_INET、AF_INET6、AF_UNIX.这样，只要取得某种sockaddr结构体的首地址，不需要知道具体是哪种类型的sockaddr结构体，就可以根据地址类型字段确定结构体中的内容。因此，socket API可以接受各种类型的sockaddr结构体指针做参数例如bind、accept、connect等函数，这些函数的参数应该设计成void*类型以便接受各种类型的指针，但是sock API的实现早于ANSI C标准化，那时还没有void*类型，因此这些函数的参数都用struct sockaddr*类型表示，在传递参数之前要强制转换一下，例如：

```
struct sockaddr_in servaddr;
bind(listen_fd,(struct sockaddr*)&servaddr,sizeof(sercaddr));
```

在IPv4的socket编程中，`sockaddr_in`中的成员``struct in_addr sin_addr`表示32位的IP地址。但是我们通常用十进制的字符串表示IP地址，以下函数可以在字符串表示和`in_addr`表示之间转换。

字符串转`in_addr`函数。：

```
#include <arpa/inet.h>
int inet_aton(const char* strpt,struct in_addr *addrptr);
int_addr_t inet_addr(const char* strptr);
int inet_pton(int family,const char* strptr,void *addrptr);
```

`in_addr`转字符串的函数:

```
char *inet_ntoa(struct in_addr inaddr);
const char* inet_ntop(int family,const void *addrptr,char *strptr,size_t len);
```

## 基于TCP协议的网络程序

TCP协议的通讯流程：

![](..\picture\微信截图_20201115150118.png)

建立连接的过程：

![](..\picture\微信截图_20201115150245.png)

服务器调用`socket()、bind()、listen()`完成初始化后，调用`accept()`阻塞等待，处于监听端口的状态，客户端调用`socket()`初始化后，调用`connect()`返沪SYN段并阻塞等待服务器应答，服务器应答一个SYN-ACK段，客户端收到后从`connect()`返回，同时应答一个ACK段，服务器收到后从`accept()`返回。

关闭连接的过程：

![](..\picture\微信截图_20201115150256.png)

如果客户端没有更多的请求了，就调用`close()`关闭连接，就像写端关闭的管道一样，服务器的`read()`返回0，这样服务器就知道客户端关闭了连接，也调用`close()`关闭连接。注意，任何一方调用`close()`后，连接的两个传输方向都关闭，不能再发送数据了。如果一方调用了`shutdown()`则连接处于半关闭状态，仍可接收对方发来的数据。

### 简单的TCP网络程序

例程：server.c

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>

#define MAXLINE 80
#define SERV_PORT 8000

int main()
{
    struct sockaddr_in servaddr,cliaddr;
    socklen_t cliaddr_len;
    int listenfd,connfd;
    char buf[MAXLINE];
    char str[INET_ADDRSTRLEN];
    int i,n;
    listenfd=socket(AF_INET,SOCK_STREAM,0);
    bzero(&servaddr,sizeof(servaddr));
    servaddr.sin_family=AF_INET;
    servaddr.sin_addr.s_addr=htonl(INADDR_ANY);
    servaddr.sin_port=htons(SERV_PORT);

    bind(listenfd,(struct sockaddr*)&servaddr,sizeof(servaddr));
    listen(listenfd,20);
    printf("Accepting connections...\n");
    while(1)
    {
        cliaddr_len=sizeof(cliaddr);
        connfd=accept(listenfd,(struct sockaddr*)&cliaddr,&cliaddr_len);
        printf("accept!\n");
        n=read(connfd,buf,MAXLINE);
      //  printf("received from %s at port %d\n",inet_ntop(AF_INET,&cliaddr.sin_addr,str,sizeof(str)),ntohs(cliaddr.sin_port));
        printf("receive :%s,num:%d\n",buf,n);
        for(i=0;i<n;i++)
        {
         //   buf[i]=toupper(buf[i]);
        }
        write(connfd,buf,n);
        close(connfd);
    }
}
```



下面介绍程序中用到的socket API，这些函数都在`sys/socket.h`中。

```
int socket(int family,int type,int protocol);
```

socket()打开一个网络通讯端口，如果成功的话，就像`open()`一样返回一个文件描述符，应用程序就可以像读写文件一样用read/write在网络上返送数据，如果`socket()`调用出错则返回-1对于IPv4,family参数指定为AF_INET.对于TCP协议，type参数指定为`SOCK_STREAM`,表示面向流的传输协议。`protocol`参数不做介绍。指定为0即可。

```
int bind(int sockfd,const struct sockaddr *myaddr,socklen_t addrlen);
```

服务器程序所监听的网络地址和端口号通常是固定不变的，客户端程序得知服务器程序的地址和端口号后就可以向服务器发起连接，因此服务器需要调用bind绑定一个固定的网络哦地址和端口号。`bind()`成功返回0，失败返回-1.

`bind()`的作用是将参数`sockfd`和`myaddr`绑定在一起，是sockfd这个用于网络通讯的文件描述符监听myaddr所描述的地址和端口号。

```
    bzero(&servaddr,sizeof(servaddr));
    servaddr.sin_family=AF_INET;
    servaddr.sin_addr.s_addr=HTONL(INADDR_ANY);
    servaddr.sin_port=htons(SERV_PORT);
```

首先将整个结构体清零，然后设置地址类型为AF_INET,网络地址为`INADDR_ANY`，这个宏定义文本的任意IP地址，因为服务器可能有多个网卡，每个网卡可能绑定多个IP地址。

```
int listen(int sockfd,int backlog);
int accept(int socked,struct sockaddr* cliaddr,socklen_t *addrlen);
```

三方握手完成后，服务器调用accept()接收连接，如果服务器调用accept()时还没有客户端的连接请求，就阻塞等待直到有客户端链接上来。`cliaddr`是一个传出参数，`accept()`返回时传出客户端的地址和端口号。addrlen参数是一个传入传出参数，传入的时调用者提供的缓冲区cliaddr的长度以避免缓冲区溢出的问题，传出的是客户端地址结构体的实际长度。如果cliaddr参数传NULL，表示不关心客户端的地址。

客户端client.c

```
#include <stdlib.h>
#include <stdio.h>
#include <strings.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

#define MAXLINE 80
#define SERV_PORT 8000

int main(int argc,char *argv[])
{
    struct sockaddr_in servaddr;
    char buf[MAXLINE];
    int sockfd,n;
    char* str;
    if(argc!=2)
    {
        fputs("usage:./client message\n",stderr);
        exit(1);
    }
    str=argv[1];
    sockfd=socket(AF_INET,SOCK_STREAM,0);
    bzero(&servaddr,sizeof(servaddr));
    servaddr.sin_family=AF_INET;
    inet_pton(AF_INET,"127.0.0.1",&servaddr.sin_addr);
    servaddr.sin_port=htons(SERV_PORT);
    connect(sockfd,(struct sockaddr*)&servaddr,sizeof(servaddr));
    write(sockfd,str,strlen(str));
    n=read(sockfd,buf,strlen(str));
    printf("Response from server:\n");
    write(STDOUT_FILENO,buf,n);
    close(sockfd);
    return 0;
}
```

由于客户端不需要固定的端口号，因此不必调用bind()，客户端的端口号由内核自动分配。注意，客户端不是不允许调用`bind()`，只是没有必要调用`bind()`绑定一个端口号，服务器也不是必须调用`bind()`但如果服务器不调用`bind()`，内核就会自动给服务器分配监听端口，每次启动服务器时端口号都不一样，客户端要连接服务器就会遇到麻烦。

```
int connect(int socked,const struct sockaddr* servaddr,socklen_t addrlen);
```

客户端需要调用`connect()`连接服务器，`connect`和`bind`的参数形式一致，区别在于bind的参数是自己的地址，而connect的参数是对方的地址。connect成功返回0，出错返回-1。



