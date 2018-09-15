---
title: '[数论] OI 中的模运算计算方法'
date: 2018-08-19 11:01:03
tags:
	- 数论
	- 模运算
categories: 数论
mathjax: true
copyright: true
---

### 写在前面

模运算在 OI 中是非常神奇的存在，有时候题目会非常好（m）心（m）地（p）让我们将答案取模，然而计算模的方法随着题目的变化而变化，网上很多博客又太零散，这里痛下决心总结一下。

<!-- more -->

### 预备知识

- 恒等式：
	- $(a\bmod n)\bmod n=a\bmod n$
	- 对于所有的正数 $x$ 有：$n^x\bmod n = 0$
	- 如果 p 是一个质数，且不为 b 的因数，此时由费马小定理有：$ab^{p−1}\bmod p=a\bmod p$
- 分配律
	- $(a-b)\bmod n=(a\bmod n-b\bmod n)\bmod n$
	- $(a+b)\bmod n=(a\bmod n+b\bmod n)\bmod n$
	- $ab\bmod n=(a\bmod n)(b\bmod n)\bmod n$

### 高次幂函数的取模

#### 暴力取模法
由于我们的高次幂函数往往拥有很高的幂，而我们在计算机中只有`int`、`float`、`double`、`long long`这几种变量类型，远远不能满足我们对于大数字运算的要求，从而导致数据溢出无法完成运算。
所以这里我们需要在每次迭代取模的过程中进行取模运算，从而保证数据不会溢出。代码如下：
```cpp
long long pow(long long a, long long b, long long c) {
	if (b == 0) return 1 % c;
	long long result = 1;
	while (b--) result = result * a % c;
	return result;
}
// 计算 a^b % c
```
这种算法的正确性毋庸置疑，但是如果幂指数太大的话，需要耗费的时间就更非常多，直接导致运算效率低下，所以只适用于指数不大的情况下使用。

#### 二分幂

要求 $a^n$，如果知道了 $a^{\frac{n}{2}}$ 那么再平方就行了。
令 $A=a^{\lfloor\frac{n}{2}\rfloor}$
若 $n$ 为偶数，那么 $Result=A^2$
若 $n$ 为奇数，那么 $Result=a\times A^2$
这样子复杂度从 $O(n)$ 降到了 $O(\log n)$

```cpp
long long pow(long long a, long long b, long long c) {
	if (b == 0) return 1 % c;
	if (b == 0) return 1;
	long long x = pow(a, b / 2, c);
	long long ans = (long long)x * x % m;
	if (b % 2 == 1) ans = ans * a % m;
	return (long long)ans;
}
```

#### 蒙哥马利算法

