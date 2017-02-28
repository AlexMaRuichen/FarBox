[题目链接](https://oj.jdfz.com.cn/oldoj/problem.php?id=1325)

# Description

自从到了南蛮之地，孔明不仅把孟获收拾的服服帖帖，而且还发现了不少少数民族的智慧，他发现少数民族的图腾往往有着一种分形的效果(看Hint)，在得到了酋长的传授后，孔明掌握了不少绘图技术，但唯独不会画他们的图腾，于是他找上了你的爷爷的爷爷的爷爷的爷爷……帮忙，作为一个好孙子的孙子的孙子的孙子……你能做到吗？

# Input

每个数据一个数字，表示图腾的大小（此大小非彼大小）  $n\le 10$

# Output

这个大小的图腾
# Solution

分形图

$sum[i]$表示第$i$层的**部分三角形**个数

我们来观察一下这个数列的性质

*1 1*

*1 1* **2 2**

*1 1 2 2* **2 2 4 4**

*1 1 2 2 2 2 4 4* **2 2 4 4 4 4 8 8**

这个数列就是将自己倍长后乘2组成的!

$space[i][j]$表示第$i$层$j$和$j+1$之间空格的个数

 0  0  0  0  0  0  0 
 0  0  0  0  0  0  0 
 0  0  0  *2*  0  0  0 
 0  0  0  0  0  0  0 
 0  0  0  6  0  0  0 
 0  0  0  **4**  0  0  0 
 0  0  *2  2  2*  0  0 
 0  0  0  0  0  0  0 
 0  0  0 14  0  0  0 
 0  0  0 12  0  0  0 
 0  0  *2* 10  *2*  0  0 
 0  0  0  8  0  0  0 
 0  0  6  6  6  0  0 
 0  0  **4  4  4**  0  0 
 *2  2  2  2  2  2  2*
 0  0  0  0  0  0  0 

这个数组中,下面两个大三角形与上面的三角形构成相同

上面的三角形也是由自己分形而来

所以非递归解法就出现了

(虽然递归更好写)

``` c++
#include <stdio.h>
#include <algorithm>
using namespace std;
const int maxn=2050;
int n;
int sum[maxn];
int space[maxn][maxn];
int main()
{
	scanf("%d",&n);
	
	sum[1]=sum[2]=1;
	for(int i=1;i<=n;i++)
		for(int k=1;k<=(1<<i);k++)
			sum[k+(1<<i)]=sum[k]<<1;
	
	int mid=(sum[1<<n])>>1;
	
	for(int i=1;i<=n;i++)
		for(int k=1,cnt=1<<i;k<=(1<<i);k++,cnt-=2)
			space[k+(1<<(i-1))][mid]=max(cnt-2,0);
	
	for(int j=1;j<=n-2;j++)
		for(int k=1;k<=(1<<j);k++)
			for(int i=1;i<=sum[k];i++)
			{
				space[k+(1<<j)][mid-i]=space[k][mid+(sum[k]>>1)-i];
				space[k+(1<<j)][mid+i]=space[k][mid+(sum[k]>>1)-i];
			}
	
	for(int j=1;j<=n-2;j++)
		for(int k=1;k<=(1<<j);k++)
			for(int i=1;i<=sum[k+(1<<j)];i++)
			{
				space[k+(1<<j)+(1<<(n-1))][mid-i]=space[k+(1<<j)][mid+(sum[k+(1<<j)]>>1)-i];
				space[k+(1<<j)+(1<<(n-1))][mid+i]=space[k+(1<<j)][mid+(sum[k+(1<<j)]>>1)-i];
			}			
			
	for(int i=1;i<=(1<<n);i++)
	{
		for(int j=1;j<=(1<<n)-i;j++)
			putchar(' ');
		if(i&1)
			for(int j=1;j<=sum[i];j++)
			{
				putchar('/'),putchar('\\');
				for(int k=1;k<=space[i][mid-(sum[i]>>1)+j];k++)
					putchar(' ');
			}
		else
			for(int j=1;j<=sum[i];j++)
			{
				putchar('/'),putchar('_'),putchar('_'),putchar('\\');
				for(int k=1;k<=space[i][mid-(sum[i]>>1)+j];k++)
					putchar(' ');
			}
		puts("");
	}
}
```

