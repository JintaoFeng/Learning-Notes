# STL

STL(标准模板库)是惠普实验室开发的一系列软件的统称。STL从广义上分为：容器、算法、迭代器，容器和算法之间通过迭代器进行无缝连接。STL几乎所有的代码都采用了模板类或者模板函数。

STL提供了六大组件，彼此之间组合套用，这六大组件分别是容器、算法、迭代器、仿函数、适配器、空间配置器。

容器：各种数据结构，如vector、list、deque、set、map等，用来存放数据，从实现角度来看，STL容器是一种class template。

算法：各种常用的算法，如sort、find、copy、for_each。从实现的角度来看，STL算法是一种function tempalte。

迭代器：扮演了容器与算法之间的胶合剂，共有五种类型，从实现角度来看，迭代器是一种将operator*、operator->、operator++、operator—等指针相关操作予以重载的class tempalte。所有STL容器都附带有自己专属的迭代器，只有容器的设计者才知道如何遍历自己的元素。原生指针也是一种迭代器。

仿函数：行为类似函数，可作为算法的某种策略。从实现角度来看，仿函数是一种重载了operate()的class或者class tempalte。

适配器：一种用来修饰容器或者仿函数或迭代器接口的东西。

空间配置器：负责空间的配置与管理。从实现角度来看，配置器是一个实现了动态空间配置、空间管理、空间释放的class tempalte。

## 容器

常用的数据结构有：数组(array)、链表(list)、树(tree)、栈(stack)、队列(queue)、集合(set)、映射表(map)，根据数据在容器中的排列特性，这些数据分为序列式容器和关联式容器两种。

* 序列式容器强调值得排序，序列式容器中得每个元素均有固定得位置，除非用删除或插入得操作改变这个位置。如Vectorr容器、Deque容器、List容器等。
* 关联式容器是非线性得树结构，更准确得说是二叉树结构。各元素之间没有严格得物理上得顺序关系，也就是说元素在容器中并没有保存元素置入容器时得逻辑顺序。关联式容器得另一个显著得特点是：在值中选择一个值作为关键字key，这个关键字对值起到索引得作用，方便查找。

## 算法

算法分为：质变算法和非质变算犯法。

质变算法：是指运算过程中会更改区间内得元素得内容。例如拷贝、替换、删除等。

非质变算法：是指运算过程中不会更改区间内得元素内容，例如查找、计数、遍历、寻找极值等。

## 迭代器

迭代器是一种抽象得设计概念，现实程序语言中并没有直接对应于这个概念得实物。iterator模式定义如下：提供一种方法使之能够依序寻访某个容器所含得各个元素，而又无需暴露该容器得内部表示方式。

迭代器得种类：

| 输入迭代器     | 提供对数据的只读访问                                         | 只读，支持++、==、！=                  |
| -------------- | ------------------------------------------------------------ | -------------------------------------- |
| 输出迭代器     | 提供对数据的只写访问                                         | 只写、支持++                           |
| 前向迭代器     | 提供读写操作，并能向前推进迭代器                             | 读写、支持++、==、！=                  |
| 双向迭代器     | 提供读写操作、并能向前和向后操作                             | 读写，支持++、–                        |
| 随机访问迭代器 | 提供读写操作，并能以跳跃的方式访问容器的任意数据，是功能最强的迭代器 | 读写、支持++、–、[n]、-n、<、<=、>、>= |
|                |                                                              |                                        |

## 常用容器

### string容器

C++标准库定义了一种string类，定义在头文件<string>

String 和c风格字符串相比：

* `char*`是一个指针，`string`是一个类。

`string`封装了`char*`,管理这个字符串，是一个`char*`型的容器。

* `string`封装了很多实用的成员方法。

查找`find`，拷贝`copy`,删除`delete`，替换`replace`，插入`insert`。

* 不考虑内存释放和越界。

string管理char*所分配的内存，每一次string的复制，取值都由string类负责维护，不用担心复制越界和取值越界等。

### string容器常用操作

#### string构造函数

```c++
string();	//创建一个空的字符串，例如：string str;
string(const string& str);	//使用一个string对象初始化另一个string对象。
string(const char* s);	//使用字符串s初始化
string(int n,char c);	//使用n各字符c初始化
```

