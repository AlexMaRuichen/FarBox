---
date: 2017-04-10 08:58
status: public
title: 'BZOJ 3771 Triple'
---

[题目链接](http://www.lydsy.com/JudgeOnline/problem.php?id=3771)

# Description

给你$N$个物品的权值, 求在他们中取出$1\sim 3$个可能构成的权值和方案数

# Solution

首先生成函数

$F(x) = \sum \limits_{a_i}x^{a_i}$

设$G(x)$为取两个相同元素的生成函数, $H(x)$为取三个相同元素的生成函数

$G(x) = \sum \limits_{a_i} x^{2a_i}$

$H(x) = \sum \limits_{a_i} x^{3a_i}$

则答案为

$ans(x) = \frac{F^3(x) - 3F(x)G(x)+2H(x)}{3!} + \frac{F^2(x) - G(x)}{2!}+\frac{F(x)}{1!}$

解释一下第一个分数的容斥

$F^3(x)$表示任取三个函数的方案数, 由于顺序有重复所以除以$3!$

$3F(x)G(x)$表示有两个相同的方案数, 减去个数重复的

最后加回来重复的三个相同的方案数

``` c++
#include <stdio.h>
#include <math.h>
 
#include <algorithm>
 
template <typename T> inline bool tense(T &a, const T &b)
{
    return b < a ? a = b, true : false;
}
 
template <typename T> inline bool relax(T &a, const T &b)
{
    return a < b ? a = b, true : false;
}
 
using namespace std;
 
const int maxn = 1 << 17;
 
struct cp
{
    double x, y;
    cp() {}
    cp(double x, double y) :x(x), y(y) {}
};
 
cp operator + (const cp &a, const cp &b)
{
    return cp(a.x + b.x, a.y + b.y);
}
 
cp operator - (const cp &a, const cp &b)
{
    return cp(a.x - b.x, a.y - b.y);
}
 
cp operator * (const cp &a, const cp &b)
{
    return cp(a.x * b.x - a.y * b.y, a.x * b.y + a.y * b.x);
}
 
void DFT(cp *a, int len, int type)
{
    for (int i = 0, t = 0; i < len; i++)
    {
        if (t < i)
            swap(a[i], a[t]);
        for (int j = len >> 1; (t ^= j) < j; j >>= 1);
    }
    for (int i = 2; i <= len; i <<= 1)
    {
        cp wn = cp(cos(2 * M_PI * type / i), sin(2 * M_PI * type / i));
        for (int j = 0; j < len; j += i)
        {
            cp w(1, 0), t;
            for (int k = 0; k < (i >> 1); k++, w = w * wn)
            {
                t = a[j + k + (i >> 1)] * w;
                a[j + k + (i >> 1)] = a[j + k] - t;
                a[j + k] = a[j + k] + t;
            }
        }
    }
    if (type < 0)
        for (int i = 0; i < len; i++)
            a[i].x /= len;
}
 
cp a[maxn];
cp b[maxn];
cp c[maxn];
cp d[maxn];
 
int main()
{
    int n;
    scanf("%d", &n);
    int mx = 0;
    for (int i = 0, x; i < n; i++)
    {
        scanf("%d", &x);
        a[x].x = 1;
        b[x << 1].x = 1;
        c[(x << 1) + x].x = 1;
        relax(mx, (x << 1) + x);
    }
    int len = 1;
    for (; len <= mx; len <<= 1);
    DFT(a, len, 1);
    DFT(b, len, 1);
    DFT(c, len, 1);
    for (int i = 0; i < len; i++)
    {
        static cp div6(1.0 / 6.0, 0), div2(1.0 / 2.0, 0), mul3(3, 0), mul2(2, 0);
        d[i] = d[i] + a[i] 
                    + (a[i] * a[i] - b[i]) * div2 
                    + (a[i] * a[i] * a[i] - mul3 * a[i] * b[i] + mul2 * c[i]) * div6;
    }
    DFT(d, len, -1);
    for (int i = 0, x; i < len; i++)
        if ((x = int(d[i].x + 0.5)))
            printf("%d %d\n", i, x);
}
```