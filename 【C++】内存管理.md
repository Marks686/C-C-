#  C/C++内存分布
C/C++这两个语言和Java不一样，如果对Java有所了解的话，会知道Java的程序不是跑在操作系统上的，Java在操作系统之上有一个虚拟机，Java的环境要比C++复杂不少，所以学习Java只关系它的虚拟机就行了，不需要太关注操作系统，C/C++的程序会以进程的方式跑在操作系统之上，所以我们需要对操作系统有很深的了解。

> C/C++数据的构成：

程序编译好了，运行起来后，C/C++的可执行程序会对内存进行一个分段。
从C语言的角度来说静态区放的是静态数据和全局数据，常量数据放到常量区，从操作系统的角度来看，分段（划分区域）跟程序密切相关的几个区域就是栈、堆，从操作系统角度喜欢称为数据段，还有代码段，代码段不仅存储常量还要存代码。

> 为什么要分区域？

因为不同的区域的数据有不同的性质，为了方便管理，不同的数据，是有不同的需求的，程序运行的时候，有些数据一会就会销毁，比如说局部数据，那么我们把局部数据存在栈，为什么要存在栈？因为函数调用要建立栈帧，栈帧里面存有局部变量，寄存器的值，包括参数返回值，但是这些都是即用即取的，程序运行过程我们有时需要一些全局变量，在程序运行期间它们都在，如果存在栈帧里面，那函数销毁它也就销毁了，这个时候我们就把这些全局数据，全局静态数据，局部静态数据就存在了数据段这个区域，动态申请的就在堆上。
![在这里插入图片描述](https://img-blog.csdnimg.cn/0383222fb1cc46309d6e0e5677a5d0bf.png)
请看下面练习：

![在这里插入图片描述](https://img-blog.csdnimg.cn/09c660fde2af4b4b83ff9e3d44e476a7.png)

#  C语言中动态内存管理方式
    malloc/calloc/realloc/free





```cpp
void Test ()
{
int* p1 = (int*) malloc(sizeof(int));
free(p1);

int* p2 = (int*)calloc(4, sizeof (int));
int* p3 = (int*)realloc(p2, sizeof(int)*10);
free(p3 );
}
```


#   C++内存管理方式
通过`new`和`delete`操作符进行动态内存管理
## new/delete操作内置类型

```cpp
int main()
{
	int* p1 = new int;
	delete p1;

	//初始化
	int* p2 = new int(0);
	delete p2;

	//开多个
	int* p3 = new int[10]{1,2,3,4};
	delete[] p3;
	return 0;
}
```
C++的`new`和`delete`相比于`malloc`和`free`，如果申请的是内置类型的对象除了用法的区别没有其他区别。真正的区别点在于自定义类型。

## new和delete操作自定义类型

```cpp
class A
{
public:
	A(int a = 0)
		: _a(a)
	{
		cout << "A():" << this << endl;
	}
	~A()
	{
		cout << "~A():" << this << endl;
	}
private:
	int _a;
};
int main()
{
	A* p1 = new A;
	A* p2 = (A*)malloc(sizeof(A));

	delete p1;
	free(p2);
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9eb59b1490f74f1784ef5941c0c26ff8.png)


C++的`new`和`delete`相比于`malloc`和`free`，如果申请的是自定义类型，除了空间管理，还会调用构造函数和析构函数。

```cpp
struct ListNode
{
	ListNode* _next;
	int _val;

	ListNode(int val = 0)
		:_next(nullptr)
		,_val(val)
	{

	}
};
int main()
{
	ListNode* n1 = new ListNode(1);
	ListNode* n2 = new ListNode(2);
	ListNode* n3 = new ListNode(3);
	ListNode* n4 = new ListNode(4);
	n1->_next = n2;
	//...
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/a4399aa47b934c38a76ddf8ea3b39dd2.png)
`new`其实就是给自定义类型准备的。

```cpp
class A
{
public:
	A(int a = 0)
		: _a(a)
	{
		cout << "A():" << this << endl;
	}
	~A()
	{
		cout << "~A():" << this << endl;
	}
private:
	int _a;
};
int main()
{
	A* p3 = new A[10];
	delete p3;
	return 0;
}
```
`new`了10个`delete`了一个，运行发现程序报错。![在这里插入图片描述](https://img-blog.csdnimg.cn/b57a8e58684c496bba66d706b31d5401.png)
出现这种情况依赖于编译器的底层实现机制，如果把析构函数屏蔽掉，可能就不报错了。所以我们一定要匹配使用，否则就会出现各种情况。
下面的写法也会报错：（VS2019）

```cpp
class A
{
public:
	A(int a = 0)
		: _a(a)
	{
		cout << "A():" << this << endl;
	}
	~A()
	{
		cout << "~A():" << this << endl;
	}
private:
	int _a;
};
int main()
{
	A* p4 = new A;
	delete[] p4;
	return 0;
}
```




如果我们一直申请结果是怎样的呢？？
`malloc`

```cpp
int main()
{
	while (1)
	{
		//malloc失败 返回空指针
		int* p1 = (int*)malloc(1024*100);
		if (p1)
		{
			cout << p1 << endl;
		}
		else
		{
			cout << "申请失败" << endl;
			break;
		}
	}
	return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/45fc91c9e46f4b558321076c9de68af7.png)

`new`

```cpp
int main()
{
	while (1)
	{
		//malloc失败 返回空指针
		int* p1 = new int[1024*100];
		if (p1)
		{
			cout << p1 << endl;
		}
		else
		{
			cout << "申请失败" << endl;
			break;
		}
	}
	return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/da5a916a49474546abee8cde8d3b85f5.png)
不断申请，最终都会失败的，可是`new`最后为什么没有打印失败呢
?说明`new`失败不是返回空指针，而是抛异常。所以`new`不需要检查返回值。

---
底层机制
#  operator new与operator delete函数
`new`和`delete`是用户进行动态内存申请和释放的操作符，`operator new` 和`operator delete`是系统提供的全局函数，`new`在底层调用`operator new`全局函数来申请空间，`delete`在底层通过`operator delete`全局函数来释放空间。
![在这里插入图片描述](https://img-blog.csdnimg.cn/6548cef3b7a44a47ac8f1c92973e4709.png)

```cpp
void* __CRTDECL operator new(size_t size) _THROW1(_STD bad_alloc)
{
	// try to allocate size bytes
	void* p;
	while ((p = malloc(size)) == 0)
if (_callnewh(size) == 0)
{
	// report no memory
	// 如果申请内存失败了，这里会抛出bad_alloc 类型异常
	static const std::bad_alloc nomem;
	_RAISE(nomem);
}
return (p);
}
/*
operator delete: 该函数最终是通过free来释放空间的
*/
void operator delete(void *pUserData)
{
     _CrtMemBlockHeader * pHead;
     RTCCALLBACK(_RTC_Free_hook, (pUserData, 0));
     if (pUserData == NULL)
         return;
     _mlock(_HEAP_LOCK);  /* block other threads */
     __TRY
         /* get a pointer to memory block header */
         pHead = pHdr(pUserData);
          /* verify block type */
         _ASSERTE(_BLOCK_TYPE_IS_VALID(pHead->nBlockUse));
         _free_dbg( pUserData, pHead->nBlockUse );
     __FINALLY
         _munlock(_HEAP_LOCK);  /* release other threads */
     __END_TRY_FINALLY
     return;
}
/*
free的实现
*/
#define   free(p)               _free_dbg(p, _NORMAL_BLOCK)
```
封装`malloc`，申请内存失败，抛异常。
总结：operator new 实际也是通过malloc来申请空间，如果malloc申请空间成功就直接返回，否则执行用户提供的空间不足应对措施，如果用户提供该措施就继续申请，否则就抛异常。operator delete 最终是通过free来释放空间的。
#  new和delete的实现原理

> new的原理

1. 调用operator new函数申请空间。
2. 在申请的空间上执行构造函数，完成对象的构造。

>delete的原理
3. 在空间上执行析构函数，完成对象中资源的清理工作。
4. 调用operator delete函数释放对象的空间。
>new T[N]的原理
5. 调用operator new[]函数，在operator new[]中实际调用operator new函数完成N个对象空间的申请。
6. 在申请的空间上执行N次构造函数。
>delete[ ]的原理
7. 在释放的对象空间上执行N次析构函数，完成N个对象中资源的清理。
8. 调用operator delete[]释放空间，实际在operator delete[]中调用operator delete来释放空间。



```cpp
class A
{
public:
	A(int a = 0)
		: _a(a)
	{
		cout << "A():" << this << endl;
 }
 ~A()
 {
	 cout << "~A():" << this << endl;
 }
private:
	int _a;
};

int main()
{
	A* p1 = new A;

	A* p2 = new A[10];
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b4ac315b8f2e464cb091731dba2042d6.png)

#  定位new表达式(placement-new)
定位new表达式是对已分配的内存空间中调用构造函数初始化一个对象。

> 使用格式：

    new (place_address) type或者new (place_address) type(initializer-list)
`place_address`必须是一个指针，`initializer-list`是类型的初始化列表

定位new一般是配合内存池使用，因为内存池分配出的内存没有初始化，所以如果是自定义类型的对象，需要使用new的定义表达式进行显示调构造函数进行初始化。


```cpp
class A
{
public:
	A(int a = 0)
		: _a(a)
	{
		cout << "A():" << this << endl;
 }
 ~A()
 {
	 cout << "~A():" << this << endl;
 }
private:
	int _a;
};

int main()
{
	A* p3 = (A*)malloc(sizeof(A));
	if (p3 == nullptr)
	{
		perror("malloc fail");
		exit(-1);
	}
	//定位new/replacement new --对p3指向空间，显示调用构造函数初始化
	new(p3)A(1);
	//释放
	//第1种
	p3->~A();//析构函数可以显示调用
	free(p3);
	//第2种
	//delete p3;
	return 0;
}
```

# malloc/free和new/delete的区别

> `malloc`/`free`和`new`/`delete`的共同点是：都是从堆上申请空间，并且需要用户手动释放。不同的地方是：1. `malloc`和`free`是函数，`new`和`delete`是操作符
>2. `malloc`申请的空间不会初始化，`new`可以初始化.
>3. `malloc`申请空间时，需要手动计算空间大小并传递，`new`只需在其后跟上空间的类型即可，如果是多个对象，[]中指定对象个数即可.
>4. `malloc`的返回值为`void*`, 在使用时必须强转，`new`不需要，因为new后跟的是空间的类型.
>5. `malloc`申请空间失败时，返回的是`NULL`，因此使用时必须判空，`new`不需要，但是`new`需要捕获异常.
>6. 申请自定义类型对象时，`malloc`/`free`只会开辟空间，不会调用构造函数与析构函数，而`new`在申请空间后会调用构造函数完成对象的初始化，`delete`在释放空间前会调用析构函数完成空间中资源的清理.





#  什么是内存泄漏，内存泄漏的危害

> 什么是内存泄漏：内存泄漏指因为疏忽或错误造成程序未能释放已经不再使用的内存的情况。内存泄漏并不是指内存在物理上的消失，而是应用程序分配某段内存后，因为设计错误，失去了对该段内存的控制，因而造成了内存的浪费。内存泄漏的危害：长期运行的程序出现内存泄漏，影响很大，如操作系统、后台服务等等，出现内存泄漏会导致响应越来越慢，最终卡死。
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/fd8989bb58104ed39b26cc1d1f0c99ba.png)
