Title: USACO Contests Silver 2015~2016 Season
[TOC]

# 3021 Switching on the Lights

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3021)

## Description

有N个房间，组成了一张N\*N的网格图，Bessie一开始位于左上角(1,1)，并且只能上下左右行走。

一开始，只有(1,1)这个房间的灯是亮着的，Bessie只能在亮着灯的房间里活动。

有另外M条信息，每条信息包含四个数a,b,c,d，表示房间(a,b)里有房间(c,d)的灯的开关。

请计算出最多有多少个房间的灯可以被打开

## Solution

### Solution 1

多次广搜,直到不能增广

### Solution 2

对于每一个点递归处理

到达这个点,打开它所有的灯,如果这个灯可以到达,就递归处理它

之后遍历这个灯的四个方向,如果有没有处理过的,就递归处理它

``` c++
#include <stdio.h>
#include <bitset>
using namespace std;
const int maxn=110;
const int maxm=20010;
 
class SwitchingOnTheLights
{
 
    int n,m;
 
    int tox[maxm];
    int toy[maxm];
    int next[maxm];
    int head[maxn][maxn];
    int tot;
    void add(int x,int y,int a,int b)
    {
        tox[++tot]=a;
        toy[tot]=b;
        next[tot]=head[x][y];
        head[x][y]=tot;
    }
 
    int dx[4];
    int dy[4];
 
    int ans;
 
    bitset <maxn> v[maxn];
    bitset <maxn> p[maxn];
 
    void process(int x,int y)
    {
        p[x][y]=true;
        for(int i=head[x][y];i;i=next[i])
        {
            v[tox[i]][toy[i]]=true;
            if(!p[tox[i]][toy[i]])
                for(int j=0;j<4;j++)
                    if(p[tox[i]+dx[j]][toy[i]+dy[j]])
                    {
                        process(tox[i],toy[i]);
                        break;
                    }
        }
        for(int i=0;i<4;i++)
            if(v[x+dx[i]][y+dy[i]]&&!p[x+dx[i]][y+dy[i]])
                process(x+dx[i],y+dy[i]);
    }
 
public:
 
    SwitchingOnTheLights()
    {
        tot=0;
        ans=0;
        dx[0]=0,dy[0]=1;
        dx[1]=1,dy[1]=0;
        dx[2]=0,dy[2]=-1;
        dx[3]=-1,dy[3]=0;
    }
 
    int solve()
    {
        scanf("%d%d",&n,&m);
        for(int i=1;i<=m;i++)
        {
            static int x,y,a,b;
            scanf("%d%d%d%d",&x,&y,&a,&b);
            add(x,y,a,b);
        }
 
        for(int i=1;i<=n;i++)
            v[i].reset(),p[i].reset();
        v[1][1]=true;
 
        process(1,1);
 
        for(int i=1;i<=n;i++)
            for(int j=1;j<=n;j++)
                if(v[i][j])
                    ++ans;
        return ans;
    }
}cls;
int main()
{
    printf("%d\n",cls.solve());
}
```

# 3022 High Card Wins

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3022)

## Description

奶牛Bessie和Elsie在玩一种卡牌游戏。一共有2N张卡牌，点数分别为1到2N，每头牛都会分到N张卡牌。

游戏一共分为N轮，因为Bessie太聪明了，她甚至可以预测出每回合Elsie会出什么牌。

每轮游戏里，两头牛分别出一张牌，点数大者获胜。

Bessie现在想知道，自己最多能获胜多少轮？

## Solution

田忌赛马

如果Bessie最小的牌比Elise的大,就更新答案

否则抵消Elise最大的牌

