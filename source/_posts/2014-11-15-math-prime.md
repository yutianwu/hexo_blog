---
layout: post
title:  "素数"
date:   2014-11-15 11:01:20
categories:   algorithms
math: true
---

### 判断一个数是否为素数

很容易想到，我们只要把$2\rightarrow n-1$遍历一遍，就可以得到一个数是否为素数。但是这样的话时间复杂度将会是O(n).再想想其实我们可以只遍历$2\rightarrow n/2$，但是这样还是$O(n)$.

那么假设d是n的约数，那么n/d也是n的约数，$$n = d * n/d$$;可以看出$min(d, n/d) \leq \sqrt{n}$,所以只要检查$2\rightarrow\sqrt{n}$的所有整数就够了，因此能够在$O(\sqrt{n})$的时间内完成。

``` cpp
bool is_prime(int n) {
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) return false;
    }
    return n != 1; //1既不是素数也不是合数
}
```

### 埃氏筛法

> 给定整数n，请问n以内有多少个素数？

如果对每一个数都进行素性测试，那么复杂度就高了。所以就有了这个解法。

首先，将2到范围内的所有整数写下来。其中最小的数字2是素数。将表中所有2的倍数都划去表中剩余的最小数字是3它不能被更小的数整除，所以是素数。再将表中所有3的倍数都划去依此类推，如果表中剩余的最小数字是m时，m就是素数。然后将表中所有m的倍数都划去。像这样反复操作，就能依次枚举n以内的素数。

``` cpp
const int MAX_N = 10e6;
int prime[MAX_N];
bool is_prime[MAX_N + 1];

int sieve(int n) {
    int p = 0;
    for (int i = 0; i<= n; i++) is_prime[i] = true;
    is_prime[0] = is_prime[1] = false;
    
    for (int i = 2; i <= n; i++) {
        if (is_prime[i]) {
            prime[p++] = i;
            for (int j = 2 * i; j <= n; j += i) is_prime[j] = false;
        }
    }
    return p;
}
```