# string
`string`是一个专门管理字符数组的类。

 ---
#  标准库中的string类
![在这里插入图片描述](https://img-blog.csdnimg.cn/b6ef447dbbdb4f3a9aabd22526f33cc0.png)
1. string是表示字符串的字符串类
2. 该类的接口与常规容器的接口基本相同，再添加了一些专门用来操作string的常规操作。
3. string在底层实际是：basic_string模板类的别名，typedef basic_string<char, char_traits, allocator> string;
4. 不能操作多字节或者变长字符的序列。

**在使用string类时，必须包含#include头文件以及using namespace std;**

 ---
## string类的常用接口

 `string()` ----> 构造空的string类对象，即空字符串
 
 `string(const char* s)` ----> 用C-string来构造string类对象


 `string(size_t n, char c)` ---->string类对象中包含n个字符c


 `string(const string&s)` ----> 拷贝构造函数

```cpp
#include<iostream>
#include<string>
using namespace std;
void test_test1()
{
	string s1;
	string s2("西安市高陵区");
	string s3 = "西安市高陵区";
	string s4(10, '*');

	cout << s1 << endl;
	cout << s2 << endl;
	cout << s3 << endl;
	cout << s4 << endl;

	string s5(s2);
	string s6 = s2;
	cout << s5 << s6 << endl;
}
int main()
{
	test_test1();
	return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/dbca00848938423e9f6c1e6e6ca7239e.png)

> `string`的三种遍历方式：



迭代器 ---- 通用的访问方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/f05cede1623c49279a00da40e8a06b96.png)
`Iterators`:行为上来说迭代器是一个像指针的东西。
`begin`:返回第一个位置的迭代器。
![在这里插入图片描述](https://img-blog.csdnimg.cn/49e444a82cc84c2dac4f1e85bcc34d81.png)

`end`：返回最后一个数据的下一个位置的迭代器。
![ ](https://img-blog.csdnimg.cn/770f38c094d342b58e21919bbae2d1b7.png)

```cpp
void test_test2()
{
	string s1("1234");
	//1.
	for (size_t i = 0; i < s1.size(); ++i)
	{
		s1[i]++;
	}
	cout << s1 << endl;

	//2.
	for (auto& ch : s1)
	{
		ch--;
	}
	cout << s1 << endl;
	//反转一下
	size_t begin = 0, end = s1.size() - 1;
	while (begin < end)
	{
		swap(s1[begin++], s1[end--]);
	}
	cout << s1 << endl;
	//迭代器
	string::iterator it1 = s1.begin();
	while (it1 != s1.end())
	{
		*it1 += 1;
		++it1;
	}

	it1 = s1.begin();
	while (it1 != s1.end())
	{
		cout << *it1 << " ";
		++it1;
	}
	cout << endl;
}
int main()
{
	test_test2();
	return 0;
}
```

```cpp
2345
1234
4321
5 4 3 2
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/1ce2961971be4898bb08876bfe81992d.png)

反向迭代器：`string::reverse_iterator`

```cpp
void test_string3()
{
	string s1("1234");
	string::iterator it = s1.begin();
	while (it != s1.end())
	{
		cout << *it << " ";
		++it;
	}
	cout << endl;

	string::reverse_iterator rit = s1.rbegin();
	while (rit != s1.rend())
	{
		cout << *rit << " ";
		++rit;
	}
	cout << endl;
}
int main()
{
	test_string3();
	return 0;
}
```

```cpp
1 2 3 4
4 3 2 1
```
反向迭代器也可以认为是一个类似 指针，指针是倒着遍历的。


---

 ## string类对象的容量操作

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/f818ee6781744dd1a06399a9bdd77555.png)

> `size`; 返回字符串有效字符长度 。


> `length` :返回字符串有效字符长度。

>`max_size()`:返回容量最大值

> `clear`:清空有效字符。

```cpp
void test_string4()
{
	string s("hello world");
	cout << s.size() << endl;
	cout << s.length() << endl;
	cout << s.max_size() << endl;
	cout << s.capacity() << endl;
	cout << s << endl;
	s.clear();
	cout << s << endl;

	//空间不清
	cout << s << endl;
	cout << s.capacity() << endl;

}
```

```cpp
11
11
2147483647
15
hello world


15
```

> `capacity`: 返回空间总大小。

