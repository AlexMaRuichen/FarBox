
[题目链接] (http://poj.org/problem?id=3977)

# Description

给你$N$个整数, 要求选择一个子集使得子集和的绝对值最小

$N \le 35$

# Solution

meet in the middle, 枚举一半的子集

与方程的解数类似

``` c++
#include <stdio.h>
#include <string.h>

#include <algorithm>
#include <map>

using namespace std;

typedef long long LL;

template <typename T> inline bool tense(T &a, const T &b)
{
	return (b < a ? a = b, true : false);
}
template <typename T> inline bool relax(T &a, const T &b)
{
	return (a < b ? a = b, true : false);
}

template <typename T> inline T abs(const T &a)
{
	return a >= 0 ? a : -a;
}

const int maxn = 45;
const LL inf = 1e18;

int n;
LL a[maxn];
bool v[maxn];

struct data
{
	LL sum;
	int cnt;
	data() {}
	data(LL sum, int cnt) :sum(sum), cnt(cnt) {}
};

bool operator < (const data &a, const data &b)
{
	return abs(a.sum) ^ abs(b.sum) ? abs(a.sum) < abs(b.sum) : a.cnt < b.cnt;
}


map <LL, int> m;
map <LL, int> :: iterator it;

int main()
{
	while (scanf("%d", &n) && n)
	{
		m.clear();
		for (int i = 1; i <= n; i++)
			scanf("%I64d", &a[i]);
		data ans(inf, 0);
		int cnt1 = n >> 1, cnt2 = n - cnt1;
		int mask1 = 1 << cnt1, mask2 = 1 << cnt2;
		for (int i = 1; i < mask1; i++)
		{
			LL sum = 0;
			int cnt = 0;
			for (int j = 0; j < cnt1; j++)
				if (i & (1 << j))
					sum += a[j + 1], cnt++;
			tense(ans, data(sum, cnt));
			if (m.count(sum))
				tense(m[sum], cnt);
			else
				m[sum] = cnt;
		}	
		for (int i = 1; i < mask2; i++)
		{
			LL sum = 0;
			int cnt = 0;
			for (int j = 0; j < cnt2; j++)
				if (i & (1 << j))
					sum += a[j + 1 + cnt1], cnt++;
			tense(ans, data(sum, cnt));
			it = m.lower_bound(-sum);
			for (int t = 0; t < 2; t++, it--)
			{
				if (it == m.end())
				{
					if (it == m.begin())
						break;
					else
						continue;
				}
				tense(ans, data(it->first + sum, it->second + cnt));
			}
		}
		printf("%I64d %d\n", abs(ans.sum), ans.cnt);
	}
}

```