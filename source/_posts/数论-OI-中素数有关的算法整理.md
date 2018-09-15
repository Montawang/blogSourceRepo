---
title: '[数论] OI 中素数有关的算法整理'
date: 2018-08-22 18:26:19
tags:
	- 数论
	- 素数
categories: 数论
mathjax: true
copyright: true
---

### 写在前面

本来是想总结一道数论神题的，但是怎么证明似乎都不是很完美，而且代码的实现涉及到很多数论有关的算法，于是想现总结一下与素数有关的算法。

<!-- more -->

### 素数的判定

#### Brute-Force 朴素算法

额，这个就不多说了吧，最无脑的，$2\thicksim n$ 枚举所有小于 $n$ 的数来进行判定。

```cpp
#include <bits/stdc++.h>
using namespace std;

bool isPrime(int n) {
	if (n == 2) return true;
	for (int i = 2; i < n; ++i)
		if (n % i == 0) return false;
	return true;
}

int main() {
	int n;
	cin >> n;
	cout << (isPrime(n) ? "Yes" : "No") << endl;
	return 0;
}
```

#### Optimized-Brute-Force 优化过的朴素算法

如果一个数不是素数是合数， 那么一定可以由两个大于 $1$ 自然数相乘得到， 其中一个大于或等于它的平方根，一个小于或等于它的平方根。并且成对出现。所以可以优化朴素算法

```cpp
#include <bits/stdc++.h>
using namespace std;

bool isPrime(int n) {
	if (n == 2) return true;
	for (int i = 2; i * i < n; ++i)
		if (n % i == 0) return false;
	return true;
}

int main() {
	int n;
	cin >> n;
	cout << (isPrime(n) ? "Yes" : "No") << endl;
	return 0;
}
```

#### 筛法求素数

上面两个菜鸡方法只能针对数很小的情况，这些情况在 OI 中出现的话出卷老师会~~死得很惨~~变成广大 OIer 的恩人，然而这种情况是不可能的。于是又有了更高效的方法——筛法。它的思想就是将一个范围内的素数全部筛出来，预处理之后方便查找和判定。最朴素的筛法莫过于 Eratosthenes 筛法了~~（不要纠结这个名字怎么读，因为我也不会）~~，它是将所有素数的倍数都给筛掉，剩下的就是素数了。时间复杂度为 $O(N\log\log N)$ 接近线性

```cpp
#include <bits/stdc++.h>
using namespace std;
#define maxn 1000010
bool prime[maxn];
void Eratosthenes() {
	memset(prime, true, sizeof(prime));
	prime[1] = false;
	for (int i = 2; i <= maxn; ++i) {
		if (!prime[i]) continue;
		for (int j = i; j <= maxn / i; ++j) {
			// 这里是一个小优化，因为小于 i^2 的数在前面肯定已经筛过了
			// 所以 j 从 i 开始取
			prime[j * i] = false;
		}
	}
}
int main() {
	int n;
	Eratosthenes();
	cin >> n;
	cout << (prime[n] ? "Yes" : "No") << endl;
	return 0;
}
```
然而，Eratosthenes 筛会重复标记素数，造成时间复杂度冗余，如果对于每个数有唯一的方法确定它，那么就可以解决这个问题。由此引出了线性筛这个方法，它通过从大到小累计质因子的方法标记每一个合数，即让 $12$ 只有 $2\times 2\times 3$ 这种产生方式。设数组 $fac$ 记录每个数的最小质因子，我们按照下面的步骤来维护 $fac$：

1. 依次考虑 $1\thicksim n$ 之见的每一个数 $i$
2. 若 $fac[i]=i$ 说明 $i$ 是质数，将它保存下来
3. 扫描不大于 $fac[i]$ 的每个质数 $p$，令 $fac[i\times p]=p$，即在 $i$ 的基础上累积一个质因子 $p$，因为 $p\leqslant fac[i]$，所以 $p$ 为合数 $i\times p$ 的最小质因子。

