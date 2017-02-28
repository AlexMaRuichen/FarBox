[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=1432)

# Description

桐桐很喜欢吃棒棒糖。他家处在一大堆糖果店的附近。 但是，他们家的区域经常出现塞车、塞人等情况，这导致他不得不等到塞的车或人走光了他才能去买到他最爱吃的棒棒糖品种。于是，他去找市长帮他修路，使得每两个糖果店之间至少有两条完全不同的路。可是市长经费有限，于是让桐桐找出哪些路被塞住后会使某些糖果店与糖果店间无法到达及最少的修路条数。你能帮助他吃到他最喜爱的糖果吗？ 

注：1-> 3-> 2    和  1-> 3-> 4-> 2  为不完全不同的路，即不符合题意的路         

1-> 3-> 4-> 2  和  1-> 5-> 2  为完全不同的路，即符合题意的路。

# Solution

点双模板题

如果一个点的儿子的low小于自己的dfn,说明该儿子的子树没有返祖边

**即该儿子想要访问父亲必须经过自己**

所以这样的边就是割边

另外搜索时由于有重边不能只判父亲,要判断是不是搜索树上的边的反向边

``` c++
#include <stdio.h>
#include <algorithm>
#include <stack>
using namespace std;
const int maxn=5010;
const int maxm=20010;
int n,m;
 
int to[maxm];
int nex[maxm];
int head[maxn];
int tot=1;
void add(int x,int y)
{
    to[++tot]=y;
    nex[tot]=head[x];
    head[x]=tot;
 
    to[++tot]=x;
    nex[tot]=head[y];
    head[y]=tot;
}
int deg[maxn];
void insert(int x,int y)
{
    ++deg[x];
}
int cnt;
int dfn[maxn];
int low[maxn];
int fa[maxn];
bool bridge[maxm];
int belong[maxn];
int ebc;
int ans;
stack <int> s;
bool ins[maxn];
void dfs(int x)
{
    dfn[x]=low[x]=++cnt;
    ins[x]=true;
    s.push(x);
    for(int i=head[x];i;i=nex[i])
        if(!dfn[to[i]])
        {
            fa[to[i]]=i,
            dfs(to[i]),
            low[x]=min(low[x],low[to[i]]);
            if(low[to[i]]>dfn[x])
                ++ans,bridge[i]=true;
        }
        else if(i^fa[x]^1&&ins[to[i]])
            low[x]=min(low[x],dfn[to[i]]);
    if(low[x]==dfn[x])
    {
        int now=0;
        ++ebc;
        while(now^x)
        {
            now=s.top();
            s.pop();
            ins[now]=false;
            belong[now]=ebc;
        }
    }
}
void rebuild()
{
    for(int x=1;x<=n;x++)
        for(int i=head[x];i;i=nex[i])
            if(belong[to[i]]!=belong[x])
                insert(belong[x],belong[to[i]]);
}
void tarjan()
{
    dfs(1);
    rebuild();
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=m;i++)
    {
        static int x,y;
        scanf("%d%d",&x,&y);
        add(x,y);
    }
    tarjan();
    printf("%d\n",ans);
    ans=0;
    for(int i=1;i<=ebc;i++)
        if(deg[i]==1)
            ++ans;
    printf("%d\n",ans+1>>1);
}
```

