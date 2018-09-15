---
title: '[图论] Dijkstra 最短路计数'
date: 2018-08-29 17:58:02
tags:
	- 图论
	- 最短路
	- 最短路计数
	- Dijkstra
categories: 图论
mathjax: true
copyright: true
---

### 写在前面

最短路计数是一个很经典的问题，今天刚好做到了这样一道题，就来总结一下。

<!--more-->

### 最短路计数

[题目：洛谷 P1144 最短路计数](https://www.luogu.org/problemnew/show/P1144)
最短路计数本质上还是求最短路，只不过在迭代的同时来更新最短路数量。首先来回忆一下 Dijkstra 求最短路的更新条件，对于一条有向边 $(u,v,w)$，我们更新到 $v$ 的最短路的条件是：
$$
dist[v] > dist[u] + w
$$
在最短路计数的时候我们可以用动态规划的思想，记 $num[x]$ 表示到 $x$ 的最短路的数量，那么：

1. 若 $dist[v] = dist[u] + w$ 我们更新 $num[v] = num[v] + num[u]$，即有另外一条路径。
2. 若 $dist[v] > dist[u] + w$ 我们更新 $num[v] = num[u]$，即有更短的路径，前面的全部作废。

所以这道题的代码就呼之欲出了：

```cpp
#include <bits/stdc++.h>
using namespace std;

#define maxm 2000010
#define maxn 1000010
#define modn 100003
#define INF 0x7f7f7f7f
struct heapNode {
	int u, d;
	bool operator<(const heapNode &b) const { return d > b.d; }
	// 这里似乎是一定要加 const，否则会 CE
};
struct edgeNode {
	int vertexTo;
	int edgeNext;
	int weight;
} edges[maxm << 1];
int numEdges = 0, heads[maxn];
priority_queue<heapNode> Q;
int dist[maxn], num[maxn];
int n, m, x, y;

void init() {
	numEdges = 0;
	memset(heads, -1, sizeof(heads));
	memset(dist, INF, sizeof(dist));
	memset(num, 0, sizeof(num));
	while (!Q.empty()) Q.pop();
}

void addEdge(int vertex1, int vertex2, int weight) {
	numEdges++;
	edges[numEdges].vertexTo = vertex2;
	edges[numEdges].weight = weight;
	edges[numEdges].edgeNext = heads[vertex1];
	heads[vertex1] = numEdges;
}

void addDuplexEdge(int vertex1, int vertex2, int weight) {
	addEdge(vertex1, vertex2, weight);
	addEdge(vertex2, vertex1, weight);
}

void Dijkstra(int x) {
	dist[x] = 0;
	num[x] = 1;
	Q.push((heapNode){ x, 0 });
	while (!Q.empty()) {
		heapNode f = Q.top();
		Q.pop();
		int u = f.u;
		int d = f.d;
		if (d != dist[u]) continue;
		for (int i = heads[u]; i != -1; i = edges[i].edgeNext) {
			int j = edges[i].vertexTo;
			int w = edges[i].weight;
			if (dist[j] == dist[u] + w) {
				num[j] += num[u]; // 更新情况一
				num[j] %= modn; // 题目要求取模
			} else if (dist[j] > dist[u] + w) {
				num[j] = num[u]; // 更新情况二
				dist[j] = dist[u] + w;
				Q.push((heapNode){ j, dist[j] });
			}
		}
	}
}

int main() {
	ios::sync_with_stdio(false);
	cin >> n >> m;
	init();
	for (int i = 1; i <= m; ++i) {
		cin >> x >> y;
		if (x == y) continue;
		addDuplexEdge(x, y, 1);
	}
	Dijkstra(1);
	for (int i = 1; i <= n; ++i) { cout << num[i] << endl; }
	return 0;
}
```

### 总结

由这个最短路计数我们可以看出动态规划实在是实用啊！

---