``` c++
#include <stdio.h>
#include <algorithm>
using namespace std;
const int maxn=50010;
class HighCardWins
{
    int n;
    int a[maxn];
    int b[maxn];
    int ans;
    int ptra,ptrb;
public:
    HighCardWins()
    {
        ans=0;
        ptra=ptrb=0;
    }
    int solve()
    {
        scanf("%d",&n);
        for(int i=1;i<=n;i++)
            scanf("%d",&a[i]);
        sort(a+1,a+n+1);
        a[n+1]=(n<<1)+1;
        for(int i=1;i<=n;i++)
            if(a[i+1]-a[i]!=1)
                for(int j=a[i]+1;j<a[i+1];j++)
                    b[++ptrb]=j;
        ptra=ptrb=1;
        while(ptra<=n)
        {
            if(a[ptra]>b[ptrb])
                --n,++ptrb;
            else
                ++ans,++ptra,++ptrb;
        }
        return ans;
    }
}cls;
int main()
{
    printf("%d\n",cls.solve());
}
```

# 3023 Breed Counting

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3023) 

## Description

给定一个长度为N的序列，每个位置上的数只可能是1，2，3中的一种。

有Q次询问，每次给定两个数a,b，请分别输出区间[a,b]里数字1，2，3的个数。

## Solution

维护前缀和即可

``` c++
#include <stdio.h>
const int maxn=100010;
class BreedCounting
{
    int f[4][maxn];
    int n,q;
    void query()
    {
        static int x,y;
        scanf("%d%d",&x,&y);
        printf("%d %d %d\n",f[1][y]-f[1][x-1],f[2][y]-f[2][x-1],f[3][y]-f[3][x-1]);
    }
public:
    int solve()
    {
        scanf("%d%d",&n,&q);
        for(int i=1;i<=n;i++)
        {
            static int x;
            scanf("%d",&x);
            ++f[x][i];
            f[1][i]+=f[1][i-1];
            f[2][i]+=f[2][i-1];
            f[3][i]+=f[3][i-1];
        }
        while(q--)
            query();
    }
 
}cls;
int main()
{
    cls.solve();
}
```

# 3034 Angry Cows

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3034)

## Description

奶牛Bessie设计了一个游戏：“愤怒的奶牛”。游戏的原型是：有一些可爆炸的草堆分布在一条数轴的某些坐标上，玩家用弹弓把一头奶牛发射到数轴上。奶牛砸到数轴上的冲击波会引发附近的草堆爆炸。游戏的目标是玩家用一些奶牛炸掉所有的草堆。 

有N个草堆在数轴的不同位置，坐标为x1,x2,….,xn。如果玩家以能量R把奶牛发射到坐标x，就会引爆半径R及以内的的草堆，即坐标范围[x−R,x+R]的草堆都会燃爆。 

现在有K头奶牛，每头奶牛的能量都是R，请计算如果要引爆所有的草堆，最小的R是多少？ 

## Solution

二分答案+贪心验证

``` c++
#include <stdio.h>
#include <vector>
#include <algorithm>
using namespace std;
class AngryCows
{
    int num;
    int ans;
    vector <int> bak;
    bool check(int mid)
    {
        int last=0,ans=0;
        for(int i=0;i<bak.size();i++)
            if(bak[i]-bak[last]>mid)
                last=i,++ans;
        ++ans;
        return ans<=num;
    }
public:
    int solve(int n,int k,vector <int> &pos)
    {
        sort(pos.begin(),pos.end());
        bak=pos;
        num=k;
        int l=pos[0],r=pos[n-1],mid;
        while(l<r)
        {
            mid=l+r>>1;
            if(check(mid<<1))
                r=mid;
            else
                l=mid+1;
        }
        return l;
    }
}cls;
int main()
{
    int n,k;
    scanf("%d%d",&n,&k);
    vector <int> pos(n);
    for(int i=0;i<n;i++)
        scanf("%d",&pos[i]);
    printf("%d\n",cls.solve(n,k,pos));
}
```

# 3035 Subsequences Summing to Sevens

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3035)

## Description

给定长度为N的数列A，求A最长的连续子序列，满足子序列中元素的和是7的倍数。输出最大的长度。如果不存在满足条件的子序列，输出0

