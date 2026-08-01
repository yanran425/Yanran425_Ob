---
coding: UTF-8
title: C++语法
date: 2026-03-22T23:39:23
updated: 2026-08-01T21:02:58
tags: []
share: true
---

# C++语法

## 1.static/namespace

- cpp中匿名命名空间的作用类似于 C 语言的static关键字。无论是 C 的 static（全局作用域）还是 C++ 的匿名命名空间，本质都是通过 **“内部链接（Internal Linkage）”** 实现作用域隔离 —— 即符号仅在当前编译单元（通常对应一个 .c 或 .cpp 文件） 内可见，不会被其他编译单元访问到，从而避免跨文件的命名冲突。

### 1.1static

- 修饰全局变量：限制作用域为当前编译单元（.c 文件）
- 修饰局部变量：延长生命周期至程序运行期（全局生命周期）
-  修饰函数：限制函数只能在当前编译单元（.c 文件）内被调用
- 修饰类的成员变量（静态成员变量）（C++中才有）
- 修饰类的成员函数（静态成员函数）（C++中才有）

### 1.2namespace

- C++ 匿名命名空间仅负责 “作用域隔离”，与 “生命周期” 完全无关（变量的生命周期仍由 static/auto 等关键字控制），避免歧义。
- 匿名命名空间可作用于 “类、模板”，而 C 全局 static 不行

## 2.大小端字节序

- 大小端字节序（Endianness）是指多字节数据（如 16 位整数、32 位浮点数）在内存中存储时，字节的排列顺序规则—— 本质是解决 “多字节数据的‘高位字节’和‘低位字节’该放在内存的‘低地址’还是‘高地址’” 的问题。
- 高字节与低字节：对多字节数据，权重高的字节是 “高字节”，权重低的是 “低字节”。例：16 位整数 0x1234（十六进制），0x12 是高字节（代表 12×256），0x34 是低字节（代表 34×1）；32 位整数 0x12345678，0x12 是最高字节，0x78 是最低字节。
- 内存地址：内存按 “字节” 划分，每个字节有唯一地址，地址值从小到大递增（低地址 → 高地址）。

- 大端字节序：高字节存低地址，低字节存高地址（即 “数据的高位在前，低位在后”，符合人类读写数字的习惯）。
```cpp
示例：32 位整数 0x12345678 的大端存储
假设内存起始地址为 0x00，字节排列如下：

内存地址存储的字节说明（数据的字节类型）
0x12最高字节（权重最高）
0x34次高字节
0x56次低字节
0x78最低字节（权重最低）
```
- 小端字节序：低字节存低地址，高字节存高地址（即 “数据的低位在前，高位在后”，与人类读写习惯相反，但更符合硬件处理效率）。

```cpp
示例：32 位整数 0x12345678 的小端存储
同样起始地址 0x00，字节排列如下：

内存地址存储的字节说明（数据的字节类型）
0x78最低字节（权重最低）
0x56次低字节
0x34次高字节
0x12最高字节（权重最高）
```

| 对比维度  | 大端序（Big-Endian）      | 小端序（Little-Endian） |
| :---: | :------------------- | :----------------- |
| 存储规则  | 高字节存低地址，低字节存高地址      | 低字节存低地址，高字节存高地址    |
| 人类可读性 | 高（符合读写习惯）            | 低（需逆序解读）           |
| 典型应用  | 网络协议（TCP/IP）、PowerPC | x86/x86_64、ARM（默认） |
| 核心问题  | 跨平台交互需统一为大端（网络序）     | 需检测并转换为目标字节序       |

## 3.指针常量与常量指针

- const修饰指针有三种情况
1. const修饰指针   --- 常量指针
2. const修饰常量   --- 指针常量
3. const即修饰指针，又修饰常量

```cpp
int main() {
	int a = 10;
	int b = 10;
	//const修饰的是指针，指针指向可以改，指针指向的值不可以更改
	const int * p1 = &a; 
	p1 = &b; //正确
	//*p1 = 100;  报错
	//const修饰的是常量，指针指向不可以改，指针指向的值可以更改
	int * const p2 = &a;
	//p2 = &b; //错误
	*p2 = 100; //正确
    //const既修饰指针又修饰常量
	const int * const p3 = &a;
	//p3 = &b; //错误
	//*p3 = 100; //错误
	system("pause");
	return 0;
}
```

- 引用的本质是在内部实现一个指针常量.

```cpp
int a = 10;
//自动转换为 int* const ref = &a; 指针常量是指针指向不可改，也说明为什么引用不可更改
int& ref = a; 
ref = 20; //内部发现ref是引用，自动转换为: *ref = 20;
```

- 在函数形参列表中，可以加==const修饰形参==，防止形参改变实参
```cpp
//int& ref = 10;  引用本身需要一个合法的内存空间，因此这行错误
//加入const就可以了，编译器优化代码，int temp = 10; const int& ref = temp;
const int& ref = 10;

//常用作类的拷贝构造函数
Person(const Person& p) {
	cout << "拷贝构造函数!" << endl;
}
```

## 4.指针数组与数组指针

- 指针

```cpp
int a = 10;
//变量名是p
int* p = &a;//p是一个变量名，其值是int*类型

int array[5] = {1, 2, 3, 4, 5}; // 定义数组
int *intptr = array; // 定义指向数组元素的指针
cout << intptr << endl << intptr[2] << endl<< *intptr <<endl<< *(intptr+1)<< endl;
int (*arrayptr)[5] = &array; // 定义指向数组的指针
cout << arrayptr << endl << *arrayptr << endl<< **arrayptr << *(*arrayptr+1 )<< endl;
//输出
012FFC20
3
1
2
012FFC20
012FFC20
12
```
![](../assets/C++%E8%AF%AD%E6%B3%95.png)
- 指针数组：是一个数组，其每个元素是指针

```cpp
//变量名是p[3]
int* p[3]; //定义一个指针数组,该数组长度为3，其内存放着三个指针变量分别是p[0]、p[1]、p[2]
int a[3][4];
for (int i = 0; i < 3; i++)
	p[i] = a[i]//要分别赋值。

int a = 10;
cout << &a << endl;
//变量名是p[3][4]
int* p[3][4] = { &a };//直接定义二维数组，其每个元素存放a的地址
cout << p[0][0] << endl;
```

- 数组指针：是一个指针，其指向一个数组

```cpp
int a[3][4] = { {1,2,3,4},{5,6,7,8},{9,10,11,12} };
//变量名是*p
int (*p)[4]; //定义一个数组指针
p=a; //将二维数组赋给一个指针，本质上将二维数组的首地址赋给p，即a[0]或&a[0][0]
p++; //该语句执行过后，也就是p=p+1;p跨过行a[0][]指向了行a[1][]

int(* pa)[3][4] = &a;
//变量名是*pa
cout << *(*(*pa)+1)+1)<<endl;
```

## 5.指针函数与函数指针

- 指针函数：是一个函数，返回值是指针

```cpp
int  *fun(int x)
Data* f(int a,int b){
    Data * data = new Data;
    data->a = a;
    data->b = b;
    return data;
}
```

- 函数指针：是一个指针，指向函数的入口

```cpp
int test(int a){
    return a;
}
int main(int argc, const char * argv[]){
    int (*fp)(int a);
    fp = test;
    //或者
    int (*fp)(int a) = &test;
    cout<<fp(2)<<endl;
    return 0;
}
```

## 6.Lambda表达式

- Lambda表达式是一种在**被调用的位置**或**作为参数传递给函数的位置**定义**匿名函数对象（闭包）** 的简便方法。

```cpp
[capture list] (parameter list) mutable -> return type { function body }
```
- [capture list] 是捕获列表，用于指定 Lambda表达式可以访问的外部变量，以及是按值还是按引用的方式访问。它标识一个Lambda的开始。函数对象参数是传递给编译器自动生成的函数对象类的构造函数的。函数对象参数只能使用那些到定义Lambda为止时Lambda所在作用范围内可见的局部变量（包括Lambda所在类的this）。
	1. 空：没有使用任何函数对象参数。
	2. =：使用Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this），并且是**值传递方式**（相当于编译器自动为我们按值传递了所有局部变量）。
	3. &：使用Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this），并且是**引用传递方式**（相当于编译器自动为我们按引用传递了所有局部变量）。
	4. this：函数体内可以使用Lambda所在类中的成员变量。此时是**引用传递类的成员变量**。
	5. a：将a按值进行传递。按值进行传递时，函数体内不能修改传递进来的a的拷贝，因为默认情况下函数是const的。**要修改传递进来的a的拷贝**，可以添加mutable修饰符。
	6. &a：将a按引用进行传递。
	7. a, &b：将a按值进行传递，b按引用进行传递。
	8. =，&a, &b。除a和b按引用进行传递外，其他参数都按值进行传递。
	9. &, a, b：除a和b按值进行传递外，其他参数都按引用进行传递。

```cpp
int x = 10;
auto f = [x] (int y) -> int { return x + y; }; // 值捕获 x
x = 20; // 修改外部的 x
cout << f(5) << endl; // 输出 15，不受外部 x 的影响

int x = 10;
auto f = [&x] (int y) -> int { return x + y; }; // 引用捕获 x
x = 20; // 修改外部的 x
cout << f(5) << endl; // 输出 25，受外部 x 的影响

int x = 10;
int y = 20;
auto f = [=, &y] (int z) -> int { return x + y + z; }; // 隐式按值捕获 x，显式按引用捕获 y
x = 30; // 修改外部的 x
y = 40; // 修改外部的 y
cout << f(5) << endl; // 输出 55，不受外部 x 的影响，受外部 y 的影响

int x = 10;
auto f = [z = x + 5] (int y) -> int { return z + y; }; // 初始化捕获 z，相当于值捕获 x + 5
x = 20; // 修改外部的 x
cout << f(5) << endl; // 输出 20，不受外部 x 的影响
```

- parameter list 是参数列表，用于表示 Lambda表达式的参数，没有参数时，可以为空。也可以省略。有参数时和普通函数一样指定参数的类型和名称，例如通过按值（如：(a,b)）和按引用（如：(&a,&b)）两种方式进行传递，还可以在 c++14 中使用 auto 关键字来实现泛型参数。
- mutable是可修改标示符，按值传递函数对象参数时，加上mutable修饰符后，可以修改按值传递进来的拷贝（注意是能修改拷贝，而不是值本身）。不使用时可以省略。
- return type 是返回值类型，用于指定 Lambda表达式的返回值类型，可以省略，表示由编译器根据函数体推导，也可以使用 -> 符号显式指定，还可以在 c++14 中使用 auto 关键字来实现泛型返回值。
- function body 是函数体，用于表示 Lambda表达式的具体逻辑，可以是一条语句，也可以是多条语句，还可以在 c++14 中使用 constexpr 来实现编译期计算。

> [!NOTE] 虽然=捕获列表是值传递，但是可以以值的形式传地址，在Lambda函数内部不修改该地址而修改该地址存放的数据以实现修改值

## 7.字符大小写转换

- 字母大写转换

```cpp
#include <cctype> // 包含 toupper 和 tolower 函数
std::string str = "Hello World!";
// 转换为大写
for (char &c : str) {
	c = std::toupper(c);
}
std::cout << str << std::endl; // 输出: HELLO WORLD!
```

- 字母小写转换

```cpp
// 转换为小写
for (char &c : str) {
	c = std::tolower(c);
}
std::cout << str << std::endl; // 输出: hello world!
```

- 字符自动识别转换
```cpp
#include <cctype>
if (isupper(ch)) { // 如果输入的是大写字母  
        ch = tolower(ch); // 转换为小写字母并输出  
        cout << "转换后的字母为：" << ch << endl;  
} else if (islower(ch)) { // 如果输入的是小写字母  
	ch = toupper(ch); // 转换为大写字母并输出  
	cout << "转换后的字母为：" << ch << endl;  
} else {  
	cout << "输入的不是英文字母。" << endl;  
}  
```
- 字符串大小写转换
```cpp
#include <algorithm>
cin>>str;
///转小写
transform(str.begin(),str.end(),str.begin(),::tolower);
cout<<"转化为小写后为："<<str<<endl;
transform(str.begin(),str.end(),str.begin(),::toupper);
cout<<"转化为大写后为："<<str<<endl;
return 0;

```

## 8.字符-数字转换

