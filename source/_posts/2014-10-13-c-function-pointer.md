---
layout: post
title:  "函数指针"
date:   2014-10-13 23:28:20
categories:   cpp
---

函数指针是指向函数的指针变量。 因而“函数指针”本身首先应是指针变量，只不过该指针变量指向函数。这正如用指针变量可指向整型变量、字符型、数组一样，这里是指向函数。如前所述，C在编译时，每一个函数都有一个入口地址，该入口地址就是函数指针所指向的地址。有了指向函数的指针变量后，可用该指针变量调用函数，就如同用指针变量可引用其他类型变量一样，在这些概念上是一致的。函数指针有两个用途：调用函数和做函数的参数。

使用函数指针有时候可以优化代码结构，减少不必要的代码开支。比如我要打印一个面积，但是有两个形状：三角形和矩形。但是计算面积的函数又不同，所以如果不用函数指针，我们必须使用if else结构来实现。但是如果使用函数指针的画，我们就可以直接传递一个函数指针就搞定了。

函数指针的声明方法：
```
	type (*func)(type a,type b)
```
为了方便，可以定义一个别名：
```
	typedef type(*func)(type a, type b)
```

``` cpp
#include <iostream>
using namespace std;

//定义一个计算面积的函数指针
typedef double(*cal)(const double &x, const double &y);

double triangle_area(const double &x, const double &y);//三角形面积
double rectangle_area(const double &x,const double &y);//矩形面积

double print_area(double(*p)(const double&,const double&), const double &x, const double &y);

//函数定义
double triangle_area(const double &x, const double &y)
{
    return x*y*0.5;
}

double rectangle_area(const double &x,const double &y)
{
    return x*y;
}

double print_area(cal p, const double &x, const double &y)
{	
	//使用函数指针来调用计算面积的函数
    cout<<"面积为"<<p(x,y)<<endl;
    return 0.0;
}

int main()
{
    double a=2,b=3;//初始化两个参数a和b
    cal p;
    p = triangle_area;
    //直接传递函数名
    print_area(p, a, b);
    
    p = rectangle_area;
    print_area(p, a, b);
    return 0;
}
```