$1 ≤ N ≤ 50000, 0 ≤ A_i ≤ 10 ^ 6$

## Solution

维护一下膜7的前缀和和每个余数的第一个位置

最后扫一遍更新答案

``` c++
#include <stdio.h>
#include <bitset>
#include <algorithm>
using namespace std;
const int maxn=50010;
class SubsequencesSummingToSevens
{
    int n;
    int mod[maxn];
    int last[7];
public:
    int solve()
    {
        int ans=0;
        scanf("%d",&n);
        for(int i=1;i<=n;i++)
        {
            static int x;
            scanf("%d",&x);
            mod[i]=(mod[i-1]+x)%7;
            if(!last[mod[i]])
                last[mod[i]]=i;
        }
        for(int i=n;i;i--)
            ans=max(ans,i-last[mod[i]]);
        return ans;
    }
}cls;
int main()
{
    printf("%d\n",cls.solve());
}
```

# 3036 Build Gates

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3036)

## Description

给定一个包含N,S,E,W的移动序列。FJ初始在(0,0)，依次按照移动序列移动，并在移动过程中建设篱笆。如FJ在(0,0)点，要向N移动，则FJ移动到(0,1)，并在(0,0)和(0,1)之间建一段篱笆。注意到这样建设出的篱笆可能自交

把平面划分成若干区域。现在要拆除若干段篱笆，使平面连通。输出最少拆除的篱笆段数。

$1 ≤ N ≤ 1000$

## Solution

乱搞

维护一下每个点是否被经过和上面,右面有没有篱笆即可

注意**偏移量**

``` c++
#include <stdio.h>
#include <bitset>
using namespace std;
const int maxn=2048;
class BuildGates
{
    bitset <maxn> vis[maxn],up[maxn],right[maxn];
    int n,x,y,ans;
    char ch[maxn];
public:
    int solve()
    {
        scanf("%d",&n);
        scanf("%s",ch+1);
        x=y=1024;
        for(int i=1;i<=n;i++)
        {
            vis[x][y]=true;
            if(ch[i]=='N')
            {
                ++y;
                if(!up[x][y-1]&&vis[x][y])
                    ++ans;
                up[x][y-1]=true;
            }
            if(ch[i]=='S')
            {
                --y;
                if(!up[x][y]&&vis[x][y])
                    ++ans;
                up[x][y]=true;
            }
            if(ch[i]=='W')
            {
                --x;
                if(!right[x][y]&&vis[x][y])
                    ++ans;
                right[x][y]=true;
            }
            if(ch[i]=='E')
            {
                ++x;
                if(!right[x-1][y]&&vis[x][y])
                    ++ans;
                right[x-1][y]=true;
            }
        }
        return ans;
    }
}cls;
int main()
{
    printf("%d\n",cls.solve());
}
```

# 3046 Circular Barn

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3046)

## Description

有一个N个点的环，相邻两个点距离是1。点顺时针标号为1..N。

最初每一个点是空的。

要求最终点i存在ri头牛。

你有$\sum r_i$头牛。你可以选择最多k个点，然后把你的牛任意分配在这k个点里。

之后，每一头牛可以选择不动，也可以顺时针走d格并呆在那里。这样，它要耗费d的能量(最初的分配不消耗能量)

通过合理选择点、合理分配牛、合理安排牛的走动，使得消耗的总能量最小。

## Solution

构造

易知,我们将链倍长之后,总能找到一个点,使在最优答案中其他的牛不经过这个点

那么如何找到这个点?

既然没有牛经过这个点,说明这个点之后N个点能"自给自足"

所以将每个点权-1后,这个点就是最大连续字段和的起点

之后暴力每个位置即可

**注意**:$(x+y)^2 > x^2 + y^2$

所以中间要尽量"换班"

