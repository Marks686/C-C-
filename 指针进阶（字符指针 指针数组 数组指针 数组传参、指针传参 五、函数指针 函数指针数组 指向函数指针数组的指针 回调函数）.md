在初阶指针里我们已经我们已经学习了指针，在初阶指针的基础上我们再深入挖掘一些指针的知识，有字符指针、数组指针、指针数组、数组传参和指针传参、函数指针、函数指针数组、指向函数指针数组的指针、回调函数、指针和数组面试试题的解析。

> 指针的概念：

1.指针就是个变量，用来存放地址，地址唯一标识一块内存空间。
2.指针的大小是固定的4/8个字节（32位平台/64位平台）
3.指针是有类型，指针的类型决定了指针的+-整数的步长，指针解引用操作的时候的权限。
4.指针的运算......
**我们继续探讨指针的高级主题。**
# 一、字符指针 



> **字符指针：char***

在c语言中有两种常见的书写形式：
**第一种：**
```c
int main()
{
	char ch = 'w';
	char* pc = &ch;
	//*说明pc是指针 char告诉我们pc指向的对象ch它的类型是char类型
	//这就是char*定义的一个pc指针变量
	*pc = 'b';//pc找到ch并改变它

	printf("%c\n", ch);
	return 0;
}
```
字符指针可以存放一个字符的地址
**第二种：**
```c
int main()
{
	char* p = "abcdef";
	printf("%s\n", p);
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9a8a3afdbaae49c793d616479ed82e96.png)
我们发现打印的结果是abcdef是一个字符串，那么这个地方p里面放的是不是就是字符串？
答案：不是。
![在这里插入图片描述](https://img-blog.csdnimg.cn/1f5f044d5f654091971d00015a3ff529.png)

```c
int a = 10；
int b = a+2；
//a+2的值赋给b，a+2是表达式 表达式的赋值给b
//表达式有值属性和类型属性，
//a+2的值属性就是12，
//因为a是int类型，2也是int类型，所以a+2的类型属性是int。
```

当我们看到abcdef字符串作为表达式一部分，把字符串abcdef赋给p的时候，把“abcdef”当成一个表达式，这个表达式赋给p的是首字符a的地址。非要把abcdef放到p里面去是放不下的，abcdef已经6个字符了，再加上后面还隐藏的\0，如果放到p里面去，p有可能是
4个字节，1个字符需要1个字节的空间，4个字节是放不了7个字符的，所以绝对不可能是把abcdef放到p里面去，这种写法是把字符串首字符a 的地址赋值给p，因为p里面放的是a的地址，打印字符串的时候给个%s打印字符串，只需要告诉从哪开始就可以了，p里放的是a的地址，它就知道从a后面往后打印，直到遇到\0。

```c
int main()
{
	char* p = "abcdef";
	*p = 'w';
	printf("%s\n", p);
	return 0;
}
```
如果这个代码在编译器上报警告，其实是把a的地址放到p里是不安全的，abcdef是个常量字符串，意思就是它不能被改，我把起始地址放到p里去，这个p权限就变大了，p没有被修饰，也就是p敢去改abcdef的内容，如果非要强行改，程序就会崩溃，所以报警告其实是很正常的

![在这里插入图片描述](https://img-blog.csdnimg.cn/e7a21aceab3547edbccc673ad9baf4e3.png)


我们可以在加个const，就是限制了*p不能改，之后*p = ‘w’这种写法编译器直接判断错误,这样就有效的保护字符串。

```c
int main()
{
	const char* p = "abcdef";
	*p = 'w';
	printf("%s\n", p);
	return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ee8f740aeb1844c5b4ba98f5a0841d02.png)

> 一道笔试题：

```c
int main()
{
    const char* p1 = "abcdef";
	const char* p2 = "abcdef";

	char arr1[] = "abcdef";
	char arr2[] = "abcdef";

	if (p1 == p2)
		printf("p1==p2\n");
	else
		printf("p1 != p2");
	if (arr1 == arr2)
		printf("arr1 == arr2");
	else
		printf("arr1 != arr2");

	return 0;
}
```
结果是：

```c
p1==p2
arr1 != arr2
```
p1和p2指向同一个字符串，这个字符串是常量字符串，放在内存中的只读区里，只读区的空间只能读不能写，只能用不能改，既然abcdef是常量字符串的话不能被改，那abcdef这样的常量字符串有没有必要存在多份，其实存一份就够了，假设a的起始地址是0x0012ff40，那么p1里存的就是这个值，p2也是这个值，所以p1 == p2，因为它俩里面放的值是相等的，再往下，发现arr1 和 arr2不相等，因为在那里，我们创建的两个独立数组，用abcdef初始化arr1[]数组，arr1[]要放下abcdef\0，就要在内存中开辟一段空间，arr2[]也一样，两块空间的起始地址不一样，arr1和arr2都是起始地址，它俩在内存中的地址绝对是不相等的。

