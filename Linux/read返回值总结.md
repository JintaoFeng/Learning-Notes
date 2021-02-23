# read返回值总结

read函数的返回值：

1. \>0 实际已经读到的字节数。

2. =0 已经都到结尾，对端关闭。

3. -1 进一步判断errno的值，

	errno=EAGAIN or EWOULDBLOCK;设置了非阻塞方式读。没有数据到达。

	errno = EINTR  慢速系统调用被中断。

	errno = “其他情况” 异常。