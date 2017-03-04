[题目链接](http://www.lydsy.com/JudgeOnline/problem.php?id=3879)
# Description
有一个长度为n的仅包含小写字母的字符串S,下标范围为[1,n].

现在有若干组询问,对于每一个询问,我们给出若干个后缀(以其在S中出现的起始位置来表示),求这些后缀两两之间的LCP(LongestCommonPrefix)的长度之和.一对后缀之间的LCP长度仅统计一遍.

# Solution

首先可以想出在后缀树上dp, 不过直接dp会超时, 所以需要构建 Suffix Virtual Tree(SvT)

总的来说就是一道码题, 不过也暴露出了我指针与数组衔接时不熟练的问题.

``` c++
#include <stdio.h>
#include <string.h>

#include <algorithm>

using namespace std;

char getc()
{
	static const int len = 1 << 15;
	static char buf[len], *s, *t;
	if (s == t)
	{
		t = (s = buf) + fread(buf, 1, len, stdin);
		if (s == t)
			return EOF;
	}
	return *s++;
}

void read(register int &x)
{
	char ch = getc();
	x = 0;
	while (ch < '0')
		ch = getc();
	while (ch >= '0')
		x = (x << 1) + (x << 3) + ch - '0', ch = getc();
}

void read(char *s)
{
	char ch = getc();
	while (ch < 'a')
		ch = getc();
	while (ch >= 'a')
		*s++ = ch, ch =getc();
	*s = 0;
}

typedef long long LL;

const int maxn = 1e6 + 10;
const int log = 23;

int n, m, cnt;
char s[maxn];

struct edge
{
	int to;
	edge *next;
	edge() {}
	edge(int to, edge *next) :to(to), next(next) {}
	void* operator new(size_t);
}*head[maxn], emem[maxn], *EDGEP = emem;

void* edge :: operator new(size_t)
{
	return EDGEP++;
}

void add(int x, int y)
{
	head[x] = new edge(y, head[x]);
}

int dis[maxn];
int end[maxn];
bool leaf[maxn];

struct SAM
{
	SAM *ch[26], *fa;
	int len, id;
	bool v;
	SAM(){};
	SAM(int x) :id(x) {}
	void insert(int x);
 	void* operator new(size_t);
}*root = new SAM(++cnt), mem[maxn], *SAMP = mem;

void* SAM :: operator new(size_t)
{
	return ++SAMP;
}

void SAM :: insert(int x)
{
	static SAM *last = this;
	SAM *p = last, *np = new SAM(++cnt);
	dis[np->id] = np->len = p->len + 1;
	while (p && !p->ch[x])
		p->ch[x] = np, p = p->fa;
	if (!p)
		np->fa = this;
	else
	{
		SAM *q = p->ch[x];
		if (p->len + 1 == q->len)
			np->fa = q;
		else
		{
			SAM *nq = new SAM(++cnt);
			*nq = *q;
			nq->id = cnt;
			dis[nq->id] = nq->len = p->len + 1;
			np->fa = q->fa = nq;
			while (p && p->ch[x] == q)
				p->ch[x] = nq, p = p->fa;
		}
	}
	last = np;
 	end[np->len] = np->id;
	leaf[np->id] = true;
}

int dep[maxn];
int pos[maxn];
int fa[log + 1][maxn];

void dfs(int x)
{
	static int cnt;
	pos[x] = ++cnt;
	for (int i = 1; i <= log; i++)
		fa[i][x] = fa[i - 1][fa[i - 1][x]];
	for (edge *p = head[x]; p; p = p->next)
	{
		fa[0][p->to] = x;
		dep[p->to] = dep[x] + 1;
		dfs(p->to);
	}
}

LL ans = 0;

int size[maxn];
int v[maxn];
int T;

void dp(int x, int F)
{
	size[x] = v[x] == T;
	for (edge *p = head[x]; p; p = p->next)
		dp(p->to, x);
	for (edge *p = head[x]; p; p = p->next) 
		(ans += size[x] * 1ll * size[p->to] * dis[x]), 
		size[x] += size[p->to];
	head[x] = NULL;
}

inline bool cmp(int x, int y)
{
	return pos[x] < pos[y];	
}

int top;
int q[3000010];
int stack[3000010];

inline int getfa(int x, int y)
{
	for (int i = 0; i <= log; i++)
		if (y & (1 << i))
			x = fa[i][x];
	return x;
}

inline int LCA(int x, int y)
{
	if (dep[x] < dep[y])
		swap(x, y);
	x = getfa(x, dep[x] - dep[y]);
	if (x == y)
		return x;
	for (int i = log; ~i; i--)
		if (fa[i][x] ^ fa[i][y])
			x = fa[i][x],
			y = fa[i][y];
	return fa[0][x];
}

int main()
{
	read(n);
	read(m);
	read(s);
	for (int i = n - 1; i >= 0; i--)
		root->insert(s[i] - 'a');
	for (int i = 2; i <= cnt; i++)
		add(mem[i].fa->id, mem[i].id);
	dep[1] = 1;
	dfs(1);
	memset(head, 0, sizeof(head));
	while (m--)
	{
		++T;
		EDGEP = emem;
		int t;
		read(t);
		for (int i = 1; i <= t; i++)
			read(q[i]), v[q[i] = end[n - q[i] + 1]] = T;
		sort(q + 1, q + t + 1, cmp);
		stack[top = 1] = 1;  
        for(int i = 1; i <= t; i++)  
        {  
			if (q[i] == q[i - 1])
				continue;
            int lca = LCA(stack[top], q[i]); 
            while (dep[lca] < dep[stack[top]])  
            {  
                if(dep[stack[top - 1]] <= dep[lca])  
                {  
                    int last = stack[top--];  
                    if(stack[top] != lca)
						stack[++top] = lca;  
                    add(lca, last);
                    break;  
                }  
                add(stack[top - 1], stack[top]);
				top--;  
            }  
            if(stack[top] != q[i])
				stack[++top] = q[i];  
        }  
        while (top > 1)
			add(stack[top - 1], stack[top]), top--; 
		ans = 0; 
		dp(1, 1);
		printf("%lld\n", ans);
	}
}
```
