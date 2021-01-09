## c++prime02

### 成员访问运算符

点运算符和箭头运算符都可以用于访问成员

```c
ptr->mem等价于(*ptr).mem
```

### 位运算符

```c
左移运算符<<,往右侧插入值为0的二进制位
右移运算符>>,如果是无符号类型，在左侧插入值为0的二进制位
位求反运算符(~):将运算对象逐位求反后得到一个新的值
```

位与（&）：两个都为1，才得1

位或（|）：两者之中有1就是1

异或（^）：两个都为1，相加为0，其余为1

### 逗号运算符

逗号运算符含有两个运算对象，按照从左向右的顺序依次求值。

首先对左侧的表达式求值，然后将求值结果丢弃，逗号运算符真正的结果是右侧表达式的值

### 函数基础

**自动对象**

我们把只存在于块执行期间的对象称为自动对象，当块的执行结束后，块中创建的自动对象的值就变成未定义的。形参就是一种自动对象

**局部静态对象**

如果需要将局部变量的生命周期贯穿函数调用及之后的时间，可以将局部变量定义成static类型从而获得这样的对象；

局部静态对象在程序的执行路径第一次经过对象定义语句时初始化，并且直到程序终止才被销毁，在此期间，即使对象所在的函数执行结束也不会对它有影响。

```c++
//局部静态对象
#include<iostream>
using namespace std;

int count()
{
	static int ctr = 0;  //调用结束后，这个值任然有效
	return ++ctr;
}

int func()
{
	int a = 0;
	return ++a;
}

int main()
{
	int i;
	for (i = 0; i != 10; ++i)
	{
		cout << count() << endl;  //这里会一值不销毁ctr的值，所以会输出1到10的所有数字
	}

	for (i = 0; i != 10; ++i)
	{
		cout << func() << endl;//这里因为不是静态变量，所以调用完一次以后就释放了，所以会一直输出1，10个1
	}

	system("pause");

	return 0;
}
```

#### 数组引用形参

```c++
void print(int (&arr)[10])
{
for(auto elem:arr)
{
	cout << elem << endl;
}
}
```

注意：&arr两端的括号不能省略

```c++
int *ptrs[10];  //ptrs是含有10个整型指针的数组
int &refs[10];  //错误，不存在引用的数组
int (*parray)[10];  //parray指向一个含有十个整数的数组
int (&arrRef)[10];  //arrRef引用一个含有十个整数的数组
/*
对于后两个，从内往外看，先看圆括号内，在看其他
*/
```

#### decltype

decltype与auto关键字一样，都是用于编译时类型推导，区别是：decltype的类型推导并不是像auto一样从变量声明的初始化表达式获得变量的类型

作用：获取表达式的类型

```c
decltype(exp) temp =3;  //temp的类型就是exp这个表达式的类型
```

decltype处理const与引用类型

decltype处理const和引用的方式与auto些许不同，如果decltype使用的表达式是一个变量，则decltype返回该变量的类型

```c++
const int i=0;
const int &j =i;
decltype(i) x = 0;  //x类型是const int,与i一致
decltype(j) y = x;  //y的类型是const int &,与j一致，y绑定x
decltype(j) z;  //报错，因为z是const int&类型，必须初始化
```

如果decltype使用的表达式不是一个变量，则decltype返回表达式结果对应的类型

```c++
int i=1,*p=&i,&q=i;
decltype(q+0) x;  //表达式q+0的结果是int类型，所以b的类型是未初始化的int类型
decltype(*p) y;  //报错，y是int& 类型，必须被初始化
/*如果表达式的内容是解引用，则decltype将得到引用类型。
  正如我们熟悉的那样，解引用指针可以得到指针所指的对象，
  而且还能给这个对象赋值。因此decltype(*p)的类型就是int &，而非int
*/
```

### decltype与auto的区别

`decltype`与`auto`的一个重要的区别是:`decltype`的结果类型与表达式形式密切相关。

如果变量名加上一对括号，得到的类型与不加括号时会有所不同，如果`decltype`使用的是一个**不加括号的变量，则得到的结果就是该变量的类型**；**如果给变量加上一层或多层括号，编译器会把它当成是一个表达式，变量是一种可以作为赋值语句左值的特殊表达式，所以这样的`decltype`就会得到引用类型。**

```c++
int i = 10;
decltype((i)) x;    //错误，x是int &类型，必须要初始化
decltype(i) y;      //正确，y是未初始化值的int类型

```

### decltype与typeid的区别

> `typeid`的作用与`decltype`相似，都可以得到一个变量或者表达式的类型，不同的是，`typeid`方法得到的类型不能用于定义变量，可以用来进行类型的比较。
>
> 

```c++
	int i = 10;
	decltype(i) x = 20;
	if (typeid(i) == typeid(x)) {
		cout << "ture" << endl;
	} else {
		cout << "false" << endl;
	}
	//使用typeid(x).name()还可以打印出这个类型
	cout << typeid(x).name() << endl;

```

#### 内联函数

将函数指定为内联函数，通常就是将它在每个调用点上“内联地”展开。一般来说内联机制用于优化规模较小，流程直接、频繁调用的函数

```c++
//一般在函数之前加上inline就可以定义为内联函数
inline const string &
    shorterString(const string &str1,const string &s2)
{
    return s1.size() <=s2.size()?s1:s2;
}
```

#### constexpr函数

是指能用于常量表达式的函数

要求：函数的返回值类型及所有形参都得是字面值类型，而且函数体中必须有且只有一条return语句