具体详细介绍见[维基百科](https://en.wikipedia.org/wiki/Montgomery_modular_multiplication)，这里直接上代码：

```cpp
long long Montgomery(long long a, long long b, long long c) {
	if (b == 0) return 1 % c;
	long long result = 1;
	long long temp = a;
	while (b > 0) {
		if (b & 1) result = (result * temp) % c;
		temp = (temp * temp) % c;
		b >>= 1;
	}
	return result;
}
```
### 组合数取模

#### 方法一：杨辉三角递推

使用递推公式：$C_n^m=C_{n-1}^m+C_{n-1}^{m-1}$
写成数组：$C[n][m]=C[n-1][m]+C[n-1][m-1]$
这也是杨辉三角的递推公式，所以边界为 $C[0][0]=1$。下面给出求一个组合数的方法：
```cpp
#include <iostream>
using namespace std;
int n, m;
long long c[10010];

int main() {
	cin >> n >> m;
	m = min(m, n - m);
	// 因为 c[n][m] == c[n][n - m]
	// 所以取最小值减少运算。
	c[0] = 1;
	for (int i = 1; i <= n; ++i)
		for (int j = m; j >= 1; --j)
			c[j] = c[j] + c[j - 1];
	cout << c[m];
	return 0;
}
// 因为只求一个组合数，所以使用了滚动数组的方式
```
由于递推时间复杂度为 $O(n^2)$，所以只适用于小数据的情况。

#### 方法二：乘法逆元

在数据很大的时候，我们对组合数取模就会比较麻烦，因为数据不一定能存下，而且通过观察组合数的公式：
$$
C_n^m=\frac{n!}{m!(n-m)!}=\frac{n\times (n-1)\times...\times(n-m+1)}{m!}
$$
我们发现组合数是带有除法的，不能走一步取一次模，为了解决除法取模的问题，人们发明了逆元这个[东西](https://en.wikipedia.org/wiki/Inverse_element)。
若 $a\times b\equiv1\pmod p$ 则称 $b$ 为 $a$ 在模 $p$ 下的乘法逆元，一般认为，$b < p$，$b$ 记为 $a^{-1}$。**注意，此处 $a$ 与 $p$ 必须互质，否则乘法逆元不存在。**那么逆元该怎么用呢？当然是借助它将除法转为乘法，再用分配律来计算了。
我们来看一个式子：
$$
\frac{a}{b}\equiv\frac{a}{b}\times b\times b^{-1}\equiv a\times b^{-1}\pmod p
$$
这样就可以把问题转化为求一个数的逆元，再转化为乘法运算。

##### 递推求逆元

求 $i​$ 在模 $p​$ 下的乘法逆元，满足 $gcd(i, p)=1, i< p​$
使用欧几里得除法的形式，令 $p=k\times i+r​$，则 $k=\lfloor\frac{p}{i}\rfloor,r=p\bmod i​$
显然，$k\times i+r\equiv 0(\bmod p)​$
两边同乘 $i^{-1}\times r^{-1}​$，得 $k\times r^{-1}+i^{-1}\equiv 0\pmod p​$
移项，整理：$i^{-1}\equiv -k\times r^{-1}\equiv (p-k)\times r^{-1}\pmod p​$
将 $k​$ 和 $r​$ 用 $p​$ 来替换：$i^{-1}\equiv (p-\lfloor\frac{p}{i}\rfloor)\times (p\bmod i)^{-1}\pmod p​$
使用 `inv[i]` 来表示 `i` 的逆元，有 `inv[i] = (p - p / i) * inv[p % i]`
时间复杂的为线性，代码如下：

```cpp
long long inv[10000010];
long long getInverse(long long x, long long p) {
	if (inv[x] != 0) return inv[x];
	inv[x] = (p - p / i) * getInverse(p % x, p) % p;
	return inv[x];
}
```

##### 费马小定理求逆元

费马小定理：~~如此可爱的定理名称~~ $a^{p-1}\equiv1\pmod p$ 当 $p$ 为素数时且 $gcd(a,p)=1$ 时。
所以求 $a$ 的逆元时，只要求出 $a^{p-2}$ 的值就可以了。

```cpp
long long Montgomery(long long a, long long b, long long c) {
	if (b == 0) return 1 % c;
	long long result = 1;
	long long temp = a;
	while (b > 0) {
		if (b & 1) result = (result * temp) % c;
		temp = (temp * temp) % c;
		b >>= 1;
	}
	return result;
}
long long getinverse(long long x, long long p) {
	return Montgomery(x, p - 2, p);
}
```

##### 逆元求组合数

从组合数原公式出发
$$
C_n^m=\frac{n!}{m!(n-m)!}=\frac{n\times (n-1)\times...\times(n-m+1)}{m!}
$$

对于分子，我们可以用传统方法，边乘边模，对于分母，我们可以求逆元再乘分子。

```cpp
#include <iostream>
using namespace std;

long long Montgomery(long long a, long long b, long long c) {
	if (b == 0) return 1 % c;
	long long result = 1;
	long long temp = a;
	while (b > 0) {
		if (b & 1) result = (result * temp) % c;
		temp = (temp * temp) % c;
		b >>= 1;
	}
	return result;
}

long long getInverse(long long x, long long p) {
	return Montgomery(x, p - 2, p);
}

long long c(long long n, long long m, long long p) {
	long long x = 1, y = 1;
	for (long long i = n; i >= n - m + 1; --i) x = x * i % p;
	for (long long i = 1; i <= m; ++i) y = y * i % p;
	return x * getInverse(y, p) % p;
}

int main() {
	long long n, m, p;
	cin >> n >> m >> p;
	cout << c(n, m, p);
	return 0;
}

```

**注：$p$ 一定要是大质数，起码得大于 $m$。若要求 $a$ 在模 $p$ 下的乘法逆元，必须要保证 $a$ 与 $p$ 互质。在此题中，要求 $m!$ 的乘法逆元，那么 $p$ 必须与 $m!$ 互质，所以 $p$ 要大于 $m$，且是个质数。~~我一开始忘记了这点，导致调了一年~~**

#### 方法三：卢卡斯定理

**适用于对答案模一个质数的情况。**
与逆元不同的是，这个方法不要求 $p$ 是个很大的素数，因此，适用范围更广（也许吧）。
**[卢卡斯定理](https://zh.wikipedia.org/wiki/%E5%8D%A2%E5%8D%A1%E6%96%AF%E5%AE%9A%E7%90%86)**：
对于非负整数 $m$ 和 $n$ 及素数 $p$，有同余式：
$$
\binom{n}{m}\equiv\prod_{i=0}^k\binom{n_i}{m_i} \pmod p
$$
成立。其中：
$$
n=n_kp^k+n_{k-1}p^{k-1}+...+n_1p+n_0
$$
并且
$$
m=m_kp^k+m_{k-1}p^{k-1}+...+m_1p+m_0
$$
以上两个式子是 $n$ 和 $m$ 的 $p$ 进制展开。当 $m < n$ 时，二项式系数 $\binom{n}{m}=0$。
可以通过[一系列的推导](https://www.luogu.org/blog/user28007/lucas)得出下面这个结论：
$$
\binom{n}{m}\equiv\binom{\lfloor\frac{n}{p}\rfloor}{\lfloor\frac{m}{p}\rfloor}\times \binom{n\bmod p}{m\bmod p} \pmod p
$$
由这个公式我们可以得出结论：**在 $n$ 和 $m$ 都小于 $p$ 时，公式一点用都没有。（啥？你问我为什么？$\binom{0}{0}=1$ 啊！）**所以这个公式是在 $n\geqslant p$ 或 $m\geqslant p$ 的情况下使用的。这样可以减小 $n$ 和 $m$，使之小于 $p$，再用乘法逆元去求组合数。 

```cpp
#include <bits/stdc++.h>
using namespace std;

long long Montgomery(long long a, long long b, long long c) {
	if (b == 0) return 1 % c;
	long long result = 1;
	long long temp = a;
	while (b > 0) {
		if (b & 1) result = (result * temp) % c;
		temp = (temp * temp) % c;
		b >>= 1;
	}
	return result;
}

long long getInverse(long long a, long long b) {
	return Montgomery(a, b - 2, b);
}

long long c(int n, int m, int p) {
	if (m == 0) return 1 % p;
	if (n == 0) return 0;
	if (n >= p || m >= p) return c(n / p, m / p, p) * c(n % p, m % p, p) % p;
	// 核心语句
	long long x = 1, y = 1;
	for (int i = n; i >= n - m + 1; --i) x = x * i % p;
	for (int i = 1; i <= m; ++i) y = y * i % p;
	return x * getInverse(y, p) % p;
}

int main() {
	long long n, m, p;
	cin >> n >> m >> p;
	cout << c(n, m, p);
	return 0;
}

```

### 未完待续......

---