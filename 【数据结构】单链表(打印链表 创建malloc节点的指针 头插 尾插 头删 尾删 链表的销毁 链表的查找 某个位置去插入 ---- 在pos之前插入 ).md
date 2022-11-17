# 前言
顺序表的缺点：
* 顺序表在头部和中部插入删除数据都是不太合适的，因为要挪动数据时间复杂度为O(N)。
* 扩容过程有消耗，如果需要大一点的空间可能不是原地扩容，拷贝数据，释放旧空间会有不少的消耗。
* 扩容一般是2倍增长，会有一定的空间浪费。


现在我们有一种这样的想法：我需要存一个数据，那么就开一块单独的空间去存这个数据，小块小块的开。
现在把这些空间开出来，数据存起来后，他们之间如何建立关系呢？
* 可以用指针，前一个节点存储后一个的指针，这个时候它们就链接在一起了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/dc50a07af53e47669a2252c0f06b996f.png)

上面说的这种结构就是我们的链表。
特点：
* 按需的申请和释放。
* 链式结构逻辑上是连续的，但在物理上不一定连续。
* 节点一般都是从堆上申请出来的。
* 从堆上申请的空间，是按照一定的策略来分配的，两次申请的空间可能连续，也可能不连续。





# 单链表

![在这里插入图片描述](https://img-blog.csdnimg.cn/dc50a07af53e47669a2252c0f06b996f.png)

内存块里有指针，数据，凡是多个数据，我们就把它定义成结构体。

```cpp
typedef int SLTDateType;
typedef struct SListNode
{
	SLTDateType data; 
	struct SListNode* next;
}SLTNode;
```



* ## 1.打印链表

```cpp
void SListPrint(SLTNode* phead)
{
	//链表有可能是空的，所以不能断言
	SLTNode* cur = phead;
	while (cur != NULL)
	{
		printf("%d->", cur->data);
		cur = cur->next;//next存的是下一个节点的地址
	}
	printf("NULL\n")
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/82c8660fe06d4154ae29732ced4037f7.png)


* ## 2.创建malloc节点的指针
```cpp
SLTNode* BuySLTNode(SLTDateType x)
{
	SLTNode* newnode = (SLTNode*)malloc(sizeof(SLTNode));
	if (newnode == NULL)
	{
		perror("malloc fail");
		exit(-1);
	}
	newnode->data = x;
	newnode->next = NULL;
	return newnode;
}
```

* ## 3.头插

错误代码：
```cpp
void SListPushFront(SLTNode* phead, SLTDateType x)
{
	SLTNode* newnode = BuySLTNode(x);
	newnode->next = phead;
	phead = newnode;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/14a19e918a0e4f72bd18a0c941666b4b.png)

正确代码：

```cpp
void SListPushFront(SLTNode** pphead, SLTDateType x)
{
	assert(pphead);
	SLTNode* newnode = BuySLTNode(x);
	newnode->next = *pphead;
	*pphead = newnode;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/25fe7fdbcf5e4870b5675355a065e35b.png)



* ## 4.尾插



```cpp
void SListPushBack(SLTNode** pphead, SLTDateType x)
{
	assert(pphead);
	SLTNode* newnode = BuySLTNode(x);
	if (*pphead == NULL)
	{
		*pphead = newnode;
	}
	else
	{
		//找尾
		SLTNode* tail = *pphead;
		while (tail->next != NULL)
		{
			tail = tail->next;
		}
		tail->next = newnode; 
	}
}
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/78501ede513e44fca362f2183f73146b.png)


* ## 5.头删


```cpp
void SListPopFront(SLTNode** pphead)
{
	assert(pphead);

	//温柔的检查
	/*if (*pphead == NULL)
	{
		return;
	}*/

	//暴力检查
	assert(*pphead != NULL);
	SLTNode* del = *pphead;
	*pphead = (*pphead)->next;
	free(del);
	del = NULL;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6e8d39c6b10a45cd9ba7c430e352858e.png)
* ## 6.尾删


双指针：
```cpp
void SListPopBack(SLTNode** pphead)
{
	assert(pphead);
	//温柔的检查
/*if (*pphead == NULL)
{
	return;
}*/

//暴力检查
	assert(*pphead != NULL);

	if ((*pphead)->next == NULL)
	{
		free(*pphead);
		*pphead = NULL;
	}
	else
	{
		//找尾
		SLTNode* prev = NULL;
		SLTNode* tail = *pphead;
		while (tail->next != NULL)
		{
			prev = tail;
			tail = tail->next;
		}
		prev->next = NULL;
		free(tail);
		tail = NULL;
	}
}
```


第二种方法：

```cpp
void SListPopBack2(SLTNode** pphead)
{
	assert(pphead);
	//温柔的检查
/*if (*pphead == NULL)
{
	return;
}*/

//暴力检查
	assert(*pphead != NULL);
	if ((*pphead)->next == NULL)
	{ 
		free(*pphead);
		*pphead = NULL;
	}
	else
	{
		//找尾
		SLTNode* tail = *pphead;
		while (tail->next->next != NULL)
		{
			tail = tail->next;
		}
		free(tail->next);
		tail->next = NULL;
	}
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/cf477b8665884afb9fce13b7ab0f526e.png)

