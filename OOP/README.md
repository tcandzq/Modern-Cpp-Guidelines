# ➕ 面向对象高级编程
Classes 的两个经典分类
Class without pointer member(s)
- complex


Class with pointer member(s)
- string

**complex.h**

```cpp
#ifndef __COMPLEX
#define __COMPLEX__

class complex
{
public:
    complex (double r = 0,double i = 0)
        : re(r).im(i)
    {}
    complex& operator += (const complex&);
    double real () const {return re;}
    double imag () const {return im;}

private:
    double re,im;

    friend complex& __doapl (complex* ,const complex&);
};
#endif
```

```cpp

inline comlex& __doapl(complex* ths,const complex& r)
{
    ths->re += r.re;
    ths->im += r.im;
    return *this;
}


inline complex& complex::operator += (const complex& r)
{
    return __doapl(this,r)
}

inline complex operator + (const complex& x,const complex& y)
{
    return complex(real(x) + real(y),imag(x)+imag(y));
}

inline complex operator + (const complex& x,double y)
{
    return complex(real(x) + y ,imag(x);
}

inline complex operator + (double x,const complex& y)
{
    return complex(x + real(y),imag(y));
}



operator << (ostream& os,const complex& x)


```

**complex-test.h**
```cpp
#include <iostream>
#include "complex.h"
using namespace std;

int main()
{
    complex c1(2,1);
    complex c2;
    cout << c1 << endl;
    cout << c2 << endl;    

    c2 = c1 + 5;
    c2 = 7 + c1;
    c2  = c1 + c2;
    c2 += c1;
    c2 += 3;
    c2 = -c1;

    cout << (c1 == c2) << endl;
    cout << (c1 != c2) << endl;
    cout << conj(c1) << endl;
}

```



**string.h**
```cpp
#inndef __MYSTRING__
#define __MYSTRING__

class String
{
...
}

String::function(...) ...

Global-function(...) ... 


#endif
```
**string-test.h**
```cpp
int main()
{
    String s1();
    String s2("hello");

    String s3(s1);
    cout << s3 << endl;

    s3 = s2;
    cout << s3 << endl;
}


```


## 头文件与类的声明
### Header(头文件)中的防卫式声明
```cpp
#ifndef __COMPLEX
#define __COMPLEX__

...

#endif
```
当程序第一次include头文件时，就定义__COMPLEX__，第二次include的时候，__COMPLEX__已经定义过了，无需再次定义，这样就防止头文件被多次包含。让使用者避免为了使用头文件，需要先include A ，再inclue B，再include C的情况出现，这样负担太重。

### Header(头文件)的布局
```cpp
#ifndef __COMPLEX
#define __COMPLEX__

#include <cmath>

class complex;  // 1.forward declarations(前置声明)

 comlex& __doapl(complex* ths,const complex& r); 


class complex  // 2.class declarations(类声明)
{
    ...  
}


complex::function ... // class definition (类-定义)

#endif

```
### class 的声明
```cpp
class complex // class head
{  // class body ({}包含的)
public:
    complex (double r = 0,double i = 0)
        : re(r).im(i)
    {}
    complex& operator += (const complex&);
    double real () const {return re;}
    double imag () const {return im;}

private:
    double re,im;

    friend complex& __doapl (complex* ,const complex&);
};
```

```cpp
complex c1(2,1);
complex c2;
```

### class template(模板) 简介
```cpp
template<typename T> 
class complex // class head
{  // class body ({}包含的)
public:
    complex (T r = 0,T i = 0)
        : re(r).im(i)
    {}
    complex& operator += (const complex&);
    T real () const {return re;}
    T imag () const {return im;}

private:
    T re,im;

    friend complex& __doapl (complex* ,const complex&);
};

```
```cpp
complex<double> c1(2.5,1.5);
complex<double> c2(2,6);
```

## 构造函数
### inline(内联)函数
函数若在class body内定义完成，便自动成为inline候选人，在外部定义则不是。inline 像宏一样，不是完全一样，有宏的特性，没有宏的缺点。

不是所有的函数都可以inline，如果函数太复杂，就无法inline，即使写了inline，也只是对编译器的建议，是不是真的变成inline，由编译器决定。

```cpp
class complex 
{  
public:
    complex (double r = 0,double i = 0)
        : re(r).im(i)
    {}  // inline 函数
    complex& operator += (const complex&);  // 不是inline函数
    double real () const {return re;}  // inline 函数
    double imag () const {return im;}  // inline 函数

private:
    double re,im;

    friend complex& __doapl (complex* ,const complex&);
};
```
### 访问级别
`puclic` 被外界看到，公开的。`private`：只有class 本身可以看到，一般放的是class的数据。


