数据结构与算法分析
====
###队列
判断队列是空队列还是已满呢？     

* 栈空： 队首标志=队尾标志时，表示栈空，即红绿两个标志在图中重叠时为栈空。
* 栈满 : 队尾+1 = 队首时，表示栈空。有一个空位，我们不存储元素。
```
	template <typename T>
	class LoopQueue
	{
	public:
		LoopQueue(int c = 10);
		~LoopQueue();
	public:
		bool isEmpty(); //队列的判空
		int size(); //队列的大小
		bool push(T t); //入队列
		bool pop(); //出队列
		T front(); //队首元素
	 
	private:
		int capacity; //数组容量
		int begin; //队首标志
		int end; //队尾标志
		T*  queue; //数组
	};
```