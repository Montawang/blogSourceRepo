---
title: '[NOIP 初赛] 总结 <苹果放盘子问题> (POJ1664)'
mathjax: true
copyright: true
comment: true
date: 2018-10-08 19:51:02
tags:
  - NOIP 初赛
categories: NOIP
photo:
---

{% note default %}

初赛模拟测试错了一道简单题，似乎还是一道陈年老题，网上去查了一下，似乎还和 [POJ 1663](http://poj.org/problem?id=1664) 这道水题一模一样，这里总结一下

{% endnote %}

<!-- more -->

### 题目

大意：有 $m$ 个一模一样的苹果放入 $n$ 个一模一样的盘子中，求放法的数量 $k$。
注意：对于 $m=7,n=3$ 这组数据：$5,1,1$ 和 $1,5,1$ 是一样的。

### 思路

我们来分类讨论，令 $\operatorname{f}(m,n)$ 表示放法的数量 $k$，
1. $m< n$，此时我们把多余的盘子拿掉，$\operatorname{f}(m,n)=\operatorname{f}(m,m)$
2. $m\geqslant n$，此时我们再分两类来讨论：有盘子空着和没有盘子空着。有盘子空着时，$\operatorname{f}(m,n)=\operatorname{f}(m,n-1)$；没有盘子空着时，$\operatorname{f}(m,n)=\operatorname{f}(m-n,n)$
于是我们得出 $\operatorname{f}(m, n)$ 的表达式：

$$
\operatorname{f}(m,n)=
\begin{cases}
1 & \text{when $m=0$ or $n=1$} \\
\operatorname{f}(m,m) & \text{when $m< n$} \\
\operatorname{f}(m,n-1)+\operatorname{f}(m-n,n) & \text{otherwise}
\end{cases}
$$

所以我们就可以写出 POJ 1664 的代码了
```cpp
#include <iostream>
using namespace std;

int f(int m, int n) {
  if (m == 0 || n == 1) return 1;
  if (m < n) return f(m, m);
  else return f(m, n - 1) + f(m - n, n);
}

int main() {
  int m, n, t;
  cin >> t;
  while (t--) {
    cin >> m >> n;
    cout << f(m, n);
  }
  return 0;
}
```

至于初赛中的那道题嘛，手动打表就可以了，应该还挺快的（似乎还可以直接暴力枚举......）。

---

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-layout="in-article"
     data-ad-format="fluid"
     data-ad-client="ca-pub-7465666912424994"
     data-ad-slot="3198608984"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

<br/>