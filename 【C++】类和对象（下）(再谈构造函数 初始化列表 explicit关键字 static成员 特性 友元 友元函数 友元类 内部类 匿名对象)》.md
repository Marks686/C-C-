
# 再谈构造函数

我们之前学习构造函数的时候，调用构造之后对象中就已经有了一个初始值，但不能说它是对对象像成员变量的初始化，**构造函数体中的语句只能将其称为赋初值**，而不能称作初始化。因为初始化只能初始化一次，而构造函数体内可以多次赋值。如下：

```cpp
class Date
{
public:
    Date(int year, int month, int day)
    {
        _year = year;
        _month = month;
        _day = day;
    }
private:
    int _year;
    int _month;
    int _day;
};
```
## 初始化列表

> 初始化列表：**冒号开始，逗号分隔数据成员列表，每个成员变量后跟一个放在括号中的初始值或表达式**。

```cpp
class Date
{
public:
	Date(int year, int month, int day)
        : _year(year)
        , _month(month)
        , _day(day)
    {}
private:
    int _year;
    int _month;
    int _day;
};
```
【注意事项】
*     1.每个成员变量在初始化列表中只能出现一次(初始化只能初始化一次)。
*     2. 类中包含以下成员，必须放在初始化列表位置进行初始化：
      1）引用成员变量
      2）const成员变量
      3）自定义类型成员(且该类没有默认构造函数时)
```cpp
class A
{
public:
	A(int a)
		:_a(a)
	{}
private:
	int _a;
};
class B
{
public:
	B(int a, int ref)
		:_aobj(a)
		, _ref(ref)
		, _n(10)
	{}
private:
	A _aobj;  // 没有默认构造函数
	int& _ref;  // 引用
	const int _n; // const 
};
```

*     3.多使用初始化列表初始化，自定义类型成员变量一定会先使用初始化列表初始化。
*     4. 成员变量在类中声明次序就是其在初始化列表中的初始化顺序，与其在初始化列表中的先后次序无关。

```cpp
class A
{
public:
    A(int a)
       :_a1(a)
       ,_a2(_a1)
   {}
    
    void Print() {
        cout<<_a1<<" "<<_a2<<endl;
   }
private:
    int _a2;
    int _a1;
};
int main() {
    A aa(1);
    aa.Print();
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/41d88dbb68da4327b3afc7d45659d25b.png)


> 总结：1.尽量使用初始化列表初始化 2.一个类尽量提供默认构造。（推荐提供全缺省）

## explicit关键字
构造函数不仅可以构造与初始化对象，对于单个参数或者除第一个参数无默认值其余均有默认值的构造函数，还具有类型转换的作用。

```cpp
class Date
{
public:
Date(int year)
	:_year(year)
{}
Date& operator=(const Date& d)
{
	if (this != &d)
	{
		_year = d._year;
		_month = d._month;
		_day = d._day;
	}
	return *this;
}
private:
	int _year;
	int _month;
	int _day;
};
int main()
{

	int i = 0;
	double d = i;
	const double& rd = i;


	Date d1(2022);
	//隐式类型转化
	Date d2 = 2022;
	const Date& d5 = 2022;
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/4c8d0eda4b294878bb3b7d6e89ae2e35.png)


如果不想让隐式类型转换，**用explicit修饰构造函数，将会禁止构造函数的隐式转换。**


```cpp
explicit Date(int year)
	:_year(year)
{}
```


虽然有多个参数，但是创建对象时后两个参数可以不传递，没有使用`explicit`修饰，具有类型转换作用。

```cpp
explicit Date(int year, int month = 1, int day = 1)
	: _year(year)
	, _month(month)
	, _day(day)
{}
```

C++98不支持多参数构造，C++11支持：
```cpp
class Date
{
public:
	Date(int year, int month = 1, int day = 1)
		: _year(year)
		, _month(month)
		, _day(day)
	{}
private:
	int _year;
	int _month;
	int _day;
};

int main()
{
	Date d1 = { 2022,10,12 };
	return 0;
} 
```
# static成员
* 声明为static的类成员称为类的静态成员。
* 用static修饰的成员变量，称之为静态成员变量。
* 用static修饰的成员函数，称之为静态成员函数。
* 静态成员变量一定要在类外进行初始化
 


统计一下A对象创建了多少个

```cpp
int N = 0;

class A
{
public:
	A(int a)
		:_a(a)
	{
		++N;
	}

	A(const A& aa)
		:_a(aa._a)
	{
		++N;
	}
private:
	int _a;
};

void F1(A& aa)
{

}
//A F2()
//{
//	A aa;
//	return aa;
//}
int main()
{
	A aa1(1);
	A aa2 = 2;

	A aa3 = aa1;

	cout << N << endl;

	F1(aa1);
	cout << N << endl;

	//F2();
	//cout << N << endl;

	return 0;
}
```
C++里面尽量避免用全局变量，类里面不能定义全局变量，静态的影响变量生命周期和链接属性。
1.全局静态
2.局部静态
3.类里静态

