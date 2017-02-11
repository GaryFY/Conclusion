数据结构与算法分析
====
###队列

	template <typename T>
	class LoopQueue
	{
	public:
		LoopQueue(int c = 10);
		~LoopQueue();
	public:
		bool isEmpty();        //队列的判空
		int size();            //队列的大小
		bool push(T t);        //入队列
		bool pop();            //出队列
		T front();            //队首元素
	 
	private:
		int capacity;
		int begin;
		int end;
		T*  queue;
	};