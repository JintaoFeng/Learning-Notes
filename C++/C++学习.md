1. C++对C的扩展

## 1.1. ::作用域运算符

​		通常情况下，如果有两个同名变量，一个是全局变量，另一个是局部变量，那么局部变量在其作用域内具有较高的优先权，他将屏蔽全局变量。

```c++
int a=10;
void test()
{
	int a=20;
	cout<<"a"<<a<<endl;
}
```

​		程序输出的结果是a：20。在test函数的输出语句里，使用变量a是test函数内定义的局部变量，因此输出的结果为局部变量a的值。

​		作用域运算符可以用来解决局部变量与全局变量的重名问题

```c++
int a=10;
coid test()
{
	int a=20;
	cout<<“局部变量a:”<<a<<endl;
	cout<<"全局变量a："<<::a<<endl;
}
```

## 1.2. 名字控制

​		在c语言中通过static关键字来使得名字只得在本币那一单元内可见，在c++中我们将通过一种命名空间来控制对名字的访问

### 1.2.1 .C++命名空间

​		关键字`namespace` (命名空间/名字空间/名称空间)

* 使用方法

```
namespace A{
	int a=10;
}
namespace B
{
	int a=20;
}
void test()
{
	cout << "A::a : " << A::a << endl;
	cout << "B::a : " << B::a << endl;
}
```

* 命名空间只能在全局范围内定义

* 命名空间可嵌套命名空间

* 命名空间是开放的，可以随时把新的成员加入已有的命名空间中，后者会和前者合并。

* 声明和实现可以分离  

  ```
  namespace MySpace{
  	void func1();
  	void func2(int param);
  }
  void MySpace::func1()
  {
  	cout << "MySpace::func1" << endl;
  }
  void MySpace::func2(int param)
  {
  	cout << "MySpace::func2 : " << param << endl;
  }
  ```

* 无名命名空间，意味着命名空间中的标识符只能在本文件内访问，相当于给这个标识符加上了static，使其可以作为内部链接。

* 命名空间别名。

  ```
  namespace veryLongName
  {
  	int a = 10;
  	void func(){ cout << "hello namespace" << endl; }
  }
  void test()
  {
  	namespace shortName = veryLongName;
  	cout << "veryLongName::a : " << shortName::a << endl;
  	veryLongName::func();
  	shortName::func();
  }
  ```

  

### 1.2.2. using声明

* using声明可是的指定的标识符可用。

```
namespace A
{
    int paramA = 20;
    int paramB = 30;
    void funcA(){ cout << "hello funcA" << endl; }
    void funcB(){ cout << "hello funcA" << endl; }
}
void test()
{
    //1. 通过命名空间域运算符
    cout << A::paramA << endl;
    A::funcA();
    //2. using 声明
    using A::paramA;
    using A::funcA;
    cout << paramA << endl;
    //cout << paramB << endl; //不可直接访问
    funcA();
    //3. 同名冲突
    //int paramA = 20; //相同作用域注意同名冲突
}
```

* using声明碰到函数重载，如果命名空间包含一组用相同名字重载的函数，using 声明就声明了这个重载函数的所有集合。

  ```
  namespace A
  {
      void func(){}
      void func(int x){}
      int func(int x,int y){}
  }
  void test()
  {
      using A::func;
      func();
      func(10);
      func(10, 20);
  }
  ```

  

### 1.2.3. using编译指令

using 编译指令使整个命名空间标识符可用.

```c++
namespace A
{
	int paramA = 20;
	int paramB = 30;
	void funcA(){ cout << "hello funcA" << endl; }
	void funcB(){ cout << "hello funcB" << endl; }
}
void test01()
{
    using namespace A;
    cout << paramA << endl;
    cout << paramB << endl;
    funcA();
    funcB();
    //不会产生二义性
    int paramA = 30;
    cout << paramA << endl;
}
namespace B
{
    int paramA = 20;
    int paramB = 30;
    void funcA(){ cout << "hello funcA" << endl; }
    void funcB(){ cout << "hello funcB" << endl; }
}
void test02()
{
    using namespace A;
    using namespace B;
    //二义性产生，不知道调用 A 还是 B 的 paramA
    //cout << paramA << endl;
}
```



​	**注意：使用 using 声明或 using 编译指令会增加命名冲突的可能性。也就是说，如果有名称空间，并在代码中使用作用域解析运算符，则不会出现二义性。需要记住的关键问题是当引入一个全局的 using 编译指令时，就为该文件打开了该命名空间，它不会影响任何其他的文件，所以可以在每一个实现文件中调整对命名空间的控制。比如，如果发现某一个实现文件中有太多的 using 指令而产生的命名冲突，就要对该文件做个简单的改变，通过明确的限定或者 using 声明来消除名字冲突，这样不需要修改其他的实现文件。**

## 1.3. 全局变量的增强

```
int a = 10; //赋值，当做定义
int a;		//没有赋值，当做声明
int main()
{
	printf("a:%d\n", a);
	//	return EXIT_SUCCESS;
}
```

此代码在c++下编译失败，在c下编译通过。c++下`a`重定义。

## 1.4. C++中所有变量和函数都必须有类型

​	**◼  在 C 语言中，int fun() 表示返回值为 int，接受任意参数的函数，int fun(void) 表示返回值为 int 的无参函数。
​	◼  在 C++ 中，int fun() 和 int fun(void) 具有相同的意义，都表示返回值为 int 的无参函数。 **

## 1.5. 更严格的类型转换

​		在c++中，不同类型的变量一般是不能直接赋值的，需要相应的墙砖。

## 1.6. struct类型的加强

​		C中定义结构体变量需要加上struct关键字，C++不需要。

​		C中的结构体只能定义成员变量，不能定义成员函数。C++既可以定义成员变量，也可以定义成员函数。

## 1.7. 新增bool类型关键字

​		标准C++的`bool`类型有两种内建的常量`true`（1）和`false`（0）表示状态。这三个名字都是关键字。

* `bool`类型只有两个值，`true`（1），`false`（0）；
* `bool`类型占1个字节
* 给`bool`类型赋值时，非0值会自动转化为`true`，0值会自动转换`false`

## 1.8. 三目运算符功能增强

C语言三木运算表达式返回值为数据值，为右值，不能赋值。

C++语言三目运算表达式返回值为变量本身,为左值，可以赋值。

**[左值和右值概念]**

**在 c++中可以放在赋值操作符左边的是左值，可以放到赋值操作符右面的是右值.** 

**有些变量即可以当左值，也可以当右值。**

**左值为 Lvalue，L 代表 Location，表示内存可以寻址，可以赋值。**

**右值为 Rvalue，R 代表 Read,就是可以知道它的值。**  

**比如:`int temp = 10;` temp 在内存中有地址，10 没有，但是可以 Read 到它的值。** 

## 1.9. C/C++中的const

### 1.9.1. const概述

​		const是一个限定符，它用来限定一个变量不允许改变，他将一个对象转换成一个常量。

### 1.9.2. C/C++中const的区别

​		在C中，`const`是一个全局只读变量，C语言中`const`修饰的只读变量时外部连接的。

​		在C++中，一个`const`不必创建内存空间。是否为`const` 分配内存空间依赖于如何使用。如果`const`用来把一个名字用一个值替代（和`#define`一样），就不必创建内存空间。

​		不过，取一个`const`地址，或者把它定义为`extern`,则会为`const`创建内存空间。

​		在c++中，出现在所有函数之外的`const`作用域整个文件，也就是说他在该文件之外不可见，默认内部连接。

