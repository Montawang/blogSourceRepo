---
title: '[平衡树] Treap 总结'
date: 2018-08-25 08:21:53
tags:
	- 树形结构
	- Treap
	- 平衡树
categories: 平衡树
copyright: true
---

### 写在前面

为了继续完善自己的数据结构有关的知识，决定先总结一波 Treap。Treap 是我学的第一个平衡树结构，花了很长时间才~~把板子背下来~~把它真正理解了，不禁感叹数据结构真的是神奇啊！

<!-- more -->

### BST 二叉查找树的性质

因为想重点总结的是 Treap 而不是[普通 BST](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%85%83%E6%90%9C%E5%B0%8B%E6%A8%B9)，所以这里只稍微提一下它的性质：

1. 若任意节点的左子树不空，则左子树上**所有节点**的值均**小于**它的根节点的值；
2. 若任意节点的右子树不空，则右子树上**所有节点**的值均**大于**它的根节点的值；
3. 任意节点的左、右子树也分别为二叉查找树；
4. **没有键值相等的节点。**

BST 是 Treap 学习的基础，更多内容可以看[这篇博客](https://lufficc.com/blog/binary-search-tree)。
上面这些性质，可以使得在 BST 上查找前驱 / 后继更为方便。这些性质是构建 BST 的基础，但是也使得 BST 不能很好地保持树的平衡，即左右子树的大小相差太大，最极端的情况就是一条链，为了保持 BST 的平衡，诞生了各种平衡树。

### Treap

#### Zig 和 Zag

保持 Treap 平衡的方式就是改变 Treap 的形态，同时又必须保证 Treap 仍然具备 BST 的性质，于是 Zig 和 Zag 操作就诞生了

<div style="align: center"> <img src="zigzag.png"/> </div>

可见这并不是简单的旋转，它还将某个节点向根节点移动，这个过程只可意会不可言传，还是好好理解上图。旋转的代码实现如下：
```cpp
void zig(int &p) {
	int q = treap[p].lChild;
	treap[p].lChild = treap[q].rChild;
	treap[q].rChild = p;
	p = q;
}

void zag(int &p) {
	int q = treap[p].rChild;
	treap[p].rChild = treap[q].lChild;
	treap[q].lChild = p;
	p = q;
}
```

#### 保证 Treap 的平衡

什么样的旋转才能保证它的平衡呢？Treap 是 Tree 和 Heap 的复合名词，所以它还满足堆性质。它利用“随机”来创造平衡的条件，每一个节点都有一个随机生成的额外权值，Treap 在满足 BST 性质的同时还通过旋转使得权值满足大根堆的性质。特别的，在删除 Treap 的节点时，可以直接把这个节点旋转到叶节点来直接删除，这样就免去了很多复杂的操作。

#### 从一道例题看代码实现

题目在这：[洛谷 P3369 【模板】普通平衡树](https://www.luogu.org/problemnew/show/P3369)

因为题目中同一个节点可能有多个，所以可以为每个节点增加一个“副本数”，同时在获取 Rank 的时候别忘了减一，因为初始化的时候有一个正无穷，一个负无穷，它们只是为了保证 BST 性质，并不是实际的节点。

代码如下：

```cpp
#include <bits/stdc++.h>
using namespace std;

#define maxn 100010
#define INF 0x7fffffff

struct treapNode {
	int lChild, rChild; // 左右子节点下标
	int value, weight;  // 节点关键码及权值
	int count, size;    // 副本数及子树大小
} treap[maxn];

int numNodes, root, n;

int newNode(int val) {
	numNodes++;
	treap[numNodes].value = val;
	treap[numNodes].weight = rand(); // 随机权值
	treap[numNodes].count = treap[numNodes].size = 1;
	return numNodes;
} // 建立一个新的节点

void update(int p) {
	treap[p].size = treap[treap[p].lChild].size +
		treap[treap[p].rChild].size +
		treap[p].count;
} // 更新某一个点的 size 方便获取排名

void build() {
	newNode(-INF); // 保证 BST 性质的两个点
	newNode(INF);
	root = 1;
	treap[root].rChild = 2;
	update(root);
} // 初始化

int getRankByVal(int p, int val) {
	if (p == 0) return 0;
	if (val == treap[p].value)
		return treap[treap[p].lChild].size + 1;
	if (val < treap[p].value)
		return getRankByVal(treap[p].lChild, val);
	return getRankByVal(treap[p].rChild, val) +
		treap[treap[p].lChild].size +
		treap[p].count;
}
// 上下两个函数很好理解，不作赘述
// 一切从 BST 性质出发
int getValByRank(int p, int rnk) {
	if (p == 0) return INF;
	if (treap[treap[p].lChild].size >= rnk)
		return getValByRank(treap[p].lChild, rnk);
	if (treap[treap[p].lChild].size + treap[p].count >= rnk)
		return treap[p].value;
	return getValByRank(treap[p].rChild,
		rnk - treap[treap[p].lChild].size - treap[p].count);
}

void zig(int &p) {
	int q = treap[p].lChild;
	treap[p].lChild = treap[q].rChild;
	treap[q].rChild = p;
	p = q;
	update(treap[p].rChild);
	update(p); // 别忘记更新
}

void zag(int &p) {
	int q = treap[p].rChild;
	treap[p].rChild = treap[q].lChild;
	treap[q].lChild = p;
	p = q;
	update(treap[p].lChild);
	update(p);
}

void insert(int &p, int val) {
	if (p == 0) {
		p = newNode(val);
		return;
	}
	if (val == treap[p].value) {
		treap[p].count++;
		update(p);
		return;
	}
	if (val < treap[p].value) {
		insert(treap[p].lChild, val);
		if (treap[p].weight < treap[treap[p].lChild].weight) zig(p);
	} else {
		insert(treap[p].rChild, val);
		if (treap[p].weight < treap[treap[p].rChild].weight) zag(p);
	} // zig 和 zag 操作，保证满足大根堆性质
	update(p);
} // 插入一个点

int getPre(int val) {
	int ans = 1;
	int p = root;
	while (p != 0) {
		if (val == treap[p].value) {
			if (treap[p].lChild > 0) {
				p = treap[p].lChild; // 左子树上不断向右走，获取最大的前驱
				while (treap[p].rChild > 0) p = treap[p].rChild;
				ans = p;
			}
			break;
		}
		if (treap[p].value < val &&
			treap[p].value > treap[ans].value) ans = p; // 尝试更新答案
		p = val < treap[p].value ? treap[p].lChild : treap[p].rChild;
	}
	return treap[ans].value;
} // 获取前驱

int getNext(int val) {
	int ans = 2;
	int p = root;
	while (p != 0) {
		if (val == treap[p].value) {
			if (treap[p].rChild > 0) {
				p = treap[p].rChild; // 右子树上不断向左走，获取最小的后继
				while (treap[p].lChild > 0) p = treap[p].lChild;
				ans = p;
			}
			break;
		}
		if (treap[p].value > val &&
			treap[p].value < treap[ans].value) ans = p; // 尝试更新答案
		p = val < treap[p].value ? treap[p].lChild : treap[p].rChild;
	}
	return treap[ans].value;
}

void remove(int &p, int val) {
	if (p == 0) return;
	if (val == treap[p].value) {
		if (treap[p].count > 1) {
			treap[p].count--;
			update(p);
		} else if (treap[p].lChild != 0 || treap[p].rChild != 0) {
			if (treap[p].rChild == 0 ||
				treap[treap[p].lChild].weight > treap[treap[p].rChild].weight) {
				zig(p);
				remove(treap[p].rChild, val);
			} else {
				zag(p);
				remove(treap[p].lChild, val);
			} // 通过旋转来删除节点
			update(p);
		} else p = 0;
		return;
	}
	if (val < treap[p].value) remove(treap[p].lChild, val);
	else remove(treap[p].rChild, val);
	update(p);
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(NULL);
	numNodes = 0;
	build();
	cin >> n;
	int opt, x;
	while (n--) {
		cin >> opt >> x;
		switch (opt) {
			case 1: { insert(root, x); break; }
			case 2: { remove(root, x); break; }
			case 3: { cout << getRankByVal(root, x) - 1 << endl; break; } // 减一
			case 4: { cout << getValByRank(root, x + 1) << endl; break; } // 加一
			case 5: { cout << getPre(x) << endl; break; }
			case 6: { cout << getNext(x) << endl; break; }
		}
	}
	return 0;
}
```

### 总结

班里的某个大佬和我说 Treap 没有 ZigZag，这让我很懵逼。但是 Zig 和 Zag 操作的确是很重要的，似乎在学 Splay 的时候也会用到，多学无害。Treap 还是很有用的，~~关键时候可以救命~~。 

---