``` c++
#include <stdio.h>
#include <algorithm>
using namespace std;
const int maxn=2010;
class CircularBarn
{
    int n;
    int a[maxn];
    int q[maxn];
    int h,t;
    long long ans;
public:
    long long solve()
    {
        scanf("%d",&n);
        for(int i=1;i<=n;i++)
            scanf("%d",&a[i]),a[i+n]=a[i];
        int last=0,sum=0,pos=0,fpos;
        for(int i=1;i<=n<<1;i++)
        {
            if(last<0)
                last=0,pos=i;
            last+=a[i]-1;
            if(last>sum)
                sum=last,fpos=pos;
        }
        h=1,t=ans=0;
        for(int i=fpos;i<fpos+n;i++)
        {
            while(a[i]--)
                q[++t]=i;
            ans+=(q[h]-i)*1ll*(q[h]-i);
            ++h;
        }
        return ans;
    }
}cls;
int main()
{
    printf("%lld\n",cls.solve());
}
 
```

# 3047 Load Balancing

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3047)

## Description

给出N个平面上的点。保证每一个点的坐标都是正奇数。

你要在平面上画两条线，一条是x=a，一条是y=b，且a和b都是偶数。

直线将平面划成4个部分，要求包含点数最多的那个部分点数最少。

## Solution

先离散

枚举横坐标,之后将纵坐标插入树状数组验证

复杂度$O(N^2\log N)$

``` c++
#include <stdio.h>
#include <bitset>
#include <string.h>
#include <algorithm>
#define lowbit(x) (x&-(x))
using namespace std;
const int maxn=1100;
class LoadBalancing
{
    int n;
    typedef pair<int,int> point;
    point p[maxn];
 
    int x[maxn];
    int y[maxn];
 
    bitset <maxn> v;
 
    int ans;
 
    struct BIT
    {
        int ans;
        int a[maxn];
        int n;
        BIT(int x)
        {
            n=x;
        }
        void update(int x)
        {
            for(int i=x;i<=n;i+=lowbit(i))
                ++a[i];
        }
        int query(int x)
        {
            ans=0;
            for(int i=x;i;i-=lowbit(i))
                ans+=a[i];
            return ans;
        }
        void clear()
        {
            memset(a,0,sizeof(a));
        }
    };
    void Discrete()
    {
        sort(x+1,x+n+1);
        sort(y+1,y+n+1);
        x[0]=unique(x+1,x+n+1)-x;
        y[0]=unique(y+1,y+n+1)-y;
        --x[0],--y[0];
        for(int i=1;i<=n;i++)
            p[i].first=lower_bound(x+1,x+x[0]+1,p[i].first)-x,
            p[i].second=lower_bound(y+1,y+y[0]+1,p[i].second)-y;
        for(int i=1;i<=x[0];i++)
            x[i]=i;
        for(int i=1;i<=y[0];i++)
            y[i]=i;
    }
public:
    int solve()
    {
        ans=~0u>>1;
 
        scanf("%d",&n);
        for(int i=1;i<=n;i++)
            scanf("%d%d",&x[i],&y[i]),p[i]=point(x[i],y[i]);
 
        Discrete();
 
        sort(p+1,p+n+1);
 
        BIT L(y[0]),R(y[0]);
        for(int i=1;i<=n;i++)
            if(!v[p[i].first])
            {
                L.clear();
                R.clear();
                v[p[i].first]=true;
                for(int j=1;j<=n;j++)
                    if(p[j].first<=p[i].first)
                        L.update(p[j].second);
                    else
                        R.update(p[j].second);
                for(int j=1;j<=y[0];j++)
                    ans=min(ans,max(max(L.query(y[j]),L.query(y[y[0]])-L.query(y[j])),
                                    max(R.query(y[j]),R.query(y[y[0]])-R.query(y[j]))));
            }
        return ans;
    }
}cls;
int main()
{
    printf("%d\n",cls.solve());
}
```

# 3048 Milk Pails

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3048)

## Description

