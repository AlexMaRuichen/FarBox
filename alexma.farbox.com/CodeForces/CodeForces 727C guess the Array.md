[题目链接](http://codeforces.com/problemset/problem/727/C)

## Description

给你一个长度为$N(N\le 5000)$的数列,你有$N$次机会询问两个位置的和,最后输出整个数列

## Solution

第一次做交互题留念

虽然是很水的小学解方程,但做完了感觉十分愉悦

就是每次查三个数两两的和,剩下的数单算

``` c++
#include <stdio.h>
#include <algorithm>
using namespace std;
const int maxn=5010;
int n;
int a[maxn];
int sum[4];
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n/3;i++)
	{
		printf("? %d %d",3*(i-1)+1,3*(i-1)+2);
		puts("");
		fflush(stdout);
		scanf("%d",&sum[1]);
		printf("? %d %d",3*(i-1)+1,3*(i-1)+3);
		puts("");
		fflush(stdout);
		scanf("%d",&sum[2]);
		printf("? %d %d",3*(i-1)+2,3*(i-1)+3);
		puts("");
		fflush(stdout);
		scanf("%d",&sum[3]);
		int x=sum[1]+sum[2]+sum[3];
		x/=2;
		a[3*(i-1)+1]=x-sum[3];
		a[3*(i-1)+2]=x-sum[2];
		a[3*(i-1)+3]=x-sum[1];
	}
	if(n%3==0)
	{
		printf("!");
		for(int i=1;i<=n;i++)
			printf(" %d",a[i]);
		puts("");
		fflush(stdout);
	}
	else if(n%3==1)
	{
		printf("? 1 %d",n);
		puts("");
		fflush(stdout);
		scanf("%d",&sum[1]);
		a[n]=sum[1]-a[1];
		printf("!");
		for(int i=1;i<=n;i++)
			printf(" %d",a[i]);
		puts("");
		fflush(stdout);
	}
	else if(n%3==2)
	{
		printf("? 1 %d",n-1);
		puts("");
		fflush(stdout);
		scanf("%d",&sum[1]);
		a[n-1]=sum[1]-a[1];
		printf("? 1 %d",n);
		puts("");		
		fflush(stdout);
		scanf("%d",&sum[1]);
		a[n]=sum[1]-a[1];
		printf("!");
		for(int i=1;i<=n;i++)
			printf(" %d",a[i]);
		puts("");
		fflush(stdout);
	}
}
```

