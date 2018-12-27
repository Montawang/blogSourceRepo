---
title: '[C++ 奇技淫巧] 字符串类的使用总结'
mathjax: true
copyright: true
comment: true
date: 2018-12-27 20:16:02
tags:
	- C++ 奇技淫巧
	- 迭代器
	- iostream
categories: 奇技淫巧
photo:
---

{% note default %}

字符串类是个好东西，不论是在 OI 竞赛中还是在实际的应用当中，C++ 中的字符串都提供了完备的函数。虽然在实现上可能并不如别的语言的字符串的实现，但是它仍然是个好东西。

{% endnote %}

<!-- more -->

### 字符串类简介

直接看[这里](https://zh.cppreference.com/w/cpp/string/basic_string)吧，说得很清楚。C++ 的 `string` 类型有很多，都是为了不同的目的设计的，这篇文章将会详细介绍 `std::string` 的用法，其它的将会一笔带过。

### `std::string` 类

#### 运算符重载

1. `+` 和 `+=`：连接字符串
2. `=`：字符串赋值
3. `>`、`>=`、`<` 和 `<=`：字符串比较（例如a < b, aa < ab）
4. `==`、`!=`：比较字符串
5. `<<`、`>>`：输出、输入字符串

**注意，两个字符串常量必须先声明再相加，不能像下面那样：**

```cpp
#include <iostream>
#include <string>
int main() {
  string str = "cat";
  cout << "apple" + "boy" // illegal!
  return 0;
}

// 因为 "apple" 和 "boy" 默认为 const char* 类型，没有重载 + 运算符
```

#### 查找方法

```cpp
// 返回字符串 ab 在 str 的位置
str.find("ab");
// 在 str[2]~str[n-1] 范围内查找并返回字符串 ab 在 str 的位置
str.find("ab", 2);
// 在 str[0]~str[2] 范围内查找并返回字符串 ab 在 str 的位置
str.rfind("ab", 2);

// first 系列函数
// 返回 apple 中任何一个字符首次在 str 中出现的位置
str.find_first_of("apple");
// 返回 apple 中任何一个字符首次在 str[2]~str[n-1] 范围中出现的位置
str.find_first_of("apple", 2);
// 返回除 apple 以外的任何一个字符在 str 中首次出现的位置
str.find_first_not_of("apple");
// 返回除 apple 以外的任何一个字符在 str[2]~str[n-1] 范围中首次出现的位置
str.find_first_not_of("apple", 2);

// last 系列函数
// 返回 apple 中任何一个字符最后一次在 str 中出现的位置
str.find_last_of("apple");
// 返回 apple 中任何一个字符最后一次在 str[0]~str[2] 范围中出现的位置
str.find_last_of("apple", 2);
// 返回除 apple 以外的任何一个字符在 str 中最后一次出现的位置
str.find_last_not_of("apple");
// 返回除 apple 以外的任何一个字符在 str[0]~str[2] 范围中最后一次出现的位置
str.find_last_not_of("apple", 2);

// 以上函数如果没有找到，均返回string::npos
cout << string::npos;
```

#### 子串

```cpp
// 返回 [3] 及以后的子串
str.substr(3);
// 返回 str[2]~str[2+(4-1)] 子串(即从[2]开始4个字符组成的字符串)
str.substr(2, 4);
```

#### 替换

```cpp
// 返回把 [2]~[2+(4-1)] 的内容替换为 "sz" 后的新字符串
str.replace(2, 4, "sz");
// 返回把 [2]~[2+(4-1)] 的内容替换为 "abcd" 的前3个字符后的新字符串
str.replace(2, 4, "abcd", 3);
```

#### 插入

```cpp
// 从 [2] 位置开始添加字符串 "sz"，并返回形成的新字符串
str.insert(2, "sz");
// 从 [2] 位置开始添加字符串 "abcd" 的前 3 个字符，并返回形成的新字符串
str.insert(2, "abcd", 3);
// 从 [2] 位置开始添加字符串 "abcd" 的前 [2]~[2+(3-1)] 个字符，并返回形成的新字符串
str.insert(2, "abcd", 1, 3);
```

#### 追加

除了用重载的 `+` 操作符，还可以使用函数来完成。
```cpp
str.push_back('a'); // 在 str 末尾添加字符 'a'
str.append("abc");  // 在 str 末尾添加字符串 "abc"
```

#### 删除

```cpp
str.erase(3);    // 删除 [3] 及以后的字符，并返回新字符串
str.erase(3, 5); // 删除从 [3] 开始的 5 个字符，并返回新字符串
```

#### 交换

```cpp
str1.swap(str2); // 把 str1 与 str2 交换
```

### 其它的 `basic_string<>` 类

| 类型             | 定义                          |
| ---------------- | ----------------------------- |
| `std::wstring`   | `std::basic_string<wchar_t>`  |
| `std::u8string`  | `std::basic_string<char8_t>`  |
| `std::u16string` | `std::basic_string<char16_t>` |
| `std::u32string` | `std::basic_string<char32_t>` |

主要还是说一下 `std::wstring` 吧，其实就是宽字符的 `string` ，Windows 和 Linux 上并不相同：

1. 在 Windows 平台下 `sizeof(wchar_t)` 为 2，而在 Linux 平台下 `sizeof(wchar_t)` 为 4；
2. 在 Windows 平台下宽字符(或字符串)字面量使用 `UTF-16` 编码，Linux平台下使用 `UTF-32` 编码。

---