有两个桶大小为X和Y，开始都是空的。
有如下三种操作，最多操作K次。
①倒满一个桶(满桶也可以倒)
②倒空一个桶(空桶也可以倒)
③把一个桶里的水倒到另一个桶里，直到一个桶空了或者另一个桶满了(看哪种情况先发生）

给出要求的水量M.
最后你获得的水量是两个桶的水量之和M'。
求|M-M'|的最小值。

## Solution

表示状态能否到达型动态规划

令$f[i][j][k]$表示操作$i$次,X桶$j$,Y桶$k$的方案能否到达

``` c++
#include <stdio.h>
#include <bitset>
#include <algorithm>
using namespace std;
const int maxn=110;
class MilkPails
{
    int x,y,K,m;
    bitset <maxn> f[maxn][maxn];
public:
    int solve()
    {
        int ans=~0u>>1;
        scanf("%d%d%d%d",&x,&y,&K,&m);
        f[1][x][0]=f[1][0][y]=f[1][0][0]=true;
        for(int i=1;i<=K;i++)
            for(int j=0;j<=x;j++)
                for(int k=0;k<=y;k++)
                    if(f[i][j][k])
                    {
                        f[i+1][j][y]=true;
                        f[i+1][x][k]=true;
                        f[i+1][0][k]=true;
                        f[i+1][j][0]=true;
                        if(j+k>=y)
                            f[i+1][j+k-y][y]=true;
                        else
                            f[i+1][0][j+k]=true;
                        if(j+k>=x)
                            f[i+1][x][j+k-x]=true;
                        else
                            f[i+1][j+k][0]=true;
                    }
        for(int j=0;j<=x;j++)
            for(int k=0;k<=y;k++)
                if(f[K][j][k])
                    ans=min(ans,abs(m-j-k));
        return ans;
    }
}cls;
int main()
{
    printf("%d\n",cls.solve());
}
```

# 3059 Field Reduction

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3059)

## Description

给定平面上N个点。现你可以删去至多3个点，接着你需要用一个矩形包含所有的点，点可以在矩形的边上，矩形的边须与坐标轴平行。最小化矩形的面积并输出这个值

$5 ≤ N ≤ 50000, 1 ≤ X_i, Y_i ≤ 40000$

## Solution

易知,当我们删去一些点后,这个矩形的横坐标最大值一定属于{最大值,次大值,第三大,第四大}

对于纵坐标和最小值同理

所以我们只需要枚举这$4^4=256$中选择,再$O(N)$验证即可

``` c++
#include <stdio.h>
#include <functional>
#include <algorithm>
using namespace std;
const int maxn=50010;
class FieldReduction
{
    int n;
    int x[maxn];
    int y[maxn];
 
    int minx[10];
    int miny[10];
    int maxx[10];
    int maxy[10];
 
public:
    int solve()
    {
        int ans=~0u>>1;
 
        scanf("%d",&n);
        for(int i=1;i<=n;i++)
        {
            scanf("%d%d",&x[i],&y[i]);
 
            minx[++minx[0]]=x[i];
            push_heap(minx+1,minx+minx[0]+1);
            if(minx[0]>4)
                pop_heap(minx+1,minx+minx[0]--+1);
 
            miny[++miny[0]]=y[i];
            push_heap(miny+1,miny+miny[0]+1);
            if(miny[0]>4)
                pop_heap(miny+1,miny+miny[0]--+1);
 
            maxx[++maxx[0]]=x[i];
            push_heap(maxx+1,maxx+maxx[0]+1,greater<int>());
            if(maxx[0]>4)
                pop_heap(maxx+1,maxx+maxx[0]--+1,greater<int>());
 
            maxy[++maxy[0]]=y[i];
            push_heap(maxy+1,maxy+maxy[0]+1,greater<int>());
            if(maxy[0]>4)
                pop_heap(maxy+1,maxy+maxy[0]--+1,greater<int>());
        }
 
        int cnt=0;
        for(int a=1;a<=4;a++)
            for(int b=1;b<=4;b++)
                for(int c=1;c<=4;c++)
                    for(int d=1;d<=4;d++)
                    {
                        cnt=0;
                        for(int i=1;i<=n;i++)
                            if(x[i]>maxx[a]||x[i]<minx[b]||y[i]>maxy[c]||y[i]<miny[d])
                                ++cnt;
                        if(cnt<=3)
                            ans=min(ans,(maxx[a]-minx[b])*(maxy[c]-miny[d]));
                    }
 
        return ans;
    }
}cls;
int main()
{
    printf("%d\n",cls.solve());
}
```

