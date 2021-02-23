**创建NML词汇表(格式函数、更新函数和消息定义)。**	

​		创建NML词汇表。(格式函数、更新函数和消息定义)。消息词汇表是一组c++类，派生自NMLmsg，可以将其视为在写操作期间将其复制到NML缓冲区，并在读操作期间将其复制出来的数据结构。每个类与一个唯一标识符相关联,一个正整数,允许读者识别哪些消息缓冲区中,除了特定的类的数据成员,每个类也需要一个更新函数调用CMS方法转换类型CMS可以处理的数据成员。目前，CMS提供了对基本C语言内置类型的支持。“long long”和“unsigned long long”类型并不是真正的标准内置类型，也不受支持。在假定一个long或unsigned long将存储64位而不是32位之前，或者在假定一个long double存储超过64位之前，应该读取NML 64位长的注释。(参见“故障排除-参数不足错误”)使CMS能够对缓冲区中的数据进行中立的格式化，或者允许NML服务器为远程进程对数据进行编码和解码。需要一个格式函数。这个正式函数只不过是一个switch语句，它将NML标识符与特定NML消息类的更新函数关联起来。format函数可以手动编程为，但建议使用NML代码生成器自动生成它。



**可变长数组**

​		一些高级用户使用可变长度数组定义消息。有几种方法可以做到这一点，但是最简单和最方便的方法通常是使用DECLARE NML动态长度数组宏。这个宏对NML代码生成器有特殊的意义。其结果是一个最大大小恒定的数组，但在每次远程读或写时，只有名长元素通过网络发送。通过将中立配置文件标志设置为1，可以强制本地读和写使用压缩版本作为你的信息，宏的文本是定义DECLARENML动态长度数组(类型，名称，大小)类型名称(大小);

```
/* nml_ex1.hh */ 
#ifndef NML_EX1_HH 
#define NML_EX1_HH 
#include "rcs.hh" 
	/* Give the new structure a unique id number */ 
#define DECLARE_NML_DYNAMIC_LENGTH_ARRAY(type, name, size) int name##_length; type name[size];
#define EXAMPLE_MSG_TYPE 101 
	/* The id number must be unique within a CMS buffer, i.e. the number must be different than the id of any other type that might be written to a particular buffer. For simplicity it is recommended that the id number also be unique within an application. */ 
	/* Define the new message structure */ 
struct EXAMPLE_MSG: public NMLmsg 
{ 
/* The constructor needs to store the id number */ 
/* and the size of the new structure */ 
/* by passing them as arguments to the base class constructor. */ 		EXAMPLE_MSG():NMLmsg(EXAMPLE_MSG_TYPE, sizeof(EXAMPLE_MSG))
	{}; 
/* Each new type needs to overload the update function. */ 
	void update(CMS *cms); /* Data in this new message format. */ 
    float f; 
    char c; 
    int i;
    DECLARE_NML_DYNAMIC_LENGTH_ARRAY(int, da, 100); 
}; 
/* Declare the NML Format function. */ 
int ex_format(NMLTYPE type, void *buf, CMS *cms); 
#endif /* End of NML_EMC_HH */
```

