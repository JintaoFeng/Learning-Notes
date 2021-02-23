# Lambda表达式

Lambda表达式的基本语法如下：

```c++
[捕获列表](参数列表)mutable(可选)异常属性->返回类型{ 
	//函数体
}	
```

所谓捕获列表，可以理解为参数的一种类型，lambda表达式内部函数体在默认情况下是不能够使用函数体外部的变量的，这时候捕获列表可以起到传递外部数据的作用。根据传递的行为：捕获列表也分为以下几种：

1. 值捕获  与参数传值类似，值捕获的前提是变量可以拷贝，不同之处在于，被捕获的变量在lambda表达式被创建时拷贝，而非调用时拷贝：

```c++
void lambda_value_capture()
{
    int value=1;
    auto copy_value=[value]{
        return value;
    };
    value=100;
    auto stored_value=copy_value();
    cout<<"stored_value:"<<stored_value<<endl;
          
}
```

2. 引用捕获。与引用传参类似，引用捕获保存的是引用，值会发生变化。

```c++
void lambda_reference_capture()
{
    int value=1;
    auto copy_value=[&value]
    {
        return value;
    };
    value=100;
    auto stored_value=copy_value();
    cout<<"stored_value"<<stored_value<<endl;
    // 这时, stored_value == 100, value == 100.
	// 因为 copy_value 保存的是引用
}
```

3. 隐式捕获

手动书写捕获列表有时候是非常复杂的，这种机械性的工作可以交给编译器来处理，这时候可以在
捕获列表中写一个 & 或 = 向编译器声明采用引用捕获或者值捕获.

总结一下，捕获提供了 lambda 表达式对外部值进行使用的功能，捕获列表的最常用的四种形式可
以是：

* [] 空捕获列表
* [name1, name2, ...] 捕获一系列变量
*  [&] 引用捕获, 让编译器自行推导捕获列表
*  [=] 值捕获, 让编译器执行推导引用列表

4. 表达式捕获

上面提到的值捕获、引用捕获都是已经在外层作用域声明的变量，因此这些捕获方式捕获的均为左
值，而不能捕获右值。

C++14 给与了我们方便，允许捕获的成员用任意的表达式进行初始化，这就允许了右值的捕获，被
声明的捕获变量类型会根据表达式进行判断，判断方式与使用 auto 本质上是相同的：

```

```

