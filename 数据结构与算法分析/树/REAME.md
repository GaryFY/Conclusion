树
====

双亲孩子表示法
```c
#define MAX_TREE_SIZE 100

typedef char ElemType;

// 孩子结点
typedef struct CTNode
{
	int child;  // 孩子结点的下标
	struct CTNode *next;  // 指向下一个孩子结点的指针
}*ChildPtr;

// 表头结构
typedef struct
{
	ElemType data;  // 存放在书中的结点的数据
	int parent;  // 存放双亲的下标
	ChildPtr firstchild;  // 指向第一个孩子的指针
}CTBox;

// 树结构
typedef struct
{
	CTBox nodes[MAX_TREE_SIZE];  // 结点数组
	int r,n;
}
```

二叉树
----

满二叉树（非叶子结点的度一定是2）
完全二叉树（叶子结点只能出现在最下两层；最下层的叶子一定集中在左部连续位置；倒数第二层，若有叶子结点，一定都在右部连续位置）    
**在二叉树的第i层上之多有2^(i-1)个结点（i>=1）**     
**具有n个结点的完全二叉树的深度为[log2n]+1向下取整**
**深度为k的二叉树至多为2^k-1个结点**
  
二叉链表   
```c
typedef struct BiTNode
{
	ElemType data;
	struct BiTNode *lchild,*rchild;
}BiTNode,*BiTNode;
```
###二叉树的遍历
**次序与访问**    
####前序遍历
空->空操作返回，根结点->前序遍历左子树->前序遍历右子数
####中序遍历
空->空操作返回，根结点（不是先访问根结点）->中序遍历左子树（根结点的左子树）->根结点->中序遍历右子数
####后序遍历
空->空操作返回，从左到右先叶子后结点的方式遍历访问左右子树，最后访问根结点
####层序遍历

###二叉树的建立和遍历算法
####二叉树的建立
保证最后的为根结点，即左右为NULL或者一方为NULL     
示例：建立二叉树并输出每个字符所在的层数。如右图要求输出   
A在第一层
B、C在第二层
D、E在第三层  
```c
#include <stdio.h>
#include <stdlib.h>

typedef char ElemType;

typedef struct BiTNode
{
	chat data;
	struct BiTNode *lchild,*rchild;
}BiTNode,*BiTree;

// 创建一棵二叉树,约定用户遵照前序遍历的方式输入数据
CreatBiTree(BiTree *T)
{
	char c;
	scanf("%c",&c);
	if(' ' == c)
	{
		*T = NULL;
	}
	else
	{	
		*T = (BitNode *)malloc(sizeof(BiTNode));
		(*T)->data = c;
		CreatBiTree(&(*T)->lchild);
		CreatBiTree(&(*T)->rchild);
	}
}

// 访问二叉树结点的具体操作
visit(char c,int level)
{
	prinf("%c 位于第 %d 层\n",c,level);
}

// 遍历二叉树
PreOrderTraverse(BiTree T,int level)
{
	if( T )
	{
		visit(T->data,level);
		PreOrderTraverse(T->lchild,level+1);
		PreOrderTraverse(T->rchild,level+1);
	}
}

int main()
{
	int level = 1;
	BiTree T = NULL;
	
	CreatBiTree(&T);
	PreOrderTraverse(T,level);
	
	return 0;
}
```
###线索二叉树
线索化：  
思路：按某种次序遍历二叉树，在遍历过程中用线索取代空指针即可。   
查找后继结点：
一：没有右孩子，直接获取；二：有右孩子，中序遍历查找右子树，第一个结点即为当前结点的后继结点。    
查找前驱结点：   
一：没有左孩子，依据线索直接获取；二：有左孩子，中序遍历左子树中往右链中第一个没有右孩子的结点即为前驱结点。   
遍历：   
以中序遍历为例，首先找到中序遍历的开始结点，然后利用线索依次查找后继结点即可。    
```c
#include <stdio.h>
#include <stdlib.h>

tyoedef char ElemType;

// 线索存储标志位
// Link(0):表示指向左右孩子的指针
// Thread(1):表示指向前驱后继的线索
typedef enum{Link,Thread} PointerTag;

typedef struct BiThrNode
{
	char data;
	struct BiThrNode *lchild,*rchild;
	PointerTag ltag;
	PointerTag rtag;
}BiThrNode,*BiThrTree;

// 全局变量，始终指向刚刚访问过的结点
BiThrTree pre;

// 创建一棵二叉树，约定用户遵照前序遍历的方式输入数据
CreatBiThrTree(BiTree *T)
{
	char c;
	scanf("%c",&c);
	if(' ' == c)
	{
		*T = NULL;
	}
	else
	{	
		*T = (BitNode *)malloc(sizeof(BiTNode));
		(*T)->data = c;
		(*T)->ltag = Link;
		(*T)->rtag = Link;
		
		CreatBiThrTree(&(*T)->lchild);
		CreatBiThrTree(&(*T)->rchild);
	}
}
// 中序遍历线索化
InThreading(BiThrTree T)
{
	if( T )
	{
		InThreading( T->lchild);  // 递归左孩子线索化
		
		// 结点处理
		if(!T->lchild)  // 如果该结点没有左孩子，设置ltag为thread，并把lchild指向刚刚访问过的结点
		{
			T->ltag = Thread;
			T->lchild = pre;
		}
		
		if(!pre->rchild)
		{
			pre->rtag = Thread;
			pre->rchild = T;
		}
		
		pre = T;
		
		InThreading( T->rchild);  // 递归右孩子线索化
	}
}

InOrderThreading(BiThrTree *p,BiThrTree T)
{
	*p = (BiThrTree)malloc(sizeof(BiThrNode));
	(*p)->ltag = Link;
	(*p)->rtag = Thread;
	(*p)->rchild = *p;
	if(!T)
	{
		(*p)->lchild = *p;
	}
	else
	{
		(*p)->lchild = T;
		pre = *p;
		InThreading(T);
		pre->rchild = *p;
		pre->rtag = Thread;
		(*p)->rchild = pre;
	}
}

int main()
{
	BiThrTree P,T = NULL;
	
	CreatBiThrTree(&T);
	
	InOrderThreading(&P,T);
	return 0;
}
```
树、森林及二叉树的相互转化
----
###树到二叉树   
* 在树中所有的兄弟结点之间加一连线     
* 对每个结点，除了保留与其长子的连线外，去掉该结点与其他孩子的连线    
**只有左子树**  
###森林到二叉树的转换
* 先将森林中的每棵树变为二叉树    
* 再将各二叉树的根结点视为兄弟从左至右连在一起，就形成可一棵二叉树   
###二叉树到树、森林的转换
* 若结点x是其双亲y的做孩子，则把x的右孩子，右孩子的右孩子，……，都与y用连线连起来    
* 去掉所有双亲到右孩子之间的连线

树与森林的遍历
----
###树
先根遍历：向访问树的根结点，然后再一次先根遍历根的每棵子树    
后根遍历：先依次遍历每棵子树，然后再访问根结点    
###森林
前序遍历   
后序遍历
**树、森林的前根（序）遍历和二叉树的前序遍历结果相同，
树、森林的后根（序）遍历和二叉树的中序遍历结果相同**

哈弗曼树
----
哈弗曼编码：定长编码，变长编码，前缀码   
左子数用0表示，右子树用1表示    
结点的权值是字符出现的次数
* build a priority queue
* build a huffmanTree
* build a huffmanTable
* encode
* decode


