* ## 7.链表的销毁



```cpp
void SListDestory(SLTNode** pphead)
{
	assert(pphead);

	SLTNode* cur = *pphead;
	while (cur != NULL)
	{
		SLTNode* next = cur->next;
		free(cur);
		cur = next;
	}

	*pphead = NULL;
}
```
* ## 8.链表的查找




```cpp
SLTNode* SListFind(SLTNode* phead, SLTDateType x)
{
	SLTNode* cur = phead;
	while (cur != NULL)
	{
		if (cur->data == x)
		{
			return cur;
		}
		cur = cur->next;
	}
	return NULL;
}
```


一般找到后直接就修改了
```cpp
void TestSList3()
{
	SLTNode* plist = NULL;
	SListPushBack(&plist, 1);
	SListPushBack(&plist, 2);
	SListPushBack(&plist, 3);
	SListPushBack(&plist, 4);
	SListPrint(plist);

	SLTNode* pos = SListFind(plist, 3);
	if (pos)
	{
		//修改
		pos->data *= 10;
		printf("找到啦\n");
	}
	else
	{
		printf("没找到\n");
	}
	
	SListPrint(plist);

	SListDestory(plist); 
}
int main()
{

	TestSList3();
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c18d1cdf9d824edab4b0d9be579910f4.png)


* ## 9.某个位置去插入  ---- 在pos之前插入

```cpp
//在pos之前插入
void SListInsert(SLTNode** pphead,SLTNode* pos, SLTDateType x)
{
	assert(pphead);
	assert(pos);

	if (pos == *pphead)
	{
		SListPushFront(pphead, x);
	}
	else
	{
		SLTNode* prev = *pphead;
		while (prev->next != pos)
		{
			prev = prev->next;

			//暴力检查，pos不在链表中
			assert(prev);
		}

		SLTNode* newnode = BuySLTNode(x);
		prev->next = newnode;
		newnode->next = pos;
	}
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/70a58808fa3c4ddcaa79f31f01f024b2.png)
* ## 10.某个位置去插入  ---- 在pos之后插入

```cpp
void SListInsertAfter(SLTNode* pos, SLTDateType x)
{
	assert(pos);
	SLTNode* newnode = BuySLTNode(x);
	newnode->next = pos->next;
	pos->next = newnode;
}
```


* # 完整代码

> SList.h




```cpp
#pragma once
#include <stdio.h>
#include<assert.h>
#include<stdlib.h>
typedef int SLTDateType;
typedef struct SListNode
{
	SLTDateType data; 
	struct SListNode* next;
}SLTNode;

//创建malloc结点的指针
SLTNode* BuySLTNode(SLTDateType x);
//链表打印
void SListPrint(SLTNode* phead);   
//链表销毁
void SListDestory(SLTNode* phead);
//链表的头部插入
void SListPushFront(SLTNode** pphead, SLTDateType x);
//尾插
void SListPushBack(SLTNode** pphead, SLTDateType x);
//尾删
void SListPopBack(SLTNode** pphead);
//头删
void SListPopFront(SLTNode** pphead);
//链表的查找
SLTNode* SListFind(SLTNode* phead,SLTDateType x);
//某个位置去插入  ---- 在pos之前插入
void SListInsert(SLTNode** pphead, SLTNode* pos, SLTDateType x);
//在pos之后插入
void SListInsertAfter(SLTNode* pos, SLTDateType x);
```

> SList.c



