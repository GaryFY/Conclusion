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




















