[题目链接](http://codeforces.com/problemset/problem/587/C)

# Description

给定一颗$N$个节点的无根树,现在将$M$个数插到节点上,每次询问节点$u$到$v$上钱$c$小的数

# Solution

倍增lca

由于询问的c不超过10,可以对每一个点倍增同时维护前10小的值

每次用归并处理两个有序区间,暴力排序效率过低

``` c++
#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include <algorithm>
using namespace std;
char getc()
{
    static const int LEN = 1<<15;
    static char buf[LEN],*S=buf,*T=buf;
    if(S == T)
    {
        T = (S=buf)+fread(buf,1,LEN,stdin);
        if(S == T)return EOF;
    }
    return *S++;
}
int  read()
{
    static char ch;
    static int   D;
    while(!isdigit(ch=getc()));
    for(D=ch-'0'; isdigit(ch=getc());)
        D=(D<<3)+(D<<1)+(ch-'0');
    return D;
}

const int maxn=1e5+10;
const int log=16;
int n,m,q;
struct node
{
	int a[11],size;
	node()
	{
		memset(a,0x3f,sizeof(a));
		size=0;
	}
	void merge(node &x)
	{
		int ptra=0,ptrb=0;
		size=0;
		node y=*this;
		while((y.a[ptra]<=m||x.a[ptrb]<=m)&&size<10)
		{
			if(y.a[ptra]<x.a[ptrb])
				a[size++]=y.a[ptra++];
			else
				a[size++]=x.a[ptrb++];
		}
	}
	void clear()
	{
		memset(a,0x3f,sizeof(a));
		size=0;
	}
}v[20][maxn];
int to[maxn<<1];
int next[maxn<<1];
int head[maxn];
int tot;
void add(int x,int y)
{
	to[++tot]=y;
	next[tot]=head[x];
	head[x]=tot;

	to[++tot]=x;
	next[tot]=head[y];
	head[y]=tot;
}

int fa[20][maxn];
int depth[maxn];
void dfs(int x,int f)
{
	fa[0][x]=f;
	for(int i=1;i<=log;i++)
	{
		fa[i][x]=fa[i-1][fa[i-1][x]];
		v[i][x].merge(v[i-1][x]);
		v[i][x].merge(v[i-1][fa[i-1][x]]);
	}
	for(int i=head[x];i;i=next[i])
		if(to[i]!=f)
		{
			depth[to[i]]=depth[x]+1;
			dfs(to[i],x);
		}
}

int getfa(int x,int k)
{
	for(int i=0;i<=log;i++)
		if((1<<i)&k)
			x=fa[i][x];
	return x;
}

int lca(int x,int y)
{
	if(depth[x]<depth[y])
		swap(x,y);
	x=getfa(x,depth[x]-depth[y]);
	if(x==y)
		return x;
	for(int i=log;i>=0;i--)
		if(fa[i][x]!=fa[i][y])
			x=fa[i][x],y=fa[i][y];
	return fa[0][x];
}
node get(int x, int k)
{
	node ans;
	for(int i=0;i<=log;i++)
		if((1<<i)&k)
		{
			ans.merge(v[i][x]);
			x=fa[i][x];
		}
	return ans;
}

int main()
{
//	freopen("boring.in","r",stdin);
//	freopen("boring.out","w",stdout);
	n=read();
	m=read();
	q=read();
	for(int i=1;i<n;i++)
	{
		static int x,y;
		x=read();
		y=read();
		add(x,y);
	}
	for (int i=1;i<=m;i++)
	{
		static int x;
		static node t;
		x=read();
		t.a[0]=i;
		v[0][x].merge(t);
	}
	dfs(1,1);
	node ans,a,b;
	while (q--)
	{
		static int x,y,z,p,tmp;
		x=read();
		y=read();
		z=read();
		p=lca(x,y);
		ans.clear();
		a=get(x,depth[x]-depth[p]);
		b=get(y,depth[y]-depth[p]+1);
		ans.merge(a);
		ans.merge(b);
		tmp=0;
		while(tmp<z&&ans.a[tmp]<=m)
			tmp++;
		z=tmp;
		printf("%d",z);
		for (int i=0;i<z;i++)
			printf(" %d",ans.a[i]);
		puts("");
	}
}
```

