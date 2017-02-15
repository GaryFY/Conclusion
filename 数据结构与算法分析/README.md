数据结构与算法分析
====
###抽象类
其成员函数都被声明为“纯虚的”，即在函数方法声明的最后有“=0”的符号。   

###队列判断队列是空队列还是已满呢？     
* 栈空： 队首标志=队尾标志时，表示栈空，即红绿两个标志在图中重叠时为栈空。
* 栈满 : 队尾+1 = 队首时，表示栈空。有一个空位，我们不存储元素。

链表
----   

####单链表
* 信息域：用来存放表中的一个元素   
* 指针域：指针域用来存放指向一个结点的指针    
     
**顺序表：**    
优点：     
* 随机存储，直接可以通过下标访问表中元素，比如L.elem+i-1直接指向表中的第i个元素 
* 适合大量访问数据的操作的程序     
缺点：    
* 内存碎片化：因为顺序表是连续存储的，每次都需要申请一大块的内存空间，这样小部分的内容就浪费了，从而形成了内存碎片     
* 大数据量的插入删除需要移动大量的元素，效率不高 
     
**单链表：**     
优点：    
* 表中的数据离散存储， 通过指针进行操作访问     
* 插入与删除元素无需移动大量元素，只需修改指针域指向的地址即可   
* 适合大量插入删除操作的程序    
缺点：    
* 失去了随机存取的优点，所以每次访问表中的某个数据都要重头到尾扫一遍 

#####头插法
新插入结点始终未当前的第一个结点      
#####尾插法 
新插入结点始终为当前的最后一个结点    

例：有趣的算法：查找单链表的中间结点    
用两个不同的指针，按照不同的移动顺序来移动，这里我们暂且把他们成为**快慢指针**！     
每次循环， **快指针向后移动两个结点： p = p -> next -> next； 
慢指针向后移动一个结点： q = q -> next** 
  
####12种基础基本操作代码实现  
* 构造空表  

```c
void InitList(LinkList L)
{
    L = (LinkList)malloc(sizeof(LNode));
    if(!L)exit(ERROR);
    L->next = NULL;
}
```
* 将链表置为空表  
```c
void ClearList(LinkList L)
{
    LinkList p = L->next;
    L->next = NULL;
    //接着就是释放头结点以外的结点了
    while(p)
    {
        p = L->next;
        free(L);  //释放首元结点
        L = p;    //移动到当前的首元结点 
    } 
} 
```     
* 判断是否为空表:**有头结点：L -> next = NULL;此时表为空表！ 
无头结点：L = NULL；此时为空表！**  
```c
Status ListEmpty(LinkList L)
{
    //有头节点的情况，只需判断头结点的指针域是否为空即可
    if(L->next) return FALSE;
    else return TRUE;   
}
```   
     
* 销毁单链表  

```c
void DestoryList(LinkList L)
{
    LinkList q;
    //删除头结点外的结点 
    while(L)
    {
        //指向首元结点，而不是头结点     
        q = L->next;
        free(L);
        L = q;      //删除后指向首元 
    }
} 
```   
	 
* 获得表长度  

```c
int ListLength(LinkList L)
{
    int i = 0;
    LinkList p = L->next;
    while(p)
    {
        i++;
        p = p->next;
    }
    return i;
} 
```   
	 
* 获得表中第i个元素的值  

```c
Status GetElem(LinkList L,int i,ElemType *e)
{
    int j = 1;
    //指向首元，然后依次后移，假如到了结尾或者j的值大于i
    //还没找个改元素说明i不合法
    LinkList p = L->next;
    while(p && j < i)
    {
        j++;
        p = p->next;
    } 
    if(!p || j> i) return ERROR;
    e = p->data;
    return OK;
}
```   
	 
* 查找表中是否存在满足条件的元素  

```c
int LocateElem(LinkList L,ElemType e,Status(*compare)(ElemType,ElemType))
{
    int i = 0;  
    LinkList p = L-> next;  
    while(p)  
    {  
        i++;  
        if(compare(p->data,e)) return i;  
        p = p-> next;  
    }  
    return 0;  
} 
```   
	 
