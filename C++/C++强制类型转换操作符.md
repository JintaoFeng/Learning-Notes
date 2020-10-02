## C++强制类型转换操作符

​		static_cast是一个强制类型转换操作符。强制类型转换，也称为显式转换，C++中强制类型转换操作符有static_cast、dynamic_cast、const_cast、reinterpert_cast四个。本节介绍static_cast操作符。

​		static_cast也可以用在于基类与派生类指针或引用类型之间的转换。然而它不做运行时的检查，不如dynamic_cast安全。static_cast仅仅是依靠类型转换语句中提供的信息来进行转换，而dynamic_cast则会遍历整个类继承体系进行类型检查,因此dynamic_cast在执行效率上比static_cast要差一些。现在我们有父类与其派生类如下：



​		 dynamic_cast：只能用于将派生类指针转化为基类指针，否则就会赋空值。这种转化只能用于is-a类型的转化，用法如下：

​		 const_cast：只用于一种用途，即将const转化为volatile，其语法

```
High bar ；
const High *pbar = &bar ；
High *pb = const_cast<High*> (pbar)； //正确的，将pbar转化为可以非const
const Low *pi = const_cast<const Low *> (pbar)； //非法的，如果将pbar转化为非const，但是又转化为const
```

​		其他转换都是非法的；

​		static_cast：只能转化可以隐式转化的类型，如double可以隐式转化为int，派生类指针可以隐式转化为基类指针，但是基类指针转化为派生类指针就会报错；

​		reinterpret_cast：功能比较强大，他可以根据内存中的值转化为另一种类型，如

```
struct dat {short a ； short b ；};
long value = 0xA224B118;
dat * pd = reinterpret_cast<dat*> (&value )；
cout << hex << pd->a ； / / display first 2 bytes of value
```

​		dat的内存是连续的，将long在内存中的位置的指针位置给dat，同时将long转化为dat类型。有时处理结构体异常有用。 



*   static_cast可以把任何类型的表达式转换成void类型。
*   static_cast把任何类型的表达式转换成void类型。
*   另外，与const_cast相比，static_cast不能把换掉变量的const属性，也包括volitale或者__unaligned属性。

*   上行转换（up-casting）--the up direction (toward the root Object class)。把子类的指针或引用转换成基类表示

*   下行转换（down-casting）--the down direction, along the inheritance path.。把基类指针或引用转换成子类表示

类型转换不安全性来源于两个方面：

*   其一是类型的窄化转化，会导致数据位数的丢失；比如int类型转short。float类型转int.

*    其二是在类继承链中，将父类对象的地址（指针）强制转化成子类的地址（指针）



​		因此上行转换一般是安全的，下行转换很可能是不安全的。子类中包含父类，所以上行转换（只能调用父类的方法，引用父类的成员变量）一般是安全的。但父类中却没有子类的任何信息，而下行转换会调用到子类的方法、引用子类的成员变量，这些父类都没有，所以很容易“指鹿为马”或者干脆指向不存在的内存空间。

​		值得一说的是，不安全的转换不一定会导致程序出错，比如一些窄化转换在很多场合都会被频繁地使用，前提是程序员足够小心以防止数据溢出；下行转换关键看其“本质”是什么，比如一个父类指针指向子类，再将这个父类指针转成子类指针，这种下行转换就不会有问题。

## static_cast

用法：static_cast < type-id> ( expression )  

编译器在编译期处理

​		将地址expression转换成类型type-id，type-id和expression必须是指针、引用、算术类型或枚举类型。四种转换类型中唯一一个可以用于非指针、非引用类型的转换。 

​		表达式static_cast<type-id>(expression), expression的值转换为模板中指定的类型type-id。在运行时转换过程中，不进行类型检查来确保转换的安全性。static_cast它能在内置的数据类型间互相转换，对于类只能在有联系的指针类型间进行转换。可以在继承体系中把指针转换来、转换去，但是不能转换成继承体系外的一种类型。

​		该运算符主要有如下几种用法：

​		① 用于类层次结构中基类和子类之间指针或引用的转换。 进行上行转换是安全的； 进行下行转换时，由于没有动态类型检查，所以是不安全的。