- 数字转字符
```cpp
int num = 1; // 数字1
char ch = num + '0'; // 转换为字符'1'
cout << "转换后的字符是: " << ch << endl; // 输出: 转换后的字符是: 1

//以下方法只能用于整形/长整型转字符数组
int g = 45612;
char j[20];
itoa(g, j, 0);
ltoa(g, j, 0);
```

- 字符转数字
```cpp
char ch = '9';
int n = ch - '0';

//以下方法只能用于字符数组转int/float/long
char cstr[] = "1234.6";
int d = atoi(cstr);
float d = atof(cstr);
long d = atol(cstr);
```
- 数字转字符串
```cpp
string to_string(int val);
string to_string(long val);
string to_string(long long val);
string to_string(unsigned int val);
string to_string(unsigned long val);
string to_string(unsigned long long val);
string to_string(float val);
string to_string(double val);
string to_string(long double val);

string si = to_string(42); // si="42"
string sl = to_string(42L); // sl="42"
string su = to_string(42u); // su="42"
string sd = to_wstring(42.0); // sd="42.000000"
string sld = to_wstring(42.0L); // sld="42.000000"
```
- 字符串转数字
```cpp
int stoi(const string& str, size_t* idx = 0; int base = 10);
long stol(const string& str, size_t* idx = 0, int base = 10);
long long stoll(const string& str, size_t* idx = 0, int base = 10);

auto i1 = std::stoi("42");
auto i2 = std::stoi("101010", nullptr, 2); // i2 = 42
auto i3 = std::stoi("052", nullptr, 8); // i3 = 42
auto i4 = std::stoi("0x2A", nullptr, 16); // i4 = 42
auto f1 = std::stof("123.45");
auto d2 = std::stod("1.2345e+2");
auto ll3 = std::stod("-123456789");
```

- toascii（将整型数转换成合法的ASCII 码字符）
```cpp
#include<ctype.h>

int toascii(int c);

toascii()会将参数c转换成7位的unsigned char值，第八位则会被清除，此字符即会被转成ASCII码字符。返回值将转换成功的ASCI
```

## 9.string与const char*

> [!NOTE] \<string\>是C++标准库头文件，包含了拟容器class std::string的声明（不过class string事实上只是basic_string\<char\>的typedef），用于字符串操作。

- const char* 转换成string

在C++中存在着从const char\*到string的隐式类型转换，换句话说，如果一个函数的参数类型是string类，直接传入const char\*类型的参数是没问题的

```cpp
const char* cstr = "demo";
string str(cstr);
```
- string与const char\*的转换

在传入参数时，有时我们传入string在编译时是会报错的，所以我们就需要传入const char\*类型

我们可以使用成员函数c_str()，来返回string对应的char数组
```cpp
string str = "demo";
const char* cstr = str.c_str();
```
并且，在进行文件读写的时候，使用const char\* 比使用string更安全。因为，string是会自动分配内存的，其内部的存储方式是不可见的，而const char\*类型是整存整取的，或者也可以自己手动存入每一部分

## 10.CString与const char\*

> [!NOTE] \<cstring\>是C标准库头文件<string.h>的C++标准库版本，包含了C风格字符串（NUL即'\0'结尾字符串）相关的一些类型和函数的声明，例如strcmp、strchr、strstr等。

