---
title: '[NOIP 模拟] 记录一道玄学的题目'
mathjax: true
copyright: true
comment: true
date: 2018-10-05 15:17:57
tags:
  - NOIP 模拟
  - 线段树
  - 数论
categories: NOIP
photo:
---

{% note default %}

今天的第二道题实在是坑，考场上暴力都不能一次打对，最后订正的时候还 WA 了 20 多次，简直太坑了啊！！😭😭😭😭

{% endnote %}

<!-- more -->

### 题目大意

给出一个长度为 $n$ 数列，和 $m$ 次询问，每次求出一个区间 $[l,r]$ 的和然后将该区间的所有数变为原来的平方然后模上 $c=2305843008676823040$

### 题解

这道题给我的第一个感觉就是：这个模数太灵魂了，这么长而且还不是个质数（$c = 2^{29}\times 3\times 5\times 17\times 257\times 65537$）。于是打了个表，发现一个数平方了几十次之后就是一个定值了，所以可以考虑优化暴力以及优化区间查询和修改，所以就自然而然的想到了线段树（当然了，似乎纯暴力修改或者分块什么的......）

```cpp
#include <bits/stdc++.h>
using namespace std;
#define maxn 70000
#define modn 2305843008676823040
typedef unsigned long long uint64;
// 数据太大了，开个 unsigned long long 防止爆（似乎 long long 就可以了）

struct segmentTreeNode { int l, r; uint64 sum; bool flag; };
// 裸线段树
segmentTreeNode t[maxn * 4];
int n, m, x, y;
uint64 a[maxn];

#define l(x) t[x].l
#define r(x) t[x].r
#define sum(x) t[x].sum
#define flag(x) t[x].flag

inline uint64 mul(uint64 a, uint64 b) {
  uint64 ans = 0;
  while (b > 0) {
    if (b & 1) ans = (ans + a) % modn;
    a = (a + a) % modn;
    b >>= 1;
  }
  return ans;
} // 快速乘，防爆装备

inline void build(int p, int l, int r) {
  l(p) = l; r(p) = r; flag(p) = false;
  if (l == r) { sum(p) = a[l]; return; }
  int mid = (l + r) >> 1;
  build(p * 2, l, mid);
  build(p * 2 + 1, mid + 1, r);
  sum(p) = sum(p * 2) + sum(p * 2 + 1);
  sum(p) %= modn; // 别忘记模！！！
  flag(p) = flag(p * 2) && flag(p * 2 + 1);
  // 玄学更新
}

inline uint64 query(int p, int l, int r) {
  if (l <= l(p) && r(p) <= r) return sum(p);
  int mid = (l(p) + r(p)) >> 1;
  uint64 val = 0;
  if (l <= mid) (val += query(p * 2, l, r)) %= modn;
  if (r > mid) (val += query(p * 2 + 1, l, r)) %= modn;
  return val;
}

inline void update(int p, int l, int r) {
  if (flag(p)) return;
  if (l(p) == r(p)) {
    uint64 tmp = mul(sum(p), sum(p));
    if (tmp == sum(p)) flag(p) = true;
    sum(p) = tmp;
    return;
  }
  int mid = (l(p) + r(p)) >> 1;
  if (l <= mid) update(p * 2, l, r);
  if (r > mid) update(p * 2 + 1, l, r);
  sum(p) = sum(p * 2) + sum(p * 2 + 1);
  sum(p) %= modn; // 别忘记模！！！
  flag(p) = flag(p * 2) && flag(p * 2 + 1);
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(NULL);
  cin >> n >> m;
  for (int i = 1; i <= n; ++i) cin >> a[i];
  build(1, 1, n);
  while (m--) {
    cin >> x >> y;
    cout << query(1, x, y) << endl;
    update(1, x, y);
  }
  return 0;
}
```

嗯，接下来证明一下为什么会变成一个定值。因为：
$$
c = 2305843008676823040 = 2^{29}\times 3\times 5\times 17\times 257\times 65537
$$
所以我们可以知道：
$$
\varphi(c)=2^{28}\times 2\times 4\times 256\times 65536=2^{59}
$$
然后我们来分类讨论对于一个数 $x$，如果 $\gcd(x, c)=1$，那么根据费马-欧拉定理：
$$
x^{\varphi(c)}\equiv 1\pmod c
$$
所以 $x^{59}$ 之后它就是 $1$ 了。如果 $\gcd(x, c)\neq 1$：

1. 如果 $x=c$，那么显然，$x$ 会变成一个定值
2. 如果 $x\neq c$，那么我们一定可以从 $c$ 中分离出一个 $c'$ 使得 $\gcd(x,c')=1$，而 $\varphi(c')<59$，所以 $x$ 在 $59$ 次方前就可以变成一个定值。

证毕，太妙了！

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