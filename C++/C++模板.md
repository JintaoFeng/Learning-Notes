## C++模板

模板是泛型编程的基础，泛型编程是一种创建泛型类或函数的蓝图和公式。

### 基本的模板语法

*   在泛型编程技术里，我们仍然需要编写自己的函数和类，但不必限定他们所使用的数据类型。
*   只需要使用一个占位符（通常用字母T来表示），然后用这个占位符来编写函数。
*   当程序需要这段代码时，你提供数据，编译器将根据你的模板即时生成实用的代码。

模板函数定义的一般形式如下：

```
template<typename type> 
ret-type func-name<parameter list>
{

}
```

在这里，type是函数所使用的数据类型的占位符名称。这个函数在函数定义中使用。

实例：

```
template<class  T>
void swap(T &a,T &b)
{
    T tmp=a;
    a=b;
    b=tmp;
}
```

在第一行代码里：在尖括号里有一个class T，用来告诉编译器：字母T将在接下来的函数里代表一种不确定的数据类型。class并不意味着T只能是一个类。

关键字class并不意味着这是一个类，他只是约定俗成的写法。在告诉计算机T是一种数据类型后，就可以向对待一种普通数据类型那样使用它了。

在创建模板时候，可以用`template<typename T>`来代替`template<class T>`，他们的含义是一样的。

### 类模板

类模板与函数模板非常相似，同样是由你先编写一个类的模板，再由编译器在你第一次使用这个模板的时候生成实际的代码。

```
template<class T>
class MyClass
{
	MyClass();
	void swap(T &a,T &b);
}
```

因为MyClass是一个类模板，所以不能构造器不能只写出`MyClass：：MyClass()`,编译器需要你在这里给出一种与`MyClass`配合使用的数据类型，必须在尖括号里提供他。因为没有确定的书话剧类型可以提供，所以使用一个T作为占位符即可。

实例：

```
#include <iostream>
#include <vector>
#include <cstdlib>
#include <string>
#include <stdexcept>
 
using namespace std;
 
template <class T>
class Stack { 
  private: 
    vector<T> elems;     // 元素 
 
  public: 
    void push(T const&);  // 入栈
    void pop();               // 出栈
    T top() const;            // 返回栈顶元素
    bool empty() const{       // 如果为空则返回真。
        return elems.empty(); 
    } 
}; 
 
template <class T>
void Stack<T>::push (T const& elem) 
{ 
    // 追加传入元素的副本
    elems.push_back(elem);    
} 
 
template <class T>
void Stack<T>::pop () 
{ 
    if (elems.empty()) { 
        throw out_of_range("Stack<>::pop(): empty stack"); 
    }
    // 删除最后一个元素
    elems.pop_back();         
} 
 
template <class T>
T Stack<T>::top () const 
{ 
    if (elems.empty()) { 
        throw out_of_range("Stack<>::top(): empty stack"); 
    }
    // 返回最后一个元素的副本 
    return elems.back();      
} 
 
int main() 
{ 
    try { 
        Stack<int>         intStack;  // int 类型的栈 
        Stack<string> stringStack;    // string 类型的栈 
 
        // 操作 int 类型的栈 
        intStack.push(7); 
        cout << intStack.top() <<endl; 
 
        // 操作 string 类型的栈 
        stringStack.push("hello"); 
        cout << stringStack.top() << std::endl; 
        stringStack.pop(); 
        stringStack.pop(); 
    } 
    catch (exception const& ex) { 
        cerr << "Exception: " << ex.what() <<endl; 
        return -1;
    } 
}
```

