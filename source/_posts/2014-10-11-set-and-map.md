---
layout: post
title:  "set & map"
date:   2014-10-11 15:34:20
categories:   cpp
---

###1.set

如果需要使用二叉搜索树时，可以不用自己来实现，用STL中的set就可以。

使用set的话必须包含set的头文件。

set的操作如下代码：

``` cpp
#include <iostream>
#include <set>
#include <vector>

using namespace std;

int main() {
    vector<int> ivec;
    for (vector<int>::size_type i = 0; i != 10; ++i) {
        ivec.push_back((int)i);
        ivec.push_back((int)i); // duplicate copies of each number
    }
    
    // iset holds unique elements from ivec
    set<int> iset(ivec.begin(), ivec.end()); //初始化操作，也可以直接申明，而不初始化
    cout << ivec.size() << endl;      // prints 20
    cout << iset.size() << endl;      // prints 10，set中都是唯一的键值
    
    iset.insert(11); //insert操作
    
    set<int>::iterator set_it;
    
    set_it = iset.find(1);     // returns iterator that refers to the element with key == 1
    
    if (set_it != iset.end()) cout<<"found"<<endl;
    else cout<<"not found"<<endl;
    
    set_it = iset.find(12);   // returns iterator == iset.end()
    
    if (set_it != iset.end()) cout<<"found"<<endl;
    else cout<<"not found"<<endl;
    
    cout<<iset.count(1)<<endl;   // returns 1
    cout<<iset.count(12)<<endl;  // returns 0
    
    iset.erase(11);     //erase 删除操作
    
    for (set_it = iset.begin(); set_it != iset.end(); set_it++) {
        cout<<*set_it<<endl;
    }
}
```

###2.map

map 是键－值对的集合。map类型通常可理解为关联数组（associative array）
可使用键作为下标来获取一个值，正如内置数组类型一样。

要使用 map 对象，则必须包含 map 头文件。在定义 map 对象时，必须分别指明键和值的类型（value type）。

	map<string, int> word_count; // empty map from string to int


map 类定义的类型:

	map<K, V>::key_type //在 map 容器中，用做索引的键的类型
	map<K, V>::mapped_type //在 map 容器中，键所关联的值的类型
	map<K, V>::value_type //一个 pair 类型，它的 first 元素具有 const map<K, V>::key_type 类型，而 second 元素则为 map<K, V>::mapped_type 类型

	
``` cpp
#include <iostream>
#include <queue>
#include <set>
#include <vector>
#include <map>


using namespace std;

const int MAX_N = 10000;

int n, L, P;
int A[MAX_N], B[MAX_N];

int main() {
    map<string, int> word_count; // empty map from string to int
    
    // if Anna not already in word_count, inserts new element with value 1
    word_count.insert(map<string, int>::value_type("Anna", 1)); //插入操作
    
    word_count.insert(make_pair("Anna", 4)); //也可以这样
    
    cout<<word_count["Anna"]<<endl; //访问map元素
    
    //对于 map 对象，count 成员的返回值只能是 0 或 1。
    cout<<word_count.count("Anna")<<endl; //return 1

    map<string,int>::iterator it = word_count.find("Anna");//find 成员函数
    if (it != word_count.end())
        cout << it->second <<endl;
    
    word_count.erase("Anna"); //删除元素
    
    cout<<word_count.count("Anna")<<endl; //return 0
    
    
}
```