```cpp
// 下面的代码会报错
complex c1(2,1)
cout << c1.re;
cout << c1.im;

//下面的代码是合法的
complex c1(2,1);
cout << c1.real();
cout << c1.imag();
```

### constructor(ctor，构造函数)
构造函数没有返回类型。
```cpp
complex (double r = 0,double i = 0) // default argument(默认实惨)
: re(x),im(i) // initialization list(初指列，初始列)
{ }
```
**利用构造函数特有的列表初始化来初始化数据，不要在构造函数体内赋值**
```cpp
complex (double r = 0,double i = 0) // default argument(默认实惨)
{ re = r;im = i;}

```
一个变量的数值设定有两个阶段，一个是初始化，一个是赋值(assignment)，因此我们可以利用变量的初始化来初始化变量，虽然在构造函数体内赋值的结果相同，但效率会很低。

**不带指针的类多半不用写析构函数。**

### ctor(构造函数)可以有多个-overloading(重载)
重载常常发生在构造函数当中。
看两组函数
```cpp
//1
complex (double r = 0,double i = 0) 
: re(x),im(i) 
{ }
//2 
complex () : re(0),im(0) { }
```
上面的函数1和函数2都有默认的参数，且没有返回类型，因此是不能同时存在类中的。例如下面的情况发生时，编译器就i不知道该调用哪个构造函数了
```cpp
{
    complex c1;
    complex c2();
}
```


## 参数传递与返回值


## 操作符重载与临时对象

```cpp
complex operator + (const complex& x,const complex& y)
```
为什么重载符“+”写成全局函数，不是成员函数？
答:如果把重载符“+”写到类的内部变成成员函数，那此时只能解决“复数+复数”的问题，不能解决于“实数+复数”、“虚数+复数”的情形。

为什么入参类型const引用，返回类型是值？
答：1. 传引用是加快速度；2.入参左边加完右边以后不会参数的改变内容，而是把结果放到局部变量里，所以加const；3.加完后的结果放在了函数内部的局部变量，所以不能返回引用。


```cpp
# include <iostream.h>
ostream& operator << (ostream& os,const complex& x)
{
    os << '(' << real(x) << ',' << imag(x) << ')';
}

complex c1(9,8);

cout << c1;

cout << c1 << endl`;
```
`cout << c1;`右边的内容丢到左边，不会改变右边的内容，所以第二个参数可以加`const`。左边的cout的状态会改变，所以第一个入参不能加`const`。

`cout << c1 `<< endl;由于操作符`<<`可以连续使用，当cout << c1结束时需要返回继续返回ostream对象给endl使用，所以返回类型是`ostream&`，另外`ostream`对象在入参中本来就有，所以返回类型可以加引用。

## 三大函数:拷贝构造、拷贝复制、析构
当在类中没有定义拷贝构造函数和拷贝赋值构造函数，编译器会提供一套拷贝构造函数和拷贝赋值构造函数。
如果类的成员里面有指针，一定要重写拷贝构造函数和赋值构造函数

### BigThree ，三个特殊函数
```cpp
class String
{
public:
    String(const char* catr = 0);
    String(const String& str);
    String& operator = (const String&);
    ~String();
    char* get_c_str() const {return m_data;}
private:
    char* m_data;
};
```
m_data是一个指向字符数组的指针，例如:m_data——>hello。

`String(const String& str)`接收的是自身类型的参数，即和类本身，故称为拷贝构造函数。

`String& operator = (const String&)`赋值的右边参数类型也是自身，故称为拷贝赋值构造函数。

`~String()` 称为析构函数。当String类产生的对象死亡时析构函数会被调用起来。


`char* get_c_str() const {return m_data}` 不改变m_data，**所以一定要加上coonst**。做到出手即证明。


class里有指针，多半要做动态分配，在对象死亡之前析构函数会被调用，需要在析构函数中释放掉动态分配的内存，否则会造成内存泄漏。

```cpp
inline String::String(const char* cstr = 0)
{
    if (cstr) // 判断传进来的字符串是否为空
    {
        m_data = new char[strlen(cstr) + 1]; //分配长度为strlen(str)+1的内存
        strcpy(m_data,cstr);
    } 
    else{  // 未指定指
        m_data = new char[1];
        *m_data = '\0';
    }
}

inline String:: ~String()
{
    delete [] m_data; // 释放内存
}

```

```cpp
String s1(),
String s2("hello");

String* p = new String("hello"); // 动态创建字符串
delete p;
```
### 拷贝构造函数


### 拷贝赋值函数


## 堆、栈与内存模型


## 类模板、函数模板、及其他


## 组合与继承

## 虚函数与多态


## 委托相关设计


参考:https://www.cnblogs.com/QG-whz/p/5517643.html