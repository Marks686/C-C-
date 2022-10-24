> 前言，我们C++这门语言，兼容C语言，C++是C语言的升级。






# 1.C++关键字

> C++总计63个关键字，C语言32个关键字



![在这里插入图片描述](https://img-blog.csdnimg.cn/abdcb8a24e324eecbd0a0f0629a55ac4.png)

# 2.命名空间

我们先使用C++来写一个"hello world"

```c
#include<iostream>
using namespace std;
int main()
{
	cout << "hello world" << endl;
	return 0;
}
```


或者

```c
#include<iostream>

int main()
{
	std::cout << "hello world" << std::endl;
	return 0;
}
```

这两种写法主要涉及两个知识，一个是IO流，另一个是命名空间，
C++在设计初期就是为了解决C语言的一些不足，C语言第一个不足之处就是名字冲突，什么是名字冲突？

```c
#include<stdio.h>
#include<stdlib.h>
int rand = 0;
int main()
{
	printf("hello world\n");
	printf("%d\n", rand);
	return 0;
}
```
这个代码一编译，就出问题了，![在这里插入图片描述](https://img-blog.csdnimg.cn/e2be94fcbd8940588d2d941f168649c9.png)
这个问题叫重定义，rand重定义，因为我们定义的rand和#include<stdlib.h>库里面的rand重定义，库里的rand是一个函数，我们定义的rand是一个变量，我们自己定义的变量和库的函数名冲突了，这就叫做命名冲突问题，C++为了解决这个问题，引入了一个概念叫做命名空间。

namespace是C++中的一个关键字，和struct类似，用它可以定义命名空间，，命名空间可以随便定义，我们把我们定义的rand放到命名空间里去，此时就不会冲突了，

```c
#include<stdio.h>
#include<stdlib.h>

namespace bit
{
	int rand = 0;
}
int main()
{
	printf("hello world\n");
	printf("%d\n", rand);
	return 0;
}
```
因为命名空间已经把它隔离起来了，我们平时搜索变量会在全局搜索，全局有两个rand，此时肯定就会报错，现在有一个rand在命名空间bit里面，就不会冲突，

```c
	printf("%d\n", rand);
```
这个里面的rand默认访问的是库里的。此时我们想访问命名空间的怎么办？C++中有一个叫做域作用限定符，rand这个变量属于bit这个域，若我们想访问的话，我们可以指定访问bit这个命名空间。

```c
printf("%d\n", bit::rand);
```

为了更好的防止冲突，C++定义了一种叫 std 的东西，C++标准库的东西放进了std命名空间。

```c
#include<iostream>
int main()
{
	std::cout << "hello world" << std::endl;
	return 0;
}
```

C语言printf是打印字符串或者指定你要打印什么格式，C++中 

```c
std::cout << "hello world" << std::endl;
```
这个叫IO流，可以自动识别类型，不同类型可以拼在一起。

问题：rand是全局变量还是局部变量？

```c
namespace bit
{
	int rand = 0;
}
```
它是一个全局变量，只有一种变量是局部变量，只有定义在函数中的变量是局部变量，局部变量的特点是它存在栈上，因为它在栈帧里面，在函数调用的时候会建立栈帧，函数结束栈帧销毁，变量也就随之销毁了。命名空间中建立的变量并不是在函数栈帧上的，它和在全局建立的rand是一样的，只是为了防止冲突，把它放在域里面，影响的只是它的查找规则，命名空间不影响生命周期，只是一个限定域，影响的是查找规则。

> 命名空间里面除了定义变量还可以定义函数、类型

直接定义，和定义在命名空间的区别是什么?
区别就是，放在全局的话，可以看成就是种了一片果树，谁都能摘，加了命名空间就是加了个围墙，如果是全局的我们就直接可以调用，
否则我们就要用指令进行访问。

```c
namespace bit
{
	int Add(int left, int right)
	{
		return left + right;
	}
	struct Node
	{
		struct Node* next;
		int val;
	};
}

int main()
{
	printf("%d\n", bit::Add(1, 2));
	
    struct bit::Node node;
	return 0;
}
```
用围墙围起来的好处是为了防止跟别人冲突。访问谁就指定谁，互相不会干扰，避免了命名冲突。

```c
namespace bit
{
	int Add(int left, int right)
	{
		return left + right;
	}

	struct Node
	{
		struct Node* next;
		int val;
	};
}

namespace byte
{
	int Add(int left, int right)
	{
		return left*10 + right*10;
	}

	struct Node
	{
		struct Node* next;
		struct Node* prev;
		int val;
	};
}
int main()
{
	printf("%d\n", bit::Add(1, 2));
	printf("%d\n", byte::Add(1, 2));

	struct bit::Node node;
	struct byte::Node dnode;
	return 0;
}
```
还有就是，命名空间里面还可以嵌套命名空间， 

```c
namespace N1
{
	int a;
	int b;
	int Add(int left, int right)
	{
		return left + right;
	}
	namespace N2
	{
		int c;
		int d;
		int Sub(int left, int right)
		{
			return left - right;
		}
	}
}

int main()
{
	//访问a，c
	N1::a = 1;
	N1::N2::c = 2;
	return 0;
}
```
里面的变量都是全局的，只是访问略有差别。

我们为了避免冲突C++把标准库里的东西都定义在了它官方的一个命名空间，这个命名空间的名字叫std（C++官方库内容定义的命名空间），官方库里的东西能不能只定义在一个文件里面？不能，C++允许定义同名的多个命名空间，它定义在多个文件，多个文件它会自动合并。

>  同一个工程中允许存在多个相同名称的命名空间,编译器最后会合成同一个命名空间中。



既然标准库把它们的东西都定义在命名空间里去，我们每次打印东西，这样写是很麻烦的，

```c
#include<iostream>
int main()
{
	std::cout << "hello world" << std::endl;
	return 0;
}
```
所以命名空间又开发了一种简单的使用方式，这种方式叫

```c
#include<iostream>
using namespace std;
int main()
{
	cout << "hello world" << endl;
	return 0;
}
```

这种方式的意义就是把命名空间里面的内容给展开，默认不指定，编译器是编译不过的。这种方式的好处就是用起来方便了，相当于把命名的隔离墙给拆了，那这种写法有什么坏处呢？
自己定义变量时容易冲突。

所以，我们建议日常练习，写个小程序，这么用可以，项目最好不这样用。在项目用的时候我们也不需要那么死板，我们可以不拆墙，进行部分展开或者叫指定展开，我们可以这样

```c
#include<iostream>
//指定展开
using std::cout;
using std::endl;

int main()
{
	cout << "hello world" << endl;
	return 0;
}
```

这种方式我们可以把常用的展开，自己定义的时候避免跟常用的重名即可。尽量避免全部展开，全部展开是有很大的风险的。


# 3. C++输入&输出

cout我们目前讲不清楚，因为cout是一个对象，我们后面讲它的原理，现阶段我们就记住，理解一个形象的说法。cout我们可以认为它就是那个控制台，我们写一个hello world可以这样写：

```c
	cout << "hello world!" << endl;
```

hello world!这个字符串流向cout这个对象里面去，这个对象就是控制台，endl代表的是换行。特点就是自动识别类型，

```c
cin >> a;
```
cin是流提取，指输入的东西从cin里面拿到，也可以自动识别类型
![在这里插入图片描述](https://img-blog.csdnimg.cn/8b158b422e4a40baa3458c456c336c50.png)
可以结合C语言输出。哪个方便用哪个。

> 注意：早期标准库将所有功能在全局域中实现，声明在.h后缀的头文件中，使用时只需包含对应 头文件即可，后来将其实现在std命名空间下，为了和C头文件区分，也为了正确使用命名空间，规定C++头文件不带.h；旧编译器(vc 6.0)中还支持<iostream.h>格式，后续编译器已不支持，因此推荐使用  < iostream > +std的方式。

# 4. 缺省参数

> 缺省参数是声明或定义函数时为函数的参数指定一个**缺省值**。在调用该函数时，如果没有指定实参则采用该形参的缺省值，否则使用指定的实参。

```c
#include <iostream>
using namespace std;
void Func(int a = 0)
{
	cout << a << endl;
}

int main()
{
	Func();
	Func(10);
	return 0;
}

```
有了这个缺省值，我们可以传参，也可以不传参，如果不传，a的值是0，传的话就没有缺省值的事了，10作为实参传给形参a。
缺省参数扩展一下，如果有多个缺省参数呢？
可以都给缺省值，

```c
void Func(int a = 10, int b = 20, int c = 30)
{
	cout << "a = " << a << endl;
	cout << "b = " << b << endl;
	cout << "c = " << c << endl;
}
int main()
{
	Func();
	Func(1);
	Func(1,2);
	Func(1, 2,3);
	return 0;
}

```
调用这个函数可以怎么调呢？
第一种：一个都不传，全部用缺省值。

```c
Func();
```

第二种：可以传一个，这一个值是传给a的，默认从左往右传，b、c还用的是缺省值。

```c
Func(1);
```

第三种：传两个值，一样是从左往右依次传，不能跳着传。

```c
Func(1,2);
```
最后一种就是全传

```c
Func(1,2，3);
```

到底是从左往右传还是从右往左传，这个都是不一定的，要看编译器平台。

全部缺省叫全缺省，它的调用非常灵活。除了全区缺省，根据需求还可以半缺省，

```c
void Func(int a, int b = 10, int c = 20)
{
	cout << "a = " << a << endl;
	cout << "b = " << b << endl;
	cout << "c = " << c << endl;
}
int main()
{
	Func(1);
	Func(1,2);
	Func(1,2,3);
	return 0;
}
```
此时半缺省我们就不能不传，传一个相当于传给a，传三个相当于缺省值一个也没用。

> 注意：部分缺省只能从右往左连续缺省。


这样是不可以的：
```c
void Func(int a, int b = 10, int c)
```
缺省参数有一个特点，就是不能在函数声明和定义中同时出现，在声明中给。缺省值必须是常量或者全局变量。


# 5. 函数重载
一个词可以有多重含义，人们可以通过上下文来判断词真是的含义，即该词被重载了。
 
> 函数重载：是函数的一种特殊情况，C++允许在同一作用域中声明及格功能类似的同名函数，这些同名函数的形参列表（参数个数或类型或类型顺序）不同，常用来处理实现功能类似数据类型不同的问题。

```c
int add(int x, int y)
{
	return x + y;
}

double add(double x, double y)
{
	return x + y;
}

int main()
{
	cout << add(1, 2) << endl;
	cout << add(1.1, 2.2) << endl;
	return 0;
}
```
C语言是不允许这俩函数同时存在的，C++可以，但是也有一些要求。
要求：函数名相同，参数不同（个数、类型、顺序）

```c
void swap(int* px, int* py)
{
	int tmp = *px;
	*px = *py;
	*py = tmp;
}

void swap(double* px, double* py)
{
	double tmp = *px;
	*px = *py;
	*py = tmp;
}

int main()
{
	int a = 0, b = 1;
	swap(&a, &b);
	
	double c = 1.1, d = 2.2;
	swap(&c, &d);
	return 0;
}
```
感觉那两个地方用的是同一个函数，但实际上不是同一个函数，这只是编译器自动识别。

类型不同：

```c
void swap(int* px, int* py)
{
	int tmp = *px;
	*px = *py;
	*py = tmp;
}

void swap(double* px, double* py)
{
	double tmp = *px;
	*px = *py;
	*py = tmp;
}
```
个数不同：

```c
void f()
{
	cout << "f()" << endl;
}
void f(int a)
{
	cout << "f(int a)" << endl;
}
```
顺序不同：

```c
void f(int a, char b)
{
 cout << "f(int a,char b)" << endl;
}
void f(char b, int a)
{
 cout << "f(char b, int a)" << endl;
}
```
顺序不同指的是形参类型顺序不同。

 


```c
void f()
{
	cout << "f()" << endl;
}

void f(int a = 0, char b = 1)
{
	cout << "f(int a,char b)" << endl;
}
```
这俩构成函数重载，但是调用的时候有没有问题？

```c
int main()
{
	f(1);
	f(10,20);

	f();
	return 0;
}
```
前两个都没有问题，但是第三个就有问题了，“f”对重载函数的调用不明确，存在歧义。
