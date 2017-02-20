构造函数与析构函数总结
====
###继承问题
```cpp
#include <iostream>
#include <cmath>
using namespace std;

class A
{
public:
	A(){cout << "Construct A" << endl;}
	~A(){cout << "Destruct A" << endl;}
};

class C
{
public:
	C(){cout << "Construct C" << endl;}
	~C(){cout << "Destruct C" << endl;}
	
};

class B: public A, public C
{
public:
	B(){cout << "Construct B" << endl;}
	~B(){cout << "Destruct B" << endl;}
};

int main(int argc, char const *argv[])
{
	B b;
	return 0;
}
```
输出结果：  
```
	Construct A
	Construct C
	Construct B
	Destruct B
	Destruct C
	Destruct A
```

结论：
* 构造函数执行的顺序是，**先构造父类，再构造子类，其中父类的构造顺序是从左到右**
* 析构函数执行的顺序是与构造函数正好**相反**的，**先执行自身的析构函数，然后再依次从右到左执行父类的析构函数**

###列表初始化成员变量（组合关系）
```cpp
	#include <iostream>
	#include <cmath>
	using namespace std;

	class A
	{
	public:
		A(){cout << "Construct A" << endl;}
		~A(){cout << "Destruct A" << endl;}
	};

	class C
	{
	public:
		C(){cout << "Construct C" << endl;}
		~C(){cout << "Destruct C" << endl;}
		
	};

	class B
	{
	public:
		// Notice
		B(): a(A()), c(C()) {cout << "Construct B" << endl;}
		~B(){cout << "Destruct B" << endl;}
		C c;
		A a;
	};

	int main(int argc, char const *argv[])
	{
		B b;
		return 0;
	}
```
输出结果：
```
	Construct C
	Construct A
	Construct B
	Destruct B
	Destruct A
	Destruct C
```
结论：    
**列表初始化是先于构造函数调用的，而且列表初始化是与初始化顺序无关的，只与数据成员定义的顺序有关**    
故先构造C，再构造A
###继承与列表初始化
```cpp
	#include <iostream>
	#include <cmath>
	using namespace std;

	class A
	{
	public:
		A(){cout << "Construct A" << endl;}
		~A(){cout << "Destruct A" << endl;}
	};

	class C
	{
	public:
		C(){cout << "Construct C" << endl;}
		~C(){cout << "Destruct C" << endl;}
		
	};

	class B: public A, public C
	{
	public:
		//Notice: List initialize
		B(): a(A()), c(C()) {cout << "Construct B" << endl;}
		~B(){cout << "Destruct B" << endl;}
		C c;
		A a;
	};

	int main(int argc, char const *argv[])
	{
		B b;
		return 0;
	}
```
输出结果：   
```
	Construct A
	Construct C
	Construct C
	Construct A
	Construct B
	Destruct B
	Destruct A
	Destruct C
	Destruct C
	Destruct A
```
结论：   
类在构造的时候会先从左到右调用父类的构造函数，然后根据类中数据成员的定义依次构造**注意：是与列表初始化顺序无关**，
然后才会调用自身的构造函数，而析构函数则正好相反。
###虚拟继承、继承与列表初始化
```cpp
#include <iostream>
#include <cmath>
using namespace std;

class A
{
public:
	A(){cout << "Construct A" << endl;}
	~A(){cout << "Destruct A" << endl;}
};

class C
{
public:
	C(){cout << "Construct C" << endl;}
	~C(){cout << "Destruct C" << endl;}
	
};

//Notice: C is a virtual public
class B: public A, public virtual C
{
public:
	B(): a(A()), c(C()) {cout << "Construct B" << endl;}
	~B(){cout << "Destruct B" << endl;}
	C c;
	A a;
};

int main(int argc, char const *argv[])
{
	B b;
	return 0;
}
```
输出结果：
```cpp
	Construct C
	Construct A
	Construct C
	Construct A
	Construct B
	Destruct B
	Destruct A
	Destruct C
	Destruct A
	Destruct C
```
结论：
* 先执行虚拟继承的父类的构造函数
* 然后从左到右执行普通继承的父类的构造函数
* 然后按照定义的顺序执行数据成员的初始化
* 最后是自身的构造函数的调用。


另外：析构函数为vircual情况
```cpp
#include <iostream.h>

struct A
{
    virtual ~A() {cout<<"~A()\n";}
};

struct B: public A
{
    ~B() {cout<<"~B()\n";}
};

void main()
{
    A* p = new B;
    delete p;
}

```
输出结果：
```
~B();
~A();
```
若A的析构函数不是virtual，则输出：
```
~A();
```