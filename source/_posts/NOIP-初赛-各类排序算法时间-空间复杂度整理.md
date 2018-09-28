title: '[NOIP 初赛] 各类排序算法时间/空间复杂度整理'
mathjax: true
copyright: true
comment: true
date: 2018-09-28 20:35:20
tags:
  - NOIP 初赛
categories: NOIP
photo:
---

{% note default %}

排序算法在初赛中似乎每次都会考到，而且考到的不外乎它们的时间复杂度和空间复杂度，所以此处进行一次整理。

{% endnote %}

<!-- more -->

### 时间复杂度

| 算法名称 | 平均时间复杂度                       | 最好时间复杂度 | 最坏时间复杂度 | 稳定性 |
| -------- | ------------------------------------ | -------------- | -------------- | ------ |
| 桶排序   | $\mathcal{O}(n)$                     | $\mathcal{O}(n)$ | $\mathcal{O}(n)$ | 不稳定 |
| 基数排序 | $\mathcal{O}(n)$                     | $\mathcal{O}(n)$ | $\mathcal{O}(n)$ | 稳定 |
| 归并排序 | $\mathcal{O}(n\log n)$               | $\mathcal{O}(n\log n)$ | $\mathcal{O}(n\log n)$ | 稳定 |
| 快速排序 | $\mathcal{O}(n\log n)$               | $\mathcal{O}(n\log n)$ | $\mathcal{O}(n^2)$ | 不稳定 |
| 选择排序 | $\mathcal{O}(n^2)$                   | $\mathcal{O}(n)$ | $\mathcal{O}(n)$ | 不稳定 |
| 插入排序 | $\mathcal{O}(n^2)$                   | $\mathcal{O}(n)$ | $\mathcal{O}(n^2)$ | 稳定 |
| 希尔排序 | [见维基百科](https://zh.wikipedia.org/wiki/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F) | [见维基百科](https://zh.wikipedia.org/wiki/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F) | [见维基百科](https://zh.wikipedia.org/wiki/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F) | 不稳定 |
| 堆排序   | $\mathcal{O}(n\log n)$             | $\mathcal{O}(n\log n)$ | $\mathcal{O}(n\log n)$ | 不稳定 |
| 冒泡排序 | $\mathcal{O}(n^2)$                   | $\mathcal{O}(n^2)$ | $\mathcal{O}(n^2)$ | 稳定 |

**注：《算法》的作者曾经说过，所有的不稳定算法都可以用额外空间变成稳定的，所以这里的整理只能用于 NOIP 初赛**

### 空间复杂度

冒泡排序,简单选择排序,堆排序,直接插入排序,希尔排序的空间复杂度为 $\mathcal{O}(1)$，因为需要一个临时变量来交换元素位置,(另外遍历序列时自然少不了用一个变量来做索引)
快速排序空间复杂度为 $\mathcal{O}(\log n)$ （因为递归调用了），归并排序空间复杂是 $\mathcal{O}(n)$，需要一个大小为n的临时数组.
基数排序的空间复杂是 $\mathcal{O}(n)$，桶排序的空间复杂度不确定。

---