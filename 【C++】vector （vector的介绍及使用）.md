前面我们学习了string，我们在学vector可以结合之前的理解，所以我们vector就不详细介绍了。

# vector的介绍及使用

1. vector是表示可变大小数组的序列容器。
2. 就像数组一样，vector也采用的连续存储空间来存储元素。也就是意味着可以采用下标对vector的元素进行访问，和数组一样高效。但是又不像数组，它的大小是可以动态改变的，而且它的大小会被容器自动处理。
3. 本质讲，vector使用动态分配数组来存储它的元素。当新元素插入时候，这个数组需要被重新分配大小为了增加存储空间。其做法是，分配一个新的数组，然后将全部元素移到这个数组。就时间而言，这是一个相对代价高的任务，因为每当一个新的元素加入到容器的时候，vector并不会每次都重新分配大小。
4. vector分配空间策略：vector会分配一些额外的空间以适应可能的增长，因为存储空间比实际需要的存储空间更大。不同的库采用不同的策略权衡空间的使用和重新分配。但是无论如何，重新分配都应该是对数增长的间隔大小，以至于在末尾插入一个元素的时候是在常数时间的复杂度完成的。
5. 因此，vector占用了更多的存储空间，为了获得管理存储空间的能力，并且以一种有效的方式动态增长。
6. 与其它动态序列容器相比（deque, list and forward_list）， vector在访问元素的时候更加高效，在末尾添加和删除元素相对高效。对于其它不在末尾的删除和插入操作，效率更低。比起list和forward_list统一的迭代器和引用更好。
![在这里插入图片描述](https://img-blog.csdnimg.cn/f97b3ad663f84db69c1df5537a9f3da2.png)
vector是有向量的意思，C++和Java都有vector，底层其实就是动态增长的顺序表，我们发现vector和string的接口设计非常相似。

![在这里插入图片描述](https://img-blog.csdnimg.cn/b2df86b06cc346618f5ce554a37e3050.png)

> **遍历:**

```cpp
#include<iostream>
#include<vector>

using namespace std;

void test_vesctor1()
{
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);

	for (size_t i = 0; i < v.size(); ++i)
	{
		cout << v[i] << " ";
	}
	cout << endl;

	vector<int>::iterator it = v.begin();
	while (it != v.end())
	{
		cout << *it << " ";
		++it;
	}
	cout << endl;
	 
	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;
}

int main()
{
	test_vesctor1();
	return 0;
}
```

```cpp
1 2 3 4
1 2 3 4
1 2 3 4
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/5e98438e7c154907813ca0c73edff27d.png)
如果n大于当前vector的capacity，那么会引起空间增长到n，如果n小于capacity，比当前的容量小时，不缩容，简单理解为以空间换时间。

```cpp
void test_vesctor2()
{
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);
	v.push_back(5);
	cout << v.capacity() << endl;

	v.reserve(10);
	cout << v.capacity() << endl;

	v.reserve(4);
	cout << v.capacity() << endl;

}


int main()
{
	test_vesctor2();
	return 0;
}
```

```cpp
6
10
10
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/aea7c0bb5d1c4db89b030ca51572107e.png)
日常生活种用resize都是对一个空的vector来进行resize。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7e3d362bf06743588c3679b471453ca7.png)
capacity的代码在vs和g++下分别运行会发现，vs下capacity是按1.5倍增长的，g++是按2倍增长的。这个问题经常会考察，不要固化的认为，vector增容都是2倍，具体增长多少是根据具体的需求定义的。vs是PJ版本STL，g++是SGI版本STL。reserve只负责开辟空间，如果确定知道需要用多少空间，reserve可以缓解vector增容的代价缺陷问题。resize在开空间的同时还会进行初始化，影响size。

**vector增容：**

```cpp
void TestVectorExpand()
{
	size_t sz;
	vector<int> v;
	sz = v.capacity();
	cout << "making v grow:\n";
	for (int i = 0; i < 100; ++i)
	{
		v.push_back(i);
		if (sz != v.capacity())
		{
			sz = v.capacity();
			cout << "capacity changed: " << sz << '\n';
		}
	}
}
int main()
{
	TestVectorExpand();
	return 0;
}
```




