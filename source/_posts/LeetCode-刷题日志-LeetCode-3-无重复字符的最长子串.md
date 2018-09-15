---
title: '[LeetCode 刷题日志] LeetCode-3 无重复字符的最长子串'
date: 2018-08-12 21:34:50
tags:
	- LeetCode
	- 字符串
	- 算法
categories: LeetCode
mathjax: true
copyright: true
---

### 写在前面

今天开始刷 [LeetCode](https://leetcode-cn.com/)，虽然说里面都是大公司的面试题，但是作为一个 OIer，刷刷也无妨，还能够提高自己的代码能力。本来希望**花个 3 天**刷完 LeetCode，结果发现这是完全不可能的，今天就被很基础的第三题给卡住了......

<!-- more -->

### LeetCode-3 题解
***——坑人的大水题***

[**题目**](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/description/)**描述**：

	给定一个字符串，找出不含有重复字符的最长子串的长度。

	示例：

	给定 "abcabcbb" ，没有重复字符的最长子串是 "abc" ，那么长度就是3。
	给定 "bbbbb" ，最长的子串就是 "b" ，长度是1。
	给定 "pwwkew" ，最长子串是 "wke" ，长度是3。请注意答案必须是一个子串，"pwke" 是 子序列  而不是子串。

**[题解]**

直接上代码，具体说明看注释。

```cpp
class Solution {
 public:
	int lengthOfLongestSubstring(string s) {
		int maxLen = 0;
		int currLen = 0;
		// table 用于记录字符出现的次数，之所以设为 128 是为了适配 ASCII 码
		int table[128], start = 0;
		// 全部初始化为 0
		memset(table, 0, sizeof(table));
		for (int i = 0; i < s.length(); ++i) {
			// 如果 ++table[s[i]] 为 2，则说明这个数在所选的子串中已经出现过了
			// 当然这里还有好多写法
			if (++table[s[i]] == 2) {
				// 因为遇到重复的字符了，所以要先更新 maxLen
				if (currLen > maxLen) maxLen = currLen;
				// 接下来查找重复的数字出现的地方，并且更新字串起始位置
				// 这里可以优化：使用一个 map<char, int>
				// 或 table0[128] 来记录字符的出现位置
				// 同时也可以与前面的 table 数组合并，既加快速度又减少空间
				// 这里因为不需要（懒），所以就打了这个未优化的算法
				// 有兴趣的读者可以去试一试优化算法。
				for (int j = start; j < i; ++j) {
					if (s[j] == s[i]) {
						table[s[j]] = 1;
						start = j + 1;
						break;
						// 因为最多出现一次，所以直接 break
					} else {
						// 没找到的话必须减小答案
						--currLen;
						// 重置
						table[s[j]] = 0;
					}
				}
			} else ++currLen; // 没有重复就直接自加一
		}
		// 最后的判断必不可少
		if (currLen > maxLen) maxLen = currLen;
		return maxLen;
	}
};

// 由于本人是一个 OIer，还是 Google 资深老粉丝，所以代码风格比较混搭（逃）。
```

那么，是什么卡了我那么长时间呢？一开始的代码遇到重复的字符，直接更新起始位置到当前字符，殊不知会有 abcdaefg 这种情况，于是一直 WA。论充分思考的重要性.....

### 总结

LeetCode 的题目难度并不是很大的，很多题差不多是`提高-`的难度，但是细节太多，同时对代码能力的要求比较高，某些数据结构也与 OI 中所写地不太一样。但是当作算法训练场来做也没什么坏处。

---
