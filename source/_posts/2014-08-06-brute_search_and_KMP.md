---
layout: post
title:  "子字符串查找——暴力查找与KMP算法"
date:   2014-08-06 14:03:20
categories:   algorithms
---


在这篇文章里，主要分析子字符串查找的两种算法：暴力查找和KMP算法。

###1.暴力查找
子字符串查找最简单的方法就是在需要查找的字符串(text)中模式(pattern)可能出现匹配的任何地方检查匹配是否存在。代码如下：
``` java
public static int search(String pat, String text) {
	int M = pat.length();
	int N = text.length();
	for (int i = 0; i <= N - M; i++) {
		int j;
		for (j = 0; j < M; j++) {
			if (text.charAt(i) ！= pat.charAt(j))
				break;
		}
		if (j == M) 
			return i;	//找到匹配
	}
	return -1;		//没找到匹配，返回-1
}
```

在程序中，我们使用了一个指针`i`来跟踪文本，一个指针`j`来跟踪模式。对每一个可能的`i(i<=M-N)`,将文本和模式进行匹配，如果模式的字符和文本的字符匹配，则将指针`j`加`1`。如果最后指针`j`为`M`，那么说明在位置`i`上文本和字符串是匹配的，返回位置`i`。否则返回`-1`，说明没有找到匹配。

这种朴素的查找方法思路简单，也很容易实现，但是时间复杂度高，最坏情况下，时间复杂度约为`MN`,所以我们想追求速度更快的子字符窜查找算法。

在暴力查找算法中，在每个位置`i`上，如果模式没有匹配，我们就直接把指针`i`回溯到`i+1`,在这种情况下，我们会浪费掉我们已经匹配过的`j`个字符的信息，所以，KMP算法就是通过利用这几个字符串的信息，减少指针`i`的回溯，而达到查找的时间复杂度为`O(N)`的目的的。

###2.KMP算法
KMP算法是Knuth-Morris-Pratt算法的缩写。

*在学习这个算法时，我是先看的《Algorithms》，但是上面讲的不是很容易懂，所以又看了《算法导论》，才把它看明白，所以以下都是基于算导，而且图也是直接截的算导的图。*


在说KMP算法之前，我们先约定两个定义：

> 1.如果对某个字符串`y`有`x=ωy`，则称字符串`ω`是`x`的前缀,记为`w<x`

> 2.如果对某个字符串`y`有`x=yω`，则称字符串`ω`是`x`的后缀,记为`w>x`

现在我们来看怎么利用一次失败的匹配中的`j`个已知字符。

如图一中`(a)`所示，在这次匹配中，模式`P`相对于文本`T`的偏移量为`s`，在模式的`q+1`位置处，匹配失败。如果按照暴力查找中的思路，我们会将文本`T`的指针指向`s+1`处，但是我们观察模式`P`,我们会发现不需要把文本的指针回退`q`，如`(b)`所示，我们只要将指针回退到`s'=s+2`。

接下来我们要找出指针回退的规律，同时我们用一个辅助数组`π[1..m]`,其中`m`为模式长度,并且约定如下：
> 已知一个模式`P[1..m]`,模式`P`的前缀函数是函数`π:{1,2,...,m}→{0,1，...,m}`，满足`π[q]=max{k:k<q,且Pk为Pq的后缀}`

> 简单点说就是假设`Pk`是`Pq`的一个前缀，那么`π[q]`为同时为`Pq`的前缀和缀的Pk的最长长度，例如模式`ABAB`,那么`AB`既是模式的前缀，同时也是模式的后缀，而且我们也找不出另外一个更长模式的子字符串既是模式的前缀又是模式的后缀。所以`π[4]=2`(因为必须是`Pk`必须是`Pq`的子字符串)。


所以假设我们已经知道了前缀数组`π`，那么我们就可以根据当前的`q`值知道下一个可能的有效偏移`s'`。如图中`(b)`所示，`π[5]=3`，而`P3`既是`P5`的前缀同时也是`T[s+1..s+5]`的后缀(因为`P[1..5]`和`T[s+1..s+5]`匹配),所以我们可以知道下一个可能的有效偏移为`s'=s+(q-π[q])=s+(5-3)=s+2`。

