Title: USACO Contests Silver 2013~2014 Season
[TOC]

# 2378 Farmer John has no Large Brown Cow

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2378)

## Description
给你N句话,每句话的格式为

> Farmer John has no xxx xxx xxx cow.

其中xxx代表一个形容词,每句话中形容词的个数是一定的,但相同位置的形容词可能有重复,比如样例
>Farmer John has no large brown noisy cow.
>Farmer John has no small white silent cow.
>Farmer John has no large spotted noisy cow.

这就一共有2\*3\*2种不同的牛,但是FJ没有读入描述的牛(因为has no嘛!)

求刨除读入的牛后第K头牛是什么

## Solution

读入不告诉你几个形容词,麻烦了我的哥

总体思路就是先想方设法读入形容词,判重后计算读入句子的排列(类康托展开),如果在K之前便++K

``` c++
#include <iostream>
#include <algorithm>
#include <string>
using namespace std;
const int maxa=30;
const int maxn=100;
int n,k,npos;
int cnt[maxa];
string adj[maxa][maxn];
string back[maxn];

bool check(int pos, string a)
{
    for(int i=0;i<cnt[pos];i++)
        if(adj[pos][i]==a)
            return true;
    return false;
}

int num(int pos1, int pos2)
{
    int total=1;
    for(int i=pos1;i<=pos2;i++)
        total*=cnt[i];
    return total;
}

string kth(int k)
{
    string s="";
    for(int i=0;i<npos;i++)
    {
        if(i>0)
            s=s+" ";
        s+=adj[i][k/num(i+1,npos-1)];
        k%=num(i+1,npos-1);
    }
    return s;
}

int before(string s)
{
    int total=0;
    for(int i=0;i<n;i++)
        if(back[i]<=s)
            total++;
    return total;
}

int main()
{
    cin>>n>>k;
    for(int i=0;i<n;i++)
    {
        string s;
        while(cin>>s)
            if(s=="no")
                break;
        int pos=0;
        s="";
        back[i]="";
        while(cin>>s)
        {
            if(s=="cow.")
                break;
            if(pos>0)
                back[i]+=" ";
            back[i]+=s;
            if(!check(pos,s))
            {
                adj[pos][cnt[pos]]=s;
                cnt[pos]++;
            }
            pos++;
        }
        npos=pos;
    }

    for(int pos=0;pos<npos;pos++)
     sort(adj[pos],adj[pos]+cnt[pos]);

    int K=k-1;
    while(K-before(kth(K))<k-1)
        K++;

    cout<<kth(K)<<endl;
}
```

# 2379 Crowded Cows

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2379)

## Description

给你$N$头牛,每头牛有一个位置$x_i$和高度$h_i$

定义**拥挤**:牛$i$的左侧$[x_i-d,x_i]$和右侧$[x_i,x_i+d]$的范围内都至少有$1$头牛的高度$\ge 2*h_i$

求一共有多少头牛感到拥挤

## Solution

又是一道类似扫描线的题目

用两个multiset分别维护当前点左右距离$d$之内的高度值,每次比较即可

```c++
#include <stdio.h>
#include <algorithm>
#include <set>
using namespace std;
const int maxn=50010;
typedef pair<int,int> point;
point a[maxn];
multiset<int> x, y;
int main()
{
    int n, d;
    scanf("%d%d",&n,&d);
    for(int i=1;i<=n;i++)
        scanf("%d%d",&a[i].first,&a[i].second);
    sort(a+1,a+n+1);
    int ans=0;
    int j=1,k=1;
    for(int i=1;i<=n;i++)
    {
        while(k<=n&&a[k].first<=a[i].first+d)
            y.insert(a[k++].second);
        while(a[j].first+d<a[i].first)
            x.erase(x.find(a[j++].second));
        x.insert(a[i].second);
        if(*x.rbegin()>=2*a[i].second&&*y.rbegin()>=2*a[i].second)
            ans++;
        y.erase(y.find(a[i].second));
    }
    printf("%d\n",ans);
}
```

# 2380 Pogo-Cow

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2380)

## Description

Bessie要学习跳远,现在在数轴上有$N(N\le 1000)$个降落点,每个点有属性坐标$x_i$和分数$p_i$,Bessie可以从任何一个降落点开始**向1个方向**跳,每次跳跃的距离要不小于上一次跳动的距离,求Bessie最多能获得多少分数

## Unperfect Solution

易知动规

令$f[i][j]$表示从第$j$个降落点跳到第$i$个降落点的最大分数,则

