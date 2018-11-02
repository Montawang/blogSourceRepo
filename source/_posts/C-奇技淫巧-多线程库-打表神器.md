---
title: '[C++ 奇技淫巧] 多线程库，打表神器 !!!'
mathjax: true
copyright: true
comment: true
date: 2018-11-02 14:50:04
tags:
    - C++ 奇技淫巧
categories: 奇技淫巧
photo: /2018/11/02/C-奇技淫巧-多线程库-打表神器/photo.png
---

{% note default %}

很多时候，当我们想不到正解，而题目的数据又很小以至于我们认为我们可以 $\mathcal{O}(1)$ 出解的时候，我们会使用一种惊天地泣鬼神，怒刚出题人的解题方法——打表！但是暴力实在是太慢了以至于我们要打一年的表，所以今天就来介绍一个多线程库（千万不要在提交的程序里用！！）。

{% endnote %}

<!-- more -->

### `pthread.h` Posix 系统原生线程

这个是在 Posix 系统下的头文件，当然装了 MinGW 等东西之后 Windows 也是可以用的，下面放一个示例代码：

```cpp
#include <iostream>
#include <pthread.h>
#include <unistd.h>
using namespace std;

typedef long long int64;
int64 n = 0;
void* threadFunc(void* arg) {
  for (int i = 0; i < 10000; ++i) ++n;
  pthread_exit(nullptr);
}

int main() {
  for (int i = 0; i < 10; ++i) {
    pthread_t pid;
    pthread_create(&pid, nullptr, threadFunc, nullptr);
    pthread_detach(pid);
  }
  sleep(1); // 等待所有线程退出
  cout << n;
  return 0;
}
```

### C++ `std::thread`

**以下代码在编译时需要加入 `-std=c++11` 命令，属于 C++11 标准中的东西**

C++11 之后有了标准的线程库 `std::thread`

#### `std::thread` 构造函数

{% tabs 构造函数, 1 %}
<!-- tab -->
**默认构造函数：**
`thread() noexcept;`
创建一个空的 `std::thread` 对象
<!-- endtab -->
<!-- tab -->
**初始化构造函数**
`template <class Fn, class... Args> explicit thread(Fn&& fn, Args&&... args);`
创建一个 `std::thread` 对象，该 `std::thread` 对象可被 `joinable`，新产生的线程会调用 `fn` 函数，该函数的参数由 `args` 给出。
<!-- endtab -->
<!-- tab -->
**拷贝构造函数（被禁用）**
`thread(const thread&) = delete;`
意味着 `std::thread` 对象不可拷贝构造。
<!-- endtab -->
<!-- tab -->
**Move 构造函数**
`thread(thread&& x) noexcept`;
`move` 构造函数，`move` 似乎是 C++11 中的新特性
<!-- endtab -->
{% endtabs %}




---