这样子，每个合数只会被它的最小质因子 $p$ 筛一次，时间复杂度为线性。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define maxn 1000010
int fac[maxn], prime[maxn];
void LinearSieve(int n) {
	memset(fac, 0, sizeof(fac));
	int m = 0;
	for (int i = 2; i <= n; ++i) {
		if (fac[i] == 0) { fac[i] = i; prime[++m] = i; }
		for (int j = 1; j <= m; ++j) {
			// 跳出循环的条件
			if (prime[j] > fac[i] || prime[j] > n / i) break;
			fac[i * prime[j]] = prime[j];
		}
	}
	for (int i = 1; i <= m; ++i) cout << prime[i] << " ";
}
int main() {
	int n;
	LinearSieve(100000);
	return 0;
}
```
线性筛又名**欧拉筛**。

#### 伪素数测试

在开始 Miller-Rabin 算法之前我们先来看一下伪素数测试，首先要了解费马小定理： $a^{p-1}\equiv1(\bmod p)$ 当 $p$ 为素数时且 $gcd(a,p)=1$ 时。费马小定理的逆命题虽然不是真命题，但是几乎成立，而不成立的时候，即如果 $n$ 是一个合数且 $a^{n-1}\equiv 1(\bmod n)$，那么称 $n$ 为基为 $a$ 的**伪素数**。
如果我们找到一个 $a$ 使得 $n$ 不满足上述等式，那么 $n$ 必定为合数，那如果满足呢？$n$ 不一定是素数，这样的数被称作 Carmicheal 数，但是这种数十分稀少，前 $10000$ 个 $n$ 中只有 $22​$ 个数为 Carmicheal 数，所以这种方法还是比较可靠的。附上算法导论中的伪代码：

<div style="align: center"> <img src="pic1.png"/> </div>

因为 Carmicheal 数的存在，人们寻求更好的，更准确的素数判定方法，于是就有了 Miller-Rabin 算法

#### Miller-Rabin 算法

通过费马小定理我们可以得出一个推论，若 $p$ 为一个奇素数，那么有：
$$
a^p\equiv a\pmod p,1\leqslant a\leqslant p
$$

我们记
$$
p-1=2^kq,2\nmid q
$$
设 $a$ 是不被 $p$ 整除的数，那么下面两个条件之一必然成立：

-   $a^q \equiv 1 \pmod p$
-   $a^q, a^{2q}, a^{2^2q}, \dots, a^{2^{k-1}q} \equiv -1 \pmod p$ 之中有一个等式成立

根据如下命题：
$$
a^2 \equiv 1 \pmod p \Rightarrow a \equiv \pm 1 \pmod p
$$
又因为上面第二个条件中的数，每一个都是前一个的平方，且最后一个数的平方是 $1$，所以如果表中一个数它模 $p$ 不余 $1$，但是它的平方模 $p$ 余 $1$，那么那个数一定是 $-1$，所以在这种情况下表中包含 $ -1$，又或者表中全是 $1$，那么第一个条件就会成立。

所以我们得到了 Miller-Rabin 素性测试的方法，也就是说，如果一个数不满足上面的性质，那么它就是和数，这个 $a$ 就成为证据。而 Miller-Rabin 的证据和上面提到的方法的证据不同，它能够保证每一个奇合数 $p$ 都会有不少于 $\frac{(p−1)}{2}$个证据（具体证明详见算法导论）。

我们只要随机 $50$ 个数来测试，那么测试失败的概率一定小于 $2^{−50}$，大约是 $8.9⋅10^{−16}$，如果你觉得不够，还可以找更多的数来测试。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long  int64;

inline int64 rand64() { return abs((int64)rand() << 32 | rand()); }
inline int64 random(int64 x, int64 y) { return x + rand64() % (y - x + 1); }
// 随机数生成函数
int64 montgomery(int64 a, int64 b, int64 c) {
	int64 result = 1;
	int64 temp = a;
	while (b > 0) {
		if (b & 1) result = (result * temp) % c;
		temp = (temp * temp) % c;
		b >>= 1;
	}
	return result;
}

bool witness(int64 a, int64 p) {
	int k = 0;
	int64 q = p - 1;
	while ((q & 1) == 0) { k++; q >>=1; }
	int64 v = montgomery(a, q, p); // 判断一
	if (v == 1 || v == p - 1) return false;
	while (k--) {
		v = v * v % p;
		if (v == p - 1) return false; // 判断二
	}
	return true;
}

bool MillerRabin(int64 p) {
	if (p == 1) return false;
	if (p == 2) return true;
	if (p % 2 == 0) return false;
	// 这里其实可以加更多的特判来节约时间
	for (int i = 1; i <= 50; ++i)
		if (witness(random(1, p - 1), p)) return false;
	return true;
}
int main() {
	int n;
	cin >> n;
	for (int i = 1; i <= n; ++i)
		if (MillerRabin(i)) cout << i << " ";
	// 输出 1 ~ n 之间的所有素数
	return 0;
}
```