```cpp
class A
{
public:
	A(int a)
		:_a(a)
	{
		++N;
	}

	A(const A& aa)
		:_a(aa._a)
	{
		++N;
	}
private:
	int _a;
	static int N;//声明
};
int A::N = 0;//定义初始化
```
类里面的`static`成员变量，属于类，并且是类的每一个对象共享的。同时构造函数不需要初始化静态的变量。我们在给`N`初始化的时候在类的外面初始。在类里面也是可以访问`N`的，生命周期是全局的，作用域受类域限制。 
##  特性

1. 静态成员为所有类对象所共享，不属于某个具体的对象，存放在静态区。
2. 静态成员变量必须在类外定义，定义时不添加static关键字，类中只是声明。
3. 类静态成员即可用 类名::静态成员 或者 对象.静态成员 来访问。
4. 静态成员函数没有隐藏的this指针，不能访问任何非静态成员。
5. 静态成员也是类的成员，受public、protected、private 访问限定符的限制。


# 友元

友元提供了一种突破封装的方式，有时提供了便利。但是友元会增加耦合度，破坏了封装，所以
友元不宜多用
## 友元函数

```cpp
class Date
{
 friend ostream& operator<<(ostream& _cout, const Date& d);
 friend istream& operator>>(istream& _cin, Date& d);
public:
 Date(int year = 1900, int month = 1, int day = 1)
 : _year(year)
 , _month(month)
 , _day(day)
 {}
private:
 int _year;
 int _month;
 int _day;
};
ostream& operator<<(ostream& _cout, const Date& d)
{
 _cout << d._year << "-" << d._month << "-" << d._day;
 return _cout; 
}
istream& operator>>(istream& _cin, Date& d)
{
 _cin >> d._year;
 _cin >> d._month;
 _cin >> d._day;
 return _cin;
}
int main()
{
 Date d;
 cin >> d;
 cout << d << endl;
 return 0;
}
```

* 友元函数可访问类的私有和保护成员，但不是类的成员函数。
* 友元函数不能用const修饰。
* 友元函数可以在类定义的任何地方声明，不受类访问限定符限制。
* 一个函数可以是多个类的友元函数。
* 友元函数的调用与普通函数的调用原理相同。


## 友元类
* 友元类的所有成员函数都可以是另一个类的友元函数，都可以访问另一个类中的非公有成员。
* 友元关系是单向的，不具有交换性。比如:上述Time类和Date类，在Time类中声明Date类为其友元类，那么可以在Date类中直接访问Time类的私有成员变量，但想在Time类中访问Date类中私有的成员变量则不行。
* 友元关系不能传递.
```cpp
class Time
{
   friend class Date;   // 声明日期类为时间类的友元类，则在日期类中就直接访问Time类
中的私有成员变量
public:
 Time(int hour = 0, int minute = 0, int second = 0)
 : _hour(hour)
 , _minute(minute)
 , _second(second)
 {}
   
private:
   int _hour;
   int _minute;
   int _second;
};
class Date
{
public:
   Date(int year = 1900, int month = 1, int day = 1)
       : _year(year)
       , _month(month)
       , _day(day)
   {}
   
   void SetTimeOfDate(int hour, int minute, int second)
   {
       // 直接访问时间类私有的成员变量
       _t._hour = hour;
       _t._minute = minute;
       _t._second = second;
   }
   
private:
   int _year;
   int _month;
   int _day;
   Time _t;
};
```
#  内部类
如果一个类定义在另一个类的内部，这个内部类就叫做内部类。内部类是一个独立的类，它不属于外部类，更不能通过外部类的对象去访问内部类的成员。外部类对内部类没有任何优越的访问权限。

```cpp
class A
{
private:
	int h;
public:// B天生就是A的友元
	class B
	{
		int _b;
	};
};

int main()
{
	cout << sizeof(A) << endl;
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/d4606a2033314f71b6080193a1dbc6f2.png)

这就说明`A`对象中没有`B`，若想让`A`中有`B`就要用到以后学的继承。A和B是两个独立的类，真正影响的是，B类的访问受A的类域和访问限定符的限制。

定义B对象：

```cpp
class A
{
private:
	int h;
public:
	class B
	{
		int _b;
	};
};
int main()
{
	A::B bb;
	return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/eda24987d0d1494a8c41f86ee870664c.png)

```cpp
class A
{
private:
 static int k;
 int h;
public:
 class B // B天生就是A的友元
 {
 public:
 void foo(const A& a)
 {
 cout << k << endl;//OK
 cout << a.h << endl;//OK
 }
 };
};
int A::k = 1;
int main()
{
    A::B b;
    b.foo(A());
    
    return 0;
}
```
# 匿名对象

```cpp
class A
{
public:
 A(int a = 0)
 :_a(a)
 {
 cout << "A(int a)" << endl;
 }
 ~A()
 {
 cout << "~A()" << endl;
 }
private:
 int _a;
};
class Solution {
public:
 int Sum_Solution(int n) {
 //...
 return n;
 }
};
int main()
{
 A aa1;
 // 不能这么定义对象，因为编译器无法识别下面是一个函数声明，还是对象定义
 //A aa1();
 // 但是我们可以这么定义匿名对象，匿名对象的特点不用取名字，
 // 但是他的生命周期只有这一行，我们可以看到下一行他就会自动调用析构函数
 A();
 A aa2(2);
 // 匿名对象在这样场景下就很好用，当然还有一些其他使用场景，这个我们以后遇到了再说
 Solution().Sum_Solution(10);
 return 0;
}
```