![kmp1](/img/kmp-1.png)<br>图一

图二中，我们可以看到关于模式`ababaca`的完整前缀函数`π`：

![kmp1](/img/kmp-2.png)<br>图二

在图二中，我们能看到一个`π*`数组，我们也给它一个定义
> 设P是长度为`m`的模式，其前缀函数为`π`，对`q=1,2..m`,有`π*[q]={k: k<q且Pk为Pq的前缀}`。例如图二中`π*[5]={3,1,0}`。

关于`π*`数组，我们还有一条性质在求`π`的时候能够用到
> 若x=max{k∈π*[q-1], 且P[q+1]=P[x+1]},那么π[q]=k+1，否则π[q]=0

> 其实这很好理解，如果我们能够在π*[q-1]数组中找到这么一个最大的x，使得 P[q+1]=P[x+1],那么π[q]=k+1。如果我们找不到这么一个x，那么π[q]=0

那么现在的问题是怎么求前缀数组呢，我们先给出算导上的伪代码：

```
	Compute-prefix-function(P)
	1 	m = P.length
	2 	let π[1..m] be a new array
	3 	π[1] = 0
	4 	k = 0
	5 	for q=2 to m
	6 		while k>0 and P[k+1] != P[q]
	7			k=π[k]
	8		if P[k+1] == P[q]
	9 			k=k+1
	10		π[q] = k
	11	return π
```

我们接下来一行行分析

> 1~2行： 对数组进行初始化
>
> 3~4行： 我们可以知道`π[1]=0`,同时我们令`k=π[q-1]=0`,这与循环结束后的条件保持一致
>
> 6~7行： 这里我们就是在π*[q-1]数组中寻找这么一个k值，使得P[k+1]=P[q],如果我们找到了这么一个k或者k为零，结束循环

> 8~9行：如果我们在上两行找到的k值满足P[k+1]=P[q]，那说明我们找到了这么一个k值，那么π[q]=k+1；如果不满足条件，那说明k=0，而且P[1]!=P[q]，则π[q]=0

到这里，我们就完成了前缀数组的计算。然后我们给出主程序的伪代码：

```
	KMP-Matcher(T, p)
	1	n=T.length
	2	m=P.length
	3	π=Compute-prefix-function(P)		
	4	q=0										//number of characters matched
	5 	for i=1 to n							//scan the text from left to right
	6		while q>0 and P[q+1]!=T[i]
	7			q=π[q]							//next character does not match
	8		if P[q+1]==T[i]
	9			q=q+1							//next character matches	
	10		if q==m								//is all of P matched?			
	11			print "Pattern occurs with shift" i-m	
	12 			q=π[q]							//look for the next match	
```

啦啦啦,KMP算法就这样啦，主要参考的是《algorithms》和《算法导论》,在第一本书中将的不如算导中好（个人感觉），所以推荐看算导。

最后附上Java实现代码：
``` java
public class KMP {
    private String pat;
    private int[] pi;

    public KMP(String pat) {
        this.pat = pat;
        int M = pat.length();
        pi = new int[M];

        pi[0] = 0;
        int k = 0;

        for (int i = 1; i < M; i++) {
            while (k > 0 && pat.charAt(k) != pat.charAt(i))
                k = pi[k-1];

            if (pat.charAt(k) == pat.charAt(i))
                k = k + 1;

            pi[i] = k;
        }
    }

    public void search(String txt) {
        int N = txt.length();
        int M = pat.length();

        int q = 0;
        for (int i = 0; i < N; i++) {
            while (q > 0 && pat.charAt(q) != txt.charAt(i))
                q = pi[q-1];

            if (pat.charAt(q) == txt.charAt(i))
               q = q + 1;

            if (q == M) {
                System.out.println("pattern occurs with shift " + (i - M + 1));
                q = pi[q-1];
            }
        }
    }

    public static void main(String[] args) {
        String pat = "ab";
        String txt = "ababc";
        KMP kmp = new KMP(pat);
        kmp.search(txt);
    }
}
```