* 获得某个结点的直接前驱 

```c
Status BeforeElem(LinkList L,ElemType choose,ElemType *before)
{
    LinkList q,p = L->next;
    while(p->next)
    {
        q = p->next;
        //判断p的后继是否为choose,是的话返回OK,否则继续后移 
        if(q->data == choose)
        {
            before = p->data;
            return OK; 
        }
        p = q;      
    }
    return ERROR; 
}
```   
	 
* 获得某个结点的直接后继  

```c
Status NextElem(LinkList L,ElemType choose,ElemType *behind)
{
    LinkList p = L->next;
    while(p->next)
    {
        if(p->data == choose)
        {
            behind = p->next->data;
            return OK;
        }
        p = p->next;
    }
    return ERROR;   
}
```   
	 
* 往表中第i个位置插入元素  

```c
Status ListInsert(LinkList L,int i,ElemType e)
{
    int j = 0;
    LinkList p,q =L;  //让q指向头结点
    while(p && j < i - 1)
    {
        j++;
        p = p->next;  //p指向下一个节点 
    }
    if(!p || j > i - 1) return ERROR;
    p = (LinkList)malloc(sizeof(LNode));
    //要先让插入的结点的指针域指向插入位置的后继结点  
    //再让插入节点的前驱的指针域指向插入结点  
    //！！！顺序不能乱  
    p->data = e;
    p->next = q->next; //插入节点的指针域指向第i个节点的后继节点
    q->next = p; //将第i个节点的指针域指向插入的新节点
    return OK;
} 
```   
	 
* 删除表中第i个元素  

```c
Status ListDelete(LinkList L,int i,ElemType *e)
{
    int j = 0;
    LinkList p,q = L;
    while(q->next && j < i-1)
    {
        j++;
        q = q->next;//将q指向要删除的第i个节点的前驱节点
    }
    if(!q || j >i -1) return ERROR;
    p = q->next;   //指向准备删除的结点
    q->next = p->next; //删除结点的前驱的指针域指向删除结点的后继   
    e = p->data; 
    free(p);    //释放要删除的结点  
    return OK;
}
```   
	 
* 遍历单链表中的所有元素  

```c
void ListTraverser(LinkList L,void(*visit)(ElemType))
{
    LinkList p = L->next;
    while(p)
    {
        visit(p->data);
        p = p->next;
    }
    printf("\n");
}
```   
####单向循环链表
将终端节点的指针域指向头结点即可，即**head->next = head**;设置**尾指针**；自己指自己   
#####插入和删除
* 注意：不同的是如果插入位置是尾结点的话，那么还需要让尾指针指向这个新插入的尾结点    
* 注意：如果删除的是尾结点的话，那么还要指向删除结点的前一个结点   