$f[i][j]=max\{f[j][k]\}+p_i\qquad x_j-x_k\le x_i-x_j$

但是这种转移是$O(N^3)$的,不过在我的努力卡常下,$608ms$水过

```c++
#include <stdio.h>
#include <algorithm>
#include <string.h>
#include <ctype.h>
#define max(a,b) ((a)>(b)?(a):(b))
using namespace std;
const int maxn=1010;
typedef pair<int,int> point;
int f[maxn][maxn];
point p[maxn];
int n;
int ans;
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
int main()
{
    n=read();
    register int i,j,k;
    for(i=1;i<=n;++i)
        p[i].first=read(),p[i].second=read(),ans=max(ans,p[i].second);
    sort(p+1,p+n+1);
    for(i=1;i<=n;++i)
    {
        f[i][i]=p[i].second;
        ans=max(ans,f[i][i]);
    }
    for(i=1;i<=n;++i)
        for(j=i-1;j;--j)
        {
            for(k=j;k&&p[j].first-p[k].first<=p[i].first-p[j].first;--k)
                f[i][j]=max(f[i][j],f[j][k]+p[i].second);
            ans=max(ans,f[i][j]);
        }
    reverse(p+1,p+n+1);
    memset(f,0,sizeof(f));
    for(i=1;i<=n;++i)
        f[i][i]=p[i].second;
    for(i=1;i<=n;++i)
        for(j=i-1;j;--j)
        {
            for(k=j;k&&p[k].first-p[j].first<=p[j].first-p[i].first;--k)
                f[i][j]=max(f[i][j],f[j][k]+p[i].second);
            ans=max(ans,f[i][j]);
        }
    printf("%d\n",ans);
}
```

## Solution

我们可以优化上述动规式中$f[j][k]$一维的转移,即在转移$f[i][j]$时直接处理可行的$k$,使复杂度降为$O(N^2)$

```c++
#include <stdio.h>
#include <algorithm>
#include <string.h>
#include <ctype.h>
#define max(a,b) ((a)>(b)?(a):(b))
using namespace std;
const int maxn=1010;
typedef pair<int,int> point;
int f[maxn][maxn];
point p[maxn];
int n;
int ans;
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
    static int D;
    while(!isdigit(ch=getc()));
    for(D=ch-'0'; isdigit(ch=getc());)
        D=(D<<3)+(D<<1)+(ch-'0');
    return D;
}
int main()
{
    n=read();
    register int i,j;
    int k,val;
    for(i=1;i<=n;++i)
        p[i].first=read(),p[i].second=read(),ans=max(ans,p[i].second);
    sort(p+1,p+n+1);
    for(i=n;i;i--)
    {
        k=n+1;
        val=0;
        for(j=1;j<=i;j++)
        {
            while(k-1>i&&p[k-1].first-p[i].first>=p[i].first-p[j].first)
            {
                --k;
                val=max(val,p[k].second+f[k][i]);
            }
            f[i][j]=val;
        }
        ans=max(ans,p[i].second+val);
    }
    reverse(p+1,p+n+1);
    for(i=1;i<=n;i++)
        p[i].first=-p[i].first;
    for(i=n;i;i--)
    {
        k=n+1;
        val=0;
        for(j=1;j<=i;j++)
        {
            while(k-1>i&&p[k-1].first-p[i].first>=p[i].first-p[j].first)
            {
                --k;
                val=max(val,p[k].second+f[k][i]);
            }
            f[i][j]=val;
        }
        ans=max(ans,p[i].second+val);
    }
    printf("%d\n",ans);
}
```

## P.S.

由于可以向两个方向走,不要忘记将数组反转再转移一次

# 2387 Milk Scheduling



[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2387)

## Description

给你$N(N\le10000)$头牛,每头牛有一个产奶量$g_i$和deadline $d_i$,FJ一个单位时间内可以给1头牛挤奶,但是如果超过了该牛的deadline,就不能再给这头牛挤奶,求最大挤奶量

**$d_i\le 10000$**

## Unperfect Solution

贪心

用set维护一下还有那些位置没有选,之后选择可选的最大位置

**虽然是正确的,而且看上去复杂度是$O(N\log N)$,但是本机就T了**