​		② 用于基本数据类型之间的转换，如把int转换成char，把int转换成enum。这种转换的安全性也要开发人员来保证。

​		③ 把空指针转换成目标类型的空指针。

​		④ 把任何类型的表达式转换成void类型。

注意：static_cast不能转换掉expression的const、volitale、或者__unaligned属性。

```

class A { ... };  
    class B { ... };  
    class D : public B { ... };  
    void f(B* pb, D* pd)  
    {  
        D* pd2 = static_cast<D*>(pb);        // 不安全, pb可能只是B的指针  
        B* pb2 = static_cast<B*>(pd);        // 安全的  
        A* pa2 = static_cast<A*>(pb);        // 错误A与B没有继承关系  
        ...  
    }
```

​		对于不相关类指针之间的转换。参见下面的例子：

```
// class type-casting
 #include <iostream>
 using namespace std;

 class CDummy {
     float i,j;
 };

 class CAddition {
     int x,y;
   public:
     CAddition (int a, int b) { x=a; y=b; }
     int result() { return x+y;}
 };

 int main () {
   CDummy d;
   CAddition * padd;
   padd = (CAddition*) &d;
   cout << padd->result();
   return 0;
 }
```

​		CAddition与CDummy类没有任何关系了，但main()中C风格的转换仍是允许的padd = (CAddition*) &d，这样的转换没有安全性可言。

## 2. dynamic_cast

用法：dynamic_cast < type-id> ( expression )

该运算符把expression转换成type-id类型的对象。Type-id必须是类的指针、类的引用或者void *；它有三个重要的约束条件，

  第一、必须用于类与子类之间的转换；

  第二、必须用于指针或引用类型的转换；

  第三、下行转换时要求基类必须有虚函数（基类中包含至少一个虚函数）。

​		如果type-id是类指针类型，那么expression也必须是一个指针，如果type-id是一个引用，那么expression也必须是一个引用。dynamic_cast主要用于类层次间的上行转换和下行转换，还可以用于类之间的交叉转换。在类层次间进行上行转换时，dynamic_cast和static_cast的效果是一样的；在进行下行转换时，dynamic_cast具有类型检查的功能，比static_cast更安全。

 		在运行期，会检查这个转换是否可能。type-id必须是一个指针、引用或无类型的指针。expression必须是决定一个指针或引用的表达式。dynamic_cast 仅能应用于指针或者引用，不支持内置数据类型。表达式dynamic_cast<type-id>(expression) 将expression值转换为类型为type-id的对象指针。如果类型type-id不是expression的某个基类型，该操作将返回一个空指针。它不仅仅像static_cast那样，检查转换前后的两个指针是否属于同一个继承树，它还要检查被指针引用的对象的实际类型，确定转换是否可行。如果可以，它返回一个新指针，甚至计算出为处理多继承的需要的必要的偏移量。如果这两个指针间不能转换，转换就会失败，此时返回空指针（NULL）。
很明显，为了让dynamic_cast能正常工作，必须让编译器支持运行期类型信息（RTTI）

```
#include <iostream>
 using namespace std;
 class CBase { };
 class CDerived: public CBase { };
 
 int main()
 {
     CBase b; CBase* pb;
     CDerived d; CDerived* pd;
 
     pb = dynamic_cast<CBase*>(&d);     // ok: derived-to-base
     pd = dynamic_cast<CDerived*>(&b);  // wrong: base-to-derived 
 }
```

​	我们看到一个奇怪的现象，将父类经过dynamic_cast转成子类的指针竟然是空指针！这正是dynamic_cast提升安全性的功能，dynamic_cast可以识别出不安全的下行转换，但并不抛出异常，而是将转换的结果设置成null（空指针）。

```
#include <iostream>
 #include <exception>
 using namespace std;
 
 class CBase { virtual void dummy() {} };
 class CDerived: public CBase { int a; };
 
 int main () {
   try {
     CBase * pba = new CDerived;
     CBase * pbb = new CBase;
     CDerived * pd;
 
     pd = dynamic_cast<CDerived*>(pba);
     if (pd==0) cout << "Null pointer on first type-cast" << endl;
 
     pd = dynamic_cast<CDerived*>(pbb);
     if (pd==0) cout << "Null pointer on second type-cast" << endl;
 
   } 
   catch (exception& e) {
     cout << "Exception: " << e.what();
   }
   return 0;
 }
```

