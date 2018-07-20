---
layout: post
mathjax: true
title: 算一・素性测试
category: algorithm
tags: algorithm, primality-test
---

素性测试(Primality test)

## ***前言***
这是一个一时兴起而造的轮子了，也是因为在刷[Project Euler][pe]发现了许许多多需要判断一个数是否为素数的问题，于是去学习了一下几个常用的素性测试的算法。

另一方面距离上一次学习如何写一个判断素数的程序已经是五六年前的事情了，而这五六年以来一直都在用着某种意义上的暴力枚举。或许是时候稍微武装一下自己愚笨的脑壳了，嗯，是时候了。

## 1.从学校出发
素数(Prime number)是指在大于$1$的自然数中，除了$1$和该数自身之外，无法被其他自然数整除的数。例如$5$是素数，但$10$却不是，因为$10$能够被$5$整除。

从定义本身出发，我们可以直接得到一个判断素数的算法。即给定一个数（自然数）$n$，判断是否有$2$到$\sqrt{n}$的数可以整除它，即

{% highlight python %} 
def brute_force(n):
    if all(n % p for p in range(2, int(n**0.5) + 1)):
        return True
    return False
{% endhighlight %}