```cpp
void TestPushBack()
{
	string s;
	size_t sz = s.capacity();
	cout << "capacity changed: " << sz << '\n';

	cout << "making s grow:\n";
	for (int i = 0; i < 100; ++i)
	{
		s.push_back('c');
		if (sz != s.capacity())
		{
			sz = s.capacity();
			cout << "capacity changed: " << sz << '\n';
		}
	}
}

void test_string5()
{
	TestPushBack();
}

int main()
{
	test_string5();
	return 0;
}
```

```cpp
capacity changed: 15
making s grow:
capacity changed: 31
capacity changed: 47
capacity changed: 70
capacity changed: 105
```

> `empty` :检测字符串释放为空串，是返回true，否则返回false。



> `reserve`:为字符串预留空间，提前开好空间，避免了扩容，提高效率

![在这里插入图片描述](https://img-blog.csdnimg.cn/e4139f4c73e748ef8d25b3272ebcae11.png)
请求string的capacity去size改变，把capacity的容量改成n来适应插入n个数据。就是改变capacity。

```cpp
void TestPushBack()
{
	string s;
	s.reserve(1000);
	size_t sz = s.capacity();
	cout << "capacity changed: " << sz << '\n';

	cout << "making s grow:\n";
	for (int i = 0; i < 100; ++i)
	{
		s.push_back('c');
		if (sz != s.capacity())
		{
			sz = s.capacity();
			cout << "capacity changed: " << sz << '\n';
		}
	}
}

void test_string5()
{
	TestPushBack();
}

int main()
{
	test_string5();
	return 0;
}
```

```cpp
capacity changed: 1007
making s grow:
capacity changed: 1510
```

>  `resize` :将有效字符的个数改成n个，多出的空间用字符c填充。







 ![在这里插入图片描述](https://img-blog.csdnimg.cn/45b68cc56eca4d9f89b868c987f271a0.png)
reserve影响的是capacity，只影响空间不会动数据，resize会改变string的长度，把string的长度改变到n，并且resize也会影响capacity。

```cpp
void test_string6()
{
	string s1("hello world");
	s1.resize(5);
	cout << s1.size() << endl;
	cout << s1.capacity() << endl;
	cout << s1 << endl << endl;

	string s2("hello world");
	s2.resize(15,'x');//在后面补齐
	cout << s2.size() << endl;
	cout << s2.capacity() << endl;
	cout << s2 << endl << endl;

	string s3("hello world");
	s3.resize(20,'x');
	cout << s3.size() << endl;
	cout << s3.capacity() << endl;
	cout << s3 << endl << endl;

}
int main()
{
	test_string6();
	return 0;
}
```

```cpp
5
15
hello

15
15
hello worldxxxx

20
31
hello worldxxxxxxxxx
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/f4e37880ad9549c4a5fa41e823373e3e.png)




`shrink_to_fit`:缩减容量。
![在这里插入图片描述](https://img-blog.csdnimg.cn/739754f017fd4f70a89d34d1ea9fc094.png)

 **注意：**


1. size()与length()方法底层实现原理完全相同，引入size()的原因是为了与其他容器的接口保持一致，一般情况下基本都是用size()。
2. clear()只是将string中有效字符清空，不改变底层空间大小。
3. resize(size_t n) 与 resize(size_t n, char c)都是将字符串中有效字符个数改变到n个，不同的是当字符个数增多时：resize(n)用0来填充多出的元素空间，resize(size_t n, char c)用字符c来填充多出的元素空间。注意：resize在改变元素个数时，如果是将元素个数增多，可能会改变底层容量的大小，如果是将元素个数减少，底层空间总大小不变。
4. reserve(size_t res_arg=0)：为string预留空间，不改变有效元素个数，当reserve的参数小于string的底层空间总大小时，reserver不会改变容量大小。



---
## string类对象的访问及遍历操作

>`operator[]` :返回pos位置的字符，const string类对象调用。

>`begin+ end`: begin获取一个字符的迭代器 + end获取最后一个字符下一个位置的迭代器。

>`rbegin + rend`: begin获取一个字符的迭代器 + end获取最后一个字符下一个位置的迭代器。

>`范围for` :C++11支持更简洁的范围for的新遍历方式。


![在这里插入图片描述](https://img-blog.csdnimg.cn/cb267e9e61c54e91bc24865d7e8da963.png)

at和operator[]功能类似，获取第pos位置的字符，但是at的区别是，at发生越界后是抛异常。operator[]是断言报错。
bank和front是取第一个字符和最后一个字符。




---

## string类对象的修改操作

![在这里插入图片描述](https://img-blog.csdnimg.cn/0bf910383c7c43d7ad91e209ec17df2d.png)


>`push_back` :尾插，在字符串后尾插字符c。
>
![在这里插入图片描述](https://img-blog.csdnimg.cn/ff61493dbf694d77b20c2984aaf9bdeb.png)

为了弥补push_back的缺陷，出现了append：
>`append `:在字符串后追加一个字符串。

![在这里插入图片描述](https://img-blog.csdnimg.cn/9730e8e56c7b470185cf515c891904ad.png)


```cpp
void test_string7()
{
	string s1("hello world");
	s1.push_back(' ');
	s1.push_back('!');
	s1.append("hello world");
	cout << s1 << endl;

	string s2("!!!!!!!");
	s1.append(s2);
	cout << s1 << endl;
}

int main()
{
	test_string7();
	return 0;
}
```

```cpp
hello world !hello world
hello world !hello world!!!!!!!
```



但是这块最好用的还是operator+= ：

>`operator+=`:  在字符串后追加字符串str。

![在这里插入图片描述](https://img-blog.csdnimg.cn/301cac759e7041a1831922605c9b113d.png)

```cpp
void test_string8()
{
	string s1("hello world");
	s1 += ' ';
	s1 += '!';
	s1 += "hello world";
	cout << s1 << endl;

	string s2("!!!!!!!");
	s1 += s2;
	cout << s1 << endl;
}

int main()
{
	test_string8();
	return 0;
}
```

```cpp
hello world !hello world
hello world !hello world!!!!!!!
```



> `insert`:插入数据


![在这里插入图片描述](https://img-blog.csdnimg.cn/4097099e46564c3fa0616ee5ea4d2d61.png)

```cpp
void test_string8()
{
	string s("hello world");
	s.insert(0, "bit");
	cout << s << endl;
	s.insert(9, "bit");
	cout << s << endl;
}

int main()
{
	test_string8();

	return 0;
}
```

```cpp
bithello world
bithello bitworld
```




> `erase`:删除字符

![在这里插入图片描述](https://img-blog.csdnimg.cn/ea8ad347e48d4f7394deafbd667a574b.png)

```cpp
void test_string8()
{
	string s("hello world");
	s.insert(0, "bit");
	cout << s << endl;
	s.insert(9, "bit");
	cout << s << endl;

	s.erase(9, 3);
	cout << s << endl;

	s.erase(0, 3);
	cout << s << endl;
	
	s.erase(5, 30);
	cout << s << endl;

}

int main()
{
	test_string8();

	return 0;
}
```

```cpp
bithello world
bithello bitworld
bithello world
hello world
hello
```











>`c_str`: 返回C格式字符串。




>`find + npos`:从字符串pos位置开始往后找字符c，返回该字符在字符串中的位置。












>`rfind `:从字符串pos位置开始往前找字符c，返回该字符在字符串中的位置。








>`substr `:在str中从pos位置开始，截取n个字符，然后将其返回。
>

**注意：**

1. size()与length()方法底层实现原理完全相同，引入size()的原因是为了与其他容器的接口保持一致，一般情况下基本都是用size()。

2. clear()只是将string中有效字符清空，不改变底层空间大小。
3. resize(size_t n) 与 resize(size_t n, char c)都是将字符串中有效字符个数改变到n个，不同的是当字符个数增多时：resize(n)用0来填充多出的元素空间，resize(size_t n, char c)用字符c来填充多出的元素空间。注意：resize在改变元素个数时，如果是将元素个数增多，可能会改变底层容量的大小，如果是将元素个数减少，底层空间总大小不变。
4. reserve(size_t res_arg=0)：为string预留空间，不改变有效元素个数，当reserve的参数小于string的底层空间总大小时，reserver不会改变容量大小。

---
想要了解更多关于`string`的知识请前往官方网站学习：`https://legacy.cplusplus.com/`

![在这里插入图片描述](https://img-blog.csdnimg.cn/5a57ff7f74e740e491557ee4d68dbabd.png)
