---
date: 2017-03-09 19:03
status: public
title: 'BZOJ 1023 cactus 仙人掌图'
---

[题目链接] (http://www.lydsy.com/JudgeOnline/problem.php?id=1023)

# Description

如果某个无向连通图的任意一条边至多只出现在一条简单回路(simple cycle)里, 我们就称这张图为仙人掌图(cactus). 所谓简单回路就是指在图上不重复经过任何一个顶点的回路. 

![](http://www.lydsy.com/JudgeOnline/images/1023/1.jpg)

举例来说, 上面的第一个例子是一张仙人图, 而第二个不是——注意到它有三条简单回路：(4, 3, 2, 1, 6, 5, 4)、(7, 8, 9, 10, 2, 3, 7)以及(4, 3, 7, 8, 9, 10, 2, 1, 6, 5, 4), 而(2, 3)同时出现在前两
个的简单回路里. 另外, 第三张图也不是仙人图, 因为它并不是连通图. 显然, 仙人图上的每条边, 或者是这张仙人图的桥(bridge), 或者在且仅在一个简单回路里, 两者必居其一. 定义在图上两点之间的距离为这两点之间最短路径的距离. 定义一个图的直径为这张图相距最远的两个点的距离. 现在我们假定仙人图的每条边的权值都是1
, 你的任务是求出给定的仙人图的直径. 

# Solution

仙人掌求直径

首先建出圆方树, 圆点直接维护最长链次长链转移即可, 方点需要用单调队列维护这个点是从链的那一侧走过来的

注意仙人掌的点数最好开到$2M$

仙人掌好难啊

``` c++
#include <stdio.h>
#include <string.h>

#include <algorithm>
#include <deque>

using namespace std;

const int maxn = 200010;

struct edge
{
	int to;
	edge *next;
	edge() {}
	edge(int to, edge *next) :to(to), next(next) {}
	void* operator new(size_t);
}*head[maxn], *old[maxn], mem[maxn << 1], *p = mem;

void* edge :: operator new(size_t)
{
	return p++;
}

void add(int x, int y)
{
	head[x] = new edge(y, head[x]);
}

void oadd(int x, int y)
{
	old[x] = new edge(y, old[x]);
	old[y] = new edge(x, old[y]);
}

int n, m;
int cnt, top;
int low[maxn];
int dep[maxn];
int stack[maxn];

void dfs(int x, int fa)
{
	stack[++top] = x;
	dep[x] = low[x] = dep[fa] + 1;
	for (edge *p = old[x]; p; p = p->next)
		if (p->to ^ fa)
		{
			if (!dep[p->to])
			{
				dfs(p->to, x);
				low[x] = min(low[p->to], low[x]);
				if (low[p->to] > dep[x])
					add(x, p->to);
			} 
			else if (dep[p->to] < dep[x])
			{
				++cnt;
				int now, pos = top;
				low[x] = min(low[x], dep[p->to]);
				while ((now = stack[pos--]) ^ p->to)
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

int f[maxn];
int ans;
deque <int> q;

void dp(int x)
{
	f[x] = 0;
	for (edge *p = head[x]; p; p = p->next)
		dp(p->to);
	if (x > n)
	{
		q.clear();
		top = 0;
		for (edge *p = head[x]; p; p = p->next)
			stack[top++] = p->to;
		int hf = top + 1 >> 1;
		for (int i = 0; i < hf; i++)
		{
			while (!q.empty() && f[stack[q.back()]] + q.back() < f[stack[i]] + i)
				q.pop_back();
			q.push_back(i);
		}
		for (int i = 0, j = hf; i < top; i++, j = (j == top ? 0 : j + 1))
		{
			if (j ^ top)
			{
				while (!q.empty() && 
				f[stack[q.back()]] + (q.back() < i? q.back() + top + 1 - i : q.back() - i) < 
				f[stack[j]] + (j < i? j + top + 1 - i: j - i))
					q.pop_back();
				q.push_back(j);
			}
			if (q.front() == i)
				q.pop_front();
			if (!q.empty())
				ans = max(ans, f[stack[i]] + f[stack[q.front()]] + (q.front() > i ? q.front() - i : q.front() + top + 1 - i));
		}
		for (int i = 0; i < hf; i++)
			f[x] = max(f[x], f[stack[i]] + i);
		for (int i = hf; i < top; i++)
			f[x] = max(f[x], f[stack[i]] + top - 1 - i);
	}
	else
	{
		int mx1 = 0, mx2 = 0;
		for (edge *p = head[x]; p; p = p->next)
			if (f[p->to] + 1 > mx1)
				mx2 = mx1,
				mx1 = f[p->to] + 1;
			else if(f[p->to] + 1 > mx2)
				mx2 = f[p->to] + 1;
		ans = max(ans, mx1 + mx2);
		f[x] = mx1;
	}
}

int main()
{
	scanf("%d%d", &n, &m);
	cnt = n;
	while (m--)
	{
		int k, x, last;
		scanf("%d%d", &k, &last);
		--k;
		while (k--)
			scanf("%d", &x),
			oadd(last, x),
			last = x;
	}
	dfs(1, 1);
	dp(1);
	printf("%d\n", ans);
}
```