#### string基本赋值操作

```c++
string& operator=(const char* s);	//char*类型字符串，赋值给当前的字符串
string& operator=(const string &s);	//把字符串s赋给当前的字符串
string& operator=(char c);	//字符赋值给当前的字符串
string& assign(const char* s);	//把字符串s赋值给当前的字符串
string& assign(const char* s，int n);	//把字符串s的前n个字符赋值给当前的字符串
string& assign(const string &s);	//把字符串s赋给当前字符串
string& assign(int n,char c);	//用n个字符c赋给当前字符串
string& assign(const string &s,int start,int n);	//将s从start开始n个字符赋值给字符串
```

#### string存取字符操作

```c++
char& operator[](int n);	//通过[]方式取字符
char& at(int n);	//通过n方法取字符
```

#### string拼接操作

```c++
string& operator+=(const string& str);	//重载+=操作符
string& operator+=(const char* str);	//重载+=操作符
string& operator+=(const char c);	//重载+=操作符
string& append(const char *s);	//把字符串 s 连接到当前字符串结尾
string& append(const char *s, int n);	//把字符串 s 的前 n 个字符连接到当前字符串结尾
string& append(const string &s);	//同 operator+=()
string& append(const string &s, int pos, int n);	//把字符串 s 中从 pos 开始的 n 个字符连接到当前字符串结尾
string& append(int n, char c);	//在当前字符串结尾添加 n 个字符 c
```

#### string查找和替换

```c++
int find(const string& str, int pos = 0) const; 	//查找 str 第一次出现位置,从 pos 开始查找
int find(const char* s, int pos = 0) const; 	//查找 s 第一次出现位置,从 pos 开始查找
int find(const char* s, int pos, int n) const; 	//从 pos 位置查找 s 的前 n 个字符第一次位置
int find(const char c, int pos = 0) const; 	//查找字符 c 第一次出现位置
int rfind(const string& str, int pos = npos) const;	//查找 str 最后一次位置,从 pos 开始查找
int rfind(const char* s, int pos = npos) const;	//查找 s 最后一次出现位置,从 pos 开始查找
int rfind(const char* s, int pos, int n) const;	//从 pos 查找 s 的前 n 个字符最后一次位置
int rfind(const char c, int pos = 0) const; 	//查找字符 c 最后一次出现位置
string& replace(int pos, int n, const string& str); 	//替换从 pos 开始 n 个字符为字符串 str
string& replace(int pos, int n, const char* s); 	//替换从 pos 开始的 n 个字符为字符串 s
```

#### string 比较操作

```c++
/*
compare 函数在>时返回 1，<时返回 -1，==时返回 0。
比较区分大小写，比较时参考字典顺序，排越前面的越小。
大写的 A 比小写的 a 小。
*/
int compare(const string &s) const;//与字符串 s 比较
int compare(const char *s) const;//与字符串 s 比较
```

#### string子串

```c++
string substr(int pos = 0, int n = npos) const;//返回由 pos 开始的 n 个字符组成的字符串
```

#### string插入和删除操作

```c++
string& insert(int pos, const char* s); //插入字符串
string& insert(int pos, const string& str); //插入字符串
string& insert(int pos, int n, char c);//在指定位置插入 n 个字符 c
string& erase(int pos, int n = npos);//删除从 Pos 开始的 n 个字符
```

#### string 和c-style字符串转换

```c++
//string 转 char*
string str = "itcast";
const char* cstr = str.c_str();
//char* 转 string
char* s = "itcast";
string str(s);
```

在 c++中存在一个从 const char* 到 string 的隐式类型转换，却不存在从一个 string 对
象到 C_string 的自动类型转换。对于 string 类型的字符串，可以通过 c_str()函数返回 string
对象对应的 C_string.
通常，程序员在整个程序中应坚持使用 string 类对象，直到必须将内容转化为 char*时才
将其转换为 C_string.

### vecrot 容器

