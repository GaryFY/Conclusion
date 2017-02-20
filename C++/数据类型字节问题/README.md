例：
```
struct A{
 long a1;
 short a2;
 int a3;
 int *a4;
};
```
在64位编译器下用sizeof(struct A)计算出的大小是多少？    
答案：24   

分析：  
1. 第一个，8字节  
2. 第二个，2字节，加起来是10，是2的倍数，不用对齐  
3. 第三个，4字节，加起来是14，不是4的倍数，对齐到16   
4. 第四个，8字节，加起来是24，是8的倍数，不用对齐  

总结：   

32位编译器：32位系统下指针占用4字节     
* char ：1个字节 
* **char*（即指针变量）: 4个字节（32位的寻址空间是2^32, 即32个bit，也就是4个字节。同理64位编译器）**
* short int : 2个字节
* int：  4个字节
* unsigned int : 4个字节
* float:  4个字节
* double:   8个字节
* **long:   4个字节**
* long long:  8个字节
* **unsigned long:  4个字节**

64位编译器：64位系统下指针占用8字节  
* char ：1个字节
* **char*(即指针变量): 8个字节**
* short int : 2个字节
* int：  4个字节
* unsigned int : 4个字节
* float:  4个字节
* double:   8个字节
* **long:   8个字节**
* long long:  8个字节
* **unsigned long:  8个字节**