[2.1 多字节与宽字节](./MFC.md##2.1%E5%A4%9A%E5%AD%97%E8%8A%82%E4%B8%8E%E5%AE%BD%E5%AD%97%E8%8A%82)

## 11.字符串读取

- cin>>
```cpp

用法1：最基本，也是最常用的用法，输入一个数字：

#include <iostream>  
using namespace std;  
int main ()  
{  
	int a,b;  
	cin>>a>>b;  
	cout<<a+b<<endl;  
}
输入：2[回车]3[回车]  
输出：5
注意:>> 是会过滤掉不可见字符（如 空格 回车，TAB 等）

用法2：接受一个字符串，遇“空格”、“TAB”、“回车”都结束
int main ()  
{  
	char a[20];  
	cin>>a;  
	cout<<a<<endl;  
}

输入：jkljkljkl
输出：jkljkljkl

输入：jkljkl jkljkl       //遇空格结束  
输出：jkljkl
```

- cin.get()
```cpp
用法1： cin.get(字符变量名)可以用来接收字符
int main ()  
{  
	char ch;  
	ch=cin.get();               //或者cin.get(ch);  
	cout<<ch<<endl;  
}

输入：jljkljkl  
输出：j

用法2：cin.get(字符数组名,接收字符数目)用来接收一行字符串,可以接收空格
int main ()  
{  
	char a[20];  
	cin.get(a,20);  
	cout<<a<<endl;  
}

输入：jkl jkl jkl  
输出：jkl jkl jkl

输入：abcdeabcdeabcdeabcdeabcde （输入25个字符）  
输出：abcdeabcdeabcdeabcd              （接收19个字符+1个'\0'）
```

- cin.getline()  // 接受一个字符串，可以接收空格并输出
```cpp

int main ()  
{  
	char m[7];  
	cin.getline(m,5);  
	cout<<m<<endl;  
}

输入：jkljkljkl  
输出：jklj
接受5个字符到m中，其中最后一个为'\0'，所以只看到4个字符输出；

如果把5改成7：  
输入：jk jkajk  
输出：jk jkajk

//延伸：  
//cin.getline()实际上有三个参数，cin.getline(接受字符串的变量,接受个数,结束字符)  
//当第三个参数省略时，系统默认为'\0'  
//如果将例子中cin.getline()改为cin.getline(m,5,'a');当输入jk jkajk时输出jk jk

```

- getline()     // 接受一个字符串，可以接收空格并输出
```cpp

#include<string>  
int main ()  
{  
	string str;  
	getline(cin,str);  
	cout<<str<<endl;  
}

输入：jkljkljkl  
输出：jkljkljkl

输入：jkl jfksldfj jklsjfl  
输出：jkl jfksldfj jklsjfl

和cin.getline()类似，但是cin.getline()属于istream流，而getline()属于string流，是不一样的两个函数

```

## 12.explicit

- explicit关键字用于修饰类的构造函数，特别是那些只有一个参数的构造函数。它的目的是要求在对象初始化或赋值时必须显式地使用构造函数，从而避免了编译器自动进行类型转换所可能引发的错误和歧义。

- 例如，如果一个类有一个接受单个整数参数的构造函数，没有*explicit*关键字修饰，那么编译器允许使用单个整数值来隐式创建该类的对象。这可能导致代码的可读性降低，因为从代码表面上看不出实际发生了类型转换。
```cpp
class MyClass {
	public:
		MyClass(int value) : _value(value) {}
	private:
		int _value;
};
MyClass obj = 10; // 隐式转换，没有使用explicit关键字
```
- 在上述代码中，_MyClass obj = 10;_这行代码会隐式地调用_MyClass_的构造函数，将整数_10_转换为_MyClass_类型的对象。这种隐式转换可能不是程序员所期望的，尤其是在复杂的程序中，它可能导致难以追踪的错误。

- 使用explicit关键字：为了避免这种隐式转换，可以在构造函数前加上*explicit*关键字。这样，编译器就会阻止隐式转换，除非显式地调用构造函数。
```cpp
class MyClass {
	public:
		explicit MyClass(int value) : _value(value) {}
	private:
		int _value;
};
MyClass obj = 10; // 错误：不能隐式转换
MyClass obj(10); // 正确：显式调用构造函数
```
- 在这个例子中，_=*explicit*关键字确保了MyClass对象的创建必须明确地调用构造函数。这样可以提高代码的清晰度和安全性。

## 13.找最大值的索引

```cpp
auto maxit = max_element(nums.begin() + i, nums.end());
int maxindex = distance(nums.begin(), maxit);
```

## 14.单例模式

- 单例模式可以分为 懒汉式 和 饿汉式 ，两者之间的区别在于创建实例的时间不同。

- 懒汉式：系统运行中，实例并不存在，只有当需要使用该实例时，才会去创建并使用实例。这种方式要考虑线程安全。

- 饿汉式：系统一运行，就初始化创建实例，当需要时，直接调用即可。这种方式本身就线程安全，没有多线程的线程安全问题。

### 14.1单例类的特点

- 构造函数和析构函数为私有类型，目的是禁止外部构造和析构。
- 拷贝构造函数和赋值构造函数是私有类型，目的是禁止外部拷贝和赋值，确保实例的唯一性。
- 类中有一个获取实例的静态方法，可以全局访问。

### 14.2懒汉单例(静态局部变量)

```cpp
//.h
class Single
{
public:
    static Single& GetInstance();// 获取单实例对象
    void Print();// 打印实例地址

private:
    Single()= default;// 禁止外部构造
    ~Single()= default;// 禁止外部析构
    Single(const Single &single) = delete;// 禁止外部拷贝构造
    const Single &operator=(const Single &single) = delete;// 禁止外部赋值操作
};

//.cpp
Single& Single::GetInstance()
{
    /**
     * 局部静态特性的方式实现单实例。
     * 静态局部变量只在当前函数内有效，其他函数无法访问。
     * 静态局部变量只在第一次被调用的时候初始化，也存储在静态存储区，生命周期从第一次被初始化起至程序结束止。
     */
    static Single single;// C++11 保证静态局部变量初始化的线程安全
    return single;
}

void Single::Print()
{
    std::cout << "我的实例内存地址是:" << this << std::endl;
}
Single::Single()
{
    std::cout << "构造函数" << std::endl;
}
Single::~Single()
{
    std::cout << "析构函数" << std::endl;
}
```
> [!NOTE] 现代 C++（C++11 及更高版本）中，**静态局部变量的初始化是线程安全的**，编译器会自动处理同步问题，确保只有一个线程能初始化该变量。
- **静态变量的生命周期**：`instance` 的内存从程序启动到结束一直存在。
- **初始化的唯一性**：编译器确保 `instance` 仅在首次调用时初始化。
- **线程安全**（C++11+）：标准强制要求静态变量初始化的原子性，避免多线程冲突。

### 14.3饿汉单例(类唯一静态成员变量)

```cpp
class Single {
public:
    static Single& GetInstance();
private:
    Single() = default;
    ~Single() = default;
    Single(const Single& single) = delete;
    Single& operator=(const Single& single) = delete;
    // 静态成员变量（类加载时初始化）
    static Single instance;
};

//.cpp

Single Single::instance;// 全局初始化实例
Single& Single::GetInstance()
{
    return single;
}
void Single::Print()
{
    std::cout << "我的实例内存地址是:" << this << std::endl;
}

Single::Single()
{
    std::cout << "构造函数" << std::endl;
}
Single::~Single()
{
    std::cout << "析构函数" << std::endl;
}
```

> [!NOTE] **静态成员变量的初始化发生在程序启动阶段**（main 函数执行前）。具体时机由编译器和运行时环境决定，但**保证在任何代码访问该类之前完成初始化**。

- 由于初始化发生在**单线程环境**（main 函数启动前），因此**天然线程安全**，无需加锁。

## 15.静态成员

### 15.1静态成员变量

- 类的静态成员变量不能类内初始化，但是可以为静态成员提供const整数类型的类内初始值。不过要求静态成员必须是字面值常量类型的constexpr。

- C++ 要求静态成员变量必须在类外**显式定义**（除非是 `constexpr` 常量）。
- 类的声明只是类型定义，不分配实际内存，静态成员变量需要独立于类的实例存在。
```cpp
//静态成员变量的声明与定义
static Singleton instance;//仅在类内声明变量，未分配内存。
Singleton Singleton::instance;//在类外定义变量，此时才分配内存并调用构造函数。
```

### 15.2静态成员函数

- 类的静态成员函数可以类内/类外实现

[4.2.8 静态成员](./C++%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B.md##4.2.8%E9%9D%99%E6%80%81%E6%88%90%E5%91%98)

## 16.模板

### 16.1函数模板

- **定义**：定义一个通用函数，允许参数或返回值的类型在调用时指定。
- **语法**：使用 `template <typename T>` 声明，后跟函数定义。

```cpp
//交换的函数模板
template<typename T>
void mySwap(T &a, T&b)
{
	T temp = a;
	a = b;
	b = temp;
}

template<class T> // 也可以替换成typename
//利用选择排序，进行对数组从大到小的排序
void mySort(T arr[], int len)
{
	for (int i = 0; i < len; i++)
	{
		int max = i; //最大数的下标
		for (int j = i + 1; j < len; j++)
		{
			if (arr[max] < arr[j])
			{
				max = j;
			}
		}
		if (max != i) //如果最大数的下标不是i，交换两者
		{
			mySwap(arr[max], arr[i]);
		}
	}
}
template<typename T>
void printArray(T arr[], int len) {

	for (int i = 0; i < len; i++) {
		cout << arr[i] << " ";
	}
	cout << endl;
}
void test01()
{
	//测试char数组
	char charArr[] = "bdcfeagh";
	int num = sizeof(charArr) / sizeof(char);
	mySort(charArr, num);
	printArray(charArr, num);
}

void test02()
{
	//测试int数组
	int intArr[] = { 7, 5, 8, 1, 3, 9, 2, 4, 6 };
	int num = sizeof(intArr) / sizeof(int);
	mySort(intArr, num);
	printArray(intArr, num);
}

int main() {

	test01();
	test02();

	system("pause");

	return 0;
}
```

### 16.2类模板

- **定义**：定义一个通用类，允许成员变量或成员函数的类型在实例化时指定。
- **语法**：使用 `template <class T>` 声明，后跟类定义。

> [!NOTE] 头文件声明的作用，就是让编译器知道，这个函数的定义应该在其他文件中，就不会因为暂时找不到函数的定义而报错。至于找到对应的定义，就是链接器需要干的事情。

```cpp
//.h文件
#pragma once
#include <iostream>
using namespace std;

template <class T>
class MyClass {
public:
    MyClass();
    MyClass(const T& val);
    void func(T a, T b);
    T value;
};

//.cpp文件
#include"SingleTon.h"
template<class T>
MyClass<T>::MyClass(){}

template <class T>
MyClass<T>::MyClass(const T&val):value(val) {}

template <class T>
void MyClass<T>::func(T a, T b)
{
	T temp = a;
	a = b;
	b = temp;
}

//main.cpp文件
#include "SingleTon.h"
int main()
{
	MyClass<int> myclass;
	myclass.func(10, 20);
	return 0;
}
```
- SingleToncpp编译时，编译器**没有看到任何具体的实例化类型**（如 `SingleTon<int>`），因此不会生成 `func()` 函数的实际代码。
- 模板代码（如 `template <typename T> void MyClass<T>::func()`）本身不会被编译成目标代码，只是 “蓝图”。
- 链接阶段就会报错：`main.cpp` 中使用了 `MyClass<int>::func()`，但 `SingleTon.o` 中没有该函数的实现，导致链接器无法找到符号定义。
![](../assets/C++%E8%AF%AD%E6%B3%95-1.png)

- 所以写在同一个hpp文件中，就不存在上述问题了
```cpp
//myclass.hpp
#pragma once
#include <iostream>
using namespace std;

template <class T>
class MyClass {
public:
    MyClass();
    MyClass(const T& val);
    void func(T a, T b);
    T value;
};

template<class T>
MyClass<T>::MyClass() {}

template <class T>
MyClass<T>::MyClass(const T& val) :value(val) {}

template <class T>
void MyClass<T>::func(T a, T b)
{
    T temp = a;
    a = b;
    b = temp;
}

//main.cpp
#include "myclass.hpp"
int main()
{
	MyClass<int> myclass;
	myclass.func(10, 20);
	return 0;
}
```

### 16.3对比

- **函数模板**是为了实现与类型无关的通用函数，依赖调用时的类型推导；
- **类模板**是为了创建通用的类结构，需在实例化时显式指定类型参数。
- 函数模板不能部分特化：函数模板只能全特化（如 `template<> void swap<int>(int&, int&)`），但类模板可以偏特化（如 `template<typename T> class Vector<T*>`）。
	- **全特化（Full Specialization）**：为模板的**所有模板参数**指定具体类型，形成一个完全确定的版本。
	- **偏特化（Partial Specialization）**：为模板的**部分模板参数**指定具体类型，保留其他参数作为模板参数（即 “部分特化，部分泛化”）。
	- C++ 标准明确禁止函数模板的偏特化，原因是 **函数重载（Function Overloading）可以完美替代偏特化的需求**，无需额外引入偏特化机制。
	- 类模板没有 “重载” 机制，因此需要偏特化来针对部分类型定制逻辑。偏特化的类模板与通用模板是不同的类，但共享模板名称。

- 类模板实例化必须显式指定类型：函数调用可以自动推导类型，但类实例化必须显式指定（如 `Vector<int>`），除非使用 C++17 的类模板参数推导（如 `std::pair p(1, "hello");`）。

|**特性**|**函数模板**|**类模板**|
|:---:|---|---|
|**实例化触发方式**|函数调用时自动推导或显式指定|必须显式指定模板参数（如 `Vector<int>`）|
|**类型推导**|支持自动类型推导（如 `max(1, 2)`）|不支持自动推导，需显式指定类型|
|**特化（Specialization）**|支持全特化（如 `template<> int max<int>(int, int)`）|支持全特化和偏特化（如 `template<typename T> class Vector<T*>`）|
|**默认模板参数**|C++11 起支持（如 `template<typename T = int>`）|支持，且更常见（如 `template<typename T = int> class Stack`）|
|**用途**|通用算法（如 `std::sort`、`std::swap`）|容器类（如 `std::vector`、`std::map`）、工具类|

## 17.STL汇总

### string

- 构造
	- `string();` //创建一个空的字符串 例如: string str;
	- `string(const char* s);` //使用字符串s初始化
	- `string(const string& str);` //使用一个string对象初始化另一个string对象
	- `string(int n, char c);` //使用n个字符c初始化
- 赋值
	- `string& operator=(const char* s);` //char*类型字符串 赋值给当前的字符串
	- `string& operator=(const string &s);` //把字符串s赋给当前的字符串
	- `string& operator=(char c);` //字符赋值给当前的字符串
	- `string& assign(const char *s);` //把字符串s赋给当前的字符串
	- `string& assign(const char *s, int n);` //把字符串s的前n个字符赋给当前的字符串
	- `string& assign(const string &s);` //把字符串s赋给当前字符串
	- `string& assign(int n, char c);` //用n个字符c赋给当前字符串
- 拼接
	- `string& operator+=(const char* str);` //重载+=操作符
	- `string& operator+=(const char c);` //重载+=操作符
	- `string& operator+=(const string& str);` //重载+=操作符
	- `string& append(const char *s);` //把字符串s连接到当前字符串结尾
	- `string& append(const char *s, int n);` //把字符串s的前n个字符连接到当前字符串结尾
	- `string& append(const string &s);` //同operator+=(const string& str)
	- `string& append(const string &s, int pos, int n);`//字符串s中从pos开始的n个字符连接到字符串结尾
- 查找替换
	- `int find(const string& str, int pos = 0) const;` //查找str第一次出现位置,从pos开始查找
	- `int find(const char* s, int pos = 0) const;` //查找s第一次出现位置,从pos开始查找
	- `int find(const char* s, int pos, int n) const;` //从pos位置查找s的前n个字符第一次位置
	- `int find(const char c, int pos = 0) const;` //查找字符c第一次出现位置
	- `int rfind(const string& str, int pos = npos) const;` //查找str最后一次位置,从pos开始查找
	- `int rfind(const char* s, int pos = npos) const;` //查找s最后一次出现位置,从pos开始查找
	- `int rfind(const char* s, int pos, int n) const;` //从pos查找s的前n个字符最后一次位置
	- `int rfind(const char c, int pos = 0) const;` //查找字符c最后一次出现位置
	- `string& replace(int pos, int n, const string& str);` //替换从pos开始n个字符为字符串str
	- `string& replace(int pos, int n,const char* s);` //替换从pos开始的n个字符为字符串s
- 字符串比较
	- `int compare(const string &s) const;` //与字符串s比较
	- `int compare(const char *s) const;` //与字符串s比较
- 字符存取
	- `char& operator[](int n);` //通过[]方式取字符
	- `char& at(int n);` //通过at方法获取字符
- 插入删除
	- `string& insert(int pos, const char* s);` //插入字符串
	- `string& insert(int pos, const string& str);` //插入字符串
	- `string& insert(int pos, int n, char c);` //在指定位置插入n个字符c
	- `string& erase(int pos, int n = npos);` //删除从Pos开始的n个字符
- 提取字串
	- `string substr(int pos = 0, int n = npos) const;` //返回由pos开始的n个字符组成的字符串

### vector

- 构造
	- `vector<T> v;` //采用模板实现类实现，默认构造函数
	- `vector(v.begin(), v.end());` //将v[begin(), end())区间中的元素拷贝给本身。
	- `vector(n, elem);` //构造函数将n个elem拷贝给本身。
	- `vector(const vector &vec);` //拷贝构造函数。
- 赋值
	- `vector& operator=(const vector &vec);`//重载等号操作符
	- `assign(beg, end);` //将\[beg, end)区间中的数据拷贝赋值给本身。
	- `assign(n, elem);` //将n个elem拷贝赋值给本身。
- 容量和大小
	- `empty();  `//判断容器是否为空
	- `capacity();`//容器的容量
	- `size();`//返回容器中元素的个数
	- `resize(int num);`//重新指定容器的长度为num，若容器变长，则以默认值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。这里改变的是size，不是capacity
	- `resize(int num, elem);`  //重新指定容器的长度为num，若容器变长，则以elem值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除
- 插入和删除
	- `push_back(ele);`                                         //尾部插入元素ele
	- `pop_back();`                                                //删除最后一个元素
	- `insert(const_iterator pos, ele);`        //迭代器指向位置pos插入元素ele
	- `insert(const_iterator pos, int count,ele);`//迭代器指向位置pos插入count个元素ele
	- `erase(const_iterator pos);`                     //删除迭代器指向的元素
	- `erase(const_iterator start, const_iterator end);`//删除迭代器从start到end之间的元素
	- `clear();`                                                        //删除容器中所有元素
- 数据存取
	- ` at(int idx);`     //返回索引idx所指的数据
	- ` operator[];`       //返回索引idx所指的数据
	- ` front();`            //返回容器中第一个数据元素
	- `back();`              //返回容器中最后一个数据元素
- 互换容器
	- `swap(vec);`  // 将vec与本身的元素互换，swap可以使两个容器互换，可以达到实用的收缩内存效果
- 预留空间
	- `reserve(int len);`//容器预留len个元素长度，预留位置不初始化，元素不可访问。减少vector在动态扩展容量时的扩展次数

### deque

- 构造
	- `deque<T>` deqT;                      //默认构造形式
	- `deque(beg, end);`                  //构造函数将[beg, end)区间中的元素拷贝给本身。
	- `deque(n, elem);`                    //构造函数将n个elem拷贝给本身。
	- `deque(const deque &deq);`   //拷贝构造函数
- 赋值
	- ` deque& operator=(const deque &deq);`//重载等号操作符
	- `assign(beg, end);`//将[beg, end)区间中的数据拷贝赋值给本身。
	- `assign(n, elem);` //将n个elem拷贝赋值给本身。
- 大小操作
	- `deque.empty();` //判断容器是否为空
	- `deque.size();`  //返回容器中元素的个数
	- `deque.resize(num);` //重新指定容器的长度为num,若容器变长，则以默认值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。
	- `deque.resize(num, elem);` //重新指定容器的长度为num,若容器变长，则以elem值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。
- 插入和删除
	两端插入操作：
	- `push_back(elem);`          //在容器尾部添加一个数据
	- `push_front(elem);`        //在容器头部插入一个数据
	- `pop_back();`                   //删除容器最后一个数据
	- `pop_front();`                 //删除容器第一个数据
	指定位置操作：
	- `insert(pos,elem);`         //在pos位置插入一个elem元素的拷贝，返回新数据的位置。
	- `insert(pos,n,elem);`     //在pos位置插入n个elem数据，无返回值。
	- `insert(pos,beg,end);`    //在pos位置插入[beg,end)区间的数据，无返回值。
	- `clear();`                           //清空容器的所有数据
	- `erase(beg,end);`             //删除[beg,end)区间的数据，返回下一个数据的位置。
	- `erase(pos);`                    //删除pos位置的数据，返回下一个数据的位置。
- 数据存取
	- ` at(int idx);  `     //返回索引idx所指的数据
	- ` operator[];  `      //返回索引idx所指的数据
	- ` front();  `            //返回容器中第一个数据元素
	- `back();`              //返回容器中最后一个数据元素

### stack

- 构造
	- `stack<T> stk;` //stack采用模板类实现， stack对象的默认构造形式
	- `stack(const stack &stk);` //拷贝构造函数
- 赋值
	- `stack& operator=(const stack &stk);` //重载等号操作符
- 数据存取：
	- `push(elem);`      //向栈顶添加元素
	- `pop();`                //从栈顶移除第一个元素
	- ` top();  `                //返回栈顶元素
- 大小操作：
	- `empty();`            //判断堆栈是否为空
	- ` size();  `              //返回栈的大小

### queue

- 构造
	- `queue<T> que;`//queue采用模板类实现，queue对象的默认构造形式
	- `queue(const queue &que);`//拷贝构造函数
- 赋值
	- `queue& operator=(const queue &que);`//重载等号操作符
- 数据存取
	- `push(elem);`                             //往队尾添加元素
	- `pop();`                                      //从队头移除第一个元素
	- `back();`                                    //返回最后一个元素
	- `front();  `                                  //返回第一个元素
- 大小操作：
	- `empty();`            //判断堆栈是否为空
	- `size();  `              //返回栈的大小

### list

- 构造
	- `list<T> lst;` //list采用采用模板类实现,对象的默认构造形式：
	- `list(beg,end);`  //构造函数将\[beg, end)区间中的元素拷贝给本身。
	- `list(n,elem);`//构造函数将n个elem拷贝给本身。
	- `list(const list &lst);`//拷贝构造函数。
- 赋值
	- `assign(beg, end);`            //将[beg, end)区间中的数据拷贝赋值给本身。
	- `assign(n, elem);`              //将n个elem拷贝赋值给本身。
	- `list& operator=(const list &lst);`         //重载等号操作符
- 交换
	- `swap(lst);`//将lst与本身的元素互换。L1.swap(L2);
- 大小操作
	- `size();`//返回容器中元素的个数
	- `empty();`//判断容器是否为空
	- `resize(num);`//重新指定容器的长度为num，若容器变长，则以默认值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。
	- `resize(num, elem);`//重新指定容器的长度为num，若容器变长，则以elem值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。
- 插入和删除
	- `push_back(elem);`//在容器尾部加入一个元素
	- `pop_back();`//删除容器中最后一个元素
	- `push_front(elem);`//在容器开头插入一个元素
	- `pop_front();`//从容器开头移除第一个元素
	- `insert(pos,elem);`//在pos位置插elem元素的拷贝，返回新数据的位置。
	- `insert(pos,n,elem);`//在pos位置插入n个elem数据，无返回值。
	- `insert(pos,beg,end);`//在pos位置插入\[beg,end)区间的数据，无返回值。
	- `clear();`//移除容器的所有数据
	- `erase(beg,end);`//删除\[beg,end)区间的数据，返回下一个数据的位置。
	- `erase(pos);`//删除pos位置的数据，返回下一个数据的位置。
	- `remove(elem);`//删除容器中所有与elem值匹配的元素。
- 数据存取
	- `front();`        //返回第一个元素。
	- `back();`         //返回最后一个元素。
- 反转和排序
	- `reverse();`   //反转链表 L.reverse();
	- `sort();`        //链表排序 L.sort(myCompare); //指定规则，从大到小

### set/multiset

- 构造
	- `set<T> st;`                        //默认构造函数：
	- `set(const set &st);`       //拷贝构造函数
- 赋值
	- `set& operator=(const set &st);`    //重载等号操作符
- 大小操作
	- `size();`          //返回容器中元素的数目
	- `empty();`        //判断容器是否为空
- 交换
	- `swap(st);`      //交换两个集合容器 s1.swap(s2);
- 插入和删除
	- `insert(elem);`           //在容器中插入元素。
	- `clear();`                    //清除所有元素
	- `erase(pos);`              //删除pos迭代器所指的元素，返回下一个元素的迭代器。
	- `erase(beg, end);`    //删除区间\[beg,end)的所有元素 ，返回下一个元素的迭代器。
	- `erase(elem);`            //删除容器中值为elem的元素。
- 查找和统计
	- `find(key);`//查找key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();
	- `count(key);`//统计key的元素个数

### pair

- 构造
	- `pair<type, type> p ( value1, value2 );`
	- `pair<type, type> p = make_pair( value1, value2 );`

### map/multimap

构造
	- `map<T1, T2> mp;`                     //map默认构造函数:
	- `map(const map &mp);`             //拷贝构造函数
赋值
	- `map& operator=(const map &mp);`    //重载等号操作符
- 大小操作
	- `size();`          //返回容器中元素的数目
	- `empty();`        //判断容器是否为空
- 交换
	- `swap(st);`      //交换两个集合容器
- 插入和删除
	- `insert(elem);`           //在容器中插入元素。
	- `clear();`                    //清除所有元素
	- `erase(pos);`              //删除pos迭代器所指的元素，返回下一个元素的迭代器。
	- `erase(beg, end);`    //删除区间\[beg,end)的所有元素 ，返回下一个元素的迭代器。
	- `erase(key);`            //删除容器中值为key的元素。
- 查找和统计
	- `find(key);`//查找key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();
	- `count(key);`//统计key的元素个数

### 内建函数对象

- 算术仿函数
	- `template<class T> T plus<T>`                //加法仿函数
	- `template<class T> T minus<T>`              //减法仿函数
	- `template<class T> T multiplies<T>`    //乘法仿函数
	- `template<class T> T divides<T>`         //除法仿函数
	- `template<class T> T modulus<T>`         //取模仿函数
	- `template<class T> T negate<T>`           //取反仿函数
- 关系仿函数
	- `template<class T> bool equal_to<T>`                    //等于
	- `template<class T> bool not_equal_to<T>`            //不等于
	- `template<class T> bool greater<T>`                      //大于
	- `template<class T> bool greater_equal<T>`          //大于等于
	- `template<class T> bool less<T>`                           //小于
	- `template<class T> bool less_equal<T>`               //小于等于
- 逻辑仿函数
	- `template<class T> bool logical_and<T>`              //逻辑与
	- `template<class T> bool logical_or<T>`                //逻辑或
	- `template<class T> bool logical_not<T>`              //逻辑非

### 遍历

- ` for_each(iterator beg, iterator end, _func);   `
	// 遍历算法 遍历容器元素
	// beg 开始迭代器
	// end 结束迭代器
	// \_func 函数或者函数对象
```cpp
void MyPrint(int val)
{
	cout << val << endl;
}
for_each(v.begin(), v.end(), MyPrint);
```

### 搬运

- `transform(iterator beg1, iterator end1, iterator beg2, _func);`
	//beg1 源容器开始迭代器
	//end1 源容器结束迭代器
	//beg2 目标容器开始迭代器
	//\_func 函数或者函数对象

### 查找统计

- `find(iterator beg, iterator end, value);`//按值查找元素，找到返回指定位置迭代器，找不到返回结束迭代器位置
	// beg 开始迭代器 end 结束迭代器value 查找的元素
	利用find可以在容器中找指定的元素，返回值是**迭代器**
- `find_if(iterator beg, iterator end, _Pred);`
	// 按值查找元素，找到返回指定位置迭代器，找不到返回结束迭代器位置
	// beg 开始迭代器
	// end 结束迭代器
	// \_Pred 函数或者谓词（返回bool类型的仿函数）
	find_if按条件查找使查找更加灵活，提供的仿函数可以改变不同的策略
- ` adjacent_find(iterator beg, iterator end);`
	// 查找相邻重复元素,返回相邻元素的第一个位置的迭代器
	// beg 开始迭代器
	// end 结束迭代器
	面试题中如果出现查找相邻重复元素，记得用STL中的adjacent_find算法
- ` bool binary_search(iterator beg, iterator end, value);`
	// 查找指定的元素，查到 返回true  否则false
	// 注意: 在**无序序列中不可用**
	// beg 开始迭代器
	// end 结束迭代器
	// value 查找的元素
	二分查找法查找效率很高，值得注意的是查找的容器中元素必须的有序序列
- `count(iterator beg, iterator end, value);`
	// 统计元素出现次数
	// beg 开始迭代器
	// end 结束迭代器
	// value 统计的元素
	统计自定义数据类型时候，需要配合重载 `operator==`
- `count_if(iterator beg, iterator end, _Pred);`
	// 按条件统计元素出现次数
	// beg 开始迭代器
	// end 结束迭代器
	// \_Pred 谓词
	按值统计用count，按条件统计用count_if

### 排序

- ` sort(iterator beg, iterator end, _Pred);`
	// 按值查找元素，找到返回指定位置迭代器，找不到返回结束迭代器位置
	//  beg    开始迭代器
	//  end    结束迭代器
	// \_Pred  谓词
- ` random_shuffle(iterator beg, iterator end);`
	// 指定范围内的元素随机调整次序
	// beg 开始迭代器
	// end 结束迭代器
	random_shuffle洗牌算法比较实用，使用时记得加随机数种子
- `merge(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);`
	// 容器元素合并，并存储到另一容器中
	// 注意: 两个容器必须是**有序的**
	// beg1   容器1开始迭代器
	// end1   容器1结束迭代器
	// beg2   容器2开始迭代器
	// end2   容器2结束迭代器
	// dest    目标容器开始迭代器
	merge合并的两个容器必须的有序序列
- ` reverse(iterator beg, iterator end);   `
	// 反转指定范围的元素
	// beg 开始迭代器
	// end 结束迭代器

### 拷贝

- ` copy(iterator beg, iterator end, iterator dest);`
	// beg  开始迭代器
	// end  结束迭代器
	// dest 目标起始迭代器
	利用copy算法在拷贝时，目标容器记得提前开辟空间

### 替换

- ` replace(iterator beg, iterator end, oldvalue, newvalue);`
	// 将区间内旧元素 替换成 新元素
	// beg 开始迭代器
	// end 结束迭代器
	// oldvalue 旧元素
	// newvalue 新元素
- ` replace_if(iterator beg, iterator end, _pred, newvalue);`
	// 按条件替换元素，满足条件的替换成指定元素
	// beg 开始迭代器
	// end 结束迭代器
	// \_pred 谓词
	// newvalue 替换的新元素

### 交换

- `swap(container c1, container c2);`
	// 互换两个容器的元素
	// c1容器1
	// c2容器2
	swap交换容器时，注意交换的容器要同种类型

### 算术

- `accumulate(iterator beg, iterator end, value);`
	// 计算容器元素累计总和
	// beg 开始迭代器
	// end 结束迭代器
	// value 起始值

### 填充

- `fill(iterator beg, iterator end, value);`
	// 向容器中填充元素
	// beg 开始迭代器
	// end 结束迭代器
	// value 填充的值

### 集合

- `set_intersection(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);`
	// 求两个集合的交集
	// **注意:两个集合必须是有序序列**
	// beg1 容器1开始迭代器
	// end1 容器1结束迭代器
	// beg2 容器2开始迭代器
	// end2 容器2结束迭代器
	// dest 目标容器开始迭代器
	求交集的两个集合必须的有序序列
	目标容器开辟空间需要从**两个容器中取小值**
	set_intersection返回值即是交集中最后一个元素的位置
- `set_union(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);`
	// 求两个集合的并集
	// **注意:两个集合必须是有序序列**
	// beg1 容器1开始迭代器
	// end1 容器1结束迭代器
	// beg2 容器2开始迭代器
	// end2 容器2结束迭代器
	// dest 目标容器开始迭代器
	求并集的两个集合必须的有序序列
	目标容器开辟空间需要**两个容器相加**
	set_union返回值既是并集中最后一个元素的位置
- `set_difference(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);`
	// 求两个集合的差集
	// **注意:两个集合必须是有序序列**
	// beg1 容器1开始迭代器
	// end1 容器1结束迭代器
	// beg2 容器2开始迭代器
	// end2 容器2结束迭代器
	// dest 目标容器开始迭代器
	求差集的两个集合必须的有序序列
	目标容器开辟空间需要从**两个容器取较大值**
	set_difference返回值既是差集中最后一个元素的位置

## 18.C++的Map

### 18.1Tree与Map

- 树结构的核心是 “层级嵌套的节点”，每个节点通常存储一个值
- Map 的核心是 “键值对（KV）映射”，通过键快速查找值。
- 普通的树结构（如二叉树）只能按 “节点值” 排序，普通的 Map（如哈希表）虽然查询快，但键是无序的。

### 18.2Tree与Map结合

- “树节点” 不再只存一个值，而是 存储一个键值对（Key + Value）；
- 树的排序规则基于 “键（Key）”，而非值（Value）—— 左子树的所有节点的键 < 根节点的键，右子树的所有节点的键 > 根节点的键（二叉搜索树的特性）。
- 由于树的有序性，Map 可以直接支持 “按键排序” 的操作（如获取键的升序 / 降序集合、查找大于 / 小于某个键的所有键值对）。
- 平衡树保证效率：用红黑树等平衡二叉树避免了普通二叉树可能退化为链表的问题，插入、查询、删除的时间复杂度稳定在 O (log n)。

```cpp
//节点结构
class Node {
    Key key;    // 键（用于排序和查找）
    Value value; // 值（实际要存储的数据）
    Node left;  // 左子节点（键 < 当前节点的键）
    Node right; // 右子节点（键 > 当前节点的键）
    // 其他辅助字段（如红黑树的颜色标记）
}
```

![](../assets/C++%E8%AF%AD%E6%B3%95-2.png)

### 18.3Map

- STL中的Map：红黑树
- map是一个有序的容器，它按照键的升序进行排序并存储元素。每个键唯一且不可重复，同时与一个值关联。

- map的底层实现采用红黑树（Red-Black Tree），这是一种自平衡二叉搜索树。红黑树保持了良好的平衡性能，确保了在任何情况下对map进行插入、删除和查找操作的时间复杂度为O(logN)，其中N是元素的数量。

- 由于红黑树的平衡特性，map适用于需要频繁查找键值对的场景。map适合在元素有序存储和访问的情况下使用，特别是当需要根据键值进行快速查找时。例如，可以使用map来实现字典、索引表、排行榜等数据结构。

### 18.4unordered_map

- STL的unordered_map容器是一个哈希表（Hash Table）实现的关联容器，它提供了高效的插入、删除和查找操作。

- 哈希映射：unordered_map内部使用哈希函数将键映射到存储桶（bucket）中，以实现快速的查找操作。由于哈希函数的作用，元素在容器中的存储位置是根据键的哈希值决定的，而不是按照键的顺序。

- 快速的插入和查找：unordered_map具有接近常数时间（平均情况下）的插入、删除和查找操作。通过哈希映射和O(1)的平均时间复杂度，可以实现高效的元素访问。

- 无序性：unordered_map中的元素没有固定的顺序，与插入的顺序无关。这种无序性使得unordered_map适用于不需要保持元素顺序的需求，例如字典、索引等。

- 唯一键：每个键在unordered_map中是唯一的，即同一个键只能插入一个值。

- 冲突解决：当多个元素被映射到同一个存储桶时，会发生哈希冲突。unordered_map使用链地址法（chaining）来处理冲突，即在同一个存储桶中通过链表或者其他数据结构将冲突的元素链接起来。

### 18.5Set和unordered_set

- set 是一个关联型容器，和 map 一样，它的底层结构是红黑树，但和 map 不一样的是，set 是直接保存 value 的，或者说，set 中的 value 就是 key。unordered_set同理类比unordered_map

### 18.6如何选择

- 选择map还是unordered_map取决于对有序性、插入和删除操作频率以及内存占用的需求。如果需要元素有序并且较少的插入/删除操作，可以选择map；如果对有序性不敏感并且需要高效的插入/删除操作，可以选择unordered_map。

1. 查找操作的需求：如果你更关注元素的有序性，并且需要根据键进行快速的查找操作，那么应该选择map。map中的元素按键的顺序进行排序，因此可以使用二分查找等算法来高效地查找元素。

2. 插入和删除操作的频率：如果你需要频繁地插入和删除元素，并且对于查找操作的顺序不敏感，那么应该选择unordered_map。unordered_map使用散列表作为底层数据结构，插入和删除操作的时间复杂度通常是常数级别的，而不会受到键的顺序影响。

3. 内存占用的考虑：由于unordered_map使用散列表，它往往需要比map更多的内存空间来存储散列桶和链表或其他解决冲突的数据结构。如果内存占用是一个重要的考虑因素，可能需要权衡使用unordered_map所带来的额外内存消耗。

4. 键的哈希函数和相等比较：在使用unordered_map时，需要确保键类型具有良好的哈希函数和相等比较操作符。如果键类型不提供这些操作，或者它们的性能较低，可能需要手动提供自定义的哈希函数和相等比较操作符。

## 19.位运算

- 运算符

|**符号**|**说明**|
|:---:|---|
|&|按位与|
|\||按位或|
|^|按位异或|
|~|取反|
|<<|左移|
|>>|右移动|

```cpp
//举例
y|=1LL<<位数

//判断一个数n ，是不是2的整数幂。
cin>>n;
if(n&(n-1))cout<<"NO";
else cout<<"Yes";

//计算一个数的二进制中1的个数:
int n = 0,num;
unsigned int flag = 1;
cin>>num;
while(flag){
	if(num & flag)
		n++;
	flag = flag<<1;
}
cout<<n<<endl;
```

## 20.stream

### 20.1流类

- 数据输入和输出的过程也是数据传输的过程。数据像水一样从一个地方流动到另一个地方，因此，在 C++将此过程称为 “流（stream）”。在 C++ 的标准类库中，将用于进行数据输入输出的类统称为“流类”。
- `cin` 是 `istream` 类的对象（输入流类），用于处理标准输入（通常是键盘）。
- `cout` 是 `ostream` 类的对象（输出流类），用于处理标准输出（通常是屏幕）。
- 标准库在初始化时会自动创建 `cin`、`cout` 等全局对象，并关联到对应的硬件设备（键盘 / 屏幕）。

![](../assets/C++%E8%AF%AD%E6%B3%95-3.png)
![](../assets/C++%E8%AF%AD%E6%B3%95-4.png)

### 20.2ios_base类

- `ios_base`：最顶层的抽象基类，封装了所有流的**格式控制**（如对齐方式、数值进制）和**状态管理**（如错误标志），与具体的输入输出操作无关。

### 20.3ios类

- `ios`：继承自 `ios_base`，增加了与模板类型相关的接口（C++ 标准中为 `template <class charT, class traits = char_traits<charT>> class basic_ios`），是后续输入输出流的直接基类。
- 核心功能：管理流的状态和格式控制，不直接处理输入输出。
    - 状态标志：如 `eofbit`（到达文件尾）、`failbit`（操作失败）、`badbit`（流损坏）等，可通过 `good()`、`eof()`、`fail()` 等成员函数判断。
    - 格式控制：如 `setf()`（设置格式，如左对齐、十六进制）、`precision()`（设置浮点数精度）等。
- 使用场景：所有流对象（如 `cin`、`cout`、`fstream` 对象）都继承了 `ios` 的成员，因此都能使用这些状态和格式功能。

### 20.4iostream类

- `istream`：继承自 `ios`，是**输入流的基类**（模板类为 `basic_istream`），定义了核心输入操作（如 `>>` 提取运算符、`get()`、`read()` 等）。
- `ostream`：继承自 `ios`，是**输出流的基类**（模板类为 `basic_ostream`），定义了核心输出操作（如 `<<` 插入运算符、`put()`、`write()` 等）。
- `iostream`：多重继承自 `istream` 和 `ostream`，是**双向流的基类**（模板类为 `basic_iostream`），同时支持输入和输出操作。
- 为了避免多继承的二义性，从 ios 派生出 istream 和 ostream 时，均使用了 virtual 关键字（虚继承）。
- 标准设备流（关联键盘 / 屏幕）
	- **`cin`**：`istream` 的实例，关联标准输入设备（键盘），用于从键盘读取数据，也可以被重定向为从文件中读取数据。
	- **`cout`**：`ostream` 的实例，关联标准输出设备（屏幕），用于向屏幕输出数据，也可以被重定向为向文件写入数据。
	- **`cerr`**：`ostream` 的实例，关联标准错误输出（屏幕，无缓冲，即时输出），用于向屏幕输出出错信息，不能被重定向。
	- **`clog`**：`ostream` 的实例，关联标准错误输出（屏幕，有缓冲），用于向屏幕输出日志信息，不能被重定向。
> [!NOTE] cerr 和 clog的区别在于：cerr 不使用缓冲区，直接向显示器输出信息；而输出到 clog 中的信息会先被存放到缓冲区，缓冲区满或者刷新时才输出到屏幕。

- 本质就是：ostream cout(&fout);
- ostream 类的无参构造函数和复制构造函数都是私有的，因此在程序中一般无法定义 ostream 类的对象，唯一能用的 ostream 类的对象就是 cout。

- cout 可以被重定向，而 cerr 不能。所谓重定向，就是将输入的源或输出的目的地改变。例如，cout 本来是输出到屏幕上的，但是经过重定向，本该输出到屏幕上的东西就可以被输出到文件中。

### 20.5fstream类

- `ifstream`/`ofstream`/`fstream`：文件流类，用于操作磁盘文件（继承自输入 / 输出 / 双向流类）。
- fstream作用：同时支持对文件的**读操作**和**写操作**（继承自 `iostream`，因此同时拥有 `istream` 和 `ostream` 的功能）。

- 使用步骤：
    1. 创建 `fstream` 对象，关联文件（通过构造函数或 `open()` 方法）。
    2. 使用 `>>` 或 `read()` 读文件，使用 `<<` 或 `write()` 写文件。
    3. 操作完成后关闭文件（`close()` 方法，对象销毁时也会自动关闭）。
- 文件流（关联磁盘文件）
	- **`ifstream`**：继承自 `istream`，用于**从文件读取数据**（模板类为 `basic_ifstream`）。
	- **`ofstream`**：继承自 `ostream`，用于**向文件写入数据**（模板类为 `basic_ofstream`）。
	- **`fstream`**：继承自 `iostream`，用于**文件的双向读写**（模板类为 `basic_fstream`）。

- `istringstream`/`ostringstream`/`stringstream`：字符串流类，用于操作内存中的字符串（类似文件操作，但数据在内存中）。

### 20.6sstream类

- **stringstream** 是 **C++**  提供的专门用于处理字符串的 **输入输出** 流类。

- 类模板 `std::basic_stringstream` 实现基于字符串的流上的输入与输出操作。它等效地存储一个  `std::basic_string` 的实例，并在其上进行输入与输出操作。（typedef basic_stringstream\<char\> stringstream;）

- 构造

```cpp
// 创建一个 string类对象s和创建一个 stringstraeam类 对象 ss
string s("hello stringstream");
stringstream ss;
// 向对象输入字符串 : "<<" 表示向一个对象中输入
ss << s;
cout << ss.str() << endl;

//构造并初始化
stringstream ss("hello stringstream");
```

两种构造有些区域：
```cpp
//构造空
int main()
{
    stringstream ss1;
    ss1 << "fre";
    ss1 << "gre";
    cout << ss1.str() << endl;//输出：fregre
    return 0;
}
```
> [!NOTE] 两个字符串直接拼接在了一起

```cpp
//构造并初始化
int main()
{
    string str("asd");
    stringstream ss2(str);
    cout << ss2.str() << endl;//输出：asd
    
    ss2 << "r";
    cout << ss2.str() << endl;//输出：rsd
 
    ss2 << "13";
    cout << ss2.str() << endl;//输出：r13
 
    ss2 << "hy";
    cout << ss2.str() << endl;//输出：r13hy
    
    return 0;
}
```
> [!NOTE] 在进行字符串拼接时，首先把原本的字符串覆盖掉，之后再进行拼接。

如果想要追加构造，需要显示指定ios_base的添加方式
```cpp
int main()
{
    ostringstream ss("1 2 3 4 ", std::ios_base::ate);	// append 方式追加
    cout << ss.str() << endl;//输出：1 2 3 4
 
    ss << "5 3 4";
    cout << ss.str() << endl;//输出：1 2 3 4 5 3 4
    
    return 0;
}
```

- 输出

> [!NOTE] `stringstream`  可以将存储于内部的字符串输出，需要调用 `str()` 函数，不可直接输出
```cpp
std::cout << ss.str() << std::endl;
// std::cout << ss << std::endl; 		// 错误不可直接输出
```

- 修改

```cpp
int main()
{
    stringstream ss("hello string");
    cout << ss.str() << endl;//输出：hello string
 
    // 修改内容
    ss.str("hello stringstream");
    cout << ss.str() << endl;//输出：hello stringstream
 
    // 清空内容
    ss.str("");
    cout << ss.str() << endl;//输出：
 
    return 0;
}
```

- `stringstream` 默认是以空格来分割字符串的，利用 `stringstream` 去除字符串空格非常方便
```cpp
int main()
{
    stringstream ss("hello string and stringstream");
    cout << ss.str() << endl;//输出：hello string and stringstream
    
	cout<< endl;
    
    string str;
    // 注意： stringstream 是一个单词一个单词 “流入”string 的
    while (ss >> str)
    {
        cout << str << endl;//输出：hello；输出：string；输出：and；输出：stringstream
    }
    return 0;
}

int main() {
    string str = "10 20.5 hello 30";
    istringstream iss(str);  // 创建iss对象，关联字符串str
    
    // 从iss中提取数据（类似cin）
    int a;
    double b;
    string c;
    int d;
    
    iss >> a >> b >> c >> d;  // 按空格或者\t或者\n分割提取，读取时会自动跳过开头的空白字符（如果有的话）。
    
    // 输出提取结果
    cout << "a: " << a << endl;    // 10
    cout << "b: " << b << endl;    // 20.5
    cout << "c: " << c << endl;    // hello
    cout << "d: " << d << endl;    // 30
    
    return 0;
}

//也可以自己指定分隔符
int main()
{
	string source = "abc,123,<!>";
    stringstream ss(source);
    cout << ss.str() << endl;//输出：abc,123,<!>
    
	cout<< endl;
 
    string str;
    while (getline(ss, str, ','))
    {
        cout << str << endl;//输出：abc；输出：123；输出：<!>
    }
 
    return 0;
}
```

- 使用 `stringstream` 进行类型转换
```cpp
//将数字转换为字符串
int main() {
    int num = 123;
    std::stringstream ss;
    ss << num; // 将整数放入流中
    std::string str = ss.str(); // 使用str()函数 从流中提取字符串
    std::cout << str << std::endl; // 输出：123
}
//将字符串转换为数值
int main() {
    std::string str = "456";
    std::stringstream ss(str); // 初始化stringstream
    int num;
    ss >> num; // 从流中提取整数
    std::cout << num << std::endl; // 输出：456
}
```

### 20.7流控制

- 控制方法
	1. **流成员函数**：如`setf()`（设置格式标志）、`precision()`（设置精度）、`width()`（设置宽度）。
	2. **操纵符（Manipulators）**：需包含`<iomanip>`头文件，如`setw()`、`setfill()`、`setprecision()`、`hex`等，使用更简洁。
- 所有流类（`cin`、`cout`、`iss`、`oss`、`fstream`等）都继承了`ios_base`的格式控制功能，因此上述方法适用于：
	1. 标准流：`cin`、`cout`、`cerr`。
	2. 字符串流：`istringstream`、`ostringstream`、`stringstream`。
	3. 文件流：`ifstream`、`ofstream`、`fstream`。

#### 20.7.1固定宽度与填充字符

- **需求**：输出时固定宽度，不足部分用指定字符（如空格、0）填充。
- **方法**：`setw(n)`（设置宽度）+ `setfill(c)`（设置填充字符）。

```cpp
#include <iostream>
#include <iomanip>  // 需包含此头文件
#include <sstream>
using namespace std;

int main() {
    // 1. 标准输出cout
    cout << "默认：" << 123 << endl;  // 输出：123
    cout << "固定宽度5，填充0：" << setw(5) << setfill('0') << 123 << endl;  // 00123
    cout << "固定宽度5，填充*：" << setw(5) << setfill('*') << 123 << endl;  // **123

    // 2. 字符串流oss
    ostringstream oss;
    oss << setw(6) << setfill('0') << 45;  // 宽度6，填充0
    cout << "oss结果：" << oss.str() << endl;  // 000045

    return 0;
}
```

> [!NOTE] 注意：`setw(n)`仅对**下一次输出有效**，之后需重新设置；`setfill(c)`则持续有效，直到被修改。
>>`setfill` 仅用于**补充 “指定宽度与实际内容宽度的差值”**。如果没有用 `setw(n)` 指定宽度，流会直接输出内容，无需填充，因此 `setfill` 的设置不会产生任何效果。默认情况下，`setfill` 的填充字符是**空格（`' '`）**。这是 C++ 流类的初始设置，无需手动配置。

#### 20.7.2数值补零

- **整数补零**：结合`setw()`和`setfill('0')`（见上面示例）。
- **浮点数补零**：配合`fixed`（固定小数位）和`setprecision(n)`（保留 n 位小数）。

```cpp
#include <iostream>
#include <iomanip>
#include <sstream>
using namespace std;

int main() {
    double num = 3.14;

    // 1. cout控制浮点数补零
    cout << "默认：" << num << endl;  // 3.14
    cout << "保留4位小数，补零：" << fixed << setprecision(4) << num << endl;  // 3.1400

    // 2. oss控制整数补零
    ostringstream oss;
    oss << setw(5) << setfill('0') << 7;  // 宽度5，填充0
    cout << "整数补零：" << oss.str() << endl;  // 00007

    return 0;
}
```

#### 20.7.3对齐方式

- **需求**：输出内容在指定宽度内左对齐或右对齐。
- **方法**：`left`（左对齐）、`right`（右对齐，默认）操纵符，配合`setw()`。

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    cout << "右对齐（默认）：" << setw(10) << setfill('-') << "hello" << endl;  // -----hello
    cout << "左对齐：" << setw(10) << left << "hello" << endl;  // hello-----

    return 0;
}
```

#### 20.7.4数值进制转换

- **需求**：以不同进制输出整数（如十六进制、八进制）。
- **方法**：`dec`（十进制，默认）、`hex`（十六进制）、`oct`（八进制）。

```cpp
#include <iostream>
#include <sstream>
using namespace std;

int main() {
    int num = 255;

    // 1. cout输出不同进制
    cout << "十进制：" << dec << num << endl;  // 255
    cout << "十六进制：" << hex << num << endl;  // ff（小写）
    cout << "十六进制大写：" << uppercase << hex << num << endl;  // FF

    // 2. oss输出十六进制
    ostringstream oss;
    oss << hex << num;  // 以十六进制写入字符串流
    cout << "oss十六进制结果：" << oss.str() << endl;  // ff

    return 0;
}
```

#### 20.7.5输入格式控制

`cin`和`iss`也可通过格式控制调整输入解析方式，例如按特定进制读取整数：

```cpp
#include <iostream>
#include <sstream>
using namespace std;

int main() {
    // 1. cin按十六进制读取
    int x;
    cout << "输入十六进制数（如ff）：";
    cin >> hex >> x;  // 读取十六进制"ff"，转换为整数255
    cout << "十进制值：" << dec << x << endl;  // 255

    // 2. iss按八进制读取
    string data = "17";  // 八进制17 = 十进制15
    istringstream iss(data);
    int y;
    iss >> oct >> y;  // 按八进制解析
    cout << "十进制值：" << y << endl;  // 15

    return 0;
}
```

## 21.左值/右值

### 21.1左值

- 左值是一个表示数据的表达式(如变量名或解引用的指针)，我们可以获取它的地址并且可以对它赋值(const修饰符后的左值，不能给他赋值，但是可以取它的地址)，左值可以出现赋值符号的左边，右值不能出现在赋值符号左边。
- 我们可以修改左值，但不可以修改右值。
- 左值引用就是给左值的引用，给左值取别名。
```cpp
//如下c,b,p都为左值。
int c = 0;
const int b = 2;
int* p = nullptr;
```

### 21.2右值

- 右值也是一个表示数据的表达式，如：字面常量、表达式返回值，函数返回值等等。
- 右值可以出现在赋值符号的右边，但是不能出现出现在赋值符号的左边，右值不能取地址，我们也不能修改右值。
- 右值引用就是对右值的引用，给右值取别名
```cpp
int fun()
{
return 1;
}
void test()
{
int a = 1, b = 2;
//右值
10;
a + b;
fun();
}
```

### 21.3左值与右值对比

- 左值一般为我们自己定义的变量，在定义时开辟了内存，我们可以对这块内存赋值，修改内存中的值，如果有const也仅从语法层面上不允许修改，这块内存在其生命周期结束的时候销毁。

- 右值一般为临时变量，是程序运行时产生的中间产物，他不是我们用户自己定义开辟空间的，是由编译器帮我们开辟空间，并且在用完就立即销毁。右值的生命周期一般只在当前语句，当我们要对右值进行赋值时，他已经释放空间了，此时我们再进行访问就是野访问，（与野指针一样造成内存问题），所以我们不能对右值进行修改，编译器强制语法检查，遇到修改操作就报错。

- 右值中特殊的就是字面常量，他们存储在内存的常量区，内存为只读属性，不可以修改，不可以取地址，他们的生命周期与程序的生命周期一样，当我们使用字面常量时，编译器帮我们开辟空间，并用字面常量初始化，这块临时空间用完即销。
- C++对于左值和右值没有标准定义，但是有一个被广泛认同的说法：
    - 可以取地址的，有名字的，非临时的就是左值；
    - 不能取地址的，没有名字的，临时的就是右值；

- 所以立即数，函数返回的值等都是右值；而非匿名对象(包括变量)，函数返回的引用，const对象等都是左值。
- 分辨左右值最常用方法就是判断他是否可以取地址。如果不可以就为右值。

## 22.左值/右值引用

### 22.1左值引用【避免对象拷贝】

- 左值引用就是给左值的引用，主要作用是避免对象拷贝。
```cpp
int c = 0;
const int b = 2;
int* p = nullptr;
 
//左值引用
int& lc = c;
const int& lb = b;
int*& lp = p;
```
- 在int& lc = c;语句后，lc与c使用的是同一块空间，lc与c完全等效，一荣俱荣，一损俱损。

```cpp
int a = 10;
int &b = a;  // 定义一个左值引用变量
b = 20;      // 通过左值引用修改引用内存的值
```
- 左值引用在汇编层面其实和普通的指针是一样的；定义引用变量必须初始化，因为引用其实就是一个别名，需要告诉编译器定义的是谁的引用。

```
int &var = 10;
```
- 上述代码是无法编译通过的，因为10无法进行取地址操作，无法对一个立即数取地址，因为立即数并没有在内存中存储，而是存储在寄存器中，可以通过下述方法解决：
```
const int &var = 10;
```
- 使用常引用来引用常量数字10，因为此刻内存上产生了临时变量保存了10，这个临时变量是可以进行取地址操作的，因此var引用的其实是这个临时变量，相当于下面的操作：
```
const int temp = 10; 
const int &var = temp;
```
- 总结来说
    - 左值引用要求右边的值必须能够取地址，如果无法取地址，可以用常引用；
    - 但使用常引用后，我们只能通过引用来读取数据，无法去修改数据，因为其被const修饰成常量引用了。

### 22.2右值引用【延长对象生命周期】

- 右值引用就是对右值的引用，给右值取别名。主要作用是把延长对象的生命周期，一般是延长到作用域的scope之外。
- 定义右值引用的格式为：`类型 && 引用名 = 右值表达式`,例如`int &&var = 10;`
- 右值引用是C++ 11新增的特性，所以C++ 98的引用为左值引用。右值引用用来绑定到右值，绑定到右值以后本来会被销毁的右值的生存期会延长至与绑定到它的右值引用的生存期。在汇编层面右值引用做的事情和常引用是相同的，即产生临时量来存储常量。但是，唯一 一点的区别是，右值引用可以进行读写操作，而常引用只能进行读操作。右值引用的存在并不是为了取代左值引用，而是充分利用右值(特别是临时对象)的构造来减少对象构造和析构操作以达到提高效率的目的。

```cpp
int fun()
{
return 1;
}
void test()
{
int a = 1, b = 2;
 
//右值引用
int && r1=10;
int && r2=(a + b);
int && r3=fun();
}
```
- 右值引用就是对右值的引用，给右值取别名。他的语法与左值引用十分相似，右值引用语法为对应类型加上两个& 。

注意：
- 右值引用 引用 右值，会使右值被存储到特定的位置。右值引用变量其实是左值，可以对它取地址和赋值（const右值引用变量可以取地址但不可以赋值，因为 const 在起作用）。
- 当然，取地址是指取变量空间的地址（右值是不能取地址的）。
```cpp
double&& rr2 = x + y;
&rr2;
rr2 = 9.4;
//右值引用 rr2 引用右值 x + y 后，该表达式的返回值被存储到特定的位置，不能取表达式返回值 x + y 的地址，但是可以取 rr2 的地址，也可以修改 rr2 。

const double&& rr4 = x + y;
&rr4;
//可以对 rr4 取地址，但不能修改 rr4，即写成rr4 = 5.3;会编译报错
```

- 主要是解决函数参数的传递中（针对返回的将亡值，即局部大块数据），解决大块数据或对象的传递效率和空间不如意的问题

### 22.3左值引用与右值引用对比

- 首先明确一点就是不管是左值引用还是右值引用，都是给一段空间取别名。对于左值引用而言，他与绑定的变量共用同一块空间，是之前我们定义该变量（左值）开辟的空间，没有再开辟出新内存。
- 对于右值引用而言，他也没有开辟内存，他绑定的一块空间是编译器运行时为右值自动开辟的空间，没有为右值引用再开辟出新内存。

- 如果按照编译器对于普通右值的处理，当前语句结束就销毁，右值引用语法规定可以获取临时变量的内存空间权力，（以该句为例int && r3=fun();）当我们用r3引用fun（）这个右值的时候，这块空间原本是由编译器释放销毁的，但被人为的抢走释放权限，加上右值引用这句话，就相当于说明我认为这块空间还有利用的价值，你编译器先别着急释放，交给我来释放，这里水很深，交给我来把握。我们就拥有了这块空间的生杀大权，编译器就不会立即释放该内存。
- 左值引用与右值引用相同之处
    1. 都是引用，都是再给一段空间取别名
    2. 除了const修饰外，左值引用与右值引用都可以修改。
    3. 底层都是指针，语法层面不同罢了

- 左值引用与右值引用不同之处
    1. 左值引用引用左值，左值可以修改；右值引用引用右值，右值不可以修改
    2. 左值是由用户自定义变量组成，右值一般为编译器运行时开辟临时变量。

### 22.4左值引用与右值引用总结

- 左值引用可以指向右值，但需要const来修饰，不能修改这个值
- 右值引用可以指向左值，需要std::move(v)即可
- 声明出来的左值引用或右值引用都是左值

- 左值引用只能引用左值，不能直接引用右值。
- 但是const左值引用既可以引用左值，也可以引用右值。

- 右值引用只能引用右值，不能直接引用左值。
- 但是右值引用可以引用被move的左值。

> [!NOTE] std::move（C++11中将参数类型强制转换为右值，不管参数是左值还是右值。），作用是将一个值强制转化为右值，以实现移动语义。左值被 move 后变为右值，于是右值引用可以引用。

```cpp
// 1.左值引用只能引用左值
int t = 8;
int& rt1 = t;
//int& rt2 = 8; // 编译报错，因为8是右值，不能直接引用右值

// 2.但是const左值引用既可以引用左值
const int& rt3 = t;

const int& rt4 = 8; // 也可以引用右值 const int& rt4本质上也是一个常量，指向一个不能被修改的右值，也是安全的
const double& r1 = x + y;
const double& r2 = fmin(x, y);

// 3.右值引用只能引用右值
int&& rr1 = 10;
double&& rr2 = x + y;
const double&& rr3 = x + y;

int t = 10;
//int&& rrt = t; // 编译报错，不能直接引用左值

// 4.但是右值引用可以引用被move的左值
int&& rrt = std::move(t);
int*&& rr4 = std::move(p);
int&& rr5 = std::move(*p);
const int&& rr6 = std::move(b);

```

### 22.5修改/取地址

- 右值是一定不可以修改和取地址的，右值引用是可以修改的，也可以取地址。
- 左值引用和左值一样，左值引用可以修改和取地址。
```cpp
int main()
{
 
//右值引用
int&& r1 = 10;//当执行完int&& r1 = 10;语句后他就与int c = 10;int& r1 = c;这两句的效果一样

cout << r1 << endl;
cout << (void *) & r1 << endl;
r1 = 20;
cout << r1 << endl;
 
return 0;
}
```
- 右值不可以修改，退一步来说取地址实际上也是为了后面修改操作。右值在当前语句结束就销毁，后面语句再访问/修改就是访问野空间，会报错。
- 右值引用可以修改，我们从编译器手中拿走了右值销毁的权利，“右值”在引用后依旧存在，可以进行正常的访问修改操作。
- 左值引用和左值使用的都是用户自定义开辟的空间，所以左值引用支持修改。

### 22.6左值引用 引用 右值

- 首先`int&& ra = 10;`是正常的定义右值引用，是没有任何问题的
- `int& la = 10;`直接使用左值是错误的，编译器报错。编译器检测右值不支持修改操作，但一般的左值引用支持修改，我们就可以加上const，禁止修改，使得编译器检测通过：`int& la = 10;`
```cpp
int fun()
{
return 1;
}
void test()
{
int a = 1, b = 2;
 
//左值引用
const int& r1 = 10;
const int& r2 = (a + b);
const int& r3 = fun();
}
```

### 22.7右值引用 引用 左值

- `int c = 0;int& lc = c;`是正常的定义左值引用，是没有任何问题的
- `int &&rc=c`直接写不行，得强制转换：`int &&rc=(int &&)c`

```cpp
int c = 0;
const int b = 2;
int* p = nullptr;
 
//右值引用
int&& lc = (int&&)c;
const int&& lb = (const int&&)b;
int*&& lp = (int*&&)p;
```

- 右值引用后，lc与c用的是同一块空间，此时仿佛lc是左值引用一样。我们可以通过如下代码检测。

### 22.8左值引用用处

引用有和指针同等的效率，那么在函数设计传递参数的时候，我们就可以传递引用来代替指针，
```cpp
void Print1(vector<int> & t)
{
}
void Print2(vector<int>* pt)
{
}
int main()
{
vector<int> t1(10);
    //引用比指针更加的安全。如果在 Print2中修改pt的值，就会造成野指针问题，
Print1(t1);
Print2(&t1);
return 0;
}

```

### 22.9右值引用用处

```cpp
//用C++实现一个简单的顺序栈：

class Stack
{
public:
    // 构造
    Stack(int size = 1000) 
:msize(size), mtop(0)
    {
cout << "Stack(int)" << endl;
mpstack = new int[size];
    }

    // 析构
    ~Stack()
    {
cout << "~Stack()" << endl;
delete[]mpstack;
mpstack = nullptr;
    }

    // 拷贝构造
    Stack(const Stack &src)
:msize(src.msize), mtop(src.mtop)
    {
cout << "Stack(const Stack&)" << endl;
mpstack = new int[src.msize];
for (int i = 0; i < mtop; ++i) {
    mpstack[i] = src.mpstack[i];
}
    }

    // 赋值重载
    Stack& operator=(const Stack &src)
    {
cout << "operator=" << endl;
if (this == &src)
         return *this;

delete[]mpstack;

msize = src.msize;
mtop = src.mtop;
mpstack = new int[src.msize];
for (int i = 0; i < mtop; ++i) {
    mpstack[i] = src.mpstack[i];
}
return *this;
    }

    int getSize() 
    {
return msize;
    }
private:
    int *mpstack;
    int mtop;
    int msize;
};

Stack GetStack(Stack &stack)
{
    Stack tmp(stack.getSize());
    return tmp;
}

int main()
{
    Stack s;
    s = GetStack(s);
    return 0;
}

//运行结果如下：

Stack(int)             // 构造s
Stack(int)             // 构造tmp
Stack(const Stack&)    // tmp拷贝构造main函数栈帧上的临时对象
~Stack()               // tmp析构
operator=              // 临时对象赋值给s
~Stack()               // 临时对象析构
~Stack()               // s析构
```
- 为了解决浅拷贝问题，为类提供了自定义的拷贝构造函数和赋值运算符重载函数，并且这两个函数内部实现都是非常的耗费时间和资源(首先开辟较大的空间，然后将数据逐个复制)，我们通过上述运行结果发现了两处使用了拷贝构造和赋值重载，分别是tmp拷贝构造main函数栈帧上的临时对象、临时对象赋值给s，其中tmp和临时对象都在各自的操作结束后便销毁了，使得程序效率非常低下。

- 那么为了提高效率，在C++11中通过提供带右值引用参数的拷贝构造函数和赋值运算符重载函数，可以把tmp持有的内存资源直接给临时对象,可以把临时对象的资源直接给s。

```cpp
// 带右值引用参数的拷贝构造函数
Stack(Stack &&src)
    :msize(src.msize), mtop(src.mtop)
{
    cout << "Stack(Stack&&)" << endl;

    /*此处没有重新开辟内存拷贝数据，把src的资源直接给当前对象，再把src置空*/
    mpstack = src.mpstack;  
    src.mpstack = nullptr;
}

// 带右值引用参数的赋值运算符重载函数
Stack& operator=(Stack &&src)
{
    cout << "operator=(Stack&&)" << endl;

    if(this == &src)
        return *this;
    
    delete[]mpstack;

    msize = src.msize;
    mtop = src.mtop;

    /*此处没有重新开辟内存拷贝数据，把src的资源直接给当前对象，再把src置空*/
    mpstack = src.mpstack;
    src.mpstack = nullptr;

    return *this;
}
mpstack = src.mpstack;
运行结果如下：

Stack(int)             // 构造s
Stack(int)             // 构造tmp
Stack(Stack&&)         // 调用带右值引用的拷贝构造函数，直接将tmp的资源给临时对象
~Stack()               // tmp析构
operator=(Stack&&)     // 调用带右值引用的赋值运算符重载函数，直接将临时对象资源给s
~Stack()               // 临时对象析构
~Stack()               // s析构
```
- 程序自动调用了带右值引用的拷贝构造函数和赋值运算符重载函数，使得程序的效率得到了很大的提升，因为并没有重新开辟内存拷贝数据。
- 在C++11中规定如果函数返回值是函数体内将要销毁的变量，那么这个将要销毁的变量是右值，叫做将亡值。

- 带右值引用参数的拷贝构造和赋值重载函数，又叫移动构造函数和移动赋值函数，在构造函数中直接掠夺右值引用的资源，我们称这种特殊的构造函数叫做移动构造。=赋值的时候也会出现右值，也可以像重载构造函数一样，重载赋值函数，提高效率。这里的移动指的是把临时量的资源移动给了当前对象，临时对象就不持有资源，为nullptr了，实际上没有进行任何的数据移动，没发生任何的内存开辟和数据拷贝。

> [!NOTE] 右值引用是基础机制，移动语义依托它实现资源的高效转移，完美转发依托它保持参数的左值 / 右值属性，std::move 则是将左值转为右值引用以触发移动语义的工具。

> [!NOTE] 有名字的变量，无论其类型是什么引用，作为表达式时都是左值。

## 23.移动语义

- 将一个对象中的资源移动到另一个对象（资源控制权的转移）。

### 23.1std::move

- std::move 是 C++11 引入的一个关键工具，它的核心作用是将左值转换为右值引用，从而允许使用移动语义而非拷贝语义。其主要用途包括：
	- 启用移动语义：让编译器知道对象可以被移动而非拷贝
	- 资源转移：高效转移资源所有权，避免不必要的深拷贝
	- 性能优化：减少临时对象的构造和析构开销

### 23.2移动构造

- 拷贝构造函数和移动构造函数都是构造函数的重载函数，所不同的是：
    - 拷贝构造函数的参数是 const左值引用，接收左值或右值；
    - 移动构造函数的参数是右值引用，接收右值或被 move 的左值。
- 如果拷贝构造函数和移动构造函数都有在类内定义的话，在构造对象时：
    - 若是左值做参数，那么就会调用拷贝构造函数，做一次拷贝（如果是像 string 这样的在堆空间上存在资源的类，那么每调用一次拷贝构造就会做一次深拷贝）。
    - 若是右值做参数，那么就会调用移动构造，而调用移动构造就会减少拷贝（如果是像 string 这样的在堆空间上存在资源的类，那么每调用一次移动构造就会少做一次深拷贝）。

### 23.3移动赋值

- 拷贝赋值函数和移动赋值函数都是赋值运算符重载函数的重载函数，所不同的是：
    - 拷贝赋值函数的参数是 const左值引用，接收左值或右值；
    - 移动赋值函数的参数是右值引用，接收右值或被 move 的左值。
- 如果这两个函数都有在类内定义的话，在进行对象的赋值时：
    - 若是左值做参数，那么就会调用拷贝赋值，做一次拷贝（如果是像 string 这样的在堆空间上存在资源的类，那么每调用一次拷贝赋值就会做一次深拷贝）。
    - 若是右值做参数，那么就会调用移动赋值，而调用移动赋值就会减少拷贝（如果是像 string 这样的在堆空间上存在资源的类，那么每调用一次移动赋值就会少做一次深拷贝）。

## 24.万能引用

- 万能引用的形式是 T&&，但必须满足两个条件,只有同时满足这两个条件，T&& 才是万能引用，否则就是普通的右值引用（&&）。
    1. 它必须是模板参数（即 T 是模板类型参数）；
    2. 必须发生类型推导（编译器需要根据传入的实参推导出 T 的类型）。
- 万能引用和右值引用区别
    1. 万能引用（T&& 带模板推导）：既能接左值，也能接右值；
    2. 右值引用（如 int&&）：只能接右值，不能接左值。
- 万能引用的类型推导规则：当万能引用绑定到左值或右值时，编译器会推导出不同的 T 类型：
    1. 若绑定到左值（如 int a; func(a)）：T 会被推导为 “左值引用类型”（如 int&），此时 T&& 会通过 “引用折叠” 变为 int&（左值引用）；
    2. 若绑定到右值（如 func(20)）：T 会被推导为 “非引用类型”（如 int），此时 T&& 就是 int&&（右值引用）。
- 确定类型的 && 表示右值引用（比如：int&& ，string&&）
- 但函数模板中的 && 不表示右值引用，而是万能引用，模板类型必须通过推断才能确定，其接收左值后会被推导为左值引用，接收右值后会被推导为右值引用。
```cpp
template<typename T>
void f(T&& t)  // 万能引用
{
    //...
}
int main()
{
    int a = 5;  // 左值
    f(a);  // 传参后万能引用被推导为左值引用

    const string s("hello");  // const左值
    f(s);  // 传参后万能引用被推导为const左值引用
   
    f(to_string(1234));  // to_string函数会返回一个string临时对象，是右值，传参后万能引用被推导为右值引用

    const double d = 1.1;
    f(std::move(d));  // const左值被move后变成const右值，传参后万能引用被推导为const右值引用
    
    return 0;
}
```
![](../assets/C++%E8%AF%AD%E6%B3%95-5.png)
- 但是右值引用变量其实是左值
```cpp

void Func(int& x) {    cout << "左值引用" << endl; }

void Func(const int& x) { cout << "const左值引用" << endl; }

void Func(int&& x) { cout << "右值引用" << endl; }

void Func(const int&& x) { cout << "const右值引用" << endl; }

template<typename T>
void f(T&& t)  // 万能引用
{
    Func(t);  // 根据参数t的类型去匹配合适的重载函数
}

int main()
{
    int a = 4;  // 左值
    f(a);
    
    const int b = 8;  // const左值
    f(b);
    
    f(10); // 10是右值
    
    const int c = 13;
    f(std::move(c));  // const左值被move后变成const右值
    
    return 0;
}

//输出结果
// 左值引用
// const左值引用
// 左值引用
// const左值引用

```
- f(10);10是右值，传参后万能引用被推导为右值引用，但该右值引用变量其实是左值，因此实际调用的函数是void Func(int& x)。
- f(std::move(c));const左值被move后变成const右值，传参后万能引用被推导为const右值引用，但该const右值引用变量其实是const左值，因此实际调用的函数是void Func(const int& x)。

- 也就是说，右值引用失去了右值的属性。我们期望传递过程中能够保持住它的原有的左值或右值属性，于是 C++11标准提出完美转发。

### 24.1完美转发

- 完美转发是指在函数模板中，完全依照模板的参数类型，将参数传递给当前函数模板中的另外一个函数。

- 具体来说，为了实现完美转发，除了使用万能引用之外，我们还要用到std::forward（C++11），它在传参的过程中保留对象的原生类型属性。这样右值引用在传递过程中就能够保持右值的属性。

```cpp
//修改f函数为PerfectForward
template<typename T>
void PerfectForward(T&& t)  // 万能引用
{
    Func(std::forward<T>(t));  // 根据参数t的类型去匹配合适的重载函数
}
//输出结果
// 左值引用
// const左值引用
// 右值引用
// const右值引用
```
> [!NOTE] 只要是右值引用，由当前函数再传递给其它函数调用，要保持右值属性，必须实现完美转发。

- 右值引用（及其支持的移动语义和完美转发）是 C++11 中加入的最重要的新特性之一，它使得 C++ 程序的运行更加高效。
- 完美转发的例子
```cpp
template<class T>
struct ListNode
{
    ListNode* _next = nullptr;
    ListNode* _prev = nullptr;
    T _data;
};

template<class T>
class List
{
    typedef ListNode<T> Node;
public:
    List()
    {
        _head = new Node;
        _head->_next = _head;
        _head->_prev = _head;
    }

    void PushBack(const T& x)  // 左值引用
    {
        Insert(_head, x);
    }

    void PushFront(const T& x)  // 左值引用
    {
        Insert(_head->_next, x);
    }

    void PushBack(T&& x)  // 右值引用
    {
        Insert(_head, std::forward<T>(x));  // 关键位置：保留对象的原生类型属性
    }

    void PushFront(T&& x)  // 右值引用
    {
        Insert(_head->_next, std::forward<T>(x));  // 关键位置：保留对象的原生类型属性
    }

    template<class TPL>    // 该函数模板实现了完美转发
    void Insert(Node* pos, TPL&& x)  // 万能引用
    {
        Node* prev = pos->_prev;
        Node* newnode = new Node;
        newnode->_data = std::forward<TPL>(x);  // 关键位置：保留对象的原生类型属性

        // prev newnode pos
        prev->_next = newnode;
        newnode->_prev = prev;
        newnode->_next = pos;
        pos->_prev = newnode;
    }

private:
    Node* _head;
};
```

### 24.2引用折叠

- 引用折叠是万能引用能 “同时接收左值和右值” 的核心原因，也广泛用于模板实例化和 auto 推导中。

> [!NOTE] 只要其中有一个是左值引用（&），最终结果就是左值引用（&）

> [!NOTE] 只有两个都是右值引用（&&），最终结果才是右值引用（&&）
- 引用折叠的 4 条规则
```cpp
//当出现 “引用的引用”（记为 A& &、A& &&、A&& &、A&& &&）时，折叠结果为：

A& & → 折叠为 A&
A& && → 折叠为 A&
A&& & → 折叠为 A&
A&& && → 折叠为 A&&
```
- 举个例子
```cpp
template <typename T>
void func(T&& t) {}

int main() {
    int a = 10;  // 左值
    func(a);     // 传入左值，推导过程：
                 // 1. 因为a是左值，T被推导为 int&（左值引用类型）；
                 // 2. 此时 T&& 变为 int& &&，根据规则2折叠为 int&（左值引用）；
                 // 3. 最终 t 是 int&，绑定到左值a。

    func(20);    // 传入右值，推导过程：
                 // 1. 20是右值，T被推导为 int（非引用类型）；
                 // 2. 此时 T&& 是 int&&，无需折叠（规则4）；
                 // 3. 最终 t 是 int&&，绑定到右值20。
    return 0;
}
int main() {
    int a = 10;
    int& la = a;       // 左值引用
    int&& ra = 20;     // 右值引用

    // auto&& 是万能引用（因为auto会触发类型推导）
    auto&& b = a;      // a是左值 → auto被推导为int& → auto&& 是 int& && → 折叠为 int&（左值引用）
    auto&& c = ra;     // ra是左值（右值引用变量本身是左值）→ auto被推导为int& → 折叠为 int&
    auto&& d = 30;     // 30是右值 → auto被推导为int → auto&& 是 int&&（右值引用）
    return 0;
}
template <typename T>
struct MyType {
    using Type = T&&;  // T&& 可能是引用的引用（需折叠）
};

int main() {
    // 实例化时T为int&（左值引用）
    typename MyType<int&>::Type t1;  // T&& 是 int& && → 折叠为 int&（左值引用）
    // 实例化时T为int&&（右值引用）
    typename MyType<int&&>::Type t2; // T&& 是 int&& && → 折叠为 int&&（右值引用）
    return 0;
}

```

## 注意事项

std下的锁机制
lock_guard
condition_variable
unique_ptr
shared_ptr
std::mutex是非递归互斥锁，同一个线程不可以多次连续调用lock方法。如果同一线程对std::mutex连续执行lock，会导致死锁（线程阻塞在第二次lock调用，等待自己释放锁）。
C 语言不支持函数重载，其函数名修饰（name mangling）规则简单，仅基于函数名本身，不包含参数类型和个数等信息。

- STD 的 move 的底层实现机制是通过引用折叠和 STD 的 reinterpret cast 转换实现的。
std::move 的核心作用是将对象转换为右值引用，其底层主要依赖引用折叠规则和static_cast，而非 reinterpret_cast。具体来说，std::move 通常的实现逻辑是：通过模板函数接收一个对象（可以是左值或右值），利用引用折叠规则确定其类型，再通过 static_cast 将其强制转换为对应的右值引用类型。reinterpret_cast 用于底层的类型重新解释，风险较高且语义与 std::move 所需的 “转换为右值引用” 不匹配，因此不会被用于 std::move 的实现中。

HTTP-2 中多路复用特性解决了 HTTP-1.1 的对头阻塞问题

对头阻塞：在同一 TCP 连接中，同一时刻只能处理一个请求 / 响应，后续请求必须等待前一个完成才能发送，若前一个请求阻塞，会导致所有后续请求被卡住。

多路复用：是 HTTP/2 的核心特性，它通过在单一 TCP 连接中同时传输多个请求 / 响应（以二进制帧的形式交错发送），避免了 HTTP/1.1 中请求排队等待的问题，从根本上解决了对头阻塞。

内联函数语法上可以是虚函数，但编译器会自动忽略内联属性，最终该函数仅作为虚函数生效，无法实现内联的核心功能（编译时代码展开）。

内联函数（inline）：编译时 “代码展开”，减少调用开销。内联函数的核心目的是消除函数调用的开销（如栈帧创建、参数压栈、返回值处理等）。其实现依赖编译时静态绑定

虚函数（virtual）：运行时 “多态绑定”，实现动态行为。虚函数的核心目的是支持运行时多态（即 “同一调用语句，根据对象的实际类型执行不同函数”）。其实现依赖运行时动态绑定，关键是 “虚函数表（vtable）” 和 “虚指针（vptr）”：

- static_cast<>

- inline
- 类的构造函数加冒号后面可以初始化成员变量也可以初始化父类。
类中包含以下成员，必须放在初始化列表位置进行初始化：

2. 引用成员变量
3. const成员变量
4. 自定义类型成员(该类没有默认构造函数)

```C
for (auto& t : threads) {
	t.join();
}
```

移动语义
右值引用

左值引用，右值引用，万能引用
完美转发
hpp文件Qt中好像用到了

可移动对象
for each

进程时间片用完时，仅会暂时释放 CPU（仍具备运行条件），应从运行状态转为就绪状态（等待下一次调度分配 CPU）

进程执行完毕后，生命周期结束，应直接从运行状态转为终止状态，而非就绪状态（就绪状态的进程仍需等待调度运行，执行完毕的进程已无运行必要）

进程发起网络请求后，需等待网络响应（属于 “不可立即完成的事件”），此时即使有 CPU 资源也无法继续运行，因此从运行状态转为阻塞状态，直至网络响应完成后再转回就绪状态。

IO 操作完成时，进程此前因等待 IO 已处于阻塞状态，事件完成后具备了运行条件（但未分配 CPU），应从阻塞状态转为就绪状态，

多线程加代码

同步需要 “主动阻塞等待” 任务完成（体现在join那一步），而异步可以 “先做别的事，等需要结果时再等”。

[^1]: 123