![在这里插入图片描述](https://img-blog.csdnimg.cn/10212ab5e5de466abb71723ee7792bbf.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/7807b9150f39493388eb2ec5299288fa.png)

下面调用的两个size是一样的。


```cpp
void Func(const vector<int>& v)
{
	v.size();
}

void test_vector()
{
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);
	v.push_back(5);
	v.size();

	Func(v);
}
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/2eafabd849114fdea3b95b5455f893fa.png)

在Func中只读不写，在下面的test_vector中可读可写。

```cpp
void Func(const vector<int>& v)
{
	v.size();
	v[0];
	//v[0]++;

}

void test_vector()
{
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);
	v.push_back(5);
	v.size();
	v[0];
	v[0]++;

	Func(v);
}
```

**成员函数是否加const根据下面规则：**

1、如果是只读的接口函数，提供const即可。
2、遇到只写的接口函数，不能加cost。比如： push_back()。
3、可读可写的接口函数，写const + 非const。比如：operator[]。

![在这里插入图片描述](https://img-blog.csdnimg.cn/6332b6266a144142a3d39efd8b6ae20c.png)

operator[]和at有同一功能，区别是：operator[]是用断言检查的，![在这里插入图片描述](https://img-blog.csdnimg.cn/7564be98a23e4110ac8460fe0eb9ca68.png)

at是一个成员函数，它出错报的是异常，
![在这里插入图片描述](https://img-blog.csdnimg.cn/b22cd5aba8dd4aab95bcd7786b17133b.png)

断言就终止了，异常我们可以捕获，断言在release不起作用的，而我们希望的是在debug阶段就找出问题。它俩区别就是在越界的时候。

```cpp
void test_vector()
{
	vector<int> v;
	v.reserve(10);
	for (size_t i = 0; i < 10; ++i)
	{
		v.at(i) = i;
	}
}
int main()
{
	try
	{
		test_vector();
	}

	catch (const exception& e)
	{
		cout << e.what() << endl;
	}
	return 0;
}
```

```cpp
invalid vector subscript
```


我们平时还是喜欢用operator[]。

![在这里插入图片描述](https://img-blog.csdnimg.cn/9a1f12bd93e94b7cb224ed0c5e6a3a66.png)

assign是一种赋值，用n个val赋值给vector，之前的值就被覆盖掉了。

```cpp
void test_vector()
{
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);

	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;
	v.assign(10, 1);


	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;

	/// ///////////////////


	vector<int> v1;
	v1.push_back(10);
	v1.push_back(20);
	v1.push_back(30);
	v.assign(v1.begin(), v1.end());

	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;
	
	/// ///////////////////
	
	string str("hello world");
	v.assign(str.begin(), str.end());
	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;
}
int main()
{
	try
	{
		test_vector();
	}

	catch (const exception& e)
	{
		cout << e.what() << endl;
	}
	return 0;
}
```

```cpp
1 2 3
1 1 1 1 1 1 1 1 1 1
10 20 30
104 101 108 108 111 32 119 111 114 108 100
```

想要头插一个数据：
![在这里插入图片描述](https://img-blog.csdnimg.cn/210f764383ca441cb03bf34760ba93d1.png)








find单独写成一个类模板的算法是为了复用，所有的容器都可以使用。

![在这里插入图片描述](https://img-blog.csdnimg.cn/f2b4c3442ef744a68d86164a0c84e9e3.png)

```cpp
void test_vector()
{
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);

	v.insert(v.begin(), 4);
	v.insert(v.begin()+2, 4);

	vector<int>::iterator it = find(v.begin(), v.end(), 3);
	if (it != v.end())
	{
		v.insert(it, 30);
	}


	for (auto e : v)
	{
		cout << e << " ";
	}
	cout << endl;

}
int main()
{
	try
	{
		test_vector();
	}

	catch (const exception& e)
	{
		cout << e.what() << endl;
	}
	return 0;
}
```

```cpp
4 1 4 2 30 3
```


下面的接口通常都不会去动capacity，最多动一下size。

```cpp
void test_vector()
{
	vector<int> v;
	v.reserve(10);
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);
	v.push_back(5);
	cout << v.size() << endl;
	cout << v.capacity() << endl;

	v.reserve(5);
	cout << v.size() << endl;
	cout << v.capacity() << endl;

	v.resize(3);
	cout << v.size() << endl;
	cout << v.capacity() << endl;
}
int main()
{
	try
	{
		test_vector();
	}

	catch (const exception& e)
	{
		cout << e.what() << endl;
	}
	return 0;
}
```

```cpp
5
10
5
10
3
10
```
这种设计理念：不动空间，不缩容。释放空间由于内存管理的一些原因，不允许分段释放，只能整体释放。空间换时间。
