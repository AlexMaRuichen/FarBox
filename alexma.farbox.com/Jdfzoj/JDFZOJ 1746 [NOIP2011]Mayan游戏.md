[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=1746)
## Description
宝石迷阵???

详见题目描述

## Solution
深搜题目*1,请注意消除函数和下落函数的写法,但是不知道是不是封装的原因常数莫名大,以至于cheat过了最后一个点,如果知道优化方法也请告诉我

``` c++
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <algorithm>
using namespace std;
int ans[10][5];
int n;
struct Mayan_puzzle
{
	int map[10][10];
	Mayan_puzzle()
	{
		memset(map,0,sizeof(map));
	}
	bool check()
	{
		for(int i=1;i<=5;i++)
			for(int j=1;j<=7;j++)
				if(map[i][j])
					return false;
		return true;
	}
	void drop()
	{
		for(int i=1;i<=5;i++)
		{
			int T=0,tmp;
			for(int j=1;j<=7;j++)
			{
				tmp=map[i][j];
				map[i][j]=0;
				if(tmp)
					map[i][++T]=tmp;
			}
		}
	}
    //掉落函数
	bool remove()
	{
		bool flag;
		bool v[10][10];
		flag=false;
		for(int i=1;i<=5;i++)
			memset(v[i],0,sizeof(v[i]));
		for(int i=1;i<=5;i++)
			for(int j=1;j<=7;j++)
			{
				if(i<=3&&map[i][j]&&map[i][j]==map[i+1][j]&&map[i][j]==map[i+2][j])
					v[i][j]=v[i+1][j]=v[i+2][j]=true;
				if(j<=5&&map[i][j]&&map[i][j]==map[i][j+1]&&map[i][j]==map[i][j+2])
					v[i][j]=v[i][j+1]=v[i][j+2]=true;
			}
		for(int i=1;i<=5;i++)
			for(int j=1;j<=7;j++)
				if(v[i][j])
					map[i][j]=0,flag=true;

		return flag;
	}
    //消除函数,验证是否有三个横向或纵向相连,若有便将其标记,最后一起消除

	Mayan_puzzle left(int x,int y)
	{
		swap(map[x][y],map[x-1][y]);
		drop();
		while(remove())
			drop();
		return *this;
	}

	Mayan_puzzle right(int x,int y)
	{
		swap(map[x][y],map[x+1][y]);
		drop();
		while(remove())
			drop();
		return *this;
	}
    //注意循环消除和掉落,也不要忘记最开始的一次掉落
	void print()
	{
		puts("***************************");
		for(int i=7;i;i--)
		{
			for(int j=1;j<=5;j++)
				printf("%d ",map[j][i]);
			puts("");
		}
		puts("***************************");
	}
    //调试函数
}tmp;
void print()
{
	for(int i=0;i<n;i++)
		printf("%d %d %d\n",ans[i][1],ans[i][2],ans[i][3]);
}
void dfs(int dep,Mayan_puzzle p)
{
//	printf("%d\n",dep);
//	p.print();
	if(dep==n)
	{
		if(p.check())
		{
			print();
			exit(0);
		}
		return;
	}
	for(int i=1;i<5;i++)
		for(int j=1;j<=7;j++)
			if(p.map[i][j])
			{
				ans[dep][1]=i-1;
				ans[dep][2]=j-1;
				ans[dep][3]=1;
				tmp=p;
				dfs(dep+1,tmp.right(i,j));
				ans[dep][1]=0;
				ans[dep][2]=0;
				ans[dep][3]=0;
			}
	for(int i=2;i<=5;i++)
		for(int j=1;j<=7;j++)
			if(p.map[i][j]&&p.map[i][j-1]!=p.map[i][j])
			{
				ans[dep][1]=i-1;
				ans[dep][2]=j-1;
				ans[dep][3]=-1;
				tmp=p;
				dfs(dep+1,tmp.left(i,j));
				ans[dep][1]=0;
				ans[dep][2]=0;
				ans[dep][3]=0;
			}
}
int main()
{
	scanf("%d",&n);
	Mayan_puzzle p;
	for(int i=1;i<=5;i++)
	{
		for(int j=1;;j++)
		{
			scanf("%d",&p.map[i][j]);
			if(!p.map[i][j])
				break;
		}
	}
	dfs(0,p);
	puts("-1");
}

```
