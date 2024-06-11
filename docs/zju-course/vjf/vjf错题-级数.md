---
tille: 微积分复习-级数错题集
date: 2024-6-12 01:00:00
tags: 
- 微积分
- 期末补天
math: true
---

## 正项级数

1. 判断下列级数的敛散性$\sum_{n=1}^{\infty}=\left[ 1 - \frac{\ln n}{n} \right]^n$

<br>

2. 讨论下面级数的敛散性$\sum_{n=1}^{\infty}=\frac{1}{x(\ln x)^p (\ln lnx)^q}$

<br>

3. 设$\sum_{n=1}^{\infty}={a_n}$为收敛的正项级数，且${a_n}$单调，证明$\lim_{n\to\infty}na_n=0$

## 一般项级数

1. 设$U_n=\int_{n\pi}^{(n+1)\pi}=\frac{\sin(x)}{\sqrt x}dx$,证明级数$\sum_{n=0}^{\infty}U_n$收敛

<br>

2. [书写不规范]设$f(x)$在$x=0$的某邻域内存在二阶连续导数，且$$\lim_{x \to 0}\frac{f(x)}{x}=a(a>0)$$,讨论级数:

$$\sum_{n=1}^{\infty}f(\frac{1}{n})$$

$$\sum_{n=1}^{\infty}(-1)^nf(\frac{1}{n})$$的敛散性

## 幂函数

1. 求下列幂函数的收敛域（主要是判断端点）

(1)$\sum_{n=1}^{\infty}\frac{n!(2x-1)^{2n}}{3n!}$

(2)$\sum_{n=1}^{\infty}(1+\frac{1}{n})^{n^2}x^n$

## 计算和函数

1. $\sum_{n=1}^{\infty}\frac{n^2}{2^n}$

不要用高中的方法写

2. 设$f(x)>0$,且在$[0,2]$上连续，令$a_n=\int_{0}^{2}x^2f(x)dx$,求幂级数$\sum_{n=0}^{\infty}\frac{x^n}{a_n}$的收敛域

## 把函数展开成幂函数

1. $f(x)=acrtan(\frac{1-x^2}{1+x^2})$

<br>

2. 将$f(x)=\frac{\ln (x+\sqrt {1+x^2} )}{\sqrt{1+x^2}}$展开成幂级数

<br>

## 傅里叶级数

1. （奇延拓偶延拓）应当如何把给定在$(0,\pi/2)$上的可积函数延拓到区间$(-\pi,\pi)$内，使得它在$(-\pi,\pi)$的傅里叶级数为
(1)$\sum_{n=1}^{\infty}a_{2n-1}cos(2n-1)x$
(2)$\sum_{n=1}^{\infty}b_{2n-1}sin(2n-1)x$

关键在于补充$(\pi/2,\pi)$的部分

2. 注意事项：把函数展开成为傅里叶级数的时候，不要丢掉开头的$a_0$项

3. $f(x)=arcscos(cos(x))$的傅里叶级数为:

(利用$x\in(0,\pi)$时，$arccos(cos(x))=x$来写)
