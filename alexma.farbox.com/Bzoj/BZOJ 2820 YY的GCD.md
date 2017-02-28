[题目链接](http://www.lydsy.com/JudgeOnline/problem.php?id=2820)

# Description
神犇YY虐完数论后给傻×kAc出了一题给定$N,M$,
求$1\le x \le N$, $1\le y\le M$,且$gcd(x, y)$为质数的$(x, y)$
有多少对

kAc这种傻×必然不会了, 于是向你来请教……多组输入

# Solution

朴素的想法是枚举所有的素数之后莫比乌斯反演计算

$\sum \limits_{Prime\text{ }p}^{min(n, m)} \sum \limits_{d}^{min(n, m)} \mu(d) \lfloor \frac{n}{pd} \rfloor \lfloor \frac{m}{pd}\rfloor$

设$T = pd$, 则

$\sum \limits_{T = 1}^{min(n,m)} \lfloor\frac{n}{T}\rfloor\lfloor\frac{m}{T}\rfloor\sum\limits_{p\mid T}\mu(\frac{T}{p})$

那么我们只需要提前计算出所有$\sum\limits_{p\mid T}\mu(\frac{T}{p})$即可, 计算方法就是暴力枚举每个素数, 均摊复杂度$O(N)$

``` c++
#include <stdio.h>
 
#include <algorithm>
 
using namespace std;
 
const int maxn = 1e7 + 10;
const int N = 1e7;
 
typedef long long LL;
 
int mu[maxn];
bool v[maxn];
int prime[670000];
int sum[maxn];
 
void linear_shaker()
{
    mu[1] = 1;
    for (int i = 2; i <= N; i++)
    {
        if (!v[i])
            prime[++prime[0]] = i, mu[i] = -1;
        for (int j = 1; j <= prime[0] && i * prime[j] <= N; j++)
        {
            v[i * prime[j]] = true;
            if(i % prime[j] == 0)
                break;
            mu[i * prime[j]] = -mu[i];
        }
    }
    for (int i = 1; i <= prime[0]; i++)
        for (int j = prime[i]; j <= N; j += prime[i])
            sum[j] += mu[j / prime[i]];
    for (int i = 1; i <= N; i++)
        sum[i] += sum[i - 1];
}
 
LL calc(int x, int y)
{
    LL ans = 0;
    for (int i = 1, last; i <= x && i <= y; i = last + 1)
    {
        last = min(x / (x / i), y / (y / i));
        ans += (x / i) * 1ll * (y / i) * (sum[last] - sum[i - 1]);
    }
    return ans;
}
 
int main()
{
    linear_shaker();
    int T, n, m;
    scanf("%d", &T);
    while (T--)
    {
        scanf("%d%d", &n, &m);
        printf("%lld\n", calc(n, m));
    }
}

```