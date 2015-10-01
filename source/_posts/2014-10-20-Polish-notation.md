---
layout: post
title: "四则运算计算器实现"
date:   2014-10-20 15:52:20
categories: algorithms
---

##波兰表示法与逆波兰表示法

*波兰表示法*（Polish notation，或波兰记法），是一种逻辑、算术和代数表示方法，其特点是操作符置于操作数的前面，因此也称做前缀表示法。与逆波兰表示法不同，前缀表达式基本没有在商业计算器中使用过，但是其体系经常在编译器构造的概念教学中首先使用。

*逆波兰表示法*（Reverse Polish notation，RPN，或逆波兰记法）中所有操作符置于操作数的后面，因此也被称为后缀表示法。逆波兰记法不需要括号来标识操作符的优先级。

逆波兰记法中，操作符置于操作数的后面。例如表达“三加四”时，写作“3 4 +”，而不是“3 + 4”。如果有多个操作符，操作符置于第二个操作数的后面，所以常规中缀记法的“3 - 4 + 5”在逆波兰记法中写作“3 4 - 5 +”：先3减去4，再加上5。使用逆波兰记法的一个好处是不需要使用括号。例如中缀记法中“3 - 4 * 5”与“（3 - 4）*5”不相同，但后缀记法中前者写做“3 4 5 * -”，无歧义地表示“3 (4 5 *) −”；后者写做“3 4 - 5 *”。

逆波兰表达式的解释器一般是基于堆栈的。解释过程一般是：操作数入栈；遇到操作符时，操作数出栈，求值，将结果入栈；当一遍后，栈顶就是表达式的值。因此逆波兰表达式的求值使用堆栈结构很容易实现，和能很快求值。

*中缀表示法*（或中缀记法）是一个通用的算术或逻辑公式表示方法， 操作符是以中缀形式处于操作数的中间（例：3 + 4）。

##调度场算法

调度场算法（Shunting Yard Algorithm）是一个用于将中缀表达式转换为后缀表达式的经典算法，因其操作类似于火车编组场而得名。
```
	当还有记号可以读取时：
		读取一个记号。
		如果这个记号表示一个数字，那么将其添加到输出队列中。
		如果这个记号表示一个操作符，记做o1，那么：
			只要存在另一个记为o2的操作符位于栈的顶端，并且
				如果o1是左结合性的并且它的运算符优先级要小于或者等于o2的优先级，或者
				如果o1是右结合性的并且它的运算符优先级比o2的要低，那么
				将o2从栈的顶端弹出并且放入输出队列中(循环直至以上条件不满足为止)；
			然后，将o1压入栈的顶端。
		如果这个记号是一个左括号，那么就将其压入栈当中。
		如果这个记号是一个右括号，那么：
			从栈当中不断地弹出操作符并且放入输出队列中，直到栈顶部的元素为左括号为止。
			将左括号从栈的顶端弹出，但并不放入输出队列中去。
			如果在找到一个左括号之前栈就已经弹出了所有元素，那么就表示在表达式中存在不匹配的括号。
	当再没有记号可以读取时：
		如果此时在栈当中还有操作符：
			如果此时位于栈顶端的操作符是一个括号，那么就表示在表达式中存在不匹配的括号。
		将操作符逐个弹出并放入输出队列中。
	退出算法。
```
![](/img/Shunting-Yard.png)


##逆波兰表示法的求值
```
	WHILE 有输入符号
		读入下一个符号
		IF 是一个操作数
			入栈
		ELSE IF 是一个操作符
			有一个先验的表格给出该操作符需要n个参数
			IF 堆栈中少于n个操作数
				（错误） 用户没有输入足够的操作数
			Else，n个操作数出栈
			计算操作符。
			将计算所得的值入栈
	IF 栈内只有一个值
		这个值就是整个计算式的结果
	ELSE 多于一个值
		（错误） 用户输入了多余的操作数
```
对于一般的算术表达式，一个操作符只需要两个参数。

