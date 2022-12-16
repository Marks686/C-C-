# 泛型编程
实现通用的交换函数，可以使用函数重载实现，但是有两个不好的地方，1.重载的函数仅仅是类型不同，代码复用率比较低，只要有新类型出现时，就需要用户自己增加对应的函数2. 代码的可维护性比较低，一个出错可能所有的重载均出错。
我们能不能把大家都在用的东西刻成某一个模具，我们搞交换函数的时候可以写一个模具，类型不一样，代码的逻辑不受影响。
![在这里插入图片描述](https://img-blog.csdnimg.cn/bcf1bf885018448cbaa74e5be8a909cf.png)

# 函数模板
## 函数模板概念
函数模板代表了一个函数家族，该函数模板与类型无关，在使用时被参数化，根据实参类型产生函数的特定类型版本。
## 函数模板格式

```cpp
template<typename T>
void Swap(T& left, T& right)
{
		T tmp = left;
		left = right;
		right = tmp;
}
int main()
{
	int a, b;
	a = 1;
	b = 2;
	Swap(a,b);

	double c, d;
	c = 1.1;
	d = 2.2;
	swap(c,d);
	return 0;
}
```


## 函数模板的原理

通过反汇编我们可以看到实际在调用的时候不是调用的模板，实际调用的是编译器生成的函数。
![在这里插入图片描述](https://img-blog.csdnimg.cn/c0a213573a3440fcb24b5b2889de69c9.png)

我们的模板实际上是写给编译器的，编译器根据我们的调用，会去进行模板的推演。
![在这里插入图片描述](https://img-blog.csdnimg.cn/d4a5ef010aaf45b2aecec46820dd98a1.png)
生成对应函数的过程叫做函数模板的的实例化，它和对象的实例化不一样，对象实例化指的是有一个类，编译器根据内存对齐的规则来计算这个类多大，内存分布是什么样子，然后开一块空间再用构造函数初始化，而函数模板的实例化就是用函数模板实例化具体的函数，把对应的`T`替换成具体的类型。
## 函数模板的实例化
模板的实例化根据用的情况来看，类型越多实例化的越多。

> 1. 隐式实例化：让编译器根据实参推演模板参数的实际类型

```cpp
template<class T>
T Add(const T& left, const T& right)
{
	return left + right;
}
int main()
{
	int a1 = 10, a2 = 20;
	double d1 = 10.0, d2 = 20.0;
	Add(a1, a2);
	Add(d1, d2);
	//这种写法会报错

	// 此时有两种处理方式：1. 用户自己来强制转化 2. 使用显式实例化
	//Add(a1, (int)d1);
	return 0;
}
```

上代码不能通过编译，在编译期间，当编译器看到该实例化时，需要推演其实参类型通过实参a1将T推演为int，通过实参d1将T推演为double类型，但模板参数列表中只有一个T，编译器无法确定此处到底该将T确定为int 或者 double类型而报错注意：在**模板中，编译器一般不会进行类型转换操作，因为一旦转化出问题，编译器就需要背黑锅`Add(a1, d1)`**

	

> 2. 显式实例化：在函数名后的<>中指定模板参数的实际类型




```cpp
template<class T>
T Add(const T& left, const T& right)
{
	return left + right;
}
int main(void)
{
	int a = 10;
	double b = 20.0;

	// 显式实例化
	Add<int>(a, b);
	return 0;
}
```

## 模板参数的匹配原则
一个非模板函数可以和一个同名的函数模板同时存在，而且该函数模板还可以被实例化为这个非模板函数。

```cpp
// 专门处理int的加法函数
int Add(int left, int right)
{
	return left + right;
}
// 通用加法函数
template<class T>
T Add(T left, T right)
{
	return left + right;
}
int main()
{
	int a = 1, b = 2;
	Add(a, b);//调用已经写好的

	//实例化T是int的函数和本来就是int的函数可以同时存在
	Add<int>(a, b);

	return 0;
}
```

# 类模板



## 类模板的定义格式

```cpp
template<class T1, class T2, ..., class Tn>
class 类模板名
{
 // 类内成员定义
}; 
```


```cpp
template<typename T>
class Stack
{
public:
	Stack(int capacity = 4)
	{
		cout << "Stack(int capacity = )" <<capacity<<endl;

		_a = (T*)malloc(sizeof(T)*capacity);
		if (_a == nullptr)
		{
			perror("malloc fail");
			exit(-1);
		}

		_top = 0;
		_capacity = capacity;
	}
	
	~Stack()
	{
		cout << "~Stack()" << endl;

		free(_a);
		_a = nullptr;
		_top = _capacity = 0;
	}

	void Push(const T& x)
	{
		// ....
		// 扩容
		_a[_top++] = x;
	}

private:
	T* _a;
	int _top;
	int _capacity;
};


int main()
{
	// 类模板一般没有推演时机，函数模板实参传递形参，推演模板参数
	// 显示实例化
	// 他们是同一个类模板实例化出来的
	// 但是模板参数不同，他们就是不同类型
	Stack<double> st1; // double
	st1.Push(1.1);

	Stack<int> st2; // int
	st2.Push(1);

	return 0;
}
```

## 类模板的实例化
类模板实例化与函数模板实例化不同，类模板实例化需要在类模板名字后跟<>，然后将实例化的类型放在<>中即可，类模板名字不是真正的类，而实例化的结果才是真正的类。
```cpp
// Stack类名，Stack<int>才是类型
Stack<double> st1;
Stack<int> st2;
