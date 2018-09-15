---
title: '[算法] RMQ 问题与 ST 算法'
date: 2018-08-11 16:31:36
tags:
	- 算法
	- RMQ 问题
	- ST 算法
categories: 算法
mathjax: true
copyright: true
---

### 写在前面

因为很多次模拟测试都败在区间最值问题（RMQ）上，所以决定开一篇博客好好总结一下。本篇文章着重介绍 RMQ 问题中最著名的 ST 算法。
<!-- more -->

### RMQ 问题

给定一个长度为 $N$ 的数列 $A$，然后进行若干次询问，每次给定一个区间 $[l_i,r_i]$ 求这个区间中的最大或最小值，这就是区间最值问题，也就是 RMQ 问题。一般来说，这样的问题都要求区间的查询是在线的，也就是提出一个查询回答一次，不能全部收到后统一回答。对于某些确定区间求最值的问题，通常采用倍增的方法来求解。而 ST 算法其实就是倍增的产物，同时加入了动态规划的思想。

### ST 算法

在 RMQ 问题中，ST 算法可以在 $O(NlogN)$ 的时间预处理数据后，以 $O(1)$ 的时间在线回答查询。一个序列的子空间个数显然有 $O(N^2)$ 个，我们首先在这个规模为 $O(N^2)$ 的状态空间中选择一些 $2$ 的整数次幂的位置作为代表值。

设 $F[i,j]$ 表示子空间 $A$ 中下标在在子区间 $[i,i+2^j-1]$ 里数的最大值，即从 $i$ 开始的 $2^j$ 个数的最大值。显然 $F[i,0]=A[i]$，即数列 $A$ 在子区间 $[i,i]$ 中的最大值。

递推时，我们将子区间长度成倍增长，公式如下：

$$F[i,j]=max\lbrace F[i,j-1],F[i+2^{j-1},j-1] \rbrace$$

即长度为 $2^j$ 的子区间的最大值是左右两半长度为 $2^{j-1}$ 的子区间的最大值中较大的那一个。所以我们先预处理我们要用到的数组：

```cpp
void STprework() {
	for (int i = 1; i <= n; ++i) f[i][0] = a[i];
	int t = log(n) / log(2) + 1;
	for (int j = 1; j < t; ++j)
		for (int i = 1; i <= n - (1 << j) + 1; ++i)
			f[i][j] = max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);
}
```

当我们要查询区间 $[l,r]$ 的最值时，我们先计算出一个 $k$，满足 $2^k < r-l+1 \leqslant 2^{k+1}$，也就是使二的 $k$ 次幂小于区间长度的前提下最大的 $k$。那么“从 $l$ 开始的 $2^k$ 个数”和“以 $r$ 结尾的 $2^k$ 个数” 这两段一定覆盖了原来的整个区间，这两段的最大值分别为 $F[l,k]$ 和 $F[r-2^k+1,k]$，二者中较大的就是整个区间的最值。因为求的是最大值，所以只要覆盖区间就行，重叠是没有关系的。

```cpp
int STquery(int l, int r) {
	int k = log(r - l + 1) / log(2);
	return max(f[l][k], f[r - (1 << k) + 1][k]);
}
```

如果要进一步提高执行效率，可以将 $f$ 数组的两个维度交换，这样对缓存更加友好。并且可以预处理出 $1 \thicksim n$ 中 $log_2 n$ 的值，甚至是各个区间对应的 $k$ 的值，具体方法视情况而定。

### 总结

1. 为什么使用 `log()` 函数而不是 `log2()` 呢，原因是前者效率更快，而且众所周知，对数是有换底公式的（啥？你不知道？赶紧补一下高中数学必修一吧！），所以这里效率会高一点
2. [ST 表的模板题地址——洛谷 p3865](https://www.luogu.org/problemnew/show/P3865)

附上完整代码：

```cpp
#include <bits/stdc++.h>
using namespace std;
#define maxn 110000
#define max(a, b) (a > b ? a : b)
#define G(a) putchar(a + 48)
int n, m, A[maxn];
int li, ri;
int f[21][maxn];
int logn[maxn];

inline int read() {
	int ans = 0;
	char c;
	bool sign = false;
	while (!isdigit(c = getchar()) && c != '-');
	if (c == '-') {
		sign = true;
		c = getchar();
	}
	do {
		ans = (ans << 3) + (ans << 1) + (c - '0');
	} while (isdigit(c = getchar()));
	return sign ? -ans : ans;
}


inline void write(int s){
	if (s < 0) {
		G(-3);
		s = -s;
	}
	if (s > 9) write(s / 10);
	G(s % 10);
}

void STprework() {
	for (int i = 1; i <= n; ++i) f[0][i] = A[i];
	int t = logn[n] + 1;
	for (int j = 1; j < t; ++j)
		for (int i = 1; i <= n - (1 << j) + 1; ++i)
			f[j][i] = max(f[j - 1][i], f[j - 1][i + (1 << (j - 1))]);
}

int STquery(int l, int r) {
	int k = logn[r - l + 1];
	return max(f[k][l], f[k][r - (1 << k) + 1]);
}

void pre() {
	logn[1] = 0;
	logn[2] = 1;
	for (int i = 3; i <= n; ++i) logn[i] = logn[i / 2] + 1;
}
int main() {
	n = read();
	m = read();
	for (int i = 1; i <= n; ++i) A[i] = read();
	pre();
	STprework();
	for (int i = 1; i <= m; ++i) {
		li = read();
		ri = read();
		write(STquery(li, ri));
		putchar('\n');
	}
	return 0;
}
```

---