---
title: '[HEXO] NexT 主题提高博客颜值'
date: 2018-08-30 20:37:23
tags: 
  - hexo
categories: hexo
mathjax: true
copyright: true
comment: true
photo: 
	- /2018/08/30/HEXO-NexT-主题提高博客颜值/photo1.jpg
---

{% note default %}
今天是把博客好好地美化了一把，今天就来讲一下 hexo 博客美化过程中的心得，因为自己使用的是 NexT 主题，所以这里的有些过程仅供别的主题的用户做参考。
{% endnote %}

<!-- more -->

### 文章的模板文件

为了每次更新方便，我直接改了 `blog/scaffolds/` 下的 `post.md`，每次生成的时候模板就都有了，不需要自己再去弄。这个是我的模板：

```md
---
title: {{ title }}
date: {{ date }}
tags:
categories:
mathjax: true
copyright: true
comment: true
photo: 
---

{% note default %}
{% endnote %}

<!-- more -->

---
```
说明一下，这个 `note` 要在 `next\_config.yml` 中开启才可以写。下面会讲到。`photo` 是文章开头的图片，就像这篇文章开头那样，注意一定要加绝对路径，比如我这张图片存在文章的文件夹下，那么路径就是 `/2018/08/30/HEXO-NexT-主题提高博客颜值/photo1.jpg`。`copyright` 这一项也是要开启之后才行，我的博客的 copyright 是自己重新写的，后面会讲到。最后这个 `---` 是结束符，和最后的结束语一起用。

### Markdown 技巧与内置样式