​		出结果是：Null pointer on second type-cast

​		两个dynamic_cast都是下行转换，第一个转换是安全的，因为指向对象的本质是子类，转换的结果使子类指针指向子类，天经地义；第二个转换是不安全的，因为指向对象的本质是父类，“指鹿为马”或指向不存在的空间很可能发生！

​		最后补充一个特殊情况，当待转换指针是void*或者转换目标指针是void*时，dynamic_cast总是认为是安全的。举例如下：

```

#include <iostream>
 using namespace std;
 class A {virtual void f(){}};
 class B {virtual void f(){}};
 
 int main() {
     A* pa = new A;
     B* pb = new B;
     void* pv = dynamic_cast<void*>(pa);
     cout << pv << endl;
     // pv now points to an object of type A
 
     pv = dynamic_cast<void*>(pb);
     cout << pv << endl;
     // pv now points to an object of type B
 }
```

​		可见dynamic_cast认为空指针的转换安全的，但这里类A和类B必须是多态的（包含虚函数），否则会编译报错。

### 3. const_cast<type-id>(expression)

编译器在编译期处理
去掉类型中的常量，除了const 或不稳定的变址数，type-id和expression必须是相同的类型。
表达式const_cast<type-id>(expression)被用于从一个类中去除以下这些属性：const, volatile, 和 __unaligned。

```cpp

    class A { ... };  
    void f()  
    {  
        const A *pa = new A;     // const对象  
        A *pb;                   // 非const对象  
        pb = pa;                 // 出错，不能将const对象指针赋值给非const对象  
        pb = const_cast<A*>(pa); // 现在OK了  
       ...  
    }
```

​		对于本身定义时为const的类型，即使你去掉const性，在你操作这片内容时候也要小心，只能r不能w操作，否则还是会出错：

```
const char* p = "123";   
char* c = const_cast<char*>(p);   
c[0] = 1;   //表面上通过编译去掉了const性，但是操作其地址时系统依然不允许这么做。
```

​		const_cast操作不能在不同的种类间转换。相反，它仅仅把一个它作用的表达式转换成常量。它可以使一个本来不是const类型的数据转换成const类型的，或者把const属性去掉。尽量不要使用const_cast,如果发现调用自己的函数，竟然使用了const_cast，那就赶紧打住，重新考虑一下设计吧。

​			去除“指针”或“引用”的const、volatile、_unaligned性。很多人都认为用这个运算符就可以让const这么“固若金汤”的东西“形同虚设”，实则不然。本质上它能做到也就是仅仅为你剥去一层虚假的外壳。如果“被指向”或“被引用”的东西本身就是const的，那么任凭你费多大力气都是徒劳的。一般它都是在这样的一套逻辑中：

```

int a=34;
const int * pcint=&a;
int *pint=const_cast<int*>(pcint);
*pint=0;
```