```c++
#include <stdio.h>
#include <set>
#include <algorithm>
using namespace std;
typedef pair<int,int> point;
const int maxn=10010;
int n;
point p[maxn];
int ans;
bool v[maxn];
set <int> pos;
set <int> :: iterator it;
int mx;
bool cmp(point x,point y)
{
	if(x.first==y.first)
		return x.second<y.second;
	return x.first>y.first;
}
int main()
{
//	freopen("2387.in","r",stdin);
//	freopen("2387.out","w",stdout);
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d%d",&p[i].first,&p[i].second);
		mx=max(mx,p[i].second);
	}
	for(int i=1;i<=mx;i++)
		pos.insert(i);
	sort(p+1,p+n+1,cmp);
	for(int i=1;i<=n;i++)
		if(*pos.begin()<=p[i].second)
		{
			it=--upper_bound(pos.begin(),pos.end(),p[i].second);
			pos.erase(it);
			ans+=p[i].first;
		}
	printf("%d\n",ans);
}
```

## Solution

堆贪心

我们不妨倒转一下思路,既然正向处理很复杂,那么我们就逆向处理

我们将牛按deadline从大到小排序, 之后枚举每个时间,将能够选择的牛加入堆中,之后选择堆顶即可

``` c++
#include <stdio.h>
#include <algorithm>
#define max(a,b) ((a)>(b)?(a):(b))
using namespace std;
const int maxn=10010;
int n;
struct cow
{
    int g,d;
    bool operator < (const cow &s) const
    {
        return s.g<g;
    }
}c[maxn];
bool cmp(const cow &a,const cow &b)
{
    return a.d>b.d;
}
int heap[maxn];
int ans;
int idx=1;
int mx;
int main()
{
    scanf("%d",&n);
    register int size=0,i;
    for(i=1;i<=n;i++)
    {
        scanf("%d%d",&c[i].g,&c[i].d);
        mx=max(mx,c[i].d);
    }
    sort(c+1,c+n+1,cmp);
    for(i=mx;i;i--)
    {
        while(idx<=n&&i<=c[idx].d)
        {
            heap[++size]=c[idx++].g;
            push_heap(heap+1,heap+size+1);
        }
        if(size)
        {
            ans+=heap[1];
            pop_heap(heap+1,heap+size--+1);
        }
    }
    printf("%d\n",ans);
}
```

## P.S.

已经查出Unperfect Solution的问题:**set自带upper_bound,不要再用algorithm的,常数爆炸**

``` c++
#include <stdio.h>
#include <set>
#include <algorithm>
using namespace std;
typedef pair<int,int> point;
const int maxn=10010;
int n;
point p[maxn];
int ans;
bool v[maxn];
set <int> pos;
set <int> :: iterator it;
int mx;
bool cmp(point x,point y)
{
	if(x.first==y.first)
		return x.second<y.second;
	return x.first>y.first;
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d%d",&p[i].first,&p[i].second);
		mx=max(mx,p[i].second);
	}
	for(int i=1;i<=mx;i++)
		pos.insert(i);
	sort(p+1,p+n+1,cmp);
	for(int i=1;i<=n&&(!pos.empty());i++)
		if(*pos.begin()<=p[i].second)
		{
			pos.erase(--pos.upper_bound(p[i].second));
			ans+=p[i].first;
		}
	printf("%d\n",ans);
}

```

# 2388 Vacation Planning

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2388)

## Description

FJ有$N(N\le200)$个农场,其中$1$~$K(K\le100,k\le N)$号农场是枢纽,这些农场由$M(M\le10000)$条有向边组成,现在有$Q(Q\le10000)$个询问,每次询问能否从$i$号农场到$j$号农场

最后输出:一共有几个询问的点对是能到达的,和这些路径的最小权值和

##　Solution

套路和之前的[Relocation](https://oj.jdfz.com.cn/oldoj/problem.php?id=2297)差不多,先跑出整个图的最短路,对于每次询问枚举$K$个点即可

``` c++
#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include <algorithm>
#define min(a,b) ((a)<(b)?(a):(b))
using namespace std;
const int maxn=210;
int map[maxn][maxn];
int n;
int K;
int m;
int q;
int ans;
int tmp;
long long sum;
int main()
{
	scanf("%d%d%d%d",&n,&m,&K,&q);
	memset(map,0x3f,sizeof(map));
	register int i,j,k;
	for(i=1;i<=m;++i)
	{
		static int x,y;
		scanf("%d%d",&x,&y);
		scanf("%d",&map[x][y]);
	}
	for(i=1;i<=n;++i)
		map[i][i]=0;
	for(k=1;k<=n;++k)
		for(i=1;i<=n;++i)
			for(j=1;j<=n;++j)
				map[i][j]=min(map[i][j],map[i][k]+map[k][j]);
	for(i=1;i<=q;++i)
	{
		static int x,y;
		scanf("%d%d",&x,&y);
		tmp=~0u>>1;
		for(j=1;j<=K;++j)
			if(map[x][j]!=1061109567&&map[j][y]!=1061109567)
				tmp=min(tmp,map[x][j]+map[j][y]);
		if(tmp!=~0u>>1)
			++ans,sum+=tmp;
	}
	printf("%d\n%lld\n",ans,sum);
}
```

# 2389 The Bessie Shuffle

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2389)

