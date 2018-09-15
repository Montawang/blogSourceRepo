---
title: '[NOIP 模拟] 记录一道坑题'
mathjax: true
copyright: true
comment: true
date: 2018-09-01 10:56:35
tags:
  - NOIP 模拟
categories: NOIP
---

{% note default %}
昨天的一场模拟，第二题本来应该是一道很水的题，然而最后打崩了，归根结底还是自己不够仔细，题目审错，最后导致方法用错。
{% endnote %}

<!-- more -->

### 题目描述

**[问题描述]**

    神即将带领一些人去他们的孤寂之境，由于这个世界的不稳定，地点之间的有向道路会不
    定期地毁坏，出于工作准备，神想知道在某些道路毁坏之后某两点之间的最短路。
    就是给定一个有向图，现有两个操作，操作1 是删除一条边（一条边可重复删除），操作2
    是询问两个点之间的最短路。

**[输入格式]**

    输入文件名为journey.in。
    第1 行两个正整数n, m，分别表示图的点数和操作数。
    第2 行至第n + 1 行每行n 个正整数，为图的邻接矩阵，第i 行第j 列的数表示点i 和点
    j 间距离，保证对角线为0。
    接下来m 行每行三个正整数c, x, y； c 表示操作种类，为1 或2，当c = 1 时表示删除x
    与y 相连的边，当 c = 2 时表示询问 x 到 y 的最短路，若不可达则输出 -1。

**[输出格式]**

    输出文件名为journey.out
    输出若干行，每个 2 操作对应一行，答案为询问中 x 到 y 的最短路或 -1

**[样例]**

    见下发/girls/girls.in(out)。

**[数据规模与约定]**

    对于30% 的数据：n, m <= 10
    对于50% 的数据：n, m <= 50
    对于100% 的数据：n <= 200; m <= 100000, 操作1 不超过200 次，边权不超过10000

### 题解

一碰到查找的题，那肯定就是两条路了，离线和在线两种思路，乍一看这 $n$ 的数据范围，并且题目中还说输入邻接矩阵，那求最短路肯定是 Floyd 了，再一想，如果是在线查询那只有朴素算法了，查一次求一次全源最短路，然后这种方法就 `TLE` 了，代码：

```cpp
#include <bits/stdc++.h>
using namespace std;

#define maxn 210
#define INF 0x3f3f3f3f
inline void read(int &x) {
  x = 0;
  bool sign = false;
  char ch = 0;
  while (!isdigit(ch)) {
    sign |= (ch == '-');
    ch = getchar();
  }
  while (isdigit(ch)) {
    x = (x << 3) + (x << 1) + (ch ^ 48);
    ch = getchar();
  }
  x = sign ? -x : x;
}
inline void write(int x) {
  if (x < 0) { putchar('-'); x = -x; }
  if (x > 9) write(x / 10);
  putchar(x % 10 + '0');
}
inline void writeln(int &x) { write(x); puts(""); }
inline void read(int &a, int &b) { read(a); read(b); }
inline void read(int &a, int &b, int &c) { read(a); read(b); read(c); }

int g[maxn][maxn], d[maxn][maxn], n, m, c, x, y;

void Floyd() {
  for (int k = 1; k <= n; ++k)
    for (int i = 1; i <= n; ++i)
      for (int j = 1; j <= n; ++j)
        g[i][j] = min(g[i][j], g[i][k] + g[k][j]);
}

int main() {
  freopen("journey.in", "r", stdin);
  freopen("journey.out", "w", stdout);
  memset(d, INF, sizeof(d));
  read(n, m);
  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= n; ++j)
      read(g[i][j]);
  while (m--) {
    read(c, x, y);
    switch (c) {
      case 1: { g[x][y] = INF; break; }
      case 2: {
        memcpy(d, g, sizeof(g));
        Floyd();
        if (g[x][y] == INF) puts("-1");
        else writeln(g[x][y]);
        memcpy(g, d, sizeof(d));
        break;
      }
    }
  }
  return 0;
}
```
所以正解肯定就是离线操作了，先把该删的边都给删掉，然后从最后一个操作开始处理，遇到操作一就加边，求一次最短路，当然这里不是用 Floyd 而是用一个 $\mathcal{O}(n^2)$ 的方法，附上代码：

```cpp
#include <bits/stdc++.h>
using namespace std;

#define INF 0x3f3f3f3f
#define maxn 210
#define maxm 100010
struct op { int c, x, y; };
// op 结构体，记录操作
op ops[maxm];
int g[maxn][maxn], d[maxn][maxn];
int w[maxm], ans[maxm];
int n, m;

inline void read(int &x) {
  x = 0;
  bool sign = false;
  char ch = 0;
  while (!isdigit(ch)) {
    sign |= (ch == '-');
    ch = getchar();
  }
  while (isdigit(ch)) {
    x = (x << 3) + (x << 1) + (ch ^ 48);
    ch = getchar();
  }
  x = sign ? -x : x;
}
inline void write(int x) {
  if (x < 0) { putchar('-'); x = -x; }
  if (x > 9) write(x / 10);
  putchar(x % 10 + '0');
}
inline void writeln(int x) { write(x); puts(""); }
inline void read(int &a, int &b) { read(a); read(b); }
inline void read(int &a, int &b, int &c) { read(a); read(b); read(c); }
// 快读快写黑科技

void Floyd() {
  for (int k = 1; k <= n; ++k)
    for (int i = 1; i <= n; ++i)
      for (int j = 1; j <= n; ++j)
        d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
}
// Floyd 对 d[][] 操作求最短路

int main() {
  freopen("journey.in", "r", stdin);
  freopen("journey.out", "w", stdout);
  read(n, m);

  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= n; ++j)
      read(g[i][j]);
  memcpy(d, g, sizeof(g));
  // 输入，g[][] 用来备份原邻接矩阵

  for (int i = 1; i <= m; ++i) {
    read(ops[i].c, ops[i].x, ops[i].y);
    if (ops[i].c == 1) {
      w[i] = g[ops[i].x][ops[i].y];
      d[ops[i].x][ops[i].y] = INF;
    }
  }
  Floyd();
  // 读入所有操作后 Floyd

  int cnt = 0;
  for (int k = m; k > 0; --k) {
  // 注意循环顺序，从最后一个开始
    int x = ops[k].x;
    int y = ops[k].y;
    if (ops[k].c == 2) ans[++cnt] = (d[x][y] == INF ? -1 : d[x][y]);
    // 题目中要求如果不连通输出 -1，所以这里特判一下
    // 标程中似乎没有这一句，自己搞了一个数据把标程给卡掉了，所以还是要加的
    else {
      // 求一次最短路，只要把 d[x][y] 加进去再比较一下就可以了
      for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
          int tmp = d[i][x] + d[y][j] + w[k];
          if (tmp < d[i][j]) d[i][j] = tmp;
        }
      }
    }
  }

  // 最后输出要注意顺序
  for (int i = cnt; i > 0; --i) writeln(ans[i]);
  return 0;
}

```
### 总结

以后看到这种题必须要读清楚题意，然后决定是否离线操作，考试的时候我根本就是智障了，直接在数组 $d$ 上面删边，求最短路，然后 GG。幸好最后醒悟过来打了个暴力。邻阶矩阵就是这点太坑。。。容易让人误操作。。。

---