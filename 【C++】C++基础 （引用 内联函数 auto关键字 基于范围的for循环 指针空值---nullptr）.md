# 1. 引用

> 引用不是新定义一个变量，而是给已存在变量取了一个名，编译器不会为引用变量开辟内存空间，它和它引用的变量共用同一块内存空间。

引用是用来让指针更简化的，它的第一个难点是，它与取地址用的是同一个符号。

```c
int main()
{
	int a = 10;
	int& ra = a;//引用
	int* p = &a;//取地址
	return 0;
}
```
从逻辑上来说就是取别名。

```c
    int a = 10;
	int& ra = a;
```
第一行的意思是开了四个字节空间，存了个10，这块空间开始取了个名字叫a，第二行的意思是，给a这块空间再取一个名字叫ra，ra就是a的别名。一个变量可以有多个别名。

```c
	int a = 10;
	int& ra = a;
	int& x = a;
	int& y = x;
```

以前我们写交换函数的时候是需要用指针的，有了引用我们就可以不用指针了。

```c
void swap(int& m, int& n)
{
	int tmp = m;
	m = n;
	n = tmp;
}
int main()
{
	int cc = 1;
	int dd = 2;
	swap(cc, dd);
	return 0;
}
```
这里m是cc的别名，n是dd的别名，没有引用的话就是传值，那m是cc的拷贝，n是dd的拷贝，m和n交换不影响cc和dd，如果用引用的话，m和n的交换就是cc和dd的交换，目的就很容易达到。

> 引用的特性：**1. 引用在定义时必须初始化2. 一个变量可以有多个引用3. 引用一旦引用一个实体，再不能引用其他实体。**


```c
void TestRef()
{
	int a = 10;
	int& ra = a;
	int& rra = a;
	printf("%p %p %p %p\n", &a, &ra, &rra);
}
```
结合上面的代码，我们可以看到下面的代码的意思是赋值，把b的值赋给ra所指的那块空间，也就是说将a的值改变了，对ra改变本质上就是对a改变。
```c
	int b = 20;
	ra = b; 
```
> 常引用：



指针和引用赋值中，权限可以缩小，但是不能放大

```c
	const int b = 1;
	int& rb = b;
```
上代码就是权限放大，有一个空间叫b，只能读，rb变成b的别名后就变成了可读可写，这个时候权限就放大了，但是权限是可以缩小的。
下面就是权限缩小:变成了只读。

```c
	int a = 0;
	const int& rra = a;
```

下面是权限的平移：

```c
	const int b = 1;
	const int& rb = b;
```
权限的放大和缩小只限于指针和引用。

下面的这种写法也不可以

```c
    double d = 12.34;
    int i = (int)d;
    //int& ri = d; 不可以
```
类型转换过程中，会产生一个临时变量，跟传值返回是一个道理。具体打印的时候打印的不是原来的变量，而是打印的产生的临时变量的值，语法规定临时变量具有常兴（不能修改），所以回到上面的问题，ri引用的是d吗？不是，引用的是那个临时变量，临时变量具有常性
```c 
void TestConstRef()
{
    const int a = 10;
    //int& ra = a;   // 该语句编译时会出错，a为常量
    const int& ra = a;
    
    // int& b = 10; // 该语句编译时会出错，b为常量
    const int& b = 10;

    double d = 12.34;
    //int& rd = d; // 该语句编译时会出错，类型不同
    const int& rd = d;
}
```







引用的使用场景

> 1.做参数

```c
void swap(int& left, int& right)
{
	int tmp = left;
	left = right;
	right = tmp;
}
```
这种参数严格来说叫做输出型参数。引用做参数和指针是重叠的功能。

相比传值传参，传值的话形参是实参的临时拷贝，
> 2.做返回值