## Description

> Bessie有了一套新的做牌技术,M张牌被Bessie洗牌后，第$i$张牌会跑到$p_i$的位置。现有$N$张牌从1..$N$编号，称为卡组。Bessie从卡组取前M张进行一次洗牌，然后将最上面的牌面朝下放置到一边的牌堆，剩下的牌面朝下放回卡组。重复此过程直至卡组剩下M-1张牌。接着Bessie将这M-1张牌逐一面朝下放到牌堆中。接下来有Q个询问，问第x[Q]张牌的编号。摘自BZOJ

## Solution

想了半天完全没有思路,看了题解想了几个小时才明白

如果暴力模拟复杂度为$O(N\log (N-M))$,肯定过不了

我们观察一个性质,如果原序列为

1 2 3 4 5

交换序列为

3 1 2

即

1 2 3 4 5

3 1 2 4 5

但是我们可以将拿走一张牌转化为将它放到牌堆尾,交换序列就变成了

1 2 3 4 5

2 5 1 3 4

之后我们用类似快速幂的形式转移,复杂度为$O(M \log (N-M))$

``` c++
#include <stdio.h>
#include <algorithm>
#include <string.h>
using namespace std;
const int maxn=100010;

int x[maxn];
int y[maxn];
int pi[maxn];

void compose(int *r,int *a,int *b,int n)//类自平方
{
    for(int i=0;i<n;i++)
        r[i]=a[b[i]];
}

int main()
{
    int n,m,q;
    scanf("%d%d%d",&n,&m,&q);
    int t=n-m+1;
    for(int i=0;i<n;i++)
    {
        x[i]=i;
        if(i<m)
        {
            static int z;
            scanf("%d",&z);
            pi[--z]=i;
        }
        else
            pi[i]=i;
    }
    rotate(pi,pi+1,pi+n);//更换位置
    for(int i=31-__builtin_clz(t);i>=0;i--) //__builtin_clz()求前导零个数
    {
        compose(y,x,x,n);
        memcpy(x,y,sizeof(x));
        if(t&1<<i)
        {
            compose(y,x,pi,n);
            memcpy(x,y,sizeof(x));
        }
    }
    for(int i=1;i<=q;i++)
    {
        static int z;
        scanf("%d",&z);
        printf("%d\n",x[(n+m-1-z)%n]+1);
    }
}

```

# 2396 Bessie Slows Down

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2396)

## Description

Bessie的初始速度为1单位每秒现在有两种减速操作

T操作:到达该时刻时减速

D操作:行走了该距离时减速

减速:速度由$\frac{1}{n}$变为$\frac{1}{n+1}$

## Solution

思博模拟,我写的简直不能再麻烦

两个指针维护排序后的两种操作,计算时间

``` c++
#include <stdio.h>
#include <functional>
#include <algorithm>
using namespace std;
const int maxn=10010;
int n;
int d[maxn];
int t[maxn];
double ans;
double dis;
int v=1;
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		static char s[5];
		static int x;
		scanf("%s%d",s,&x);
		if(s[0]=='T')
			t[++t[0]]=x;
		else if(s[0]=='D')
			d[++d[0]]=x;
	}
	make_heap(d+1,d+d[0]+1,greater<int>());
	make_heap(t+1,t+t[0]+1,greater<int>());
	while(d[0]||t[0])
	{
		if(!d[0])
		{
			dis+=(t[1]-ans)/v;
			ans=t[1];
			++v;
			pop_heap(t+1,t+t[0]--+1,greater<int>());
		}
		else if(!t[0])
		{
			ans+=(d[1]-dis)*v;
			++v;
			dis=d[1];
			pop_heap(d+1,d+d[0]--+1,greater<int>());
		}
		else if((d[1]-dis)*v-(t[1]-ans)<=1e-8&&(d[1]-dis)-(t[1]-ans)*v>0)
		{
			dis+=(t[1]-ans)/v;
			ans=t[1];
			++v;
			pop_heap(t+1,t+t[0]--+1,greater<int>());
			pop_heap(d+1,d+d[0]--+1,greater<int>());
		}
		else if((d[1]-dis)*v<(t[1]-ans))
		{
			ans+=(d[1]-dis)*v;
			++v;
			dis=d[1];
			pop_heap(d+1,d+d[0]--+1,greater<int>());
		}
		else if((d[1]-dis)*v>(t[1]-ans))
		{
			dis+=(t[1]-ans)/v;
			ans=t[1];
			++v;
			pop_heap(t+1,t+t[0]--+1,greater<int>());
		}
	}
	ans+=(1000-dis)*v;
	printf("%d\n",int(ans+0.5));
}

```

