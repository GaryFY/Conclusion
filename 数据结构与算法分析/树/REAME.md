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



常见问题
----
二叉树节点定义如下： 
```
struct BinaryTreeNode
{
    int m_nValue;
    BinaryTreeNode* m_pLeft;
    BinaryTreeNode* m_pRight;
};
```
###求二叉树中的节点个数
递归解法：      
1. 如果二叉树为空，节点个数为0
2. 如果二叉树不为空，二叉树节点个数 = 左子树节点个数 + 右子树节点个数 + 1
```
int GetNodeNum(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL) // 递归出口  
        return 0;  
    return GetNodeNum(pRoot->m_pLeft) + GetNodeNum(pRoot->m_pRight) + 1;  
} 
```
###求二叉树的深度
递归解法：   
1. 如果二叉树为空，二叉树的深度为0
2. 如果二叉树不为空，二叉树的深度 = max(左子树深度， 右子树深度) + 1  
```
int GetDepth(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL) // 递归出口  
        return 0;  
    int depthLeft = GetDepth(pRoot->m_pLeft);  
    int depthRight = GetDepth(pRoot->m_pRight);  
    return depthLeft > depthRight ? (depthLeft + 1) : (depthRight + 1);   
}  
```

###前序遍历，中序遍历，后序遍历
前序遍历递归解法：
1. 如果二叉树为空，空操作
2. 如果二叉树不为空，访问根节点，前序遍历左子树，前序遍历右子树
```
void PreOrderTraverse(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL)  
        return;  
    Visit(pRoot); // 访问根节点  
    PreOrderTraverse(pRoot->m_pLeft); // 前序遍历左子树  
    PreOrderTraverse(pRoot->m_pRight); // 前序遍历右子树  
} 
```
中序遍历递归解法
1. 如果二叉树为空，空操作。
2. 如果二叉树不为空，中序遍历左子树，访问根节点，中序遍历右子树
```
void InOrderTraverse(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL)  
        return;  
    InOrderTraverse(pRoot->m_pLeft); // 中序遍历左子树  
    Visit(pRoot); // 访问根节点  
    InOrderTraverse(pRoot->m_pRight); // 中序遍历右子树  
}  
```
后序遍历递归解法
1. 如果二叉树为空，空操作
2. 如果二叉树不为空，后序遍历左子树，后序遍历右子树，访问根节点
```
void PostOrderTraverse(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL)  
        return;  
    PostOrderTraverse(pRoot->m_pLeft); // 后序遍历左子树  
    PostOrderTraverse(pRoot->m_pRight); // 后序遍历右子树  
    Visit(pRoot); // 访问根节点  
} 
```
###分层遍历二叉树（按层次从上往下，从左往右）
相当于广度优先搜索，使用队列实现。队列初始化，将根节点压入队列。
当队列不为空，进行如下操作：弹出一个节点，访问，若左子节点或右子节点不为空，将其压入队列。
```
void LevelTraverse(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL)  
        return;  
    queue<BinaryTreeNode *> q;  
    q.push(pRoot);  
    while(!q.empty())  
    {  
        BinaryTreeNode * pNode = q.front();  
        q.pop();  
        Visit(pNode); // 访问节点  
        if(pNode->m_pLeft != NULL)  
            q.push(pNode->m_pLeft);  
        if(pNode->m_pRight != NULL)  
            q.push(pNode->m_pRight);  
    }  
    return;  
} 
```
###将二叉查找树变为有序的双向链表
要求不能创建新节点，只调整指针。    
递归解法：    
1. 如果二叉树查找树为空，不需要转换，对应双向链表的第一个节点是NULL，最后一个节点是NULL
2. 如果二叉查找树不为空：
* 如果左子树为空，对应双向有序链表的第一个节点是根节点，左边不需要其他操作；
* 如果左子树不为空，转换左子树，二叉查找树对应双向有序链表的第一个节点就是左子树转换后双向有序链表的第一个节点，
同时将根节点和左子树转换后的双向有序链表的最后一个节点连接；
* 如果右子树为空，对应双向有序链表的最后一个节点是根节点，右边不需要其他操作；
* 如果右子树不为空，对应双向有序链表的最后一个节点就是右子树转换后双向有序链表的最后一个节点，
同时将根节点和右子树转换后的双向有序链表的第一个节点连接。
```
/****************************************************************************** 
参数： 
pRoot: 二叉查找树根节点指针 
pFirstNode: 转换后双向有序链表的第一个节点指针 
pLastNode: 转换后双向有序链表的最后一个节点指针 
******************************************************************************/  
void Convert(BinaryTreeNode * pRoot,   
             BinaryTreeNode * & pFirstNode, BinaryTreeNode * & pLastNode)  
{  
    BinaryTreeNode *pFirstLeft, *pLastLeft, * pFirstRight, *pLastRight;  
    if(pRoot == NULL)   
    {  
        pFirstNode = NULL;  
        pLastNode = NULL;  
        return;  
    }  
  
    if(pRoot->m_pLeft == NULL)  
    {  
        // 如果左子树为空，对应双向有序链表的第一个节点是根节点  
        pFirstNode = pRoot;  
    }  
    else  
    {  
        Convert(pRoot->m_pLeft, pFirstLeft, pLastLeft);  
        // 二叉查找树对应双向有序链表的第一个节点就是左子树转换后双向有序链表的第一个节点  
        pFirstNode = pFirstLeft;  
        // 将根节点和左子树转换后的双向有序链表的最后一个节点连接  
        pRoot->m_pLeft = pLastLeft;  
        pLastLeft->m_pRight = pRoot;  
    }  
  
    if(pRoot->m_pRight == NULL)  
    {  
        // 对应双向有序链表的最后一个节点是根节点  
        pLastNode = pRoot;  
    }  
    else  
    {  
        Convert(pRoot->m_pRight, pFirstRight, pLastRight);  
        // 对应双向有序链表的最后一个节点就是右子树转换后双向有序链表的最后一个节点  
        pLastNode = pLastRight;  
        // 将根节点和右子树转换后的双向有序链表的第一个节点连接  
        pRoot->m_pRight = pFirstRight;  
        pFirstRight->m_pLeft = pRoot;  
    }  
  
    return;  
}  
```
###求二叉树第K层的节点个数
递归解法：   
1. 如果二叉树为空或者k<1返回0
2. 如果二叉树不为空并且k==1，返回1
3. 如果二叉树不为空且k>1，返回左子树中k-1层的节点个数与右子树k-1层节点个数之和
```
int GetNodeNumKthLevel(BinaryTreeNode * pRoot, int k)  
{  
    if(pRoot == NULL || k < 1)  
        return 0;  
    if(k == 1)  
        return 1;  
    int numLeft = GetNodeNumKthLevel(pRoot->m_pLeft, k-1); // 左子树中k-1层的节点个数  
    int numRight = GetNodeNumKthLevel(pRoot->m_pRight, k-1); // 右子树中k-1层的节点个数  
    return (numLeft + numRight);  
}  
```
###求二叉树中叶子节点的个数
递归解法：  
1. 如果二叉树为空，返回0
2. 如果二叉树不为空且左右子树为空，返回1
3. 如果二叉树不为空，且左右子树不同时为空，返回左子树中叶子节点个数加上右子树中叶子节点个数
```
int GetLeafNodeNum(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL)  
        return 0;  
    if(pRoot->m_pLeft == NULL && pRoot->m_pRight == NULL)  
        return 1;  
    int numLeft = GetLeafNodeNum(pRoot->m_pLeft); // 左子树中叶节点的个数  
    int numRight = GetLeafNodeNum(pRoot->m_pRight); // 右子树中叶节点的个数  
    return (numLeft + numRight);  
} 
```
###判断两棵二叉树是否结构相同
不考虑数据内容。结构相同意味着对应的左子树和对应的右子树都结构相同。   
递归解法：   
1. 如果两棵二叉树都为空，返回真
2. 如果两棵二叉树一棵为空，另一棵不为空，返回假
3. 如果两棵二叉树都不为空，如果对应的左子树和右子树都同构返回真，其他返回假
```
bool StructureCmp(BinaryTreeNode * pRoot1, BinaryTreeNode * pRoot2)  
{  
    if(pRoot1 == NULL && pRoot2 == NULL) // 都为空，返回真  
        return true;  
    else if(pRoot1 == NULL || pRoot2 == NULL) // 有一个为空，一个不为空，返回假  
        return false;  
    bool resultLeft = StructureCmp(pRoot1->m_pLeft, pRoot2->m_pLeft); // 比较对应左子树   
    bool resultRight = StructureCmp(pRoot1->m_pRight, pRoot2->m_pRight); // 比较对应右子树  
    return (resultLeft && resultRight);  
}
```
###判断二叉树是不是平衡二叉树
递归解法：    
1. 如果二叉树为空，返回真
2. 如果二叉树不为空，如果左子树和右子树都是AVL树并且左子树和右子树高度相差不大于1，返回真，其他返回假
```
bool IsAVL(BinaryTreeNode * pRoot, int & height)  
{  
    if(pRoot == NULL) // 空树，返回真  
    {  
        height = 0;  
        return true;  
    }  
    int heightLeft;  
    bool resultLeft = IsAVL(pRoot->m_pLeft, heightLeft);  
    int heightRight;  
    bool resultRight = IsAVL(pRoot->m_pRight, heightRight);  
    if(resultLeft && resultRight && abs(heightLeft - heightRight) <= 1) // 左子树和右子树都是AVL，并且高度相差不大于1，返回真  
    {  
        height = max(heightLeft, heightRight) + 1;  
        return true;  
    }  
    else  
    {  
        height = max(heightLeft, heightRight) + 1;  
        return false;  
    }  
}  
```
###求二叉树的镜像
递归解法：
1. 如果二叉树为空，返回空
2. 如果二叉树不为空，求左子树和右子树的镜像，然后交换左子树和右子树
```
BinaryTreeNode * Mirror(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL) // 返回NULL  
        return NULL;  
    BinaryTreeNode * pLeft = Mirror(pRoot->m_pLeft); // 求左子树镜像  
    BinaryTreeNode * pRight = Mirror(pRoot->m_pRight); // 求右子树镜像  
    // 交换左子树和右子树  
    pRoot->m_pLeft = pRight;  
    pRoot->m_pRight = pLeft;  
    return pRoot;  
}  
```
###求二叉树中两个节点的最低公共祖先节点
先求从根节点到两个节点的路径，然后再比较对应路径的节点就行，最后一个相同的节点也就是他们在二叉树中的最低公共祖先节点
```
bool GetNodePath(BinaryTreeNode * pRoot, BinaryTreeNode * pNode,   
                 list<BinaryTreeNode *> & path)  
{  
    if(pRoot == pNode)  
    {     
        path.push_back(pRoot);  
        return true;  
    }  
    if(pRoot == NULL)  
        return false;  
    path.push_back(pRoot);  
    bool found = false;  
    found = GetNodePath(pRoot->m_pLeft, pNode, path);  
    if(!found)  
        found = GetNodePath(pRoot->m_pRight, pNode, path);  
    if(!found)  
        path.pop_back();  
    return found;  
}  
BinaryTreeNode * GetLastCommonParent(BinaryTreeNode * pRoot, BinaryTreeNode * pNode1, BinaryTreeNode * pNode2)  
{  
    if(pRoot == NULL || pNode1 == NULL || pNode2 == NULL)  
        return NULL;  
    list<BinaryTreeNode*> path1;  
    bool bResult1 = GetNodePath(pRoot, pNode1, path1);  
    list<BinaryTreeNode*> path2;  
    bool bResult2 = GetNodePath(pRoot, pNode2, path2);  
    if(!bResult1 || !bResult2)   
        return NULL;  
    BinaryTreeNode * pLast = NULL;  
    list<BinaryTreeNode*>::const_iterator iter1 = path1.begin();  
    list<BinaryTreeNode*>::const_iterator iter2 = path2.begin();  
    while(iter1 != path1.end() && iter2 != path2.end())  
    {  
        if(*iter1 == *iter2)  
            pLast = *iter1;  
        else  
            break;  
        iter1++;  
        iter2++;  
    }  
    return pLast;  
}  
```
###求二叉树中节点的最大距离
即二叉树中相距最远的两个节点之间的距离。   
递归解法：   
1. 如果二叉树为空，返回0，同时记录左子树和右子树的深度，都为0
2. 如果二叉树不为空，最大距离要么是左子树中的最大距离，要么是右子树中的最大距离，要么是左子树节点中到根节点的最大距离+
```
int GetMaxDistance(BinaryTreeNode * pRoot, int & maxLeft, int & maxRight)  
{  
    // maxLeft, 左子树中的节点距离根节点的最远距离  
    // maxRight, 右子树中的节点距离根节点的最远距离  
    if(pRoot == NULL)  
    {  
        maxLeft = 0;  
        maxRight = 0;  
        return 0;  
    }  
    int maxLL, maxLR, maxRL, maxRR;  
    int maxDistLeft, maxDistRight;  
    if(pRoot->m_pLeft != NULL)  
    {  
        maxDistLeft = GetMaxDistance(pRoot->m_pLeft, maxLL, maxLR);  
        maxLeft = max(maxLL, maxLR) + 1;  
    }  
    else  
    {  
        maxDistLeft = 0;  
        maxLeft = 0;  
    }  
    if(pRoot->m_pRight != NULL)  
    {  
        maxDistRight = GetMaxDistance(pRoot->m_pRight, maxRL, maxRR);  
        maxRight = max(maxRL, maxRR) + 1;  
    }  
    else  
    {  
        maxDistRight = 0;  
        maxRight = 0;  
    }  
    return max(max(maxDistLeft, maxDistRight), maxLeft+maxRight);  
} 
```
###由前序遍历序列和中序遍历序列重建二叉树
二叉树前序遍历序列中，第一个元素总是树的根节点的值。中序遍历序列中，左子树的节点的值位于根节点的值的左边，右子树的节点的值位于根节点的值的右边。     
递归解法：    
1. 如果前序遍历为空或中序遍历为空或节点个数小于等于0，返回NULL。
2. 创建根节点。前序遍历的第一个数据就是根节点的数据，在中序遍历中找到根节点的位置，可分别得知左子树和右子树的前序和中序遍历序列，重建左右子树。
```
BinaryTreeNode * RebuildBinaryTree(int* pPreOrder, int* pInOrder, int nodeNum)  
{  
    if(pPreOrder == NULL || pInOrder == NULL || nodeNum <= 0)  
        return NULL;  
    BinaryTreeNode * pRoot = new BinaryTreeNode;  
    // 前序遍历的第一个数据就是根节点数据  
    pRoot->m_nValue = pPreOrder[0];  
    pRoot->m_pLeft = NULL;  
    pRoot->m_pRight = NULL;  
    // 查找根节点在中序遍历中的位置，中序遍历中，根节点左边为左子树，右边为右子树  
    int rootPositionInOrder = -1;  
    for(int i = 0; i < nodeNum; i++)  
        if(pInOrder[i] == pRoot->m_nValue)  
        {  
            rootPositionInOrder = i;  
            break;  
        }  
    if(rootPositionInOrder == -1)  
    {  
        throw std::exception("Invalid input.");  
    }  
    // 重建左子树  
    int nodeNumLeft = rootPositionInOrder;  
    int * pPreOrderLeft = pPreOrder + 1;  
    int * pInOrderLeft = pInOrder;  
    pRoot->m_pLeft = RebuildBinaryTree(pPreOrderLeft, pInOrderLeft, nodeNumLeft);  
    // 重建右子树  
    int nodeNumRight = nodeNum - nodeNumLeft - 1;  
    int * pPreOrderRight = pPreOrder + 1 + nodeNumLeft;  
    int * pInOrderRight = pInOrder + nodeNumLeft + 1;  
    pRoot->m_pRight = RebuildBinaryTree(pPreOrderRight, pInOrderRight, nodeNumRight);  
    return pRoot;  
}  
```
###判断二叉树是不是完全二叉树
若设二叉树的深度为h，除第 h 层外，其它各层 (1～h-1) 的结点数都达到最大个数，第 h 层所有的结点都连续集中在最左边，这就是完全二叉树。     
有如下算法，按层次（从上到下，从左到右）遍历二叉树，当遇到一个节点的左子树为空时，则该节点右子树必须为空，且后面遍历的节点左
右子树都必须为空，否则不是完全二叉树。
```
bool IsCompleteBinaryTree(BinaryTreeNode * pRoot)  
{  
    if(pRoot == NULL)  
        return false;  
    queue<BinaryTreeNode *> q;  
    q.push(pRoot);  
    bool mustHaveNoChild = false;  
    bool result = true;  
    while(!q.empty())  
    {  
        BinaryTreeNode * pNode = q.front();  
        q.pop();  
        if(mustHaveNoChild) // 已经出现了有空子树的节点了，后面出现的必须为叶节点（左右子树都为空）  
        {  
            if(pNode->m_pLeft != NULL || pNode->m_pRight != NULL)  
            {  
                result = false;  
                break;  
            }  
        }  
        else  
        {  
            if(pNode->m_pLeft != NULL && pNode->m_pRight != NULL)  
            {  
                q.push(pNode->m_pLeft);  
                q.push(pNode->m_pRight);  
            }  
            else if(pNode->m_pLeft != NULL && pNode->m_pRight == NULL)  
            {  
                mustHaveNoChild = true;  
                q.push(pNode->m_pLeft);  
            }  
            else if(pNode->m_pLeft == NULL && pNode->m_pRight != NULL)  
            {  
                result = false;  
                break;  
            }  
            else  
            {  
                mustHaveNoChild = true;  
            }  
        }  
    }  
    return result;  
}  

```









