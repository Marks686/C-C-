# 树的概念

树是一种非线性的数据结构，它是由n （n >= 0）





![在这里插入图片描述](https://img-blog.csdnimg.cn/85e988b7913a4ced814697d67f96954c.png)



**节点的度**：一个节点含有的子树的个数称为该节点的度； 如上图：A的为6

**叶节点或终端节点**：度为0的节点称为叶节点； 如上图：B、C、H、I...等节点为叶节点

**非终端节点或分支节点**：度不为0的节点； 如上图：D、E、F、G...等节点为分支节点

**双亲节点或父节点**：若一个节点含有子节点，则这个节点称为其子节点的父节点； 如上图：A是B的父节点

**孩子节点或子节点**：一个节点含有的子树的根节点称为该节点的子节点； 如上图：B是A的孩子节点

**兄弟节点**：具有相同父节点的节点互称为兄弟节点； 如上图：B、C是兄弟节点

**树的度**：一棵树中，最大的节点的度称为树的度； 如上图：树的度为6

**节点的层次**：从根开始定义起，根为第1层，根的子节点为第2层，以此类推；

**树的高度或深度**：树中节点的最大层次； 如上图：树的高度为4

**堂兄弟节点**：双亲在同一层的节点互为堂兄弟；如上图：H、I互为兄弟节点

**节点的祖先**：从根到该节点所经分支上的所有节点；如上图：A是所有节点的祖先

**子孙**：以某节点为根的子树中任一节点都称为该节点的子孙。如上图：所有节点都是A的子孙

**森林**：由m（m>0）棵互不相交的树的集合称为森林；

 ## 树的表示

### 普通表示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/4cc2f58056124664b3d9863c10cf2e80.png)


### 双亲表示法：
有可能用数组去表示树，一个节点可能有多个孩子，但是只有一个父亲，我们就用叶子往上走这样表示，双亲表示法就是只存储父亲的下标。
A没有父亲它的下标就是-1，B的父亲下标是0，C的父亲下标是0，G的父亲下标是2。
这种方式倒着往上走就是可以从孩子找祖先。
![在这里插入图片描述](https://img-blog.csdnimg.cn/5055d2accc0941239b61d07911b12ebf.png)

## 树实际应用（Linux树状目录）
![在这里插入图片描述](https://img-blog.csdnimg.cn/e1aec85939f940febcd355d7495d7ecf.png)



# 二叉树的概念及结构

## 概念
一棵二叉树是结点的一个有限集合，该集合:
1. 或者为空
2. 由一个根节点加上两棵别称为左子树和右子树的二叉树组成
![在这里插入图片描述](https://img-blog.csdnimg.cn/bdc261665f1f4f3da7728c1862751a35.png)
 **二叉树度结点不大于2。
二叉树是有序树， 二叉树的子树有左右之分，次序不能颠倒。**


















## 特殊的二叉树
### 斜树

左斜树是所有的结点都只有左子树的二叉树，所有结点都只有右子树的二叉树叫右斜树。这两者统称为斜树。斜树有很明显的特点，就是每一层都只有一个结点，结点的个数与二叉树的深度相同。
### 满二叉树
从第一层开始，每一层都是满的，若层数是k，则节点总数是2的k次幂减1。

![在这里插入图片描述](https://img-blog.csdnimg.cn/6bb66a2b66a347e6aceeb19c621fffb0.png)


### 完全二叉树
前k-1层都是满的，最后一层不一定满（满或者不满），但是从左到右必须是连续的，它的节点范围：
**【2^(k-1)^ ，2^k^ - 1】**

![在这里插入图片描述](https://img-blog.csdnimg.cn/224d6b90f01248ec97ae78a23f8f7af3.png)

## 二叉树性质

1. 若规定根节点的层数为1，则一棵非空二叉树的第i层上最多有2^(i-1)^个结点.


2. 若规定根节点的层数为1，则深度为h的二叉树的最大结点数是2^h^-1 .
3. 对任何一棵二叉树, 如果度为0其叶结点个数为n0 , 度为2的分支结点个数为n2,则有n0 ＝ n2＋1
4. 若规定根节点的层数为1，具有n个结点的满二叉树的深度，h=log(n+1) . (ps： 是log以2为底，n+1为对数)
5. 对于具有n个结点的完全二叉树，如果按照从上至下从左至右的数组顺序对所有节点从0开始编号，则对
于序号为i的结点有：

> 1. 若i>0，i位置节点的双亲序号：(i-1)/2；i=0，i为根节点编号，无双亲节点。
>2. 若2i+1<n，左孩子序号：2i+1，2i+1>=n则无左孩子。
>3. 若2i+2<n，右孩子序号：2i+2，2i+2>=n则无右孩子。



![在这里插入图片描述](https://img-blog.csdnimg.cn/da1d352e4d094f559d063a7130384a0c.png)






# 二叉树顺序结构及实现
## 堆的实现
堆是属于操作系统进程地址空间内存区域的划分。

我们下面实现数据结构中的堆。
堆是一个完全二叉树：分为小根堆和大根堆。
小根堆：任何一个节点的值都<=孩子的值
![在这里插入图片描述](https://img-blog.csdnimg.cn/f0baa73b256c435eabc9e61d20ddc01a.png)

大根堆：任何一个节点的值都>=孩子的值
![在这里插入图片描述](https://img-blog.csdnimg.cn/08fed4cbe74f4fd5b8c37916dc68e1d1.png)

> 应用：

1.堆排序，第一个时间复杂度达到--O(N*log N)的排序。
2.topK问题：找一堆数据前K大或者前K小。



数组下标计算父子关系公式：

左孩子：**leftchild = parent*2 + 1**
右孩子：**rightchild = parent*2 + 2** 
孩子算父亲：**parent = (child - 1) / 2**

### 堆向下调整算法
给出一个数组，逻辑上看做一颗完全二叉树。通过从根节点开始的向下调整算法可以把它调整成一个小堆。
前提：左右子树必须是一个堆，才能调整。

```cpp
int array[] = {27,15,19,18,28,34,65,49,25,37};
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20504ed6954849ee99cccc28951248fe.png)
### 堆的创建

给出一个数组，逻辑上可以看做一颗完全二叉树，但是还不是一个堆，现在我们把它构建成一个堆。根节点左右子树不是堆，这里我们从**倒数的第一个非叶子节点的子树**开始调整，一直调整到根节点的树，就可以调整成堆。
采用向下调整建堆。

```cpp
int a[] = {1,5,3,8,7,6}; 
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/ecaedc533e7e46a9ac4f25e9f3d6763d.png)




### 堆的插入
先插入一个数组的尾上，再进行向上调整算法，直到满足堆。


**1.先将元素插入到对的末尾，即最后一个孩子之后。
2.插入之后如果堆的性质遭到了破坏，将新插入节点顺着双亲往上调整到合适位置即可。**
![在这里插入图片描述](https://img-blog.csdnimg.cn/e6a31814c6034babb93373d4005eb454.png)
`AdjustUp`


###  堆的删除
删除堆是删除堆顶的数据，将堆顶的数据根最后一个数据一换，然后删除数组最后一个数据，再进行向下调整算法。

1.将堆顶元素与堆中追后一个元素进行交换。
2.删除堆中最后一个元素
3.将堆顶元素向下调整到满足堆特性为止。

![在这里插入图片描述](https://img-blog.csdnimg.cn/e1df926c72ed45cfaaafd4c6af68b3b0.png)

### 堆的代码实现



> heap.h


```cpp
#pragma once

#include <stdio.h>
#include <assert.h>
#include <stdlib.h>
#include <stdbool.h>

typedef int HPDataType;
typedef struct Heap
{
	HPDataType* a;
	int size;
	int capacity;
}HP;

void HeapPrint(HP* php);

void Swap(HPDataType* p1, HPDataType* p2);
void AdjustUp(HPDataType* a, int child);
void AdjustDown(HPDataType* a, int n, int parent);

void HeapInit(HP* php);
void HeapDestroy(HP* php);
// xֶ̬
void HeapPush(HP* php, HPDataType x);
// ɾѶԪ
void HeapPop(HP* php);
// ضѶԪ
HPDataType HeapTop(HP* php);
bool HeapEmpty(HP* php);
int HeapSize(HP* php);
```










 
> Heap.c

```cpp
#include "Heap.h"

void HeapPrint(HP* php)
{
	for (int i = 0; i < php->size; ++i)
	{
		printf("%d ", php->a[i]);
	}
	printf("\n");
}

void HeapInit(HP* php)
{
	assert(php);
	php->a = NULL;
	php->size = php->capacity = 0;
}

void HeapDestroy(HP* php)
{
	assert(php);

	free(php->a);
	php->a = NULL;
	php->capacity = php->size = 0;
}

void Swap(HPDataType* p1, HPDataType* p2)
{
	HPDataType tmp = *p1;
	*p1 = *p2;
	*p2 = tmp;
}

void AdjustUp(HPDataType* a, int child)
{
	int parent = (child - 1) / 2;
	//while (parent >= 0)
	while (child > 0)
	{
		if (a[child] > a[parent])
		{
			Swap(&a[child], &a[parent]);
			child = parent;
			parent = (child - 1) / 2;
		}
		else
		{
			break;
		}
	}
}

// 插入x继续保持堆形态 -- logN
void HeapPush(HP* php, HPDataType x)
{
	assert(php);
	// 扩容
	if (php->size == php->capacity)
	{
		int newCapacity = php->capacity == 0 ? 4 : php->capacity * 2;
		HPDataType* tmp = (HPDataType*)realloc(php->a, newCapacity*sizeof(HPDataType));
		if (tmp == NULL)
		{
			perror("realloc fail");
			exit(-1);
		}

		php->a = tmp;
		php->capacity = newCapacity;
	}

	php->a[php->size] = x;
	php->size++;

	AdjustUp(php->a, php->size - 1);
}

void AdjustDown(HPDataType* a, int n, int parent)
{
	int minChild = parent * 2 + 1;
	while (minChild < n)
	{
		// 找出小的那个孩子
		if (minChild+1 < n && a[minChild + 1] < a[minChild])
		{
			minChild++;
		}

		if (a[minChild] < a[parent])
		{
			Swap(&a[minChild], &a[parent]);
			parent = minChild;
			minChild = parent * 2 + 1;
		}
		else
		{
			break;
		}
	}
}

// 删除堆顶元素 -- 找次大或者次小 -- logN
// O(logN)
void HeapPop(HP* php)
{
	assert(php);
	assert(!HeapEmpty(php));
	Swap(&php->a[0], &php->a[php->size - 1]);
	php->size--;

	AdjustDown(php->a, php->size, 0);
}

// 返回堆顶的元素
HPDataType HeapTop(HP* php)
{
	assert(php);
	assert(!HeapEmpty(php));

	return php->a[0];
}

bool HeapEmpty(HP* php)
{
	assert(php);
	return php->size == 0;
}

int HeapSize(HP* php)
{
	assert(php);

	return php->size;
}
```

### 堆的应用

> 堆排序：

**1.建堆:**
升序：建大堆
降序：建小堆
**2.利用堆删除思想来进行排序**


```cpp
int a[] = {20，17，4，16，5，3}; 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6de96e95c7ef4a59ab9a286c86605a39.png)

建堆和堆删除中都用到了向下调整，因此必须掌握了向下调整。




# 二叉树链式结构及实现
快速进入二叉树操作学习，等二叉树结构了解的差不多时，我们反过头再来研究二叉树真正的创建方式。



**二叉树：**
1. 空树
2. 非空：根节点，根节点的左子树、根节点的右子树组成的。

二叉树定义是递归式的，因此后序基本操作中基本都是按照该概念实现的。





![在这里插入图片描述](https://img-blog.csdnimg.cn/ef73627c4f6f483f954afcf32d62a509.png)

## 二叉树的遍历
###  前序、中序以及后序遍历


1. **前序遍历(Preorder Traversal 亦称先序遍历)——访问根结点的操作发生在遍历其左右子树之前。**

**根 左子树 右子树**
 
遇到根先访问，访问1，然后访问左子树的根，访问2，再访问2的左子树 右子树，访问3，访问3的左子树 右子树都为NULL，然后访问2的右子树，右子树为NULL。再访问1的右子树，右子树的根是4，然后访问4的左子树，访问5，然后访问5的左子树，左子树为NULL，然后访问5的右子树，右子树为NULL，然后访问4的右子树，根为6，再访问6的左子树，左子树为NULL，在访问右子树也为NULL，4的右子树结束，4这棵树作为1的右子树，到此整棵树访问结束。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2f5a3b5946d94c4bb85980f35e0f17d8.png)



```cpp
// 二叉树前序遍历
void PreOrder(BTNode* root)
{
	if (root == NULL)
	{
		printf("NULL ");
		return;
	}

	printf("%d ", root->data);
	PreOrder(root->left);
	PreOrder(root->right);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/d555884e88e34297ad44acca03a62c1b.png)




根不是空，打印第一个值就是1，打印以后要去走左子树右子树，左子树右子树别分为规模类似的子问题，从栈帧的角度，往下走建立第二个栈帧，1打印了就往1的左树递归，1的左是2，2不是空打印2，再紧接着访问2的左子树，再往下递归，从物理上来说这里建立了三层栈帧，第一层栈帧root存的是1的地址，第二层栈帧root存的是2的地址，第三层栈帧root存的是3的地址，3打印了，3不是空，再往3的左子树去走，3的左边是空，所以就打印出了123NULL，所有的函数结束就回到调用它的地方，所以就回到了3，3走完了就走3的右，3的右还是空继续打印NULL，然后return回到调用的地方。3这个函数结束了，3是作为2的左递归下来的，3结束了回到它调用的地方，就回到2，2的左完了，就去2的右，2的右是NULL，打印NULL然后回到调用的地方，2的左、右都结束了，2作为1的左递归下来的，现在回到1的左，1的左结束，再去递归1的右，1的右是4，4不为NULL打印4，然后就递归4的左，4的左是5，打印5，递归5的左，5的左是空，打印NULL，然后回来在递归5的右，5的右也是空，打印NULL，然后继续回去4的左，继续递归走，走的是4的右，4的右是6，打印6，6不是NULL，继续6的左，6的左是NULL,打印NULL然后回到6的左，继续递归6的右，6的右也是空，打印完也回到6，此时6结束了，6是作为4的右下来的，结束后6回到4，对于4而言，4的左、右都结束了，4作为1的右下来的，所以回到1，1的左、右也结束了，1再出去，函数的调用全部结束。




**从底层的角度整个过程在建立栈帧，保存数据。右数建立的栈帧，和左树递归调用建立的栈帧是重叠的，递归右边的时候，左边栈帧已经销毁了。**





















---

2. **中序遍历(Inorder Traversal)——访问根结点的操作发生在遍历其左右子树之中（间）。**


**左子树 根 右子树**


![在这里插入图片描述](https://img-blog.csdnimg.cn/7f9f655701624cb78f6e5147b5b4de88.png)



```cpp
// 二叉树中序遍历
void InOrder(BTNode * root)
{
	if (root == NULL)
	{
		printf("NULL ");
		return;
	}

	InOrder(root->left);
	printf("%d ", root->data);
	InOrder(root->right);
}
```


我们只看看中序的左边，第一个是根，根1不是空，先要去递归它的左子树，然后就遇到2，2不是空，2不能访问，继续递归2的左子树，左子树是3，遇到3不能访问，先访问3的左子树，3的左子树是NULL，打印NULL，NULL结束了回到3，此时打印3，然后访问3的右子树，右子树是空，打印NULL继续返回，3递归完了就会回到它递归调用的地方，回到2，然后就打印2，然后再递归2的右子树，2的右子树也是一个NULL，打印NULL，回到2的右，2递归结束就回到2最初递归调用的地方，也就是1的左。然后就访问1，打印1，然后再访问1的右，不能访问4，就访问4的左......不断往下走，走到空就是尽头，然后再不断返回。



![在这里插入图片描述](https://img-blog.csdnimg.cn/44e10bac595b45eea86f6f9eca3afda2.png)







---
3. **后序遍历(Postorder Traversal)——访问根结点的操作发生在遍历其左右子树之后。**


![在这里插入图片描述](https://img-blog.csdnimg.cn/1bc2f889908d4ffd92ea6fbe1dfa2540.png)




**左子树 右子树 根**


```cpp
// 二叉树后序遍历
void PostOrder(BTNode* root)
{
	if (root == NULL)
	{
		printf("NULL ");
		return;
	}

	InOrder(root->left);
	InOrder(root->right);
	printf("%d ", root->data);
}
```


```cpp
#include <stdio.h>
#include <assert.h>
#include <stdlib.h>


typedef int BTDataType;
typedef struct BinaryTreeNode
{
	BTDataType data;
	struct BinaryTreeNode* left;
	struct BinaryTreeNode* right;
}BTNode;


// 二叉树前序遍历
void PreOrder(BTNode* root)
{
	if (root == NULL)
	{
		printf("NULL ");
		return;
	}

	printf("%d ", root->data);
	PreOrder(root->left);
	PreOrder(root->right);
}
// 二叉树中序遍历
void InOrder(BTNode * root)
{
	if (root == NULL)
	{
		printf("NULL ");
		return;
	}

	InOrder(root->left);
	printf("%d ", root->data);
	InOrder(root->right);
}

// 二叉树后序遍历
void PostOrder(BTNode* root)
{
	if (root == NULL)
	{
		printf("NULL ");
		return;
	}

	InOrder(root->left);
	InOrder(root->right);
	printf("%d ", root->data);
}



BTNode* CreateTree()
{
	BTNode* n1 = (BTNode*)malloc(sizeof(BTNode));
	assert(n1);
	BTNode* n2 = (BTNode*)malloc(sizeof(BTNode));
	assert(n2);
	BTNode* n3 = (BTNode*)malloc(sizeof(BTNode));
	assert(n3);
	BTNode* n4 = (BTNode*)malloc(sizeof(BTNode));
	assert(n4);
	BTNode* n5 = (BTNode*)malloc(sizeof(BTNode));
	assert(n5);
	BTNode* n6 = (BTNode*)malloc(sizeof(BTNode));
	assert(n6);
	

	n1->data = 1;
	n2->data = 2;
	n3->data = 3;
	n4->data = 4;
	n5->data = 5;
	n6->data = 6;

	n1->left = n2;
	n1->right = n4;
	n2->left = n3;
	n2->right = NULL;
	n4->left = n5;
	n4->right = n6;
	n3->left = NULL;
	n3->right = NULL;
	n5->left = NULL;
	n5->right = NULL;
	n6->left = NULL;
	n6->right = NULL;

	

	return n1;
}


int main()
{
	BTNode* root = CreateTree();
	PreOrder(root);
	printf("\n");

	InOrder(root);
	printf("\n");


	return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/85c96349f3b041b98ca3dc833dfa4154.png)


---


###  节点个数以及高度等

#### 求结点个数


> 求一棵树结点的个数：遍历计数




```cpp
//求一棵树节点的个数
int TreeSize(BTNode* root)
{
	static int count = 0;
	if (root == NULL)
		return count;

	++count;
	TreeSize(root->left);
	TreeSize(root->right);

	return count;
}
```

采用静态变量进行计数，我们发现调用一次可以，调用两次就出现了问题，局部的静态成员变量只有在第一次调用的时候会初始化。所以采用局部静态变量的这种方法是不可取的。我们要把静态变量放到全局去。


![在这里插入图片描述](https://img-blog.csdnimg.cn/70fdeaec153c46848c29d0e2310d8378.png)



```cpp
//求一棵树节点的个数
static int count = 0;
void TreeSize(BTNode* root)
{
	if (root == NULL)
		return count;

	++count;
	TreeSize(root->left);
	TreeSize(root->right);

	return ;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/978aec3b8aab40d69049554986300fd7.png)

>还有一种方式：切分子问题



```cpp
int TreeSize(BTNode* root)
{
	return root == NULL ? 0 :
		TreeSize(root->left) + TreeSize(root->right) + 1;
}
```





![在这里插入图片描述](https://img-blog.csdnimg.cn/370436946ad44f5c8804f9335633e155.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/979f57264192474ea9aee311acea4011.png)


#### 叶子结点的个数

```cpp
int TreeLeafSize(BTNode* root)
{
	if (root == NULL)
		return 0;
	if (root->left == NULL
		&& root->right == NULL)
		return 1;

	return TreeLeafSize(root->left)
		+ TreeLeafSize(root->right);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6947c53e1d8d44f390aa51e6ffdf2c1b.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2921c19b204a4b6598d0f82caa31d96c.png)



---

####  深度（高度）

父亲的高度 = 左右子树大的那个 + 1

```cpp
//深度高度
int TreeHeight(BTNode* root)
{
	if (root == NULL)
	{
		return 0;
	}

	int lh = TreeHeight(root->left);
	int rh = TreeHeight(root->right);

	return lh > rh ? lh + 1 : rh + 1;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ec2f7130db394f2ea291fa2fd89f73de.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/9fbee47ef7f14314ac4c6dbb6d9b54f2.png)

---


#### 求k层节点数

假如k是3，求第三层就转换成求左子树和右子树的第二层。即求左右子树的k-1层。


递归过程：
从1开始递归，求第三层结点数，然后就求左子树的第二层，左子树的第二层就转换为求它左子树的第一层，然后就返回1,再求2为根节点的右子树的第一层，为NULL，返回0，左树加起来返回1。然后去找右树......

![在这里插入图片描述](https://img-blog.csdnimg.cn/2ab68043718f4c40825e8027e2d3ea25.png)

```cpp
//求k层节点数
int TreeKLevel(BTNode* root, int k)
{
	assert(k > 0);
	if (root == NULL)
	{
		return 0;
	}

	if (k == 1)
	{
		return 1;
	}

	//转换求子树的k-1层
	return TreeKLevel(root->left, k - 1)
		+ TreeKLevel(root->right, k - 1);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/4fa157776a33459dbc43a66856332d04.png)



#### 返回x所在的节点

找到节点后是层层往回返回的过程。比如说找6，从1进入，递归左子树，没找到，再去递归右子树。

```cpp
//返回x所在的节点
BTNode* TreeFind(BTNode* root,BTDataType x)
{
	if (root == NULL)
	{
		return NULL;
	}

	if (root->data == x)
	{
		return root;
	}

	//先去左树找
	BTNode* lret = TreeFind(root->left, x);
	if (lret)
		return lret;
	//左树没找到，再去右树找
	BTNode* rret = TreeFind(root->right, x);
	if (rret)
		return rret;
	return NULL;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/33b3b43ffb2e4e91bf138e429c9182a5.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/9071406fee2042148492817ef0e7ad44.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/53196845b43a468e88f85bf9a64cda0b.png)









### 层序遍历


## 二叉树的创建和销毁
