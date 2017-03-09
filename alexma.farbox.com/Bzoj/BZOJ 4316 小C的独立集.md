[题目链接] (http://www.lydsy.com/JudgeOnline/problem.php?id=4316)

# Description

图论小王子小C经常虐菜，特别是在图论方面，经常把小D虐得很惨很惨。
这不，小C让小D去求一个无向图的最大独立集，通俗地讲就是：在无向图中选出若干个点，这些点互相没有边连接，并使取出的点尽量多。
小D虽然图论很弱，但是也知道无向图最大独立集是npc，但是小C很仁慈的给了一个很有特点的图： 图中任何一条边属于且仅属于一个简单环，图中没有重边和自环。小C说这样就会比较水了。
小D觉得这个题目很有趣，就交给你了，相信你一定可以解出来的。

# Solution

仙人掌最大独立集

先构建圆方树, 之后可以拆成树和环两部分处理

树: $f[0/1][x]$ 表示选/不选x这个点时, x子树中的最大独立集

$f[0][x] = \sum \limits_{p \in son_x} max(f[0][p], f[1][p])$

$f[1][x] = \sum \limits_{p \in son_x} f[0][p]$

环: 首先拆成一条链, 之后先选/不选链首, 得到不选链尾的值赋给不选链首, 再强制不选链首, 得到选链尾的值赋给选链首

``` c++
#include <stdio.h>
 
#include <algorithm>
 
using namespace std;
 
const int maxn = 1.2e5 + 10;
const int inf = ~0u >> 2;
 
struct edge
{
    int to;
    edge *next;
    edge() {}
    edge(int to, edge *next) :to(to), next(next) {}
    void* operator new(size_t);
}*old[maxn], *head[maxn], mem[maxn << 1];
 
void* edge :: operator new(size_t)
{
    static edge *p = mem;
    return p++;
}
 
void oadd(int x, int y)
{
    old[x] = new edge(y, old[x]);
    old[y] = new edge(x, old[y]);
}
 
void add(int x, int y)
{
    head[x] = new edge(y, head[x]);
}
 
int cnt, top;
int dep[maxn];
int low[maxn];
int stack[maxn];
 
void dfs(int x, int fa)
{
    dep[x] = low[x] = dep[fa] + 1;
    stack[++top] = x;
    for (edge *p = old[x]; p; p = p->next)
        if (p->to ^ fa)
        {
            if (!dep[p->to])
            {
                dfs(p->to, x);
                low[x] = min(low[x], low[p->to]);
                if (low[p->to] > dep[x])
                    add(x, p->to);
            }
            else if (dep[p->to] < dep[x])
            {
                ++cnt;
                low[x] = min(low[x], dep[p->to]);
                int now = 0, pos = top;
                while ((now = stack[pos--])^ p->to)
                    add(cnt, now);
                add(p->to, cnt);
            }
        }
    --top;
}
 
void print(int x, int fa)
{
    printf("%d:", x);
    for (edge *p = head[x]; p; p = p->next)
        printf("%d ", p->to);
    puts("");
    for (edge *p = head[x]; p; p = p->next)
        print(p->to, x);
}
 
int n, m;
int f[2][maxn];
int g[2][maxn];
 
void dp(int x, int fa)
{
//  printf("%d %d\n", x, fa);
    if (x <= n)
    {
        f[0][x] = 0, f[1][x] = 1;
        for (edge *p = head[x]; p; p = p->next)
        {
            dp(p->to, x);
            if (p->to <= n)
                f[0][x] += max(f[0][p->to], f[1][p->to]),
                f[1][x] += f[0][p->to];
        }
    }
    else
    {
        for (edge *p = head[x]; p; p = p->next)
            dp(p->to, x);
        int top = 0;
        g[0][++top] = f[0][fa];
        g[1][top] = f[1][fa];
//      printf("%d ", fa);
        for (edge *p = head[x]; p; p = p->next)
//          printf("%d ", p->to),
            g[0][++top] = f[0][p->to],
            g[1][top] = f[1][p->to];
//      puts("");
        for (int i = top - 1; i; i--)
            g[0][i] += max(g[0][i + 1], g[1][i + 1]),
            g[1][i] += g[0][i + 1];
        f[0][fa] = g[0][1];
        for (int i = 1; i < top ; i++)
            g[0][i] -= max(g[0][i + 1], g[1][i + 1]),
            g[1][i] -= g[0][i + 1];
        g[1][top] = -inf;   
        for (int i = top - 1; i; i--)
            g[0][i] += max(g[0][i + 1], g[1][i + 1]),
            g[1][i] += g[0][i + 1];
        f[1][fa] = g[1][1];
    }
}
 
int main()
{
    scanf("%d%d", &n, &m);
    for (int i = 1, x, y; i <= m; i++)
        scanf("%d%d", &x, &y),
        oadd(x, y);
    cnt = n;
    dfs(1, 1);
//  print(1, 1);
    dp(1, 1);
    printf("%d\n", max(f[0][1], f[1][1]));
}
``` 