# 二、指针数组
在初阶我们学习了指针数组，指针数组是一个存放指针的数组，这里我们简单复习下。
指针数组：是数组，是用来存放指针的数组，

```c
int arr[10];//整形数组
char ch[5];//字符数组
//指针数组
int* arr2[6];//这是一个存放整型指针的数组
//arr2数组名，6个元素，数组的每个元素是int*类型的值
//每个元素都是int* 类型的值
char* arr3[5];//存放字符指针的数组
```

> 指针数组有什么用呢？


```c
int main()
{
	int arr1[] = { 1,2,3,4,5 };
	int arr2[] = { 2,3,4,5,6 };
	int arr3[] = { 3,4,5,6,7 };

	int* parr[] = { arr1,arr2,arr3 };

	return 0;
}
```
arr1相当于1的地址,arr2相当于2的地址,arr3相当于3的地址。arr1 arr2 arr3都是整型的地址，所以parr这个数组应该是int*类型的。


![在这里插入图片描述](https://img-blog.csdnimg.cn/c878ec82564c4bdcbeaadcab4a8021c9.png)

```c
int main()
{
	int arr1[] = { 1,2,3,4,5 };
	int arr2[] = { 2,3,4,5,6 };
	int arr3[] = { 3,4,5,6,7 };

	int* parr[3] = { arr1,arr2,arr3 };

	int i = 0;
	int j = 0;
	for (i = 0; i < 3; i++)
	{
		for (j = 0; j < 5; j++)
		{
			printf("%d ", *(parr[i] + j));//也可以写成parr[i][j]
		}
		printf("\n");
	}
	return 0;
}
```

> 打印出来结果：

```c
1 2 3 4 5
2 3 4 5 6
3 4 5 6 7
```


# 三、数组指针
## 1.数组指针的定义
数组指针是指针

> **类比：**

整型指针——指向整型的指针
字符指针——指向字符的指针
**数组指针——指向数组的指针**

```c
int *p1[10];
int (*p2)[10];
//p1, p2分别是什么？
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/588ad0a69cae496887f2cd844dd98502.png)
想要使用数组指针，我们还得先把数组名深入理解一下：
## 2.&数组名vs数组名

```c
int main()
{
	int arr[10] = { 0 };
	printf("%p\n", arr);
	printf("%p\n", &arr[0]);
	return 0;
}
```

> 打印结果：

```c
006FFAD0
006FFAD0
//数组名就是首元素地址
```

```c
int main()
{
	int arr[10] = { 0 };
	printf("%p\n", arr);
    printf("%p\n", &arr[0]);
	int sz = sizeof(arr);
	printf("%d\n", sz);
	return 0;
}
```

> 打印结果：

```c
006FFAD0
006FFAD0
40
```



当我们这样去写的时候，有的同学就会产生歧义，刚刚我们说了，数组名是首元素地址，如果数组名是首元素地址，首元素地址是地址，所以就是4个字节或者8个字节，那sizeof算出来应该是4 或者 8 ，但是发现结果是40，所以两个地址是一模一样，好像认定了数组名就是首元素地址，但是当我们写sizeof（arr）的时候发现数组名好像不是首元素地址，这个我们要注意：
**数组名通常表示的都是数组首元素地址。**

> **但是有两个例外：**

1.sizeof（数组名）sizeof里面单独放一个数组名的时候是一个例外，这里的数组名表示整个数组，计算的是整个数组的大小，单位是字节。
2.&数组名，这里的数组名表示的依然是整个数组，所以&数组名，取出的是整个数组的地址。
![在这里插入图片描述](https://img-blog.csdnimg.cn/e5acc4b9de244f0ea4d704ea470cfdf8.png)

区别请看下面代码：
```c
int main()
{
	int arr[10] = { 0 };
	printf("%p\n", arr);
	printf("%p\n", arr+1);

    printf("%p\n", &arr[0]);
	printf("%p\n", &arr[0]+1);

	printf("%p\n", &arr);
	printf("%p\n", &arr+1);

	return 0;
}
```

> 打印结果：


```c
00BBFB70
00BBFB74
00BBFB70
00BBFB74
00BBFB70
00BBFB98
```
我们观察，arr 和arr+1差了4个字节，&arr[0] 和 &arr[0]+1差了4个字节，我们发现首元素地址+1，加了4个字节，因为我们知道这个数组每个元素都是int类型的，因为数组名表示首元素地址的话，这里就是int类型的地址，那arr的类型是int* ，而int*的指针+1跳过四个字节，所以都是相差4。
那&arr 和 &arr + 1 相差40，0x28——>40,&数组名，取出的是整个数组的地址，&arr+1 表示跳过整个数组。



> **整形指针是用来存放整型的地址
字符指针用来存放字符的地址
数组指针用来存放数组的地址**


```c
int main()
{
	int arr[10] = { 0 };
	int* p = arr;
	int (*p2)[10] = &arr;//数组指针
	return 0;
}
```
## 3.数组指针的使用

```c
int main()
{
	int arr[] = { 1,2,3,4,5,6,7,8,9,10 };
	int(*p)[] = &arr;//这样写是不行的
	return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/f2a529bd70024242adf7e3ef76c8f43a.png)
warning ： “int ( * )[0]”和“int ( * )[10]”数组的下标不同，int（*）[ ]认为 [ ]里面是0，两个对不上，所以必须写清楚几个元素，不能省略10！






```c
int main()
{
	int arr[] = { 1,2,3,4,5,6,7,8,9,10 };
	int(*p)[10] = &arr;

	int i = 0;
	int sz = sizeof(arr) / sizeof(arr[0]);
	for (i = 0; i < sz; i++)
	{
		printf("%d ", *(*p+i));

	}
	return 0;
}
```

		p是指向数组的，*p其实就相当于数组名
		数组名，又是数组首元素的地址，所以*p本质上数组首元素地址


> 打印结果：


```c
1 2 3 4 5 6 7 8 9 10
```
我们发现这种用法非常的别扭，这种用方法很不好，下面我们看看正常的写法：

```c
int main()
{
	int arr[] = { 1,2,3,4,5,6,7,8,9,10 };
	int* p = arr;
	int i = 0;
	for (i = 0; i < 10; i++)
	{
		printf("%d ", *(p + i));
	}
	return 0;
}
```
这种写法就很容易让人理解。

**数组指针多用在 二维三维数组里**
 用数组指针写的话

> 大概思路：

![在这里插入图片描述](https://img-blog.csdnimg.cn/d1fcea384b8143daa2cf96333beea92c.png)


```c
//用数组传参实现：
void print1(int arr[3][5], int r, int c)
{
	int i = 0;
	for (i = 0; i < r; i++)
	{
		int j = 0;
		for (j = 0; j < c; j++)
		{
			printf("%d ",arr[i][j]);

		}
		printf("\n");
	}
}
int main()
{
	int arr[3][5] = { 1,2,3,4,5,2,3,4,5,6,3,4,5,6,7 };
	print1(arr,3,5);
	return 0;
}
//数组指针的写法：
void print2(int(*p)[5], int r, int c)
{
	int i = 0;
	for (i = 0; i < r; i++)
	{
		int j = 0;
		for (j = 0; j < c; j++)
		{
			printf("%d ", *(*(p + i) + j));
		}
		printf("\n");
	}
}
int main()
{
	int arr[3][5] = { 1,2,3,4,5,2,3,4,5,6,3,4,5,6,7 };
	print2(arr,3,5);
	return 0;
}
```



> 打印结果：


```c
1 2 3 4 5
2 3 4 5 6
3 4 5 6 7
```

> **再捋一遍思路：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/52b2ec90cc714e628e2601d48f10b6d0.png)
p我们说指向的是第一行，p+1为什么就跳过一行呢？


当我们有int (* p) [5]这样一个指针的时候，
p的类型是:  int (*)[5] ;
p 是指向一个整型数组的，数组是5个元素 int [5]
p+1就是跳过一个5个int元素的数组。
# 四、数组传参、指针传参
写代码的时候，难免会把【数组】或者【指针】传给函数，那函数的参数该如何设计呢？
## 1.一维数组传参
> **一维数组传参**

![在这里插入图片描述](https://img-blog.csdnimg.cn/33da62c8283f4bcd98a76ab9d20a9f92.png)
## 2.二维数组传参
> **二维数组传参**

![在这里插入图片描述](https://img-blog.csdnimg.cn/5b18c3bb6ddc400aa6fe9077b7e21f5d.png)

## 3.一级指针传参
> **一级指针传参**


![在这里插入图片描述](https://img-blog.csdnimg.cn/e78d1b3292b2446786f6c05c6b67d4ce.png)
## 4.二级指针传参

> 二级指针传参


![在这里插入图片描述](https://img-blog.csdnimg.cn/488492e8469b48a0a83b9bfbf1bb652b.png)

# 五、函数指针

一般是函数指针和数组指针类比


**数组指针—指向数组的指针就是数组指针
函数指针—指向函数的指针就是函数指针**

```c
int Add(int x, int y)
{
	return x + y;
}
int main()
{
	//&函数名 - 取出的是不是函数的地址呢？
	printf("%p ", &Add);
	//如果把&Add 换成Add 拿出的结果是一样的
	return 0;
}

```

> **打印出的是函数的地址**


```c
003410B4
```
取地址函数名确实拿到的是**函数的地址**
对函数来说，&函数名和函数名都是函数的地址

> **如何存函数的地址？有什么应用？**


```c
int Add(int x, int y)
{
	return x + y;
}
int main()
{
 	int(*pf)(int,int) = &Add;
	int ret = (*pf)(2, 3);
	printf("%d\n", ret);
	return 0;
}
```
> **打印结果**

```c
5
```
这里其实就做到了间接的（通过地址来调用函数）通过函数指着访问函数。

> **简单的应用：**

```c
int Add(int x, int y)
{
	return x + y;
}
void calc(int (*pf)(int,int))
{
	int a = 1;
	int b = 4;
	int ret = pf(a, b);
	printf("%d\n", ret);
}
int main()
{
	calc(Add);
	return 0;
}
```

> **输出结果**

```c
5
```
有了函数指针的这样一个东西，我们就可以把函数地址进行传参，传给另外一个函数，这个函数想调用那个函数的时候，就可以通过函数指针调用。
# 六、函数指针数组

> 把函数和指针放在数组中就是函数指针数组

```c
#include<stdio.h>
int Add(int a, int b)
{
	return a + b;
}
int Sub(int a, int b)
{
	return a - b;
}
int Mul(int a, int b)
{
	return a * b;
}
int Div(int a, int b)
{
	return a / b;
}
int main()
{
	int (*pf)(int,int) = Add;//pf是函数指针

	int (*arr[4])(int, int) = {Add, Sub, Mul, Div};//arr就是函数指针的数组

	int i = 0;
	for (i = 0; i < 4; i++)
	{
		int ret = arr[i](8, 4);
		printf("%d \n", ret);
	}
	return 0;
}
```

```c
12
4
32
2
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a20b0b39f7494664830b9323e2f63989.png)
# 七、指向函数指针数组的指针

```c
#include<stdio.h>
int Add(int a, int b)
{
	return a + b;
}
int Sub(int a, int b)
{
	return a - b;
}
int Mul(int a, int b)
{
	return a * b;
}
int Div(int a, int b)
{
	return a / b;
}
int main()
{
	//函数指针数组
	int (*pfArr[])(int, int) = { 0,Add, Sub, Mul, Div };

	//指向【函数指针数组】的指针
	int (*(*ppfArr)[5])(int, int) = &pfArr;

	return 0;
}
```
# 八、回调函数

> 回调函数就是一个**通过函数指针调用的函数**。如果你把函数的指针（地址）作为参数传递给另一个函数，当这个指针被用来调用其所指向的函数时，我们就说这是回调函数。回调函数不是由该函数的实现方直接调用，而是在特定的事件或条件发生时由另外的一方调用的，用于对该事件或条件进
行响应。

实例：冒泡排序
![在这里插入图片描述](https://img-blog.csdnimg.cn/74e6c90204f842c1aac69001e14274bc.png)

```c
void qsort(void* base, //排序的数据的起始位置
	       size_t num,//待排序的数据元素的个数
	       size_t width,//待排序的数据元素的大小（单位是字节）
	       int(* comp)(const void* e1, const void* e2)//函数指针 - 比较函数
           );

//__cdecl - 函数调用约定
```

```c
int main()
{
	int a = 10;
	//char* pa = &a;//int*
	void* pv = &a;//void* 是无具体类型的指针，可以接受任意类型的地址
	//void* 是无具体类型的指针，所以不能解引用，也不能+-整数
	return 0;
}
```

```c
int cmp_int(const void* e1, const void* e2)
{
	return (*(int*)e1 - * (int*)e2);
}
int main()
{
	int arr[] = { 9,8,7,6,5,4,3,2,1,0 };
	//把数组排成升序
	int sz = sizeof(arr) / sizeof(arr[0]);
	/*bubble_sort(arr, sz);*/
	qsort(arr, sz, sizeof(arr[0]),cmp_int );
	int i = 0;
	for (i = 0; i < sz; i++)
	{
		printf("%d ",arr[i]);
	}
	return 0;

}
```

```c
0 1 2 3 4 5 6 7 8 9
```
