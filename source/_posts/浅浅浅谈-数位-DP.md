---
title: '[浅浅浅谈] 数位 DP'
mathjax: true
copyright: true
comment: true
date: 2018-10-30 16:12:04
tags:
categories:
photo: /2018/10/30/浅浅浅谈-数位-DP/photo.png
---

{% note default %}
“出题人是死的。”总有些出题人会闲的蛋疼来出一些像下面这样的题目：
{% cq %}
求区间 $[l,r]$ 之间满足规则 $g$ 的数有多少个。
{% endcq %}
{% endnote %}

<!-- more -->

### 一般解法

代码如下：

```cpp
for (int i = l; i <= r; ++i) if (judge(i)) cnt++;
```

如果每一次 `judge()` 的复杂度为 $\mathcal{O}(\operatorname{length}(i))$，则它的复杂度为 $\mathcal{O}\left(n\times\operatorname{length}(i)\right)$，如果再算上一堆常数（比如开个数组什么的），那 `TLE` 简直是轻轻松松的🤣🤣。

### 用数位 DP

我们可以从数字的每一位来枚举，从高位向低位枚举，我们可以用 $\operatorname{dp}[i][j]$ 来表示枚举到 $i$ 位时，数字为 $j$ 时的方案数（答案数）。但是我们需要考虑一些限制我们决策的条件，假设我们要求小于等于 $243$ 的数字中符合条件的有几个：

1. 当我们第一项为 $1$ 时，我们后面一位可以枚举 $0\sim 9$ 
2. 当我们第一项为 $2$ 时，我们后面一项就只能枚举 $0\sim 4$

可见我们需要通过判断前一项来决定后一项最高可以取值的大小。同时我们也需要考虑一些数字前面一直是 $0$，并且会影响答案的情况（例如统计各个数字出现的次数）。以下是模板：

```cpp
#include <bits/stdc++.h>
using namespace std;
#define N 20
int dp[N][N], a[N], n, m;
// 数位 dp 是需要深搜的，`dp` 数组只是做记忆化搜索用
int dfs(int pos, int pre, bool limit, bool frontzero) {
  // `frontzero`: 前导 0 的判断
  //       `pre`: `pos` 前一位的数字
  if (pos == 0) return 1; // 枚举完毕，退出
  if (!frontzero && !limit && dp[pos][pre] != -1) return dp[pos][pre];
  // 返回 `dp[pos][pre]` 的条件：前导非零 且 无上限限制 且 `dp` 数组的这一位有值
  // 注意这里必须把 `!frontzero` 和 `!limit` 写在前面 来防止 `dp` 数组越界
  // 因为后面需要在前导零的时候做一点操作
  int p, ret = 0;
  int up = limit ? a[pos] : 9;
  for (int i = 0; i <= up; ++i) {
    if () continue;
    // 当枚举到的这一位不符合条件时就忽略，继续枚举
    p = i;
    if (frontzero && i == 0) p = -INF;
    // 这里 `-INF` 只是一个前导 0 的标记，数值并没有太大意义。
    ret += dfs(pos - 1, p, limit & (i == up), (p == -INF));
    // 这里 `p = -INF` 时也是会传进函数作为 `pre` 参数的，
    // 所以前面要把 `frontzero` 写前面
  }
  if (!frontzero && !limit) f[pos][pre] = ret;
  return ret;
}

int solve(int x) {
  // `solve(x)`: 处理不大于 `x` 的数的答案
  int idx = 0;
  while (x) {
    a[++idx] = x % 10;
    x /= 10;
  } // 预处理 `x` 的每一位
  memset(dp, -1, sizeof(dp));
  return dfs(idx, -INF, 1, 1);
  // 注意初始化
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(NULL);
  cin >> n >> m;
  cout << (solve(m) - solve(n - 1));
  return 0;
}

### 例题

#### [HDU 2089 不要 62](http://acm.hdu.edu.cn/showproblem.php?pid=2089)

解决代码如下：

```cpp
#include <bits/stdc++.h>
using namespace std;
#define N 20
int a[N], dp[N][2];
// `dp[pos][0 / 1]` 表示不含 4 和 6 的情况下，剩余长度为 `pos` (也就是当前的位数)，
// 首位是(1)否(0)为 6 的 时候的方案数。
int dfs(int pos, int pre, int status, bool limit) {
  if (pos == -1) return 1;
  if (!limit && dp[pos][status] != -1) return dp[pos][status];
  int up = limit ? a[pos] : 9;
  int ret = 0;
  for (int i = 0; i <= up; ++i) {
    if (pre == 6 && i == 2) continue;
    if (i == 4) continue;
    // 仔细读题，这里有两个条件
    ret += dfs(pos - 1, i, i == 6, limit & (i == a[pos]));
  }
  if (!limit) dp[pos][status] = ret;
  return ret;
}

int solve(int x) {
  int pos = 0;
  while (x) {
    a[pos++] = x % 10;
    x /= 10;
  }
  return dfs(pos - 1, -1, 0, true);
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  int l, r;
  cin >> l >> r;
  while ((l != 0) || (r != 0)) {
    memset(dp, -1, sizeof(dp));
    int ans = solve(r) - solve(l - 1);
    cout << ans << endl;
    cin >> l >> r;
  }
  return 0;
} 
```

#### [Windy 数](https://www.luogu.org/problemnew/show/P2657)

```cpp
#include <bits/stdc++.h>
using namespace std;

#define N 20
#define INF 0x7f7f7f7f

int a[N], dp[N][N], n, m;

int dfs(int pos, int pre, bool limit, bool frontzero) {
  if (pos == 0) return 1;
  if (!frontzero && !limit && dp[pos][pre] != -1) return dp[pos][pre];
  int p, ret = 0;
  int up = limit ? a[pos] : 9;
  for (int i = 0; i <= up; ++i) {
    if (abs(i - pre) < 2) continue;
    // 判定当前枚举的位能否满足题目中的条件
    p = i;
    if (frontzero && i == 0) p = -INF;
    ret += dfs(pos - 1, p, limit && (i == up), (p == -INF));
  }
  if (!frontzero && !limit) dp[pos][pre] = ret;
  return ret;
}

int solve(int x) {
  int len = 0;
  while (x) {
    a[++len] = x % 10;
    x /= 10;
  }
  return dfs(len, -INF, 1, 1);
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(NULL);
  cin >> n >> m;
  memset(dp, -1, sizeof(dp));
  // 初始化
  cout << (solve(m) - solve(n - 1));
  return 0;
}
```





```





---