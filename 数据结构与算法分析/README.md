数据结构与算法分析
====
###抽象类
其成员函数都被声明为“纯虚的”，即在函数方法声明的最后有“=0”的符号。   

###队列判断队列是空队列还是已满呢？     
* 栈空： 队首标志=队尾标志时，表示栈空，即红绿两个标志在图中重叠时为栈空。
* 栈满 : 队尾+1 = 队首时，表示栈空。有一个空位，我们不存储元素。

###链表   

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
1. 构造空表  

```
void InitList(LinkList L)
{
    L = (LinkList)malloc(sizeof(LNode));
    if(!L)exit(ERROR);
    L->next = NULL;
}
``` 
     
2. 将链表置为空表  

```
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
      
3. 判断是否为空表:**有头结点：L -> next = NULL;此时表为空表！ 
无头结点：L = NULL；此时为空表！**  

```
Status ListEmpty(LinkList L)
{
    //有头节点的情况，只需判断头结点的指针域是否为空即可
    if(L->next) return FALSE;
    else return TRUE;   
}
```   
     
4. 销毁单链表  

```
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
	 
5. 获得表长度  

```
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
	 
6. 获得表中第i个元素的值  

```
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
	 
7. 查找表中是否存在满足条件的元素  

```
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
	 
8. 获得某个结点的直接前驱 

```
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
	 
9. 获得某个结点的直接后继  

```
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
	 
10. 往表中第i个位置插入元素  

```
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
	 
11. 删除表中第i个元素  

```
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
	 
12. 遍历单链表中的所有元素  

```
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
