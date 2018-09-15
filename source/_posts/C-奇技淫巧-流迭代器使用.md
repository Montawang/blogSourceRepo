---
title: '[C++ 奇技淫巧] 流迭代器使用'
date: 2018-08-17 19:09:16
tags:
	- C++ 奇技淫巧
	- 迭代器
	- iostream
categories: 奇技淫巧
mathjax: true
copyright: true
---

### 写在前面

自从熟知了 STL 库这个东西，我就再也离不开它了，以前一直以为**只有容器才会有迭代器可以用**，然而这是因为自己的知识水平还不够高，今天就介绍一下流迭代器这种神奇的存在。

<!-- more -->

### 流迭代器

**iostream 迭代器（iostream iterator）**：与输入或输出流绑定在一起，用于迭代遍历所关联的 IO 流。

虽然 `iostream` 类型不是容器，但标准库同样提供了在 `iostream` 对象上使用的迭代器：`istream_iterator` 用于读取输入流，而 `ostream_iterator` 则用于写输出流。这些迭代器将它们所对应的流视为特定类型的**元素序列**。

下面列出流迭代器的使用方法：

|     `iostream`  迭代器的构造函数      |                          对应的意义                          |
| :-----------------------------------: | :----------------------------------------------------------: |
|    `istream_iterator<T> in(strm)`     | 创建从输入流 `strm` 中读取T类型对象的 `istream_iterator` 对象 |
|        `istream_terator<T> in`        |           `istream_iterator` 对象的超出末端迭代器            |
|    `ostream_iterator<T> out(strm)`    | 创建将T类型的对象写到输出流 `strm` 的 `ostream_iterator` 对象 |
| `ostream_iterator<T> out(str, delim)` | 创建将 `T` 类型的对象写到输出流 `strm` 的 `ostream_iterator` 对象，在写入过程中使用 `delim` 作为元素的分隔符。`delim` 是以空字符结束的字符数组（C风格字符串）。 |

| `istream_iterator` 的操作 |                          对应的意义                          |
| :-----------------------: | :----------------------------------------------------------: |
| `it1 == it2; it1 != it2`  | 比较两上 `istream_iterator` 对象是否相等（不等）。迭代器读取的必须是相同的类型。如果两个迭代器都是 `end` 值，则它们相等。对于两个都不指向流结束位置的迭代器，如果它们使用同一个输入流构造，则它们也相等 |
|           `*it`           |                      返回从流中读取的值                      |
|         `it->mem`         |   是 `(*it).mem` 的同义词。返回从流中读取的对象的 mem 成员   |
|       `++it; it++`        | 通过使用元素类型提供的 `>>` 操作从输入流中读取下一个元素值，使迭代器向前移动。通常，前缀版本使用迭代器在流中向前移动，并返回对加 $1$ 后的迭代器的引用。而后缀版本使迭代器在流中向前移动后，返回原值 |

**注：流迭代器都是类模板：任何已定义输入操作符（`>>` 操作符）的类型都可以定义istream_iterator。任何已定义输出操作符（`<<` 操作符）的类型可定义 `ostream_iterator`。** 

### 流迭代器使用

**注：迭代器的头文件别忘记引入。 `#include <iterator>`**

```cpp
#include<iostream>
#include<vector>
#include<iterator>
using namespace std;

int main() {
	istream_iterator<int> cinIter(cin);
	istream_iterator<int> endOfStream;
	vector<int> vec;
	while (cinIter != endOfStream)
		//注意++先引用后增值，不能写出前缀
		vec.push_back(*cinIter++);
	//也可以写成  vector<int> vec(cinIter, endOfStream);

	for (int i = 0; i != vec.size(); ++i) cout << vec[i];
	return 0;
}
```

**注：其中 `end_of_stream` 定义为空的迭代器对象，用作结束迭代器。绑定流上的迭代器在遇到文件结束或某个错误时，将等于结束迭代器的值。** 

```cpp
int main(int argc, char **argv) {
	ostream_iterator<string> outIter(cout, "\n");
	istream_iterator<string> inIter(cin), endOfStream;
	while(endOfStream != inIter)
		*outIter++ = *inIter++; 
	return 0;
}
```
**注：执行顺序是先 `iter++`，返回 `iter`，最后 `*iter`。**
**注意输出，输入 `1 2 3 4 5`，按 `enter` 输出 `1\n2\n3\n4\n`，按 `ctrl+z` 输出 `5`。**