vector容器的数据安排以及操作方式，与array非常相似，两者的唯一差别在于空间的运用的灵活性。Array是静态空间，一旦配置了就不能改变，要换大一点或者小一点的空间，得需要自己完成一切。首先需要配置一块新的空间，然后将旧空间的数据搬往新空间，再释放原来的空间。Vector是动态空间，随着元素的加入，它的内部机制会自动扩充空间以容纳新元素。因此vector的运用对于内存的合理利用与运用的灵活性都有很大的帮助。

![image-20201211230624336](..\picture\image-20201211230624336.png)

#### vector迭代器

Vector 维护一个线性空间，所以不论元素的型别如何，普通指针都可以作为 vector 的迭代器，因为 vector 迭代器所需要的操作行为，如 operaroe*, operator->, operator++,operator--, operator+, operator-, operator+=, operator-=, 普通指针天生具备。Vector支持随机存取，而普通指针正有着这样的能力。所以 vector 提供的是随机访问迭代器

我们可以得知：

```c++
vector<int>::iterator it1;
vector<Teacher>::iterator it2;
```

it1的型别是int*，it2的型别是Teacher\*

#### vecrot的数据结构

vector所采用的数据结构非常简单，线性连续空间，他以两个迭代器\_myfirst和_mylast分别指向配置得来的连续空间中目前已被使用的范围，并以迭代器\_myend指向整块连续内存空间的尾端。

为了降低空间配置时的速度成本，vecrot实际配置的大小可能比客户端需求大一些，以备将来可能的扩充。换句话说，一个vector的容量永远大于或等于其大小，一旦容量等于大小，便是满载，下次再有新增元素，整个vector容器就得重新换一块地方

注意：所谓动态增加大小，并不是在原空间之后续接新空间(因为无法保证原空间之后尚有可配置的空间)，而是一块更大的内存空间，然后将原数据拷贝新空间，并释放原空间。因此，对vector的任何操作，一旦引起空间的重新配置，指向原vector的所有迭代器就都失效了。

#### vector常用API

##### 构造函数

```c++
vector<T> v; 	//采用模板实现类实现，默认构造函数
vector(v.begin(), v.end());	//将 v[begin(), end())区间中的元素拷贝给本身。
vector(n, elem);	//构造函数将 n 个 elem 拷贝给本身。
vector(const vector &vec);		//拷贝构造函数。
	//例子 使用第二个构造函数 我们可以...
int arr[] = {2,3,4,1,9};
vector<int> v1(arr, arr + sizeof(arr) / sizeof(int));
```

##### vector长用赋值操作

```c++
assign(beg, end);//将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);//将 n 个 elem 拷贝赋值给本身。
vector& operator=(const vector &vec);//重载等号操作符
swap(vec);// 将 vec 与本身的元素互换。
```

##### vector大小操作

```c++
size();		//返回容器中元素的个数
empty();	//判断容器是否为空
resize(int num);	//重新指定容器的长度为 num，若容器变长，则以默认值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。
resize(int num, elem);	//重新指定容器的长度为 num，若容器变长，则以 elem 值填充新位置。如果容器变短，则末尾超出容器长>度的元素被删除。
capacity();		//容器的容量
reserve(int len);	//容器预留 len 个元素长度，预留位置不初始化，元素不可访问。
```

##### vector数据存取操作

```c++
at(int idx); 	//返回索引 idx 所指的数据，如果 idx 越界，抛出 out_of_range 异常。
operator[];		//返回索引 idx 所指的数据，越界时，运行直接报错
front();	//返回容器中第一个数据元素
back();		//返回容器中最后一个数据元素
```

##### vector插入和删除操作

```c++
insert(const_iterator pos, int count,ele);	//迭代器指向位置 pos 插入 count 个元素 ele.
push_back(ele); 	//尾部插入元素 ele
pop_back();		//删除最后一个元素
erase(const_iterator start, const_iterator end);//删除迭代器从 start 到 end 之间的元素
erase(const_iterator pos);//删除迭代器指向的元素
clear();//删除容器中所有元素
```

#### 例子

