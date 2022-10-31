# 1. 类的6个默认成员函数
![在这里插入图片描述](https://img-blog.csdnimg.cn/d8e68fb15bd244abb62d11acbbe788e1.png)
特殊成员函数，我们不写，编译器会自己生成一个，我们自己写了，我们写了，编译器就不会生成。
隐含的意思：对于有些类，需要我们自己写，对于另外一些类，编译器默认生成就可以用。

# 2. 构造函数
## 2.1概念

> **构造函数**：是一个特殊的成员函数，名字与类名相同,创建类类型对象时由编译器自动调用，以保证每个数据成员都有 一个合适的初始值，并且在对象整个生命周期内只调用一次。
## 2.2特性
构造函数是特殊的成员函数，需要注意的是，构造函数虽然名叫构造，但是构造函数的主要任务并不是开空间创建对象，而是**初始化对象**。
> 特征：
1. 函数名与类名相同。
2. 无返回值。
3. 对象实例化时编译器自动调用对应的构造函数。
4. 构造函数可以重载。(提供多个构造函数，多种初始化方式)


```cpp
class Date
{
public:
	Date(int year = 1, int month = 1, int day = 1)
	{
		_year = year;
		_month = month;
		_day = day;
	}
	void print()
	{
		cout << _year << "-" << _month << "-" << _day << endl;
	}
private:
	int _year;
	int _month;
	int _day;
};
int main()
{
	Date d1(2022, 9, 21);
	Date d2(2022, 9, 21);
	Date d3;
	d1.print();
	d2.print();
	d3.print();

	return 0;
}
```

> 栈的初始化

```cpp
	Stack(int capacity = 4)
	{
		_a = (int*)malloc(sizeof(int) * capacity);
		if (_a == nullptr)
		{
			perror("malloc fail");
			exit(-1);
		}

		_top = 0;
		_capacity = capacity;
	}
```


5. 如果类中没有显式定义构造函数，则C++编译器会自动生成一个无参的默认构造函数，一旦用户显式定义编译器将不再生成。
阅读下面代码并思考问题：
```cpp
 class Date
 {
  public:
 /*
 // 如果用户显式定义了构造函数，编译器将不再生成
 Date(int year, int month, int day)
 {
 _year = year;
 _month = month;
 _day = day;
 }
 */
 
 void Print()
 {
 cout << _year << "-" << _month << "-" << _day << endl;
 }
  
  private:
 int _year;
 int _month;
 int _day;
 };
  
  int main()
 {
 // 将Date类中构造函数屏蔽后，代码可以通过编译，因为编译器生成了一个无参的默认构造函
数
 // 将Date类中构造函数放开，代码编译失败，因为一旦显式定义任何构造函数，编译器将不再
生成
      // 无参构造函数，放开后报错：error C2512: “Date”: 没有合适的默认构造函数可用
 Date d1;
 return 0;
 }
```

> 问题：不实现构造函数的情况下，编译器会生成默认的构造函数。但是看起来默认构造函数又没什么用？d对象调用了编译器生成的默认构造函数，但是d对象_year/_month/_day，依旧是随机值。也就说在这里编译器生成的默认构造函数并没有什么用？？



答：C++把类型分成**内置类型**(基本类型)和**自定义类型**。内置类型就是语言提供的数据类型，如：int/char/任意类型指针...，自定义类型就是我们使用class/struct/union等自己定义的类型，看看下面的程序，就会发现编译器生成默认的构造函数会对自定类型成员_t调用的它的默认成员函数。

> 编译器会生成默认构造函数，编译器对内置类型不处理，自定义类型会调用它的默认构造。

```cpp
class Time
{
public:
 Time()
 {
 cout << "Time()" << endl;
 _hour = 0;
 _minute = 0;
 _second = 0;
 }
private:
 int _hour;
 int _minute;
 int _second;
};
class Date
{
private:
 // 基本类型(内置类型)
 int _year;
 int _month;
 int _day;
 // 自定义类型
 Time _t;
};
int main()
{
 Date d;
 return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/05088575805241d39ba17a00832eb735.png)


注意：C++11 中针对内置类型成员不初始化的缺陷，又打了补丁，即：内置类型成员变量在类中声明时可以给默认值。

```cpp
class Time
{
public:
	Time()
	{
		cout << "Time()" << endl;
		_hour = 0;
		_minute = 0;
		_second = 0;
 }
private:
	int _hour;
	int _minute;
	int _second;
};
class Date
{
private:
	// 基本类型(内置类型)
	int _year = 1970;
	int _month = 1;
	int _day = 1;
	// 自定义类型
	Time _t;
};
int main()
{
	Date d;
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ca4dff3d5b7e483785bc451452948396.png)


6. 无参的构造函数和全缺省的构造函数都称为默认构造函数，并且默认构造函数只能有一个。不传参数就可以调用的构造函数就叫默认构造。注意：无参构造函数、全缺省构造函数、我们没写编译器默认生成的构造函数，都可以认为是默认构造函数。
# 3. 析构函数
## 3.1概念
析构函数：与构造函数功能相反，析构函数不是完成对对象本身的销毁，局部对象销毁工作是由编译器完成的。**而对象在销毁时会自动调用析构函数，完成对象中资源的清理工作**。
## 3.2特性
1. 析构函数名是在类名前加上字符 ~。
2. 无参数无返回值类型。
3. 一个类只能有一个析构函数。若未显式定义，系统会自动生成默认的析构函数。注意：析构函数不能重载。
4. 对象生命周期结束时，C++编译系统系统自动调用析构函数。



```cpp
	~Stack()
	{
		free(_a);
		_a = nullptr;
		_top = _capacity = 0;
	}
```
5. 关于编译器自动生成的析构函数，是否会完成一些事情呢？下面的程序我们会看到，编译器
生成的默认析构函数，对自定类型成员调用它的析构函数。
6. 如果类中没有申请资源时，析构函数可以不写，直接使用编译器生成的默认析构函数，比如
Date类；有资源申请时，一定要写，否则会造成资源泄漏，比如Stack类。





# 4. 拷贝构造函数
## 4.1 概念
拷贝构造函数：只有**单个形参**，该形参是对本类类型对象的引用(一般常用const修饰)，在用**已存在的类类型对象创建新对象时由编译器自动调用**。
## 4.2特征
1. 拷贝构造函数是构造函数的一个重载形式。
2. 拷贝构造函数的参数只有一个且必须是类类型对象的引用，使用传值方式编译器直接报错，因为会引发无穷递归调用。









```cpp
class Date
{
public:
 Date(int year = 1900, int month = 1, int day = 1)
 {
 _year = year;
 _month = month;
 _day = day;
 }
    Date(const Date& d)   
 {
 _year = d._year;
 _month = d._month;
 _day = d._day;
 }
private:
 int _year;
 int _month;
 int _day;
};
int main()
{
 Date d1;
 Date d2(d1);
 return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/12cb08a9729244c78445eba10faca59b.png)
 

3. 若未显式定义，编译器会生成默认的拷贝构造函数。 默认的拷贝构造函数对象按内存存储按字节序完成拷贝，这种拷贝叫做浅拷贝，或者值拷贝。

> 注意：在编译器生成的默认拷贝构造函数中，**内置类型是按照字节方式直接拷贝**的，而**自定义类型是调用其拷贝构造函数**完成拷贝的。

5. 编译器生成的默认拷贝构造函数已经可以完成字节序的值拷贝了，还需要自己显式实现吗？
不是，例如栈，虽然帮我们完成了拷贝，但是不是我们想要的，
![在这里插入图片描述](https://img-blog.csdnimg.cn/8458c23f87744e1fad9dca6201501ae2.png)
最后我们会发现程序在free这个地方崩掉了，崩溃的原因就是因为同一块地方析构了两次。 
![在这里插入图片描述](https://img-blog.csdnimg.cn/4124fa23b851409d8814a4db20f1b06d.png)

# 5. 赋值运算符重载
## 5.1运算符重载
运算符重载是为了让自定义类型对象能用运算符
> C++为了增强代码的可读性引入了运算符重载，运算符重载是具有特殊函数名的函数，也具有其
返回值类型，函数名字以及参数列表，其返回值类型与参数列表与普通的函数类似。
函数名字为：关键字operator后面接需要重载的运算符符号。
函数原型：返回值类型 operator操作符(参数列表)

注意：
不能通过连接其他符号来创建新的操作符：比如operator@ 
重载操作符必须有一个类类型参数
用于内置类型的运算符，其含义不能改变，例如：内置的整型+，不 能改变其含义
作为类成员函数重载时，其形参看起来比操作数数目少1，因为成员函数的第一个参数为隐藏的this
.*   |   ::    |   sizeof   |    ?:    |    . 注意以上5个运算符不能重载。这个经常在笔试选择题中出现。

```cpp
bool operator==(const Date& d1, const Date& d2)
{
    return d1._year == d2._year
        && d1._month == d2._month
        && d1._day == d2._day;
}
```

```cpp
// 这里需要注意的是，左操作数是this，指向调用函数的对象
    bool operator==(const Date& d2)
 {
        return _year == d2._year;
            && _month == d2._month
            && _day == d2._day;
 }
```