```cpp
#include <fstream>
#include <iostream>
#include <vector>
#include <iterator>
using namespace std;
 
int main(int argc, char **argv) {
	ifstream infile("in.txt");
	istream_iterator<int> cinIter(infile);
	istream_iterator<int> endOfStream;
	vector<int> vec(cinIter, endOfStream);
	sort(vec.begin(), vec.end());
	ostream_iterator<int> output(cout, " ");
	unique_copy(vec.begin(), vec.end(), output);
	return 0;
}
```
示例输入/输出：

	INPUT(in.txt): 1 2 2 2 5 5 6 6 7 8 9 10 9 100 
	       OUTPUT: 1 2 5 6 7 8 9 10 100

### 流迭代器的限制

- 不可能从 `ostream_iterator` 对象读入，也不可能写到 `istream_iterator` 对象中。
- 一旦给 `ostream_iterator`对象赋了一个值，写入就提交了。赋值后，没有办法再改变这个值。此外，`ostream_iterator` 对象中每个不同的值都只能正好输出一次。
- `ostream_iterator` 没有 `->` 操作符。

### 流迭代器的速度

为了测试流迭代器的速度，我先随机生成了 $10000000$ 个数据，生成代码如下：

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
	ofstream test("in.txt");
	srand((unsigned)time(NULL) * 19260817); // +1s
	for (int i = 1; i <= 10000000; ++i)
		test << rand() << " ";
	return 0;
}
```
#### 迭代器测试

```cpp
#include <fstream>
#include <iostream>
#include <vector>
#include <iterator>
#include <algorithm>
#include <chrono>
using namespace std;
using namespace std::chrono;
 
int main(int argc, char **argv) {
	auto start = system_clock::now();
	ifstream infile("in.txt");
	istream_iterator<int> cinIter(infile);
	istream_iterator<int> endOfStream;
	vector<int> vec(cinIter, endOfStream);
	auto end = system_clock::now();
	auto duration = duration_cast<microseconds>(end - start);
	cout << "cost: " 
		 << double(duration.count()) * microseconds::period::num / microseconds::period::den 
		 << " seconds" << endl;
	return 0;
}
```
结果：

	cost: 10.7543 seconds

**注：为了测试时间尽量精准，这里引入了 `chrono` 库，使用了支持 C++ 11 的 gcc 7.3.0，并且是在 Linux(Ubuntu) 上编译运行的，鉴于编译器的优化能力一个版本比一个版本强，此处没有开优化但结果仍可能有误差，与 OI 中使用迭代器的速度不一样，读者可以使用别的库再进行实验。**

#### 开了 $O_2$ 优化的流迭代器

```cpp
#pragma GCC optimize(2)

#include <fstream>
#include <iostream>
#include <vector>
#include <iterator>
#include <algorithm>
#include <chrono>
using namespace std;
using namespace std::chrono;
 
int main(int argc, char **argv) {
	auto start = system_clock::now();
	ifstream infile("in.txt");
	istream_iterator<int> cinIter(infile);
	istream_iterator<int> endOfStream;
	vector<int> vec(cinIter, endOfStream);
	auto end = system_clock::now();
	auto duration = duration_cast<microseconds>(end - start);
	cout << "cost: " 
		 << double(duration.count()) * microseconds::period::num / microseconds::period::den 
		 << " seconds" << endl;
	return 0;
}
```
结果：
	
	cost: 2.31234 seconds

**注：开了优化的结果，读者自行判断**

#### OI 中快读的时间测试

```cpp
#include <cstdlib>
#include <cstdio>
#include <chrono>
#include <cctype>
#include <iostream>
#include <vector>
using namespace std;
using namespace std::chrono;

inline void read(int &x) {
	x = 0;
	bool sign = false;
	char ch = 0;
	while (!isdigit(ch)) {
		sign |= (ch == '-');
		ch = getchar();
	}
	while (isdigit(ch)) {
		x = (x << 3) + (x << 1) + (ch ^ 48);
		ch = getchar();
	}
	x = sign ? -x : x;
}

int main(int argc, char **argv) {
	auto start = system_clock::now();
	freopen("in.txt", "r", stdin);
	int x;
	vector<int> a;
	for (int i = 0; i < 10000000; ++i) {
		read(x);
		a.push_back(x);
	}

	auto end = system_clock::now();
	auto duration = duration_cast<microseconds>(end - start);
	cout << "cost: " 
		 << double(duration.count()) * microseconds::period::num / microseconds::period::den 
		 << " seconds" << endl;
	return 0;
}
```

结果

	cost: 2.61905 seconds

**注：快读的写法也许不是最优，读者可以自行实验。**

### 总结

事实上，抛开 OI 比赛不说，STL 的库使用优化还是很强的，但是 OI 中嘛，如果作大死用这个也许真的会崩。至于有关 STL 速度的讨论可以看[这里](https://www.zhihu.com/question/51650118)。

---