# 2397 Cross Country Skiing

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2397)

## Description

在一个$N \times M(N,M\le 500)$的矩阵中,每个点都有$1$个海拔,其中有$D$个点是关键点,求能到达所有关键点的最小海拔差

## Solution

二分+floodfill验证

## P.S.

卡常大赛

题目很简单,但是本机$600ms$,交上去$1136ms$T了

最后卡了半个小时勉强$932ms​$过了

官方题解代码光荣$1092ms$TLE

强烈要求更换测评机配置

``` c++
#include <stdio.h>
#include <string.h>
#include <queue>
#include <algorithm>
#define max(a,b) ((a)>(b)?(a):(b))
using namespace std;
const int maxn=510;
typedef pair<int,int> point;
int m,n;
int map[maxn][maxn];
int belong[maxn][maxn];
int cnt;
queue <point> q;
int dx[]={1,0,-1,0};
int dy[]={0,1,0,-1};
int mx;
point p[maxn*maxn];
int c;
int main()
{
	scanf("%d%d",&m,&n);
	register int i,j,k;
	for(i=1;i<=m;i++)
		for(j=1;j<=n;j++)
		{
			scanf("%d",&map[i][j]);
			mx=max(mx,max(max(abs(map[i][j]-map[i+1][j]),abs(map[i][j]-map[i-1][j])),max(abs(map[i][j]-map[i][j+1]),abs(map[i][j]-map[i][j-1]))));
		}
	int x;
	for(i=1;i<=m;i++)
		for(j=1;j<=n;j++)
		{
			scanf("%d",&x);
			if(x)
				p[++c]=point(i,j);
		}
	int l=0,r=mx,mid;
	while(l<r)
	{
		mid=l+r>>1;
		q.push(p[1]);
		belong[p[1].first][p[1].second]=++cnt;
		while(!q.empty())
		{
			static int x,y;
			x=q.front().first;
			y=q.front().second;
			q.pop();
			for(k=0;k<4;k++)
				if(x+dx[k]>0&&
                   x+dx[k]<=m&&
                   y+dy[k]>0&&
                   y+dy[k]<=n&&
                   belong[x+dx[k]][y+dy[k]]^belong[x][y]&&
                   abs(map[x+dx[k]][y+dy[k]]-map[x][y])<=mid
                  )
                  q.push(point(x+dx[k],y+dy[k])),belong[x+dx[k]][y+dy[k]]=cnt;
		}
		for(i=2;i<=c;i++)
			if(belong[p[i].first][p[i].second]!=belong[p[i-1].first][p[i-1].second])
			{
				l=mid+1;
				goto lableA;
			}
		r=mid;
		lableA:;
	}
	printf("%d\n",l);
}

```

# 2398 Recording the Moolympics

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2398)

## Description

**膜林匹克运动会**的节目都是一个区间,现在FJ有**两个**录影机,求一共能录多少个节目

$N\le 150$

## Unperfect Solution

第一反应:先做一遍1个录影机,删掉所选的节目,再做一遍

这样会WA3个点

反例

>Track 1 : |-| |-| |---------|
>Track 2 : |---------| |-| |-|

如果用上述方式会先选择4个小区间,这样就不能选择两个大区间

## Solution

### Dual-thread dynamic-programming

双线程dp,状态表示为每一个记录器记录的最后一个节目

### Greedy

按照右端点排序贪心

如果能存储多的能存就存在存储多的,否则向少的里存,否则不存

正确性:能存储就要存储,否则还会限制后面的区间

``` c++
#include <stdio.h>
#include <vector>
#include <algorithm>
using namespace std;
const int maxn=160;
int n;
struct seg
{
	int l,r;
}s[maxn];
int ans;
int c1,c2;
bool cmp(seg a,seg b)
{
	return a.r<b.r;
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
		scanf("%d%d",&s[i].l,&s[i].r);
	sort(s+1,s+n+1,cmp);
	for(int i=1;i<=n;i++)
	{
		if(s[i].l>=c1)
			++ans,c1=s[i].r;
		else if(s[i].l>=c2)
			++ans,c2=s[i].r;
		if(c1<c2)
			c1^=c2^=c1^=c2;
	}
	printf("%d\n",ans);
}

```

