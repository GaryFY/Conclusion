栈与队列（操作受限的线性表）
====

* 栈：后进先出
* 队列：先进先出

####存储结构及基本操作
* 存储结构:top - base等于当前栈中的元素个数;非空栈的栈顶指针始终在栈顶元素的下一个位置上
```c
typedef struct
{
    ElemType *base; //栈底指针，始终指向栈底，如果为null说明栈不存在
    ElemType *top; //栈顶指针，当top == base时，为空栈；
    int stackSize; //当前已分配的存储空间，以元素为单位
}SqStack;
```
* 构造空栈
```c
void InitStack(SqStack *S)
{
    S->base = (SElemType *)malloc(STACK_INIT_SIZE * sizeof(SElemType));
    if(!S->base) exit(ERROR);
    S->top = S->base;  //栈顶指向栈底
    S->stacksize = STACK_INIT_SIZE; 
}
```
* 将栈置空
```c
void ClearStack(SqStack *S)
{
    S->top = S->base;  //栈顶指向栈底 
} 
```
* 判断是否为空栈
```c
Status StackEmpty(SqStack *S)
{
    return S->top == S->base?TRUE:FALSE;
} 
```
* 销毁栈
```c
void DestoryStack(SqStack S)
{
    free(S->base); //释放栈空间
    S->top = S->base = NULL; //将栈顶和栈底设置为NULL 
    S->stacksize = 0; //存储空间设置为0 
} 
```
* 获得栈中的元素个数
```c
int StackLength(SqStack S)
{
    return S.top - S.base;
} 
```
* 获得栈顶元素
```c
Status GetTop(SqStack S,SElemType *e)
{
    if(S->top > S->base)
    {
        e = S->top;
        return OK;
    }else{
        return ERROR;
    }
}
```
* 往栈中插入元素(压栈)
```c
void PushStack(SqStack *S,SElemType e) 
{
    //判断当前栈容量是否满了，满了需要增加空间 
    if(S->top - S->base == S->stacksize) 
    {
        S->base = (SElemType *)realloc(S >base,
             (S->stacksize + STACK_INCREMENT)*sizeof(SElemType));
        if(!S->base)exit(ERROR);
        S->top = S->base + S->stacksize; //修改栈顶指针指向新的栈顶
        S->stacksize += STACK_INCREMENT; //更新容量 
    }
    *(S ->top ++) = e; //栈顶指针加一，并将新插入元素赋值给栈顶空间
}
```
* 弹出栈中的元素
```c
Status PopStack(SqStack *S,SElemType e) 
 {
    if(S->top == S->base) return ERROR;  //栈为空
    e = *(--S->top);   //栈顶元素值付给e,栈顶指针下移 （先赋值后下移）
    return OK;      
 }
```
* 遍历栈中的元素
```c
void StackTraverse(SqStack S,void *visit(SElemType))
 {
    //从栈底开始到栈顶 
    while(S->top > S->base)
    visit(*(S->base ++));
    printf("\n");
 }
```
