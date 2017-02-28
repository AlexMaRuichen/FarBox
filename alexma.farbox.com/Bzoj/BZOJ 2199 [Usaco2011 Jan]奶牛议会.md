[题目链接](http://www.lydsy.com/JudgeOnline/problem.php?id=2199)

# Description

Displeased with Farmer John's leadership, the cows have seceded
from the farm and have formed the first Continental Cowngress.
Built on the principle of "every cow gets something they want,"
they've decided on the following voting system:
   The M (1 <= M <= 4000) cows in attendance will vote on N (1 <=
   N <= 1,000) legislative bills. Each cow casts a "yes" or "no"
   vote (denoted as 'Y' or 'N' in the input file) on exactly two
   (distinct) bills B_i and C_i (1 <= B_i <= N; 1 <= C_i <= N). The
   votes are called VB_i (VB_i in {'Y', 'N'}) and VC_i (VC_i in
   {'Y', 'N'}) respectively.
   Finally, the bills are to be passed or not in such a way that
   every cow gets her way on at least one of her votes. For example,
   if Bessie votes "yes" on Bill 1, and "no" on Bill 2, then in any
   valid solution, it must be the case that either Bill 1 gets
   passed or Bill 2 gets rejected (or both).
Given the votes of each of the cows, it's your job to figure out
which bills will be passed and which bills will be rejected in order
to conform to the rules above. If there is no solution, print
"IMPOSSIBLE". If there is at least one solution, then for each bill,
display:
    Y if in every solution this bill passes
    N if in every solution this bill fails
    ? if there are solutions where this bill passes and solutions
      where it does not pass
Consider the following set of votes (two for each cow):

```
       - - - - - BILL - - - - -
         1        2        3
Cow 1   YES      NO
Cow 2   NO       NO
Cow 3   YES               YES
Cow 4   YES      YES
```

From this, two solutions satisfy every cow:
    * Bill 1 passes (this then satisfies cows 1, 3, and 4)
    * Bill 2 fails (this then satisfies cow 2)
    * Bill 3 could pass or fail (and this is the reason there are
      two solutions)
In fact, these are the only two solutions, so the answer is the
three character string below:

YN?

# Solution

2-SAT模型很明显

讲课时没有附代码,现在补一个?

就补我最爱的奶牛题吧

``` c++
#include <stdio.h>
#include <string.h>
#include <algorithm>
#define in(i) (i<<1)
#define out(i) (i<<1|1)
#define inv(i) (i&1?i^1:i|1)
using namespace std;
const int maxn=2010;
const int maxm=8010;
int n,m;
 
int to[maxm];
int next[maxm];
int head[maxn];
int tot;
void add(int x,int y)
{
    to[++tot]=y;
    next[tot]=head[x];
    head[x]=tot;
}
 
bool col[maxn];
int fin[maxn];
void dye(int x)
{
    col[x]=true;
    for(int i=head[x];i;i=next[i])
        if(!col[to[i]])
            dye(to[i]);
}
bool check(int x)
{
    memset(col,0,sizeof(col));
    dye(x);
    for(int i=1;i<=n;i++)
        if(col[in(i)]&&col[out(i)])
            return false;
    return true;
}
 
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=m;i++)
    {
        static int x,y;
        static char a[5],b[5];
        scanf("%d%s%d%s",&x,a,&y,b);
        x=a[0]=='Y'?in(x):out(x);
        y=b[0]=='Y'?in(y):out(y);
        add(inv(x),y);
        add(inv(y),x);
    }
    for(int i=1;i<=n;i++)
    {
        static bool x,y;
        x=check(in(i));
        y=check(out(i));
        if(!x&&!y)
            return puts("IMPOSSIBLE"),0;
        else if(!(x&&y))
            fin[i]+=x?1:2;
    }
    for(int i=1;i<=n;i++)
    {
        static char s[10]="?YN";
        printf("%c",s[fin[i]]);
    }
    puts("");
}
﻿
```

