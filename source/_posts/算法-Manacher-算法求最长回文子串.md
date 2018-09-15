---
title: '[算法] Manacher 算法求最长回文子串'
date: 2018-08-14 19:30:29
tags:
	- 算法
	- Manacher
	- 字符串
categories: 算法
mathjax: true
copyright: true
---

### 写在前面

最近几次考试都涉及到了字符串处理这个话题，所以自己打算恶补字符串有关的算法，同时又刚好做到了 LeetCode 上[这么一道题](https://leetcode-cn.com/problems/longest-palindromic-substring/description/)，所以干脆一同解决掉。

<!-- more -->

### 最长回文字串问题

**最长回文子串问题：给定一个字符串，求它的最长回文子串长度。**
如果一个字符串正着读和反着读是一样的，那它就是回文串。下面是一些回文串的实例：

	12321 a aba abba aaaa tattarrattat（牛津英语词典中最长的回文单词

那么如何解决这个问题呢？有很多种方法：

1. Brute-Force 暴力求解：找到所有字串，一一验证，时间复杂度为 $O(n^3)$
2. 改进版 Brute-Force：遍历整个字符串的中间字符及空隙，然后向两端扫描，时间复杂度为 $O(n^2)$。
3. 动态规划，此处暂时不介绍
4. 下面将要介绍的 Manacher 算法，又名***马拉车***。

### Manacher 算法

#### 暴力解法的缺陷所在

1. 回文串奇偶性不定，使用上述方法二需要分类讨论，降低效率。
2. 字串重复访问，时间复杂度冗余。

举个例子：

	char: a b a b a
	   i: 0 1 2 3 4

在 $i=1$ 和 $i=2$ 时，字串 `aba` 被分别遍历了一次。
Manacher 算法正式针对这些算法进行改进的算法，可以大大提高算法的运行效率。

#### 解决长度奇偶性问题

Manacher 算法通过一种预处理方式来解决第一个问题，它在字符串首尾及中间空隙插入一个字符，并且满足这个符号不在字符串中出现，这样就可以让所有的字符串长度变为奇数。以插入`#`为例：

	aba  --> #a#b#a#
	abba --> #a#b#b#a#

**插入的是同样的符号，且符号不存在于原串，因此子串的回文性不受影响，原来是回文的串，插完之后还是回文的，原来不是回文的，依然不会是回文**

#### 解决重复访问问题

我们把一个回文串中最左或最右位置的字符与其对称轴的距离称为回文半径。Manacher定义了一个回文半径数组 $RL$，用 $RL[i]$ 表示以第 $i$ 个字符为对称轴的回文串的回文半径。我们一般对字符串从左往右处理，因此这里定义 $RL[i]$ 为第 $i$ 个字符为对称轴的回文串的最右一个字符与字符 $i$ 的距离。对于上面插入分隔符之后的两个串，可以得到 $RL$ 数组：

| $char$ | # | $a$ | # | $b$ | # | $a$ | # |
|:------:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| $RL$ | $1$ | $2$ | $1$ | $4$ | $1$ | $2$ | $1$ |
| $RL-1$ | $0$ | $1$ | $0$ | $3$ | $0$ | $1$ | $0$ |
| $i$ | $0$ | $1$ | $2$ | $3$ | $4$ | $5$ | $6$ |

上面我们还求了一下 $RL[i]-1$。通过观察可以发现，$RL[i]-1$的值，正是在原本那个没有插入过分隔符的串中，以位置i为对称轴的最长回文串的长度。那么只要我们求出了 $RL$ 数组，就能得到最长回文子串的长度。

于是问题变成了，**如何高效地求的 $RL$ 数组**。基本思路是**利用回文串的对称性，扩展回文串**。

我们再引入一个辅助变量 $MaxRight$，表示当前访问到的**所有回文子串**，所能触及的**最右**一个字符的位置。另外还要记录下 $MaxRight$ 对应的回文串的对称轴所在的位置，记为 $pos$，它们的位置关系如下：

<div style="align: center"> <img src="pic1.jpg"/> </div>

我们从左往右地访问字符串来求 $RL$，假设当前访问到的位置为 $i$，即要求 $RL[i]$，在对应上图，$i$ 必然是在 $pos$ 右边的。但我们更关注的是，$i$ 是在 $MaxRight$ 的左边还是右边。我们分情况来讨论：

##### 情况一：$i$ 在 $MaxRight$ 左边

如下图所示：

<div style="align: center"> <img src="pic2.jpg"/> </div>

我们知道，图中两个红色块之间（包括红色块）的串是回文的；并且以 $i$ 为对称轴的回文串，是与红色块间的回文串有所重叠的。我们找到 $i$ 关于 $pos$ 的对称位置 $j$，这个 $j$ 对应的 $RL[j]$ 我们是已经算过的。根据回文串的对称性，以i为对称轴的回文串和以j为对称轴的回文串，有一部分是相同的。这里又有两种细分的情况:

###### 以 $j$ 为对称轴的回文串较短

如下图：

<div style="align: center"> <img src="pic3.jpg"/> </div>

这时我们知道 $RL[i]$ 至少不会小于 $RL[j]$，并且已经知道了部分的以i为中心的回文串，于是可以令 $RL[i]=RL[j]$。但是以i为对称轴的回文串可能实际上更长，因此我们试着以 $i$ 为对称轴，继续往左右两边扩展，直到左右两边字符不同，或者到达边界。

###### 以 $j$ 为对称轴的回文串较长

如下图：

<div style="align: center"> <img src="pic4.jpg"/> </div>

这时，我们只能确定，两条蓝线之间的部分（即不超过 $MaxRight$ 的部分）是回文的，于是从这个长度开始，尝试以i为中心向左右两边扩展，，直到左右两边字符不同，或者到达边界。

###### 小结
不论以上哪种情况，之后都要尝试更新MaxRight和pos，因为有可能得到更大的MaxRight。

具体操作如下：

1. 令 $RL[i]=min\lbrace RL[2\times pos-i], MaxRight-i\rbrace$
2. 以 $i$ 为中心扩展回文串，直到左右两边字符不同，或者到达边界
3. 更新 $MaxRight$ 和 $pos$

##### 情况二：$i$ 在 $MaxRight$ 右边

如下图：

<div style="align: center"> <img src="pic5.jpg"/> </div>

遇到这种情况，说明以 $i$ 为对称轴的回文串还没有任何一个部分被访问过，于是只能从 $i$ 的左右两边开始尝试扩展了，当左右两边字符不同，或者到达字符串边界时停止。然后更新 $MaxRight$ 和 $pos$。

#### 代码实现

此处以 [LeetCode 上的题](https://leetcode-cn.com/problems/longest-palindromic-substring/description/)为例：

```cpp
class Solution {
public:
	string longestPalindrome(string s) {
		int len = s.size();
		if (len <= 1) return s;
		string str = preProcess(s);
		int n = str.size(), pos = 0, MaxRight = 0;
		vector<int> RL(n, 0);
		for (int i = 1; i < n - 1; ++i) {
			RL[i] = MaxRight > i ? min(RL[2 * pos - i], MaxRight - i) : 1;
			while (str[i + RL[i]] == str[i - RL[i]]) ++RL[i];
			if (i + RL[i] > MaxRight) {
				MaxRight = i + RL[i];
				pos = i;
			}
		}
		int maxLen = 0, index = 0;
		for(int i = 1; i < n - 1; ++i) {
			if(RL[i] > maxLen) {
				maxLen = RL[i];
				index = i;
			}
		} // 这里可以和上面并在一起
		return s.substr((index - maxLen) / 2, maxLen - 1);
	}
	//预处理字符串，abc预处理后变成$#a#b#c#^
	string preProcess(const string &s) {
		int n = s.size();
		string res;
		res.push_back('$');
		res.push_back('#');
		for(int i = 0; i < n; i++) {
			res.push_back(s[i]);
			res.push_back('#');
		}
		res.push_back('^');
		return res;
	}
	// 注：此处字符串预处理在头上加入 '$' 是为了方便处理
	// 如果是空间瘤患者，直接在有关的地方减一就行了
};
```
### 总结

Manacher 算法其实可以算奇技淫巧吧，主要还是掌握它的思路，充分利用待求解问题的性质，从而降低求解的复杂度。这个也是在竞赛中会用到的技巧。

---