```c++
int main()
{
	vector<int> v;
	for (int i = 0; i < 100000; i++)
	{
		v.push_back(i);
	}
	cout << "capacity:" << v.capacity() << endl;
	cout << "sie:" << v.size() << endl;

	//通过resize改变容器的大小
	v.resize(10);

	cout << "capacity:" << v.capacity() << endl;
	cout << "sie:" << v.size() << endl;

	vector<int>(v).swap(v);

	cout << "capacity:" << v.capacity() << endl;
	cout << "sie:" << v.size() << endl;

}
```

```
int main()
{
	vector<int> v;
	//v.reserve(100000);
	int* pStart = NULL;
	int count = 0;
	for (int i = 0; i < 100000; i++)
	{
		v.push_back(i);
		if (pStart != &v[0])
		{
			pStart = &v[0];
			count++;
		}
	}
	cout << "count:" << count << endl;
}
```

### deque容器

Vector容器是单向开口的连续内存空间，deque则是一种双向开口的连续线性空间。所谓的双向开口，意思是可以在头尾两端分别做元素的插入和删除操作，当然，vector荣光其也可以在头尾两端插入元素，但是在其头部操作效率奇差，无法被接受。

![image-20201212161437048](..\picture\image-20201212161437048.png)

Deque容器和vector容器最大的差异，一在于deque允许使用常数项时间对头端进行元素的插入和删除。二在于deque没有容量的概念，因为他是动态的以分段连续空间组合而成，随时可以增加一段新的空间。所以deque容器不像vector容器那样：旧空间不足而重新配置一块更大空间，然后赋值元素，再释放旧空间。这种事情不会发生在deque容器上。所以，deque容器没有reserve功能。

虽然deque容器也提供了Random Access Iterator，但是他的迭代器并不是普通的指针。其复杂度和vector不是一个量级。

Deque是由一段一段定量的连续空间组成。一旦有必要在deque前端或者尾端增加新的空间，便配置一段连续定量的空间，串接在deque的头端或者尾端。Deque最大的工作就是维护这些分段连续的内存空间整体性的假象，并提供随机存取的接口，避开了重新配置空间，复制，释放的轮回，代价就是复杂的迭代器架构。

Deque 采取一块所谓的 map(注意，不是 STL 的 map 容器)作为主控，这里所谓的 map是一小块连续的内存空间，其中每一个元素(此处成为一个结点)都是一个指针，指向另一段连续性内存空间，称作缓冲区。缓冲区才是 deque 的存储空间的主体。

![image-20201213000327209](..\picture\image-20201213000327209.png)

#### deque常用API

##### 构造函数

```c++
deque<T> deqT;	//默认构造函数
deque(beg,end);		//构造函数将[beg,end)区间中的元素拷贝给本身
deque(n,elem);		//构造函数将n个elem拷贝给本身
deque(const deque &deq);	//拷贝构造函数
```

##### deque赋值操作

```c++
assign(beg, end);		//将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);		//将 n 个 elem 拷贝赋值给本身。
deque& operator=(const deque &deq); 		//重载等号操作符
swap(deq);		// 将 deq 与本身的元素互换
```

##### deque大下操作

````c++
deque.resize(num)	//重新指定容器的长度为num，若容器变长，则以默认值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除
deque.resize(num,elem)	//重新指定容器的长度为num，若容器变长，则以elem值填充新位置，如果容器变短，则末尾超出容器长度的元素被删除
````

##### deque数据存取

```c++
at(idx);	//返回索引idx所指的数据，如果idx越界，抛出out_of_range
operator[];	//返回索引idx所指的数据，如果idx越界，不抛出异常，直接出错
front();	//返回第一个数据
back();		//返回最后一个数据
```

##### deque删除操作

```c++
clear();	//移除容器的所有数据
erase(beg,end);		//删除[beg,end]区间的数据，返回i爱一个数据的位置
erase(pos);		//删除pos位置的数据，返回下一个数据的位置
```

### stack容器基本概念

stack是一种先进后出的数据结构，他只有一个出口，形式如下图所示。stack容器允许新增元素，移除元素，取得栈顶元素，但是除了最顶端外，没有任何其他方法可以存取stack的其他元素。换言之，stack不允许有遍历行为。

有元素推入栈的操作称为push，将元素推出stack的操作称为pop。

![image-20201223203357045](..\picture\image-20201223203357045.png)