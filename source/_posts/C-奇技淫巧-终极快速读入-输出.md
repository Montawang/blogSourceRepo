---
title: '[C++ 奇技淫巧] 终极快速读入/输出'
mathjax: true
copyright: true
comment: true
date: 2018-09-22 18:24:03
tags:
  - C++ 奇技淫巧
categories: 奇技淫巧
photo: /2018/09/22/C-奇技淫巧-终极快速读入-输出/photo1.png
---

{% note default %}

上次看到一个手写 `getchar()` 的大神，发现自己的快速读入还是不够骚，于是去查了下网络，发现了更加厉害的快读方式。顺便把自己的快输用非递归优化了一下。

{% endnote %}

<!-- more -->

### 手写 `getchar()`

```cpp
inline char nextchar() {
  static char buf[100000], *p1 = buf, *p2 = buf;
  return (p1 == p2) &&
    (p2 = (p1 = buf) + fread(buf, 1, 100000, stdin), p1 == p1) ? EOF : *p1++;
} // 使用 fread() 来提高速度
```
### 快读使用 `nextchar()`

```cpp
inline void read(int& x) {
  x = 0;
  static char ch = 0;
  static bool sign = false;
  // 使用 static 节省空间
  while (!isdigit(ch)) { sign |= (ch == '-'); ch = nextchar(); }
  while (isdigit(ch)) { x = x * 10 + (ch ^ 48); ch = nextchar(); }
  // 这里 x * 10 和 (x << 1) + (x << 3) 编译后的代码是一样的
  // 所以不需要用位运算了
  x = sign ? -x : x;
}
```
### 非递归 `write()`

```cpp
inline void write(int x) {
  static int stk[100], top = 0;
  if (x == 0) { putchar('0'); return; }
  if (x < 0) { x = -x; putchar('-'); }
  while (x) { stk[++top] = x % 10; x /= 10; }
  while (top) { putchar(stk[top--] + '0'); }
}
```

### 速度测试

#### 数据生成

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
  ofstream test("testdata.txt");
  srand((unsigned)time(NULL) * 19260817); // +1s
  for (int i = 1; i <= 5000000; ++i)
    test << rand() << " ";
  return 0;
} // 祖传代码
```

#### 老版本测试

```cpp
#include <bits/stdc++.h>
#include <chrono>
using namespace std;
using namespace std::chrono;

inline void read(int& x) {
  x = 0;
  char ch = 0;
  bool sign = false;
  while (!isdigit(ch)) { sign |= (ch == '-'); ch = getchar(); }
  while (isdigit(ch)) { x = x * 10 + (ch ^ 48); ch = getchar(); }
  x = sign ? -x : x;
}

inline void write(int x) {
  if (x < 0) { putchar('-'); x = -x; }
  if (x > 9) write(x / 10);
  putchar(x % 10 + '0');
}

int main() {
  freopen("testdata.txt", "r", stdin);
  freopen("output.txt", "w", stdout);
  int array[1000000];
  auto start = system_clock::now();
  
  for (int i = 1; i <= 1000000; ++i) read(array[i - 1]);
  for (int i = 1; i <= 1000000; ++i) write(array[i - 1]), putchar(' ');

  auto end = system_clock::now();
  auto duration = duration_cast<microseconds>(end - start);
  cout << "cost: "
       << double(duration.count()) *
          microseconds::period::num / microseconds::period::den
       << " seconds" << endl;
  return 0;
}
``` 

    cost: 0.406813 seconds

#### 新版本测试

```cpp
#include <bits/stdc++.h>
#include <chrono>
using namespace std;
using namespace std::chrono;

inline char nextchar() {
  static char buf[100000], *p1 = buf, *p2 = buf;
  return (p1 == p2) &&
    (p2 = (p1 = buf) + fread(buf, 1, 100000, stdin), p1 == p1) ? EOF : *p1++;
} // 使用 fread() 来提高速度

inline void read(int& x) {
  x = 0;
  static char ch = 0;
  static bool sign = false;
  // 使用 static 节省空间
  while (!isdigit(ch)) { sign |= (ch == '-'); ch = nextchar(); }
  while (isdigit(ch)) { x = x * 10 + (ch ^ 48); ch = nextchar(); }
  // 这里 x * 10 和 (x << 1) + (x << 3) 编译后的代码是一样的
  // 所以不需要用位运算了
  x = sign ? -x : x;
}

inline void write(int x) {
  static int stk[100], top = 0;
  if (x == 0) { putchar('0'); return; }
  if (x < 0) { x = -x; putchar('-'); }
  while (x) { stk[++top] = x % 10; x /= 10; }
  while (top) { putchar(stk[top--] + '0'); }
}

int main() {
  freopen("testdata.txt", "r", stdin);
  freopen("output.txt", "w", stdout);
  int array[1000000];
  auto start = system_clock::now();
  
  for (int i = 1; i <= 1000000; ++i) read(array[i - 1]);
  for (int i = 1; i <= 1000000; ++i) write(array[i - 1]), putchar(' ');

  auto end = system_clock::now();
  auto duration = duration_cast<microseconds>(end - start);
  cout << "cost: "
       << double(duration.count()) *
          microseconds::period::num / microseconds::period::den
       << " seconds" << endl;
  return 0;
}
``` 

    cost: 0.275003 seconds

### 总结

新的快读几乎是快了一倍，不过两者都已经是神仙打架了，像 `scanf` 这种都已经被踢飞了，不过，快读虽好，使用还是要谨慎，速度的提高主要还是靠算法的优化（除了某些高性能的题）。

---