# 2405 Auto-Complete

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2405)

## Description

给你$W(W\le 10^5)$个单词和$N(N\le 10^4)$个前缀,求该前缀下的第$K$个单词在$W$个单词中的顺序

## Solution

直接string+lower_bound查找位置

## P.S.

这题不要交BZ,cinRE有毒

网上有个Trie+主席树的版本,自己体会吧

``` c++
#include <iostream>
#include <string>
#include <map>
#include <algorithm>
using namespace std;
const int maxn=1000010;
int w,n;
string s;
string in[1000010];
map <string,int> m;
int main()
{
    cin>>w>>n;
    for(int i=1;i<=w;i++)
    {
        cin>>in[i];
        m[in[i]]=i;
    }
    sort(in+1,in+w+1);
    for(int i=1;i<=n;i++)
    {
        static int x,pos;
        cin>>x>>s;
        pos=lower_bound(in+1,in+w+1,s)-in;
        pos+=x-1;
        if(pos>w||in[pos].substr(0,s.size())!=s)
            cout<<"-1"<<endl;
        else
            cout<<m[in[pos]]<<endl;
    }
}
```

# 2406 Roadblock

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2406)

和我之前讲过的[Roadblock](https://oj.jdfz.com.cn/oldoj/problem.php?id=2278)完全一样,只是数据范围加大到250,做法不变

加上[金组](https://oj.jdfz.com.cn/oldoj/problem.php?id=2408)三倍经验

[题解](http://alexma.farbox.com/post/jdfzoj/usaco-contests-silver-2011-2012-season)

# 2407 Secret Code

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2407)

## Description

> XYW和他的男人聊天的时候，不让DZY看见他们聊天内容，他们决定用以下方式给一个字符串加>密：每次把这个字符串的一个非空前缀或者后缀（不能是这个字符串的本身）加到这个字符串的前面或者后面。比如ABC就可以加密成 AABC ABABC BCABC CABC ABCA ABCAB ABCBCABCC。
> 现在DZY拿到一个字符串（长度至多100），已知这个字符串从一个长度最少为2的字符串经过了至少一次加密（可以多次），现在DZY想要知道有多少种加密方案可以加密得到当前的字符串（初始字符串不同或者操作序列不同都视为不同的方案，结合样例解释食用更佳）。请你输出方案数对2014取模的值。

摘自BZOJ

## Solution

记忆化搜索(区间DP也行)

``` c++
#include <iostream>
#include <algorithm>
#include <string>
#include <map>
using namespace std;
const int mod=2014;
map <string,int> m;
string s;
int recursion(string s)
{
    if(m.find(s)!=m.end())
        return m[s];
    int ans=1;
    int l=s.length();
    for(int i=1;i*2<l;i++)
    {
        if(s.substr(0,i)==s.substr(l-i,i))
            ans+=recursion(s.substr(i,l-i));
        if(s.substr(0,i)==s.substr(i,i))
            ans+=recursion(s.substr(i,l-i));
        if(s.substr(0,i)==s.substr(l-i,i))
            ans+=recursion(s.substr(0,l-i));
        if(s.substr(l-i*2,i)==s.substr(l-i,i))
            ans+=recursion(s.substr(0,l-i));
    }
    ans%=mod;
    m[s]=ans;
    return ans;
}
int main()
{
    cin>>s;
    cout<<((recursion(s)-1)%mod+mod)%mod<<endl;
}
```
# 2416 Mooo Moo

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2416)

## Description

J有n块田，在一条直线上，田里有b种牛，每种牛的都有自己的叫声大小vi，由于有风，每块田可以听到的叫声大小为田里所有牛的叫声和加左边的叫声大小减一(最左边那块田的话，则只能听到自己田地里的声音），给定每种牛的叫声大小已及每块田所听到的声音，求最小可能存在多少头牛，没有解输出-1

$1 \le N\le100\quad1\le B\le 20\quad 1\le V_i\le 100 \quad V_{max}\le 100000$

## Solution

鉴于本题$V_{max}$较小,我们可以考虑一种非常规转移

我们先处理出每个**点**的牛发出的声音和每个音量最少需要的牛数即可

``` c++
#include <stdio.h>
#include <string.h>
#include <algorithm>
using namespace std;
const int maxn=110;
int n,b;
int v[maxn];
int a[maxn];
int f[100001];
int ans=0;
int main()
{
    scanf("%d%d",&n,&b);
    for(int i=1;i<=b;i++)
        scanf("%d",&v[i]);
    for(int i=1;i<=n;i++)
        scanf("%d",&a[i]);
    for(int i=n;i>1;i--)
        if(a[i-1])
            a[i]-=a[i-1]-1;
    memset(f,0x3f,sizeof(f));
    f[0]=0;
    for(int j=1;j<=b;j++)
        for(int i=v[j];i<=100000;i++)
            f[i]=min(f[i],f[i-v[j]]+1);
    for(int i=1;i<=n;i++)
    {
        if(f[a[i]]==0x3f3f3f3f)
        {
            puts("-1");
            return 0;
        }
        ans+=f[a[i]];
    }
    printf("%d\n",ans);
}
```

# 2423 Fair Photography

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2423)

## Description

给你若干个在不同位置的0,1

可以先任意把0染成1

区间长度定义为,[L,R]中最右和最左的数的差的绝对值

求一个最长区间,满足区间中所有数0和1的个数相同

## Solution

有一个很值得借鉴的思路:**将0看成-1**

我们维护一下前缀和$sum$和该前缀和出现的最小位置$pos_{sum}$,那么答案可以由$pos_{sum},pos_{sum+2}\cdots$更新

``` c++
#include <stdio.h>
#include <string.h>
#include <algorithm>
using namespace std;
const int maxn=100010;
int n;
typedef pair<int,int> cow;
cow c[maxn];
char s[10];
int sum;
int ans;
int last[maxn<<1];
int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n;i++)
    {
        scanf("%d%s",&c[i].first,s);
        if(s[0]=='W')
            c[i].second=-1;
        else
            c[i].second=1;
    }
    sort(c+1,c+n+1);
    sum=n;
    memset(last,0x3f,sizeof(last));
    last[sum]=c[1].first;
    for(int i=1;i<n;i++)
    {
        sum+=c[i].second;
        last[sum]=min(last[sum],c[i+1].first);
    }
    for(int i=2*n;i>=0;i--)
        last[i]=min(last[i+2],last[i]);
    sum=n;
    for(int i=1;i<=n;i++)
    {
        sum+=c[i].second;
        ans=max(ans,c[i].first-last[sum]);
    }
    printf("%d\n",ans);
}
```

# 2424 Dueling GPSs

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2424)