<hr>
下面给出四则运算器的代码实现
``` cpp
#include <iostream>
#include <stack>
#include <queue>

using namespace std;

//操作符的优先级
// 操作符 只考虑最简单的加减乘除
// 优先级		符号		运算顺序
// 1		* /     从左至右
// 2		+ -		从左至右
int op_pri(const char c) {
    switch(c) {
            case '*':
            case '/':
                return 2;
            case '-':
            case '+':
                return 1;
    }
    return -1;
}

//判断字符是否为数字
bool is_ident(const char c) {
    return c >= '0' && c <= '9';
}

//判断字符是否为合法的操作符
bool is_op(const char c) {
    return c == '/' || c == '*' || c == '^' || c == '-' || c == '+';
}

//调度场函数
bool shunting_yard(const vector<char> &input, vector<char> &output) {
    //清空输出队列
    while(!output.empty())
        output.clear();
    
    //如果输入为空，直接返回
    if (input.empty())
        return true;
    
    stack<char> op; //操作符栈
    for (int i = 0; i < input.size(); i++) {
        char c = input[i];
        if (c == ' ')
            continue;
        
        if (is_ident(c)) {
            //如果是数字，直接将字符加入到输出队列中
            output.push_back(c);
        } else if (is_op(c)) {
            //如果是操作符
            //如果操作符栈为空，直接将操作符压栈
            if (op.empty()) {
                op.push(c);
                continue;
            }
            
            char op_top = op.top(); //栈顶操作符
            if (!is_op(op_top) || op_pri(c) > op_pri(op_top)) {
                //如果操作符的优先级要大于栈顶的操作符或者栈顶操作符为括号，直接压栈
                op.push(c);
            } else {
                //如果操作符的优先级要小于等于栈顶操作符，那么就一直将栈顶弹出
                //放入输出队列
                while(op_pri(c) <= op_pri(op_top)) {
                    output.push_back(op_top);
                    op.pop();
                    if (op.empty())
                        break;
                    op_top = op.top();
                }
                op.push(c);
            }
        } else if (c == '(') {
            //如果是左括号，直接压栈
            op.push(c);
        } else if (c == ')') {
            //如果是右括号，弹出操作符，直到弹出左括号
            while (!op.empty()) {
                char op_top = op.top();
                if (op_top != '(') {
                    output.push_back(op_top);
                    op.pop();
                } else {
                    break;
                }
            }
            
            if (!op.empty()) {
                //如果找到了左括号，把左括号弹出
                op.pop();
            } else {
                //如果栈弹空了还没找到，那么表达式错误
                output.clear();
                cout<<"表达式错误"<<endl;
            }
        } else {
            //如果有其他字符，那么错误
            cout<<"表达式错误"<<endl;
            output.clear();
            return false;
        }
    }
    
    while (!op.empty()) {
        char op_top = op.top();
        if (is_op(op_top)) {
            output.push_back(op_top);
            op.pop();
        } else {
            cout<<"表达式错误"<<endl;
            output.clear();
            return false;
        }
    }
    
    return true;
}

//计算你波兰表达式的值
bool calculate(const vector<char> &output, double &result) {
    if (output.empty())
        return false;
    
    stack<double> values;
    for (int i = 0; i < output.size(); i++) {
        char c = output[i];
        if (is_ident(c)) {
            values.push(c - '0');
        } else if (is_op(c)) {
            if (values.empty()) {
                cout<<"表达式错误"<<endl;
                return false;
            }
            int left = values.top();
            values.pop();
            
            if (values.empty()) {
                cout<<"表达式错误"<<endl;
                return false;
            }
            int right = values.top();
            values.pop();
            
            switch(c) {
                case '+':
                    values.push(right + left);
                    break;
                case '-':
                    values.push(right - left);
                    break;
                case '*':
                    values.push(right * left);
                    break;
                case '/':
                    values.push(right / left);
                    break;
            }
        } else {
            cout<<"表达式错误"<<endl;
        }
    }
    
    result = values.top();
    values.pop();
    if (!values.empty()) {
        cout<<"表达式有误"<<endl;
        return false;
    }
    return true;
}

int main () {
    vector<char> input;
    input.push_back('3');
    input.push_back('+');
    input.push_back('4');
    input.push_back('*');
    input.push_back('2');
    input.push_back('/');
    input.push_back('(');
    input.push_back('1');
    input.push_back('-');
    input.push_back('5');
    input.push_back(')');

    vector<char> output;
    shunting_yard(input, output);
    for (int i = 0; i < output.size(); i++) {
        cout<<output[i];
    }
    double result;
    if (calculate(output, result)) {
        cout<<result<<endl;
    } else {
        cout<<"计算错误"<<endl;
    }
}
```