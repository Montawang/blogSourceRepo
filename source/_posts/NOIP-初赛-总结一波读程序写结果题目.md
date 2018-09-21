---
title: '[NOIP 初赛] 总结一波读程序写结果题目'
mathjax: true
copyright: true
comment: true
date: 2018-09-20 17:57:14
tags:
  - NOIP 初赛
categories: NOIP
photo: /2018/09/20/NOIP-初赛-总结一波读程序写结果题目/photo1.jpg
---

{% note default %}

为了提高初赛分数，痛下决心总结一波**读程序写结果**的题。

ps: 一次更新不完，会持续更新

{% endnote %}

<!-- more -->

### 第二十一届 / NOIP 2015

```cpp
#include <iostream>
using namespace std;

void fun(char *a, char *b) {
  a = b;
  (*a)++;
}

int main() {
  char c1, c2, *p1, *p2;
  c1 = 'A';
  c2 = 'a';
  p1 = &c1;
  p2 = &c2;
  fun(p1, p2);
  cout << c1 << c2 << endl;
  return 0;
}

// 输出：Ab

```

这道题中先是将 `p1` 赋值为 `c1` 的地址，将 `p2` 赋值为 `c2` 的地址，然后将 `p1` 赋值为 `p2` 的值，即现在 `p1` 所表示的是 `c2` 的地址，所以最后只有 `c2` 会发生改变，这道题一开始没看出来，后来仔细想了想才反应过来，不失为一道坑题。

<hr />

```cpp
#include <iostream>
using namespace std;

int fun(int n, int fromPos, int toPos) {
  int t, tot;
  if (n == 0)
    return 0;
  for (t = 1; t <= 3; t++)
    if (t != fromPos && t != toPos)
      break;
  tot = 0;
  tot += fun(n - 1, fromPos, t);
  tot++;
  tot += fun(n - 1, t, toPos);
  return tot;
}

int main() {
  int n;
  cin >> n;
  cout << fun(n, 1, 3) << endl;
  return 0;
}

// 输入：5
// 输出：31

```

这道题其实纯属糊弄人，可以发现 `fun` 这个函数中不管 `fromPos` 和 `toPos` 为何值，总能继续下去，所以简化后的函数如下：

```cpp
int fun(int n) {
  if (n == 0) return 0;
  return fun(n - 1) * 2 + 1;
}
```

好了，这样子的递归函数手推就行了。


### 未完待续......

---