```c
//引用返回
int& Count()
{
	static int n = 0;
	n++;
	//...
	return n;
}
//传值返回
int Count()
{
	static int n = 0;
	n++;
	//...
	return n;
}
```
引用返回和传值返回在中间过程有一个极大的不同，
![在这里插入图片描述](https://img-blog.csdnimg.cn/9f3466cd26014f4c837dab0fe83ca5a9.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/df1fb1565a4443aeaa9b96ea5a6806d6.png)

细品下面的例子：
```c
int& Count()
{
    int n = 0;
	n++;
	//...
	return n;
}

void Func()
{
	int x = 100;
}

int main()
{
	int& ret = Count();
	cout << ret << endl;
	cout << ret << endl;

	Func();
	cout << ret << endl;
	return 0;
}
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/46ac8c80e5324d83b3a8cd3f2429e62a.png)


下面是正确的用法：

```c
int& Count1()
{
    static int n = 0;
	n++;
	//...
	return n;
}
int Count2()
{
	int n = 0;
	n++;
	//...
	return n;
}
```


传引用返回：
**1.减少拷贝，提高效率。**
**2.修改返回值**

减少拷贝，提高效率：
```c
#include <time.h>
struct A { int a[10000]; };
A a;
// 值返回
A TestFunc1() { return a; }
// 引用返回
A& TestFunc2() { return a; }
void TestReturnByRefOrValue()
{
	// 以值作为函数的返回值类型
	size_t begin1 = clock();
	for (size_t i = 0; i < 100000; ++i)
		TestFunc1();
	size_t end1 = clock();
	// 以引用作为函数的返回值类型
	size_t begin2 = clock();
	for (size_t i = 0; i < 100000; ++i)
		TestFunc2();
	size_t end2 = clock();
	// 计算两个函数运算完成之后的时间
	cout << "TestFunc1 time:" << end1 - begin1 << endl;
	cout << "TestFunc2 time:" << end2 - begin2 << endl;
}

int main()
{
	TestReturnByRefOrValue();
	return 0;
}
```

```c
TestFunc1 time:124
TestFunc2 time:1
```

传引用返回确实有优化！

修改返回值：
![在这里插入图片描述](https://img-blog.csdnimg.cn/70d3d7efad1b444fa8bdd42b1ce4035c.png)


> **引用和指针的区别：**
> 在语法概念上引用就是一个别名，没有独立空间，和其引用实体共用同一块空间。在底层实现上实际是有空间的，因为引用是按照指针方式来实现的。


```c
int main()
{
	int a = 10;
	
	int& ra = a;
	ra = 20;

	int* pa = &a;
	*pa = 30;

	return 0;
}
```

> **引用语法上ra是a的别名，不开空间，底层实现，引用是使用指针实现的。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/f2ca3a824caa4dabaa88be0dae10db52.png)

引用和指针的不同点:
1. 引用概念上定义一个变量的别名，指针存储一个变量地址。
2. **引用**在定义时**必须初始化**，指针没有要求
3. **引用**在初始化时引用一个实体后，就**不能再引用其他实体**，而指针可以在任何时候指向任何
一个同类型实体
4. **没有NULL引用**，但有NULL指针
5. **在sizeof中含义不同：引用**结果为**引用类型的大小**，但**指针**始终是**地址空间所占字节个数**(32
位平台下占4个字节)
6. 引用自加即引用的实体增加1，指针自加即指针向后偏移一
7. **有多级指针，但是没有多级引用**
8. 访问实体方式不同，**指针需要显式解引用，引用编译器自己处理**
9. **引用比指针使用起来相对更安全**
# 2. 内联函数
内联函数就是在一个函数前面加个inline，inline是C++里新加的一个关键字，内联函数的特点是在调用它的地方展开，没有函数调用建立栈帧的开销，内联函数提升程序运行的效率。

函数调用是在栈这个地方不断去建立栈帧，栈帧里面存储的是局部变量，比如参数、返回值等等，栈这个区域的内存就像快餐式的，只用一次，栈帧里面除了这些还有一些其他的东西，我们在建立栈帧的时候这个过程是有消耗的，像我们写快排的时候，会大量的调用我们的swap函数，假设排10万个数据，此时的数据量非常大，Sort栈帧里不断去callSwap这个小函数，需要不断的建立栈帧销毁栈帧，甚至几十万次、几百万次，这个时候调用Swap函数消耗会非常大，某个函数调用特别特别多，在C语言中我们可以用宏函数，它没有栈帧的消耗是因为它预处理阶段替换了。（频繁调用的小函数）
C++给出了一种新方案，我们就可以使用内联函数，我们把建立Swap函数栈帧的消耗给优化掉，C++为什么要用内联函数来替换C语言的方案？因为宏的缺点：1.不能调试 2.没有类型安全的检查 3.容易写错。
inline函数就把上面几个缺点都给解决掉了。

> 写一个Add宏函数


```c
#define Add(a,b)((a)+(b))
```
宏是要做替换的，所以不能加分号，结合下面代码理解

```c
#define Add(a,b)((a)+(b))
int main()
{
	if (Add(1, 2))
	{

	}
	return 0;
}
```

如果不加外层的括号，下面的情况优先级就受到了影响，

```c
#define Add(a,b)((a)+(b))
int main()
{
	Add(1, 2) * 3;
	return 0;
}
```
里面的括号不加，如果a和b传的是表达式就出问题了，如下：

```c
#define Add(a,b)((a)+(b))
int main()
{
	Add(1 | 3, 2 & 4);
	return 0;
}
```

结合上面各种问题我们发现宏真的是很容易写错的一个东西，所以就有C++大佬就重新设计了一个东西，叫做内联函数来替代宏。

```c
inline int Add(int x, int y)
{
	return x + y;
}
```
上面的场景它都能适应，它讲宏的缺点进行了补充，也有宏的功能，它可以在它调用的地方进行展开。
内联在debug模式下默认不展开，因为展开了就不支持调试了。
 内联函数有没有缺陷呢？
 递归和长函数都不合适，会给编译器太大的负担，所以说不是我们写一个内联，编译器就一定去替换。
 

> 特性：

 
1. inline是一种以空间换时间的做法，如果编译器将函数当成内联函数处理，在编译阶段，会用函数体替换函数调用，缺陷：可能会使目标文件变大，优势：少了调用开销，提高程序运行效率。
2. inline对于编译器而言只是一个建议，不同编译器关于inline实现机制可能不同，一般建议：将函数规模较小(即函数不是很长，具体没有准确的说法，取决于编译器内部实现)、不是递归、且频繁调用的函数采用inline修饰，否则编译器会忽略inline特性。
![《C++prime》](https://img-blog.csdnimg.cn/280906d6f6a24a1d9dbbee1556274d76.png)


通过下面的实例和反汇编理解：
![在这里插入图片描述](https://img-blog.csdnimg.cn/c149556bccd445b2945dfe2571431d37.png)
为什么函数长了以后不展开？
代码膨胀！
![在这里插入图片描述](https://img-blog.csdnimg.cn/624f9d0f60a54725b3eaa5cbfeca426e.png)

回过头来，我们明白内联不应该都展开。

inline不建议声明和定义分离，分离会导致链接错误。因为inline被展开，就没有函数地址
了，链接就会找不到。

![在这里插入图片描述](https://img-blog.csdnimg.cn/09ac94b12fd34327b66b36a2090dd234.png)
加了内联为什么就找不到了？
在链接test.cpp的时候，认为func1是内联函数，正常使用内联函数是不会去call它的地址的，所以编译器不会把它放到符号表里，所以内联函数是不会对应生成上面那些指令的。加上内联，有下面的报错：
![在这里插入图片描述](https://img-blog.csdnimg.cn/953521c1805d4d88b08caa75ba1bb0c4.png)
所以以后内联可以直接在.h去定义！！
# 3. auto关键字(C++11)


> 随着程序越来越复杂，程序中用到的类型也越来越复杂，经常体现在：1. 类型难于拼写。2. 含义不明确导致容易出错。

auto用于我们定义变量会很方便，他会自动推导，编译器根据a的类型推导b的类型。

```c
int main()
{
	int a = 10;
	auto b = a;
	return 0;
}
```
实例：

```c
#include <string>
#include <map>
int main()
{
	std::map<std::string, std::string> m{ { "apple", "苹果" }, { "orange",
   "橙子" },
	  {"pear","梨"} };
	std::map<std::string, std::string>::iterator it = m.begin();
	return 0;
}
```

```c
std::map<std::string, std::string>::iterator 
```
这是一个类型，但是该类型太长了，特别容易写错。那么用auto就更好啊。
```c
#include <string>
#include <map>
int main()
{
	std::map<std::string, std::string> m{ { "apple", "苹果" }, { "orange",
   "橙子" },
	  {"pear","梨"} };
	//std::map<std::string, std::string>::iterator it = m.begin();
	auto it = m.begin();
	return 0;
}
```

阅读下面代码：
```c
int main()
{
    int x = 10;
    auto a = &x;
    auto* b = &x;
    auto& c = x;
    cout << typeid(a).name() << endl;
    cout << typeid(b).name() << endl;
    cout << typeid(c).name() << endl;
    *a = 20;
    *b = 30;
     c = 40;

	return 0;
}
```

```c
int *
int *
int
```
为什么c的类型是int呢？因为在语法上有一块空间，开始的名字是x，现在取了个别名叫c，c的类型还是int。

> 在同一行定义多个变量当在同一行声明多个变量时，这些变量必须是相同的类型，否则编译器将会报错，因为编译器实际只对第一个类型进行推导，然后用推导出来的类型定义其他变量。


```c
void TestAuto()
{
    auto a = 1, b = 2; 
    auto c = 3, d = 4.0;  
    // 该行代码会编译失败，
    //因为c和d的初始化表达式类型不同
}
```

> 注意：1. auto不能作为函数的参数。 2.auto不能直接用来声明数组。


auto不能做参数是因为编译要建立栈帧，auto没办法确定栈帧的大小。
auto更常用的我们以后再说。
# 4. 基于范围的for循环(C++11)

以前我们遍历数组，

```c
	int array[] = { 1, 2, 3, 4, 5 };
	for (int i = 0; i < sizeof(array) / sizeof(array[0]); ++i)
		cout << array[i] << endl;
```
写起来很不爽，太长了，老要去算数组的大小，现在我们用范围for遍历：

```c
	for (auto e : array)
	{
		cout << e << " ";
	}
	cout << endl;
```
它会依次取array中的数据赋值给e，自动判断结束，自动迭代。
# 5. 指针空值---nullptr(C++11)
C++98搞了一个坑，空指针的宏有BUG，

```c
#ifndef NULL
#ifdef __cplusplus
#define NULL   0
```
空被定义成了0，正常情况下，下面俩函数重载后，0就会传到第一个函数，NULL就会传到第二个，现在它俩都传给了第一个，就是因为上面的BUG，

```c
void f(int)
{
	cout << "f(int)" << endl;
}
void f(int*)
{
	cout << "f(int*)" << endl;
}
int main()
{
	f(0);
	f(NULL);
	f((int*)NULL);
	return 0;
}
```

C++11为了补上面的坑，新加了个东西叫nullptr

> 1. 在使用nullptr表示指针空值时，**不需要包含头文件**，因为**nullptr是C++11作为新关键字引入的**。
> 2. 在C++11中sizeof(nullptr) 与 sizeof((void*)0)所占的字节数相同。
> 3. 为了提高代码的健壮性，在后续表示指针空值时建议最好使用nullptr。