## Description

FJ的在订购的时候不小心安装了**两个型号不同的GPS,它们对同一段路径的时间估价不同**

假设FJ在X号节点,如果它走的边**不是在当前点意义下到终点的某个GPS估价的最短路上的边**,GPS会发出警报(如果这条边两个GPS都不属于,那么算两次报警)

求FJ从1号节点到N号节点最少报警几次

## Solution

本题的思路也很有趣

### Solution 1

依据官方题解,我们在处理出两个GPS的最短路后重新构图,直接判断该边是否在最短路上,若在,加一条0边,否则将比边权+1,再跑一次dij

### Solution 2

我的做法是处理出最短路后用SPFA动规,速度和内存更优

``` c++
#include <stdio.h>
#include <string.h>
#include <queue>
#include <functional>
#include <algorithm>
using namespace std;
const int maxn=1e4+10;
const int maxm=5e4+10;
typedef pair<int,int> point;
int n,m;
point heap[maxm];
int size;
 
int to[maxm];
int rto[maxm];
int next[maxm];
int rnext[maxm];
int len1[maxm];
int len2[maxm];
int head[maxn];
int rhead[maxn];
int tot;
void add(int x,int y,int a,int b)
{
    to[++tot]=y;
    len1[tot]=a;
    len2[tot]=b;
    next[tot]=head[x];
    head[x]=tot;
    rto[tot]=x;
    rnext[tot]=rhead[y];
    rhead[y]=tot;
}
 
int f1[maxn];
int f2[maxn];
bool v[maxn];
void dijkstra()
{
    size=0;
    memset(f1,0x3f,sizeof(f1));
    heap[++size]=point(0,n);
    f1[n]=0;
    while(size)
    {
        int x=heap[1].second;
        pop_heap(heap+1,heap+size--+1,greater<point>());
        if(v[x])
            continue;
        v[x]=true;
        for(int i=rhead[x];i;i=rnext[i])
            if(f1[rto[i]]>f1[x]+len1[i])
            {
                f1[rto[i]]=f1[x]+len1[i];
                heap[++size]=point(f1[rto[i]],rto[i]);
                push_heap(heap+1,heap+size+1,greater<point>());
            }
    }
    memset(v,0,sizeof(v));
    memset(f2,0x3f,sizeof(f2));
    heap[++size]=point(0,n);
    f2[n]=0;
    while(size)
    {
        int x=heap[1].second;
        pop_heap(heap+1,heap+size--+1,greater<point>());
        if(v[x])
            continue;
        v[x]=true;
        for(int i=rhead[x];i;i=rnext[i])
            if(f2[rto[i]]>f2[x]+len2[i])
            {
                f2[rto[i]]=f2[x]+len2[i];
                heap[++size]=point(f2[rto[i]],rto[i]);
                push_heap(heap+1,heap+size+1,greater<point>());
            }
    }
}
int f[maxn];
queue <int> q;
void spfa()
{
    memset(f,0x3f,sizeof(f));
    memset(v,0,sizeof(v));
    f[1]=0;
    v[1]=true;
    q.push(1);
    while(!q.empty())
    {
        int x=q.front();
        q.pop();
        for(int i=head[x];i;i=next[i])
            if(f[to[i]]>(f[x]+(len1[i]+f1[to[i]]!=f1[x]?1:0)+(len2[i]+f2[to[i]]!=f2[x]?1:0)))
            {
                f[to[i]]=(f[x]+(len1[i]+f1[to[i]]!=f1[x]?1:0)+(len2[i]+f2[to[i]]!=f2[x]?1:0));
                if(!v[to[i]])
                    v[to[i]]=true,q.push(to[i]);
            }
        v[x]=false;
    }
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=m;i++)
    {
        static int x,y,a,b;
        scanf("%d%d%d%d",&x,&y,&a,&b);
        add(x,y,a,b);
    }
    dijkstra();
    spfa();
    printf("%d\n",f[n]);
}
```

