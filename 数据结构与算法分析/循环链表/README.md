循环链表
====

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