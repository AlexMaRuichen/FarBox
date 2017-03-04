[题目链接](http://poj.org/problem?id=3348)

# Description

求凸包面积

# Solution

主要是练习$O(N \log N)$ 复杂度维护凸包

[参考资料](http://blog.csdn.net/bone_ace/article/details/46239187)

Graham扫描法比较容易理解

首先找出纵坐标最小的点, 并一这个点为原点将其它点按极角从小到大排序, 极角相同按距离从小到大排序, 易知原点和排序后的第一个点一定是凸包中的点, 所以将它们压入一个栈

之后依次枚举每个点, 将连接当前点和栈顶第二个元素, 一直弹栈直到栈顶在向量右侧, 这样做到最后栈里的元素就是凸包

``` c++
#include <stdio.h>
#include <math.h>

#include <algorithm>

using namespace std;

const int maxn = 10010;
const int inf = 1e9;
const double eps = 1e-8;

struct point
{
	int x, y;
	point() {}
	point(int x, int y) :x(x), y(y) {}
}p[maxn], start(inf, inf), last;

point operator + (const point &a, const point &b)
{
	return point(a.x + b.x, a.y + b.y);
}

point operator - (const point &a, const point &b)
{
	return point(a.x - b.x, a.y - b.y);
}


int operator * (const point &a, const point &b)
{
	return a.x * b.y - a.y * b.x;
}

bool left(const point &a, const point &b, const point &p)
{
	return (p - a) * b < 0;
}

inline double alpha(const point &p)
{
	return atan2(p.y, p.x);
}

bool equal (double x, double y)
{
	return fabs(x - y) < eps;
}

int dis(const point &a, const point &b)
{
	return (a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y);
}

bool cmp(const point &a, const point &b)
{
	static double a1, a2;
	return equal(a1 = alpha(a - start), a2 = alpha(b - start)) ?  dis(a, start) < dis(b, start) : a1 < a2;
}

int id;

int top;
point stack[maxn];

void Graham(int n)
{
	for (int i = 1; i <= n; i++)
		if (start.y > p[i].y)
			id = i, start = p[i];
	swap(p[id], p[n--]);
	sort(p + 1, p + n + 1, cmp);
	stack[++top] = start;
	stack[++top] = p[1];
	for (int i = 2; i <= n; i++)
	{
		while (top > 1 && left(stack[top - 1], p[i] - stack[top - 1], stack[top]))
			--top;
		stack[++top] = p[i];
	}
	last = stack[top];
	int square = 0;
	for (int i = 2; i < top; i++)
		square += (stack[i] - start) * (stack[i + 1] - start);
/*	printf("%d\n", square);
	printf("%d\n", top);
	while (top)
		printf("%d %d\n", stack[top].x, stack[top].y), --top;*/
	printf("%d\n", square / 100);
}

int main()
{
	int n;
	scanf("%d", &n);
	for (int i = 1; i <= n; i++)
		scanf("%d%d", &p[i].x, &p[i].y);
	Graham(n);
}
``` 

