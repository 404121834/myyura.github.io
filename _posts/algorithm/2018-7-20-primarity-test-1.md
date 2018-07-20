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
    if all(n % p for p in range(2, int(n ** 0.5) + 1)):
        return True
    return False
{% endhighlight %}
我选择使用Python来描述（实现）算法，一来是因为某种意义上其比较贴近于自然语言，二来我个人并不太喜欢用伪代码来描述一个算法，我更倾向于实现---许多伪代码都有这样一个毛病，它们往往将许多核心结构一带而过，使得阅读者将其复现的难度很大。

且很快就能注意到$2$和$3$的倍数是相当好剔除的---令$k$为一个自然数，则显然$6k$，$6k + 2$和$6k + 4$能够被$2$整除，且有$6k + 3$能够被$3$整除，因而一个改进的枚举算法已经呼之欲出了，即
{% highlight python %} 
def brute_force_6k(n):
    if n <= 3:
        return True
    elif n % 2 == 0 or n % 3 == 0:
        return False
    else:
        for p in range(5, int(n ** 0.5) + 1, 6):
            if n % p == 0 or n % (p + 2) == 0:
                return False
    return True
{% endhighlight %}
我们通常把这个做法叫做$6k$优化。很显然这两个布鲁特福斯算法的复杂度均为$\matcal{O}(\sqrt{n})$。

除此之外，相信大家还在课堂上学习过埃拉托斯特尼筛法（sieve of Eratosthenes）用以求得一个区间内的所有素数。它的基本思想非常简单---每个素数的各个倍数一定是一个合数，即有
{% highlight python %}
def eratosthenes_sieve(n):
    is_prime = [True for _ in range(n + 1)]
    for p in range(2, int(n ** 0.5) + 1):
        if is_prime[p]:
            for i in range(p * 2, n + 1, p):
                is_prime[i] = False
    return [p for p in range(2, n + 1) if is_prime[p] == True]
{% endhighlight %}
让我们来一起分析一下埃拉托斯特尼筛法的复杂度，可以观察到，对于每一个素数，内循环会执行$\floor{n/p}$次，因而问题转变为了求解以下和式

$$
n \sum_{\text{p is a prime}} \frac{1}{p}
$$

它看起来似乎和调和级数非常类似，让我们回想起调和级数的定义

$$
1 + \frac{1}{2} + \frac{1}{3} + \cdots
$$

由[算数基本定理][ftoa]可知，调和级数可以写为如下形式

$$
(1 + \frac{1}{2} + \frac{1}{4} + \cdots)(1 + \frac{1}{3} + \frac{1}{9} + \cdots)(1 + \frac{1}{5} + \cdots) \cdots
$$

即

$$ 
\prod_{\text{p is prime}} \sum_{k = 0}^{\infty} \frac{1}{p^k} = \prod_{\text{p is prime}} \frac{1}{1 - \frac{1}{p}}
$$

似乎出现了我们想要的东西，尽管还有些差距，因为我们希望得到一个和式而不是连乘。幸运的是，我们有一种方法能将这个连乘变为和式---取对数，则有（注意到最后一步我们使用了[对数分布][ld]的结论）

$$
\begin{align}
\ln (\sum_n \frac{1}{n}) &= \ln \prod_{\text{p is prime}} \frac{1}{1 - \frac{1}{p}} \\
&= - \sum_{\text{p is prime}} {\ln (1-\frac{1}{p})}
&= \sum_{\text{p is prime}} \frac{1}{p} + \sum_{\text{p is prime}} \frac{1}{2p^2} + \sum_{\text{p is prime}} \frac{1}{3p^3} + \cdots \\
\end{align}

即有

$$
\ln (\sum_n \frac{1}{n}) ~ \sum_{\text{p is prime}} \frac{1}{p}
$$

因而我们能知道，埃拉托斯特尼筛法的时间复杂度是$\mathcal{O}(n\log \log n)$。


[pe]: https://projecteuler.net/
[ftoa]: https://en.wikipedia.org/wiki/Fundamental_theorem_of_arithmetic
[ld]: https://en.wikipedia.org/wiki/Logarithmic_distribution
