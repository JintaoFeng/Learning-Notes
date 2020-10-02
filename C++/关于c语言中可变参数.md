## 关于c语言中可变参数

​		在学习C语言的时候，知道`printf()`是一个可变参数的函数，例如：

```
int a=1;
double b=1.0;
char c=1;
printf("%d\n",a);
printf("%d,%f\n",a,b);
printf("%d,%f,%c\n",a,b,c);
```

​		自己定义函数的时候，可以通过C语言的标准库<stdarg.h>来实现函数的变参传递。

`stdarg.h`文件定义了一个变量类型`va_list`和三个宏。

```
typedef char *va_list
void va_list(va_list ap,last_arg);
type va_arg(va_list ap,type);
void ca_end(va_list ap);
```





