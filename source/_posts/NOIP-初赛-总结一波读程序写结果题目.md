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

{% endnote %}

<!-- more -->

### 

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

第二十一届的题目似乎特别的水，比如

---