# 3060 Diamond Collector

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3060)

## Description

给定长度为N的数列a,要求选出两个互不相交的子序列(可以不连续),满足同一个子序列中任意两个元素差的绝

对值不超过K。最大化两个子序列长度的和并输出这个值.

$1 ≤ N ≤ 50000, 1 ≤ a_i ≤ 10 ^ 9, 0 ≤ K ≤ 10^ 9$

## Solution

贪心维护出每个点选择它之后可以选择它之后的几个点

选择最大的两端不相交求和即可

``` c++
#include <stdio.h>
#include <algorithm>
using namespace std;
const int maxn=5e4+10;
class DiamondCollector
{
    int n,k;
    int a[maxn];
    int res;
    int ans[maxn];
public:
    int solve()
    {
        scanf("%d%d",&n,&k);
        for(int i=1;i<=n;i++)
            scanf("%d",&a[i]);
        sort(a+1,a+n+1);
        int j=1;
        for(int i=1;i<=n;i++)
        {
            while(a[j]-a[i]<=k&&j<=n)
                ++j;
            ans[i]=j-i;
        }
        for(int i=n;i;i--)
        {
            res=max(res,ans[i]+ans[i+ans[i]]);
            ans[i]=max(ans[i],ans[i+1]);
        }
 
        return res;
    }
}cls;
int main()
{
    printf("%d\n",cls.solve());
}
```

# 3061 Closing the Farm

[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=3061)

## Description

FJ有$N$个农场,当他关闭了一个农场,所有与它相连的双向边也都会断掉

FJ要依次关闭所有农场,现在告诉你关闭的顺序,求剩下的图是否连通

## Solution

在线做LCT

离线后反向处理,用并查集维护联通块个数即可

还有[金组](https://oj.jdfz.com.cn/oldoj/problem.php?id=3063)双倍经验

``` c++
#include <stdio.h>
#include <bitset>
#include <algorithm>
using namespace std;
const int maxn=3010;
const int maxm=6010;
class ClosingTheFarm
{
    int n,m;
    int fa[maxn];
    int size[maxn];
    int cnt;
    void init()
    {
        for(int i=1;i<=n;i++)
            fa[i]=i,size[i]=1;
    }
    int getfa(int x)
    {
        if(fa[x]!=x)
            fa[x]=getfa(fa[x]);
        return fa[x];
    }
 
    int to[maxm];
    int next[maxm];
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
 
    int q[maxn];
    bitset <maxn> v;
public:
    void solve()
    {
        scanf("%d%d",&n,&m);
        for(int i=1;i<=m;i++)
        {
            static int x,y;
            scanf("%d%d",&x,&y);
            add(x,y);
        }
        for(int i=1;i<=n;i++)
            scanf("%d",&q[i]);
        reverse(q+1,q+n+1);
 
        init();
        for(int t=1;t<=n;t++)
        {
            ++cnt;
            for(int i=head[q[t]];i;i=next[i])
                if(v[to[i]])
                {
                    int x=getfa(q[t]),y=getfa(to[i]);
                    if(x==y)
                        continue;
                    --cnt;
                    if(size[x]>size[y])
                        swap(x,y);
                    size[y]+=size[x];
                    fa[x]=y;
                }
            v[q[t]]=true;
            q[t]=cnt==1;
        }
        reverse(q+1,q+n+1);
        for(int i=1;i<=n;i++)
            printf("%s\n",q[i]?"YES":"NO");
    }
}cls;
int main()
{
    cls.solve();
}
```