例子：约瑟夫问题 （循环链表）    
一堆人，围成一个圈，然后规定一个数N，然后依次报数，当报数到N，这个人自杀，其他人鼓掌！啪啪啪， 
接着又从1开始报数，报到N又自杀…以此类推，直到死剩最后一个人，那么游戏结束！  
```c 
#include <stdio.h>
#include <stdlib.h>
#include <malloc.h>

//定义循环链表的存储结构
typedef struct LNode
{
    int data;         //数据域
    struct LNode *next;   //指针域 
}LNode;  
typedef struct LNode *LinkList;

//定义循环链表的初始化方法
LinkList createList(int n)
{
    printf("\n初始化循环链表\n\n"); 
    LinkList head,s,p;
    int i = 1;
    head = (LinkList)malloc(sizeof(LNode));
    p = head;
    if(n != 0)
    {
        while(i <= n)
        {
            s = (LinkList)malloc(sizeof(LNode));
            s->data = i++;  //为链表初始化，第一个结点值为1，第二个为2这样以此类推
            p->next = s;
            p = s;
        }
         s->next = head->next;  
    }
    free(head);  
    return s->next;  
}



int main()
{
    int n,m,i;
    printf("输入参加的人数:\n");  
    scanf("%d",&n);  
    printf("输入每隔多少死一个人:\n");  
    scanf("%d",&m);
    LinkList p = createList(n);
    LinkList temp;
    m %= n;  //为了防止报数的人大于参与的人，求余可以理解为重头开始
    while(p != p->next)
    {
        for(i = 1;i < m - 1;i++)
        {
            p = p->next; //将p指向要删除的第m个节点的前驱节点
        }
        printf("第%d个人自杀了\t",p->next->data);
        //删除第m个结点
        temp = p->next;
        p->next = temp->next;
        free(temp);
        p = p->next; 
    } 
    printf("\n第%d个人活下来了！\n\n", p->data );    
    return 0;  
}
```  
####双向循环链表
双向循环链表的特点：**多了一耳光指向前驱的指针**   
判断空表：L ->next = L -> prior = L;  
```c
typedef struct LNode
{
    ElemType data;         //数据域
    struct LNode *prior;   //前驱指针 
    struct LNode *next;   //后继指针

}LNode;  
typedef struct LNode *LinkList;
```
* 构建空表
```c
Status InitList(LinkList L)
{
    L = (LinkList)malloc(sizeof(LNode));
    if(!L)exit(ERROR);
    else L->next = L->prior = L;
    return OK;
} 
```
* 将表置空
```c
void ClearList(LinkList L)
{
    LinkList p = L->next;   //指向第一个结点 
    while(p != L)
    {
        p = p->next;  //指向下一个结点 
        free(p->prior); //释放该结点的前驱结点 
    }
    L->next = L->prior = L; //自己指自己 
}
```
* 判断是否为空表
```c
Status ListEmpty(LinkList L)
{
    return L->next == L && L->prior == L?TRUE:FALSE;
}
```
* 销毁表
```c
void DestoryList(LinkList L)
{
    ClearList(L);
    free(L);
    L = NULL;
}
```
* 获得表长度
```c
int ListLength(LinkList L)
{
    int i = 0;
    LinkList p = L->next;
    while(p != L)
    {
        i++;
        p = p->next;
    }
    return i;
} 
```
* 获得表中第i个元素的值
```c
Status GetElem(LinkList L,int i,ElemType *e)
{
    int j = 1;
    LinkList p = L ->next;  //指向第一个结点
    while(p != L && j < i)   //指针后移 
    {
        j++;
        p = p ->next;
    } 
    if(p == L || j > i)return ERROR;  //找不到该元素
    e = p ->data;
    return OK;  
}
```
* 查找表中是否存在满足条件的元素
```c
int LocateElem(LinkList L,ElemType e,Status(*compare)(ElemType,ElemType)) 
{
    int i = 0;
    LinkList p = L ->next ->next;  //指向第一个结点
    while(p != L ->next)
    {
        i++;
        if(compare(p->data,e))return i;
        p = p ->next; 
    } 
    return 0;   //找不到，返回0 
}
```
* 获得某个节点的直接前驱
```c
Status BeforeElem(LinkList L,ElemType choose,ElemType *before)
{
    LinkList p = L->next->next;  //指向第二个结点
    while(p != L)   //未指向头结点
    {
        if(p->data == choose)
        {
            before = p->prior->data; 
            return OK;
        }
        p = p->next;
    } 
    return ERROR;
}
```
* 获得某个节点的直接后继
```c
Status NextElem(LinkList L,ElemType choose,ElemType *behind)
{
    LinkList p = L->next->next; //指向第二个结点
    while(p != L)
    {
        if(p->prior->data == choose)
        {
            behind = p->data;
            return OK;
        }
        p = p->next;
    } 
    return ERROR;
}

```
* 返回第i个元素的地址
```c
LinkList GetElemAdd(LinkList L,int i)
{
    int j;
    LinkList p = L;
    if(i < 0 || i > ListLength(L))return NULL; //判断i值位置是否合法
    for(j = 1;j < = i;j++)
    {
        p = p->next;
    } 
    return p;
} 
```
* **往第i个位置插入元素**
```c
Status ListInsert(LinkList L,int i,ElemType e)
{
    LinkList p,q;
    //判断i值是否合法
    if(i < 1 || i > ListLength(L) + 1)return ERROR;
    p =  GetElemAdd(L,i - 1);
    //NULL的话说明，第i个结点的前驱不存在，
    //这里假设头节点为第一个结点的前驱   
    if(!p) return ERROR; 
    q = (LinkList)malloc(sizeof(LNode));
    if(!q) return ERROR;
    q->data = e;  //给新结点赋值
    q->prior = p;  //新结点的前驱为第i - 1个结点
    q->next = p->next; //新结点的后记为第i个结点
    p->next->prior = q; //第i个结点前驱指向新结点 
    p->next = q;  //第i-1个结点的后继指向新结点 
    return OK; 
}
```
![CSDN](http://7xjqvu.com1.z0.glb.clouddn.com/15-12-26/50137676.jpg)

* **删除第i个位置的元素**
```c
Status ListDelete(LinkList L,int i,ElemType *e)
{
    LinkList p;
    if(i < 1)return ERROR; //判断删除位置是否合法
    p = GetElemAdd(L,i);
    if(!p)return ERROR;  //为NULL说明第i个元素不存在
    e = p ->data;
    p ->prior ->next = p ->next; //i-1个结点的后继指向第i+1个结点
    p ->next ->prior = p ->prior; //第i+1个结点的前驱指向第i - 1个结点
    free(p); //释放第i个结点
    return OK; 
}
```
![CSDN](http://7xjqvu.com1.z0.glb.clouddn.com/15-12-26/59728721.jpg)

示例：正序和逆序遍历表中元素    
```c
#include <stdio.h>
#include<stdlib.h>

#define OK 1
#define ERROR 0
#define TRUE 1
#define FALSE 0

typedef int ElemType;  
typedef int Status;
typedef struct LNode
{
    ElemType data;         //数据域
    struct LNode *prior;   //前驱指针 
    struct LNode *next;   //后继指针
}LNode;  
typedef struct LNode *LinkList;

//定义一个创建N个结点的方法
 LinkList ListCreate(int N)
 {
    LinkList p,q,head;
    int i,data;
    q = head;
    head = (LinkList)malloc(sizeof(LNode)); 
    head ->prior = head;
    head ->next = head;
    p = head;
    for(i = 0;i < N;i++)
    {
        printf("请输入第%d个结点的值：",i + 1);
        scanf("%d",&data);
        q = (LinkList)malloc(sizeof(LNode));
        q ->data = data;
        p ->next = q;
        q ->prior = p;
        q ->next = head; 
        head ->prior = q; 
        p = q; 
    }
    return head;
 } 

 //定义一个打印结点数据的方法
 void PrintNode(ElemType e)
 {
    printf("%d\t",e);
 } 

 //定义一个正序输出链表的方法
 void ListTraverse(LinkList L)
 {
    LinkList p = L->next;  //指向首元结点
    while(p!=L)
    {
        PrintNode(p->data);
        p = p ->next;
    } 
    printf("\n");
 } 

 //定义一个逆序输出链表的方法
 void ListTraverseBack(LinkList L)
 {
    LinkList p = L ->prior;  //指向最后一个结点 
    while(p!=L)
    {
        PrintNode(p->data);
        p = p ->prior;
    } 
    printf("\n");
 } 

 int main()
 {
    LinkList p;
    int N = 0;
    printf("请输入双向链表的结点个数：");
    scanf("%d", &N);
    p = ListCreate(N);
    printf("正序打印链表中的结点：\n");
    ListTraverse(p);
    printf("逆序打印链表中的结点：\n");
    ListTraverseBack(p); 
    return 0;
 }
```