​		这样就修改了本来是const的指针(该指针要求不能修改它指向的东东），这里如果改为const int a=34；那么虽然编译依然能通过，运行依然OK，但是实际上当你用*pint来修改时，你会得到修改后的值为：a依然为34；而*pint确实为0了，但有趣的是此时pint=&a依然成立，虽然pint指向a，但是取出它的值却不等于a了。至此，希望大家今后不要再误用const_cast了。



### 4. reinterpret_cast<type-id>(expression)--重解释转换

编译器在编译期处理
		任何指针都可以转换成其它类型的指针，type-id必须是一个指针、引用、算术类型、指向函数的指针或指向一个类成员的指针。
		表达式reinterpret_cast<type-id>(a)能够用于诸如char* 到 int*，或者One_class* 到 Unrelated_class*等类似这样的转换，因此可能是不安全的。用于对指针的重新包装，也就是指针类别之间的转化。除此之外还可以用于指针类型与unsigned int类型之间的转化。

```

class A { ... };  
class B { ... };  
void f()  
{  
    A* pa = new A;  
    void* pv = reinterpret_cast<B*>(pa);  
    // pv 现在指向了一个类型为B的对象，这可能是不安全的  
    ...  
} 
```

使用reinterpret_cast 的场合不多，仅在非常必要的情形下，其他类型的强制转换不能满足要求时才使用。

这个转换是最“不安全”的，两个没有任何关系的类指针之间转换都可以用这个转换实现，举个例子：

class A {};

class B {};

A * a = new A;

B * b = reinterpret_cast<B*>(a);// 正确

更厉害的是，reinterpret_cast可以把整型数转换成地址（指针），这种转换在系统底层的操作，有极强的平台依赖性，移植性差。它同样要求new_type是指针或引用，下面的例子是通不过编译的。

double a = 2000.3;

short b;

b = reinterpret_cast<short> (a); // 编译错误



## **类型转换符相互比较**

### 1. static_cast vs reinterpret_cast

   reinterpret_cast是为了映射到一个完全不同类型的意思，这个关键词在我们需要把类型映射回原有类型时用到它。我们映射到的类型仅仅是为了故弄玄虚和其他目的，这是所有映射中最危险的(这句话是C++编程思想中的原话)。
   static_cast 和 reinterpret_cast 操作符修改了操作数类型，它们不是互逆的。 
   static_cast 在编译时使用类型信息执行转换，在转换执行必要的检测(诸如指针越界计算, 类型检查). 其操作数相对是安全的。
   另一方面，reinterpret_cast是C++里的强制类型转换符,操作符修改了操作数类型,但仅仅是重新解释了给出的对象的比特模型而没有进行二进制转换。
例子如下：

```
int n=9;   
double d=static_cast < double > (n);
```

​		这次， 结果有所不同。在进行计算以后， d 包含无用值。这是因为 reinterpret_cast 仅仅是复制 n 的比特位到 d，没有进行必要的分析。因此，你需要谨慎使reinterpret_cast。
reinterpret_casts的最普通的用途就是在函数指针类型之间进行转换。
例如，假设你有一个函数指针数组：

```
typedefvoid(*FuncPtr)();// FuncPtr is一个指向函数的指针，该函数没有参数,返回值类型为void  
FuncPtrfuncPtrArray[10];// funcPtrArray是一个能容纳10个FuncPtrs指针的数组 
```



你不能不经过类型转换而直接去做，因为doSomething函数对于funcPtrArray数组来说有一个错误的类型。在FuncPtrArray数组里的函数返回值是void类型，而doSomething函数返回值是int类型。

funcPtrArray[0] = &doSomething;// 错误！类型不匹配 

reinterpret_cast可以让你迫使编译器以你的方法去看待它们：

funcPtrArray[0] = reinterpret_cast<FuncPtr>(&doSomething);

转换函数指针的代码是不可移植的（C++不保证所有的函数指针都被用一样的方法表示），在一些情况下这样的转换会产生不正确的结果。

### **2. dynamic_cast** vs **static_cast**

class B { ... }; 



class D : public B { ... }; 

void f(B* pb) 
{

  D* pd1 = **dynamic_cast**<D*>(pb);

  D* pd2 = **static_cast**<D*>(pb); 
} 
  如果pb确实是指向D类型对象，则pd1和pd2将拥有同样的值。即使pd为0，它们的值也相同。如果pb执行B类型对象而不是D类型，则dynamic_cast返回0.可是static_cast依赖编程者安全断言--pb指向D类型对象并简单地返回给定的D对象指针。



  即**dynamic_cast**可用于继承体系中的向下转型，即将基类指针转换为派生类指针，比**static_cast**更严格更安全。**dynamic_cast**在执行效率上比**static_cast**要差一些，但**static_cast**在更宽上范围内可以完成映射，这种不加限制的映射伴随着不安全性。**static_cast**覆盖的变换类型除类层次的静态导航以外，还包括无映射变换、窄化变换(这种变换会导致对象切片,丢失信息)、用VOID*的强制变换、隐式类型变换等.