[基础的语法](https://segmentfault.com/markdown)就不提了，点进来的应该都会了。这里就讲讲一些技巧。

#### 编辑器

我用的 Markdown 编辑器是 [Typora](https://typora.io/)，有些行内公式的显示可以在设置里打开。这是我用过的最好的 Markdown编辑器

#### 分隔线和空行

```md
这是文字
<hr />
上面是分隔线
<br />
上面是空行
```
效果如下：

这是文字
<hr />
上面是分隔线
<br />
上面是空行

#### 引用

```md
<blockquote>引用内容</blockquote>
<!-- 如果前后间隙很小，可以像下面这样写 -->
<p><blockquote>引用内容</blockquote></p>
```
效果如下：
<blockquote>引用内容</blockquote>
<p><blockquote>引用内容</blockquote></p>

#### 居中和右对齐
```md
<!-- 居中 -->
<center>内容</center>
<!-- 右对齐 -->
<div style="text-align:right">内容</div>
```
效果如下：
<center>内容</center>
<div style="text-align:right">内容</div>

#### 字体大小和颜色

```md
<font color="#187892" size="number">内容</font>
<!-- 详细请查看 http://www.w3school.com.cn/tags/tag_font.asp -->
```
效果如下：
<font color="#187892" size="number">内容</font>

#### Todo list

```md
<ul>
<li><i class="fa fa-check-square"></i> 已完成</li>
<li><i class="fa fa-square"></i> 未完成</li>
</ul>
```
效果如下：
<ul>
<li><i class="fa fa-check-square"></i> 已完成</li>
<li><i class="fa fa-square"></i> 未完成</li>
</ul>

#### Note 嵌套 Todo list

```md
<!-- 一共有两种写法，效果看下面 -->
<div class="note primary">
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-square"></i> 未完成
  <i class="fa fa-square"></i> 未完成
  <i class="fa fa-square"></i> 未完成
</div>
<div class="note primary">
  <p>
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-square"></i> 未完成
    <i class="fa fa-square"></i> 未完成
    <i class="fa fa-square"></i> 未完成
  </p>
</div>
```
效果如下：
<div class="note primary">
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-check-square"></i> 已完成
  <i class="fa fa-square"></i> 未完成
  <i class="fa fa-square"></i> 未完成
  <i class="fa fa-square"></i> 未完成
</div>
<div class="note primary">
  <p>
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-check-square"></i> 已完成
    <i class="fa fa-square"></i> 未完成
    <i class="fa fa-square"></i> 未完成
    <i class="fa fa-square"></i> 未完成
  </p>
</div>

这两个写法熟悉 HTML 的同学应该是明白的，这里~~因为我太菜了~~就不讲了。主要说一下 `<i class="fa fa-square"></i>` 是从 [Font Awesome](https://fontawesome.com/) 上获取图标的方法，不过有些图标似乎是不能加载的，我也不知道为什么。

#### Font Awesome

Font Awesome 更多的使用可以看[官网示例](https://fontawesome.com/how-to-use/on-the-web/setup/getting-started?using=web-fonts-with-css)，这里讲一个放大图标的方法

```md
<i class="fa fa-download"></i> 普通
<i class="fa fa-download fa-lg"></i> 变大 33%
<i class="fa fa-download fa-2x"></i> 两倍大
```
效果如下：
<i class="fa fa-download"></i> 普通
<i class="fa fa-download fa-lg"></i> 变大 33%
<i class="fa fa-download fa-2x"></i> 两倍大

#### 代码高亮

主题自带样式 代码块高亮
```
```[language] [title] [url] [link-text]
`代码`
```
`[language]` 是代码语言的名称，用来设置代码块颜色高亮，非必须；
`[title]` 是顶部左边的说明，非必须；
`[url]` 是顶部右边的超链接地址，非必须；
`[link text]` 如它的字面意思，超链接的名称，非必须。
亲测这 4 项应该是根据空格来分隔，而不是[]，故请不要加[]。除非如果你想写后面两个，但不想写前面两个，那么就必须加 `[]` 了，要这样写：`[] [] [url] [link text]`。

首先关于代码块颜色高亮，高亮的模式可以在主题配置文件中设置：
```yml
# Code Highlight theme
# Available value:
#    normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme

highlight_theme: night eighties
```
要颜色正确高亮，代码语言的名称肯定要写对，各种支持语言的名称可以查看[这篇文章](https://almostover.ru/2016-07/hexo-highlight-code-styles/)。当然，如果你和我一样懒，可以在站点配置文件 `_config.yml` 中设置自动高亮（不过这个自动高亮似乎有问题，不建议打开）：
```diff
highlight:
  enable: true
  line_number: true
# 代码自动高亮
-  auto_detect: false
+  auto_detect: true
```
从网上找到的一个文本效果如下（别跟我说你不知道 `diff` 是什么）：
```diff
Index: languages/ini.js
===================================================================
--- languages/ini.js    (revision 199)
+++ languages/ini.js    (revision 200)
@@ -1,8 +1,7 @@
 hljs.LANGUAGES.ini =
 {
-   case_insensitive: true,
-  defaultMode:
   {
+  defaultMode: {
-     contains: ['comment', 'title', 'setting'],
+     illegal: '[^\\s]'
+   },
*** /path/to/original timestamp
--- /path/to/new      timestamp
***************
*** 1,3 ****
--- 1,9 ----
+ This is an important
+ notice! It should
+ therefore be located at
+ the beginning of this
+ document!
! compress the size of the
! changes.
  It is important to spell

```
代码仅仅高亮肯定是不够地，代码块地功能也很重要，比如复制这个功能。只要在 `_config.yml` 里打开就行啦：
```yml
codeblock:
  # Manual define the border radius in codeblock
  # Leave it empty for the default 1
  border_radius: 3
  # Add copy button on codeblock
  copy_button:
    enable: true
    # Show text copy result
    show_result: true
```

#### Note 标签

在主题配置文件_config.yml里有一个关于这个的配置:
```yml
# Note tag (bs-callout).
note:
  # 风格
  style: flat
  # 要不要图标
  icons: true
  # 圆角矩形
  border_radius: 3
  light_bg_offset: 0
```

用 HTML 写就是这个样子
```html
<div class="note default"><p>default</p></div>
<div class="note primary"><p>primary</p></div>
<div class="note success"><p>success</p></div>
<div class="note info"><p>info</p></div>
<div class="note warning"><p>warning</p></div>
<div class="note danger"><p>danger</p></div>
<div class="note danger no-icon"><p>danger no-icon</p></div>
```
用 swig 语法写就是这样

```
{% note [class] %}
Any content (support inline tags too).
{% endnote %}
[class] : default | primary | success | info | warning | danger.
          May be not defined.

```
里面的三种风格长啥样？开启图标长啥样？可以查看[这个页面](https://github.com/iissnan/hexo-theme-next/pull/1697)，更多的介绍也在这个页面，请自行查看

最后的几种效果：
<div class="note default"><p>default</p></div>
<div class="note primary"><p>primary</p></div>
<div class="note success"><p>success</p></div>
<div class="note info"><p>info</p></div>
<div class="note warning"><p>warning</p></div>
<div class="note danger"><p>danger</p></div>
<div class="note danger no-icon"><p>danger no-icon</p></div>

#### Label 标签
`label` 标签不建议加在段首, 首先可以在主题配置文件中有配置：
```
# Label tag.
label: true
```
然后效果如下（`@`前面的是label的名字，后面的是要显示的文字）

{% label default@default %}
```
{% label default@default %}
```
{% label primary@primary %}
```
{% label primary@primary %}
```
{% label success@success %}
```
{% label success@success %}
```
{% label info@info %}
```
{% label info@info %}
```
{% label warning@warning %}
```
{% label warning@warning %}
```
{% label danger@danger %}
```
{% label danger@danger %}
```

#### Tab 选项卡

当然也是要先配置一下：
```yml
# Tabs tag.
tabs:
  enable: true
  transition:
    tabs: true
    labels: true
  border_radius: 3
```
代码如下：
```
{% tabs 选项卡, 2 %}
<!-- tab -->
**这是选项卡 1** 呵呵哈哈哈哈哈哈
<!-- endtab -->
<!-- tab -->
**这是选项卡 2** 额。。。
<!-- endtab -->
<!-- tab -->
**这是选项卡 3** 哇，你找到我了！
<!-- endtab -->
{% endtabs %}
```
效果如下：
{% tabs 选项卡, 2 %}
<!-- tab -->
**这是选项卡 1** 呵呵哈哈哈哈哈哈
<!-- endtab -->
<!-- tab -->
**这是选项卡 2** 额。。。
<!-- endtab -->
<!-- tab -->
**这是选项卡 3** 哇，你找到我了！
<!-- endtab -->
{% endtabs %}

上面源码中，2 表示一开始在第二个选项卡，非必须，若数值为 -1 则隐藏选项卡内容。

#### 按钮样式
代码如下：
```
{% btn https://almostover.ru/2016-01/hexo-theme-next-test/#Button-tag-test, 更多关于按钮的使用点这里, download fa-lg fa-fw %}
```
效果如下：

{% btn https://almostover.ru/2016-01/hexo-theme-next-test/#Button-tag-test, 更多关于按钮的使用点这里, download fa-lg fa-fw %}

### 更多的主题配置

#### 返回顶部按钮与阅读进度

在 `next/_config.yml` 里找到 `b2t` 这个地方进行如下配置：
```yml
  # Back to top in sidebar (only for Pisces | Gemini).
  b2t: true
  # Scroll percent label in b2t button.
  scrollpercent: true
```

#### 添加 RSS 订阅

首先安装一个模块
```bash
$ npm install hexo-generator-feed --save
```
然后在**根目录**下的 `_config.yml` 里添加：
```yml
plugins: hexo-generate-feed
```
最后在 `next\_config.yml` 里配置：
```yml
# Set rss to false to disable feed link.
# Leave rss as empty to use site's feed link, and install hexo-generator-feed: `npm install hexo-generator-feed --save`.
# Set rss to specific value if you have burned your feed already.
rss: /atom.xml
```
重新生成就可以使用 RSS 了。

### 评论系统重配置

以前用的是 Disqus，然而需要翻墙，所以决定换成 Valine。这个 Valine 要先在 [LeanCloud](https://leancloud.cn/) 里注册并创建一个应用，这里就不再赘述.然后在 `next\_config.yml` 里配置。注意：得在 `LeanCloud -> 应用 -> 设置 -> 应用 Key` 中把 AppID 和 AppKey 给复制到 `next\_config.yml` 里面去，并且在 `LeanCloud -> 应用 -> 设置 -> 安全中心` 里将自己的域名添加到 `Web 安全域名` 中。

```yml
valine:
  enable: true # When enable is set to be true, leancloud_visitors is recommended to be closed for the re-initialization problem within different leancloud adk version.
  appid: # your leancloud application appid
  appkey: # your leancloud application appkey
  notify: true # mail notifier , https://github.com/xCss/Valine/wiki
  verify: true # Verification code
  placeholder: 😎看了这么多，不想说点什么嘛😉 # comment box placeholder
  avatar: mm # gravatar style
  guest_info: nick,mail,link # custom comment header
  pageSize: 10 # pagination size
  visitor: false # leancloud-counter-security is not supported for now. When visitor is set to be true, appid and appkey are recommended to be the same as leancloud_visitors' for counter compatibility. Article reading statistic https://valine.js.org/visitor.html

```
这个 LeanCloud 也是比较良心的啊，可以免费使用，当然我用的是开发版，所以大家不要太着急🤣。


---