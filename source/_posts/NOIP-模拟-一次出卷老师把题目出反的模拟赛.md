---
title: '[NOIP 模拟] 一次出卷老师把题目出反的模拟赛'
mathjax: true
copyright: true
comment: true
date: 2018-10-01 15:35:03
tags:
  - NOIP 模拟
categories: NOIP
photo: /2018/10/01/NOIP-模拟-一次出卷老师把题目出反的模拟赛/photo1.png
---

{% note success %}

**在这里先祝祖国母亲生日快乐，以及成为世界上存在时间最长的社会主义国家！😊😊**。

{% endnote %}

{% note default %}

好了，说今天这套题目，隶属于国庆七连测😭，第一道题最恶心，第二题最难想，第三道题最简单。

{% endnote %}

<!-- more -->

### T1 bread

#### 题目

    N 个数，M 次操作，给出两个参数 p, q，第i次染色将区间 (i * p + q) % N + 1 和 (i * q + p) % N + 1 之间的数变为 i（后面的操作将前面的覆盖）

    输入 M, N, p, q

    输出每个数的最终数字

    100% 数据 n <= 1e6, m <= 1e7
    m * p + q, m * q + p 在 int 范围内

#### 题解

一开始看到这道题是懵逼的，先打了一波暴力模拟，最大的数据跑一年都跑不出来，于是开始思考更好的算法，后来在某大佬的提示下，想到了并查集的做法。
先打表，发现在 $M > N$ 时，区间是会有循环的，并且是 $N$ 次一循环，而这道题中最后某个点的颜色是以最后一次染色为准的，所以只要对 $\max\lbrace M - N + 1, 1\rbrace$ 到 $M$ 这几次操作进行运算即可。但是发现这样还是会超时，因为区间是会有重复的，这会造成冗余的复杂度，所以需要考虑如何将这些运算去掉。考虑倒序进行处理，当我们对区间 $[l, r]$ 进行处理时，对于我们遇到的每一个点 $k$ 有两种情况：

1. $k$ 未被更新过。
2. $k$ 已经被更新过了，那么上次更新时它所在区间的所有的点也都被更新过了。

从 $r$ 向前到 $l$ 进行更新，我们记录点 $k$ 上次更新时的左边界 $l'$，若 $k$ 被更新过，那么直接跳到 $l'-1$ 再继续向前更新。那为什么要用并查集呢？因为若 $l'-1$ 也被更新过了，就可以直接跳到更前面，进一步减少冗余运算，同时也防止数据卡我们。

贴上代码：
```cpp
#include <bits/stdc++.h>
using namespace std;

inline char nextchar() {
  static char buf[100000], *p1 = buf, *p2 = buf;
  return (p1 == p2) &&
    (p2 = (p1 = buf) + fread(buf, 1, 100000, stdin), p1 == p2) ? EOF : *p1++;
}

inline void read(int &x) {
  x = 0;
  static char ch = 0;
  static bool sign = false;
  while (!isdigit(ch)) { sign |= (ch == '-'); ch = nextchar(); }
  while (isdigit(ch)) { x = x * 10 + (ch ^ 48); ch = nextchar(); }
  x = sign ? -x : x;
}

inline void write(int x) {
  static int stk[100], top = 0;
  if (x == 0) { putchar('0'); return; }
  if (x < 0) { putchar('-'); x = -x; }
  while (x) { stk[++top] = x % 10; x /= 10; }
  while (top) putchar(stk[top--] + '0');
}

struct PAINT { int l, r; };

#define maxm 10000010
#define maxn 1000010

int N, M, p, q, ans[maxn], fa[maxn];
bool painted[maxn];
PAINT paint[maxm];

inline int get(int x) {
  if (fa[x] == x) return x;
  return fa[x] = get(fa[x]);
  // 这里交的时候打成了 fa[x] == get(fa[x]) TAT
  // 然而只 WA  了一个点 orz
}

int main() {
  freopen("bread.in", "r", stdin);
  freopen("bread.out", "w", stdout);
  read(N); read(M); read(p); read(q);
  memset(ans, 0, sizeof(ans));
  for (int i = 1; i <= N; ++i) fa[i] = i;
  int low = max(M - N + 1, 1);
  for (int i = M; i >= low; --i) {
    int l = min((i * p + q) % N + 1, (i * q + p) % N + 1);
    int r = max((i * p + q) % N + 1, (i * q + p) % N + 1);
    paint[i].l = l;
    paint[i].r = r;
  } // 防止区间出错，因为又可能出现 l > r 的情况。
  for (int i = M; i >= low; --i) {
    int l = paint[i].l;
    int r = paint[i].r;
    int j = r;
    while (j >= l) {
      if (ans[j] == 0) {
        ans[j] = i;
        fa[j] = get(l);
        j--;
      } else { j = fa[j] - 1; }
    }
  }
  for (int i = 1; i <= N; ++i) { write(ans[i]); puts(""); }
  return 0;
}
```

### T2 divide

#### 题目

    你有 n1 个 1，n2 个 2, n3 个 3, n4 个 4, n5 个 5, n6 个 6，问能否将他们分成两部分使得两部分的和相等

    输入 第一行一个整数n，之后n行每行六个整数n1, n2, n3, n4, n5, n6

    输出 如果可以输出 "Can be divided."，不行输出 "Can't be divided."

#### 题解

这道题老师来说了下 $n$ 最大有 $5000$，于是坚信了这道题是数论题 TAT。所以思考的角度完全错误，直接爆零。
比赛结束后知道了应该用背包做，但是裸的多重背包会 TLE，更何况有 $5000$ 的数据，所以去阅读了标程，发现了原来正解是一个很神奇的 dp。

首先 我们令



```cpp
#include <bits/stdc++.h>
using namespace std;

inline char nextchar() {
  static char buf[100000], *p1 = buf, *p2 = buf;
  return (p1 == p2) &&
    (p2 = (p1 = buf) + fread(buf, 1, 100000, stdin), p1 == p2) ? EOF : *p1++;
}

inline void read(int &x) {
  x = 0;
  static char ch = 0;
  static bool sign = false;
  while (!isdigit(ch)) { sign |= (ch == '-'); ch = nextchar(); }
  while (isdigit(ch)) { x = x * 10 + (ch ^ 48); ch = nextchar(); }
  x = sign ? -x : x;
}

inline void write(int x) {
  static int stk[100], top = 0;
  if (x == 0) { putchar('0'); return; }
  if (x < 0) { putchar('-'); x = -x; }
  while (x) { stk[++top] = x % 10; x /= 10; }
  while (top) putchar(stk[top--] + '0');
}


#define maxn 20010
int n, a[10], sum, f[maxn];

int main() {
  read(n);
  while (n--) {
    sum = 0;
    for (int i = 1; i <= 6; ++i) { read(a[i]); sum += a[i] * i; }
    if (sum & 1) { puts("Can't be divided."); continue; }
    sum >>= 1;
    memset(f, -1, sizeof(f));
    f[0] = 0;
    for (int i = 1; i <= 6; ++i) {
      for (int j = 0; j <= sum; ++j) {
        if (f[j] >= 0) f[j] = a[i];
        else if (i <= j) f[j] = f[j - i] - 1;
        else f[j] = -1; 
      }
    }
    if (f[sum] >= 0) puts("Can be divided.");
    else puts("Can't be divided.");
  }
  return 0;
}
```

---