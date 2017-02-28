[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=1272)
## Description
给你一个N进制算式,求每个字母代表的数
## Solution
**我不要再写搜索了**

开始的时候十分智障输出的时候改成了ABC,最后发现不用将权值变成字母

裸搜索能过7个点

倒着算黑科技+已知一层三个数剪枝+已知一层两个数推第三个数优化=满分

``` c++
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <algorithm>
using namespace std;
int n;
int a[27];
int b[27];
int c[27];
int ref[27];//答案映射
bool v[27];
char s[30];
int pick[30];
int mx;//进位

void print()
{
	for(int i=1;i<=n;i++)
		if(i==1)
			printf("%d",ref[i]);
		else
			printf(" %d",ref[i]);
	puts("");
	exit(0);
}

void dfs(int x,int y,int val)
{
	for(int i=1;i<=x;i++)
	{
		if(ref[a[i]]==-1||ref[b[i]]==-1||ref[c[i]]==-1)
			continue;
		if(((ref[c[i]]-(ref[a[i]]+ref[b[i]]))%n+n)%n>mx)
			return;
	}
	if(x==0)
	{
		print();
		exit(0);
	}
	if(y==1)
	{
		if(ref[a[x]]!=-1)
			y==2?dfs(x-1,1,val):dfs(x,y+1,val);
		else
			for(int i=n-1;i>=0;i--)
				if(!v[i])
				{
					v[i]=1;
					ref[a[x]]=i;
					y==2?dfs(x-1,1,val):dfs(x,y+1,val);
					ref[a[x]]=-1;
					v[i]=0;
				}
	}
	else
	{
		int t1=ref[a[x]],t2=ref[b[x]],g=ref[c[x]];
		if(ref[b[x]]!=-1)
		{
			int sum=t1+t2+val;
			if(sum%n==g)
				y==2?dfs(x-1,1,sum/n):dfs(x,y+1,sum/n);
			else if(g==-1)
			{
				v[sum%n]=1;
				ref[c[x]]=sum%n;
				y==2?dfs(x-1,1,sum/n):dfs(x,y+1,sum/n);
				ref[c[x]]=-1;
				v[sum%n]=0;
			}
		}
		else
			for(int i=n-1;i>=0;i--)
				if(!v[i])
				{
					int sum=t1+i+val;
					if(sum%n!=g&&g!=-1)
						continue;
					if(g==-1)
					{
						if(v[sum%n])
							continue;
						v[sum%n]=1;
						ref[c[x]]=sum%n;
					}
					v[i]=1;
					ref[b[x]]=i;
					y==2?dfs(x-1,1,sum/n):dfs(x,y+1,sum/n);
					ref[b[x]]=-1;
					v[i]=0;
					if(g==-1)
					{
						v[sum%n]=0;
						ref[c[x]]=-1;
					}
				}
	}
}

int main()
{
//	freopen("1272.in","r",stdin);
//	freopen("1272.out","w",stdout);
	scanf("%d",&n);
	mx=2*n-2;
	mx/=n;
	memset(ref,-1,sizeof(ref));
	scanf("%s",s);
	for(int j=0;j<n;j++)
		a[j+1]=s[j]-'A'+1;
	scanf("%s",s);
	for(int j=0;j<n;j++)
		b[j+1]=s[j]-'A'+1;
	scanf("%s",s);
	for(int j=0;j<n;j++)
		c[j+1]=s[j]-'A'+1;
	dfs(n,1,0);
}

```
