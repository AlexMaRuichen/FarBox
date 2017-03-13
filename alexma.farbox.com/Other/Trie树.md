---
date: 2017-03-13 18:45
status: public
title: Trie树
---

# Description

　字母(Trie)树是一个表示一个字符串集合中所有字符串的前缀的数据结构, 其有如下特征:

　　1.树的每一条边表示字母表中的一个字母 

　　2.树根表示一个空的前缀 

　　3.树上所有其他的节点都表示一个非空前缀, 每一个节点表示的前缀为树根到该节点的路径上所有字母依次连接而成的字符串 

　　4.一个节点的所有出边（节点到儿子节点的边）中不存在重复的字母 
　　　　 
　　现在Matej手上有N个英文小写字母组成的单词, 他想知道, 如果将这N个单词中的字母分别进行重新排列, 形成的字母树的节点数最少是多少

# Solution 

状压dp, 枚举合并哪两棵子树, 用记忆化搜索优化

枚举子集的方法值得学习

$subset = set \cap (subset - 1)$

``` c++
#include <stdio.h>

#include <algorithm>

using namespace std;


const int maxn = 1e6 + 10;
const int inf = 1e9;

int n;
char str[maxn], *s;
int mem[(1 << 16) + 10];

struct data
{
	int cnt[26];
}d[(1 << 16) + 10];

int dp(int x)
{
	if (mem[x])
		return mem[x];
	int &tmp = mem[x], ans;
	tmp = ans = inf;
	for (int i = x & (x - 1); i; i = (i - 1) & x)
	{
		tmp = min(tmp, dp(i) + dp(x - i));
		if (ans == inf)
		{
			ans = 0;
			for (int k = 0; k < 26; k++)
				ans += (d[x].cnt[k] = min(d[i].cnt[k], d[x - i].cnt[k]));
		}
	}
	if (ans == inf)
		ans = 0;
	return tmp = tmp - ans;
}

int main()
{
	freopen("trie.in", "r", stdin);
	freopen("trie.out", "w", stdout);
	
	scanf("%d", &n);
	for (int i = 0, cnt; i < n; i++)
	{
		cnt = 0;
		scanf("%s", str);
		s = str;
		while(*s)
			++cnt, ++d[1 << i].cnt[*s++ - 'a'];
		mem[1 << i] = cnt;
	}
	printf("%d\n", dp((1 << n) - 1) + 1);
}
```