# 2425 Odometer

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=2425)

## Description

定义一个数是有趣的,当且仅当这个数(除去前导零)中一个数字出现了超过这个数长度一半次

求[$L,R$]中有趣的数的个数

**$100\le L,R\le 10^{18}$**

## Solution

数位dp神题

令$f[i][und][k][is0]$表示第$i$位,是否小于当前数,含有$k$个所求的数,是否含有前导零的方案数

这样转移会有一个问题,444555这类的数会重复计算

所以再dp减掉重复的就可以了

``` c++
#include<stdio.h>
#include <string.h>
#include<algorithm>
#include<iostream>
using namespace std;
typedef long long ll;
const int maxn=50;
string a,b;
ll f[maxn][2][maxn][2];
ll getf(string s,int n,int targ1,int targ2)
{
    memset(f,0,sizeof(f));
    f[0][false][25][true]=1;
    for(int i=0;i<n;i++)
        for(int und=0;und<2;und++)
            for(int k=0;k<maxn;k++)
                for(int is0=0;is0<2;is0++)
                {
                    ll cur=f[i][und][k][is0];
                    for(int nxt=0;nxt<=9;nxt++)
                    {
                        if(targ2!=-1&&(nxt!=0||is0==false)&&nxt!=targ1&&nxt!=targ2)
                            continue;
                        if(und==0&&nxt>s[i]-'0')
                            continue;
                        bool nis0=is0;
                        nis0&=!nxt;
                        int nk=k;
                        if(!nis0)
                        {
                            if(targ2!=-1)
                            {
                                if(nxt==targ1)
                                    nk--;
                                else if(nxt==targ2)
                                    nk++;
                            }
                            else
                            {
                                if(nxt==targ1)
                                    nk--;
                                else
                                    nk++;
                            }
                        }
                        f[i+1][und|(nxt<s[i]-'0')][nk][nis0]+=cur;
                    }
                }
    ll res=0;
    if(targ2!=-1)
    {
        for(int i=0;i<2;i++)
            res+=f[n][i][25][false];
        return res;
    }
    for(int i=0;i<2;i++)
        for(int k=0;k<=25;k++)
            res+=f[n][i][k][false];
    return res;
}
ll F(string s)
{
    int n=s.size();
    ll res=0;
    for(int targ1=0;targ1<=9;targ1++)
        res+=getf(s,n,targ1,-1);
    for(int targ1=0;targ1<=9;targ1++)
        for(int targ2=targ1+1;targ2<=9;targ2++)
            res-=getf(s,n,targ1,targ2);
    return res;
}
int main()
{
    cin>>a>>b;
    for(int i=a.size()-1;i>=0;i--)
    {
        int c=a[i]-'0';
        if(c>0)
        {
            a[i]--;
            break;
        }
        else
            a[i]='9';
    }
    printf("%lld\n",F(b)-F(a));
}
```