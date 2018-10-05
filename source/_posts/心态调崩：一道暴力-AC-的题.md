---
title: 心态调崩：一道暴力 AC 的题
mathjax: true
copyright: true
comment: true
date: 2018-10-05 14:55:07
tags:
  - NOIP 模拟
  - 最小生成树
categories: NOIP
photo:
---

{% note default %}

今天有是令人愉悦的国庆七连测，第一道题整整卡了我两个小时！！都没时间去思考第三道题😭😭。然而做出来了之后却发现是如此的弱智。。。

{% endnote %}

<!-- more -->

### 题目大意

你有一个图，你从最左下角的点开始，可以向上或向下走 不大于 $h$ 的单位到另一个 `#`，或者在同一层相连的 `#`，求能走到终点 $(x,y)$ 的 $h$ 的最小值。

### 输入/输出

**[样例输入]**

    5 8
    ####____
    ___#_###
    ###__#__
    ______#_
    ########
    2 4

**[样例输出]**

    2

### 题解

嗯......这道题可以直接 BFS 模拟然后每次找 $h$ 的时候二分答案就可以过了。考试的时候我是用最小生成树做的，因为最小生成树有一个性质：最短的边一定会被包含在 MST 中，这也是 Kruskal 算法的原理。**所以我们在做 Kruskal 的时候就可以用并查集来判断一下最左下角的点和目标点是否已经连通，如果连通就可以跳出了**也就是这个地方让我思考了一年，一开始还在想怎么在做完 Kruskal 之后搜索路径......

```cpp
#include <bits/stdc++.h>
using namespace std;
#define maxn 1010

struct edgeNode {
  int vertexTo;
  int vertexFrom;
  int edgeNext;
  int weight;
};
// 这个地方其实不需要前向星，一开始想错了才打成这个

char ch;
edgeNode edges[maxn * maxn * 2];
int heads[maxn * maxn* 2], numEdges = 0, fa[maxn * maxn], ans = 0;
bool dat[maxn][maxn];
int n, m, x, y;

inline void addEdge(int vertex1, int vertex2, int weight) {
  numEdges++;
  edges[numEdges].vertexTo = vertex2;
  edges[numEdges].vertexFrom = vertex1;
  edges[numEdges].weight = weight;
  edges[numEdges].edgeNext = heads[vertex1];
  heads[vertex1] = numEdges;
}

inline void addDuplexEdge(int vertex1, int vertex2, int weight) {
  addEdge(vertex1, vertex2, weight);
  addEdge(vertex2, vertex1, weight);
}

inline bool operator<(edgeNode& a, edgeNode& b) {
  return a.weight < b.weight;
}

inline int get(int x) {
  return fa[x] == x ? x : (fa[x] = get(fa[x]));
}

inline int change(int x, int y) {
  return (x - 1) * m + y;
} // 点的有序数对转序号

inline void kruskal() {
  for (int i = 1; i <= numEdges; ++i) {
    int u = get(edges[i].vertexFrom);
    int v = get(edges[i].vertexTo);
    if (u == v) continue;
    fa[u] = v;
    ans = max(ans, edges[i].weight);
    if (get(change(x, y)) == get(change(n, 1))) break;
    // 弹出
  }
}

int main() {
  ifstream in("mario.in");
  ofstream out("mario.out");
  // 懒得打快读 QAQ
  in >> n >> m;
  memset(dat, false, sizeof(dat));
  for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= m; ++j) {
      in >> ch;
      dat[i][j] |= (ch == '#'); 
    }
  }
  in >> x >> y;
  ////////////////////////////////////////////
  memset(heads, -1, sizeof(heads));
  for (int i = 1; i <= m; ++i) {
    int k = 1;
    while (!dat[k][i]) k++;
    for (int j = k + 1; j <= n; ++j) {
      if (dat[j][i]) {
        addDuplexEdge(change(k, i), change(j, i), j - k);
        k = j;
      }
    }
  }
  for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= m; ++j) {
      if (!dat[i][j]) continue;
      else if (!dat[i][j + 1]) continue;
      else addDuplexEdge(change(i, j), change(i, j + 1), 0);
    }
  }
  ////////////////////////////////////////////
  /////////////////// 加边 ///////////////////
  ////////////////////////////////////////////
  for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= m; ++j) {
      fa[change(i, j)] = change(i, j);
    }
  }
  sort(edges + 1, edges + numEdges + 1);
  kruskal();
  out << ans;
  return 0;
} 

```

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