对于大的素数，为了节约判定的时间，我们只能退而求其次选择非完美算法来进行验证，不过不得不说，Miller-Rabin 其实已经很完备了。

#### 总结

事实上，对于素数的判定还有很多算法，例如 [Solovay-Strassen 算法](https://en.wikipedia.org/wiki/Solovay%E2%80%93Strassen_primality_test)和 [AKS 算法](https://en.wikipedia.org/wiki/AKS_primality_test)，然而这两个似乎都超出了 OI 的范围，一般不会用得到，这里也就不再赘述。

### 质因数分解

#### 试除法

结合素数判定的“试除法”和“Eratosthenes 筛法”，我们可以扫描 $2\thicksim\lfloor\sqrt{n}\rfloor$ 中的每个数 $d$，若 $d$ 能整除 $n$，则从 $n$ 中除掉所有的因子 $d$，同时累计个数。

```cpp
int p[maxn], c[maxn];
void divide(int n) {
	int m = 0;
	for (int i = 2; i * i <= n; ++i) {
		if (n % i == 0) {
			p[++m] = i;
			c[m] = 0;
			while (n % i == 0) { n /= i; c[m]++; }
		}
	}
	if (n > 1) { p[++m] = n; c[m] = 1 }
	for (int i = 1; i <= m; ++i) cout << p[i] << '^' << c[i] << endl;
}
```
#### Pollard-Rho 算法

Pollard-Rho 也是一个不完美算法，不完美之处在时间复杂度上体现。Pollard-Rho算法分解一个数 $n$ 的过程大体上是这样子的：

1. 找到一个数 $p$，使得 $p|n$，将 $n$ 分解为 $p$ 与 $\frac{n}{p}$
2. 如果 $p$ 或 $\frac{n}{p}$ 不为质数，将其带入递归上述过程
3. 如果其是质数，将其记录并退出

那有人就会问了：这跟暴力分解有什么区别？好像时间复杂度比暴力还高啊！！？？所以，下面的优化才是关键。第一个优化，使用 Miller-Rabin 判定其是否为质数。第二个优化才是最关键的：对于一个大整数n，我们要找到一个 $p$ 满足 $p|n$，这如同大海捞针。但是如果我们要找出 $p_1$、$p_2$，使得 $(|p_1−p_2|)|n$，这看起来似乎要容易一些。实际上我们只需要找出 $\gcd((|p1−p2|),n)>1$的 $p_1$、$p_2$，则其 $\gcd$ 值肯定为n的约数。这看起来又容易了一些。实际上，这让整个算法容易许多。根据[生日悖论](https://zh.wikipedia.org/wiki/%E7%94%9F%E6%97%A5%E5%95%8F%E9%A1%8C)，这种两两比较的方式，在加入比较的数越来越多的时候，其效率会大大提升，比找一个数的效率快很多。于是，找 $p$ 的过程变成了这个样子：

1. 找到一个数 $p_1$
2. 通过某种玄学推导手段找出一个与 $p_1$ 对应的 $p_2$
3. 判断 $\gcd((|p1−p2|),n)$ 是否大于 $1$，不大于则将 $p_2$作为新的 $p_1$，重复过程，否则就找到了

为什么要用玄学手段？因为只有通过推导手段，才能保证不做重复判断。理论上的推导手段可以有很多，但实际使用中一般使用如下公式推导：
$$
p_2=(p_1^2+c)\bmod n
$$
其中 $c$ 为随机常数。
这个公式的好处:

  1. 推导出来的 $p_2-p_1$ 差值基本不会相等。
  2. 可以证明，该推导结果会出现循环。也就是说，在出现循环之前，结果不会重复，少做了许多无用的判断。

出现循环了怎么办？换一个随机常数再搞。这就是该算法“非完美”的地方，~~时间复杂度要看脸的~~。那么如何判环呢？这里介绍一个算法：[Floyd 判圈](https://zh.wikipedia.org/wiki/Floyd%E5%88%A4%E5%9C%88%E7%AE%97%E6%B3%95)。 需要注意的是，之所以不能一个标记定在原地，是因为循环节不一定在开头就产生，可能走着走着才遇到循环。这条路径就类似于 $\rho$，Pollard-Rho 算法也是因此得名的。顺便附上算法导论中的一张玄学图：

<div style="align: center"> <img src="pic2.png"/> </div>

注意，在小数分解的时候就别用这个了，老老实实用试除法吧。

### 未完待续......

---