```cpp
#include"SList.h"
void SListPrint(SLTNode* phead)
{
	//链表有可能是空的，所以不能断言
	SLTNode* cur = phead;
	while (cur != NULL)
	{
		printf("%d->", cur->data);
		cur = cur->next;//next存的是下一个结点的地址
	}
	printf("NULL\n");
}

SLTNode* BuySLTNode(SLTDateType x)
{
	SLTNode* newnode = (SLTNode*)malloc(sizeof(SLTNode));
	if (newnode == NULL)
	{
		perror("malloc fail");
		exit(-1);
	}
	newnode->data = x;
	newnode->next = NULL;
	return newnode;
}

void SListPushFront(SLTNode** pphead, SLTDateType x)
{
	assert(pphead);
	SLTNode* newnode = BuySLTNode(x);
	newnode->next = *pphead;
	*pphead = newnode;
}

void SListPushBack(SLTNode** pphead, SLTDateType x)
{
	assert(pphead);
	SLTNode* newnode = BuySLTNode(x);
	if (*pphead == NULL)
	{
		*pphead = newnode;
	}
	else
	{
		//找尾
		SLTNode* tail = *pphead;
		while (tail->next != NULL)
		{
			tail = tail->next;
		}
		tail->next = newnode; 
	}
}

void SListPopFront(SLTNode** pphead)
{
	assert(pphead);

	//温柔的检查
	/*if (*pphead == NULL)
	{
		return;
	}*/

	//暴力检查
	assert(*pphead != NULL);
	SLTNode* del = *pphead;
	*pphead = (*pphead)->next;
	free(del);
	del = NULL;
}

void SListPopBack(SLTNode** pphead)
{
	assert(pphead);

	//温柔的检查
/*if (*pphead == NULL)
{
	return;
}*/

//暴力检查
	assert(*pphead != NULL);

	if ((*pphead)->next == NULL)
	{
		free(*pphead);
		*pphead = NULL;
	}
	else
	{
		//找尾
		SLTNode* prev = NULL;
		SLTNode* tail = *pphead;
		while (tail->next != NULL)
		{
			prev = tail;
			tail = tail->next;
		}

		prev->next = NULL;
		free(tail);
		tail = NULL;
	}
}

void SListPopBack2(SLTNode** pphead)
{
	assert(pphead);

	//温柔的检查
/*if (*pphead == NULL)
{
	return;
}*/

//暴力检查
	assert(*pphead != NULL);

	if ((*pphead)->next == NULL)
	{
		free(*pphead);
		*pphead = NULL;
	}
	else
	{
		//找尾
		SLTNode* tail = *pphead;
		while (tail->next->next != NULL)
		{
			tail = tail->next;
		}
		free(tail->next);
		tail->next = NULL;
	}
}

void SListDestory(SLTNode** pphead)
{
	assert(pphead);

	SLTNode* cur = *pphead;
	while (cur != NULL)
	{
		SLTNode* next = cur->next;
		free(cur);
		cur = next;
	}

	*pphead = NULL;
}

SLTNode* SListFind(SLTNode* phead, SLTDateType x)
{
	SLTNode* cur = phead;
	while (cur != NULL)
	{
		if (cur->data == x)
		{
			return cur;
		}
		cur = cur->next;
	}
	return NULL;
}


//在pos之前插入
void SListInsert(SLTNode** pphead,SLTNode* pos, SLTDateType x)
{
	assert(pphead);
	assert(pos);

	if (pos == *pphead)
	{
		SListPushFront(pphead, x);
	}
	else
	{
		SLTNode* prev = *pphead;
		while (prev->next != pos)
		{
			prev = prev->next;

			//暴力检查，pos不在链表中
			assert(prev);
		}

		SLTNode* newnode = BuySLTNode(x);
		prev->next = newnode;
		newnode->next = pos;
	}
}

void SListInsertAfter(SLTNode* pos, SLTDateType x)
{
	assert(pos);
	SLTNode* newnode = BuySLTNode(x);
	newnode->next = pos->next;
	pos->next = newnode;
}
```


> test.c

```cpp
#include"SList.h"

void TestSList1()
{
	SLTNode* plist = NULL;
	SListPushFront(&plist, 1);
	SListPushFront(&plist, 2);
	SListPushFront(&plist, 3);
	SListPushFront(&plist, 4);
	SListPrint(plist);

	SListPopFront(&plist);
	SListPrint(plist);

	SListPopFront(&plist);
	SListPrint(plist);

	SListPopFront(&plist);
	SListPrint(plist);

	SListPopFront(&plist);
	SListPrint(plist);

	/*SListPopFront(&plist);
	SListPrint(plist);*/
}
void TestSList2()
{
	SLTNode* plist = NULL;
	SListPushBack(&plist, 1);
	SListPushBack(&plist, 2);
	SListPushBack(&plist, 3);
	SListPushBack(&plist, 4);
	SListPrint(plist);

	SListPopBack(&plist);
	SListPrint(plist);

	SListPopBack(&plist);
	SListPrint(plist);	

	SListPopBack(&plist);
	SListPrint(plist);

	SListPopBack(&plist);
	SListPrint(plist);

	/*SListPopBack(&plist);
	SListPrint(plist);*/
}

void TestSList3()
{
	SLTNode* plist = NULL;
	SListPushBack(&plist, 1);
	SListPushBack(&plist, 2);
	SListPushBack(&plist, 3);
	SListPushBack(&plist, 4);
	SListPrint(plist);

	SLTNode* pos = SListFind(plist, 3);
	if (pos)
	{
		//修改
		pos->data *= 10;
		printf("找到啦\n");
	}
	else
	{
		printf("没找到\n");
	}
	
	SListPrint(plist);
	pos = SListFind(plist, 1);
	if (pos)
	{
		SListInsert(&plist, pos, 20);
	}
	SListPrint(plist);

	SListDestory(plist); 
}
int main()
{
	//TestSList1();
	//TestSList2();
	TestSList3();
	return 0;
}
```
