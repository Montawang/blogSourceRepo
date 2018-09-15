---
title: '[HEXO] hexo 中插入视频的方法'
date: 2018-08-29 20:31:30
tags:
	- hexo
categories: hexo
copyright: true
---

### 写在前面

以前一直以为 hexo 是不能插入视频的，今天突然知道了，原来插入视频是可以的！！本篇博客主要讲解一下 hexo 博客如何插入视频。

<!-- more -->

### 插入 Youtube 视频

插入 Youtube 视频其实很简单，在 YouTube 视频的下方的“共享”中，选择嵌入即可获取类似这样的链接：

```html
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/7gJ8mGFjeqA" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
```
效果如下（要梯子）：
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/7gJ8mGFjeqA" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

可以将 `width` 或 `height` 改为 `"100%"`（当然也可以两个都改，具体效果试了才知道，这个数值是可以随便改的）

```html
<iframe width="100%" height="350" src="https://www.youtube-nocookie.com/embed/7gJ8mGFjeqA" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
```
效果如下（要梯子）：

<iframe width="100%" height="350" src="https://www.youtube-nocookie.com/embed/7gJ8mGFjeqA" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

### 插入 Bilibili 视频

其实也是一样，点击“分享”，获取 `iframe` 这个代码就可以了

```html
<iframe src="//player.bilibili.com/player.html?aid=19448254&cid=31715906&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
```
效果如下：

<iframe src="//player.bilibili.com/player.html?aid=19448254&cid=31715906&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

自定义了大小之后的效果：

<iframe width="100%" height="350" src="//player.bilibili.com/player.html?aid=19448254&cid=31715906&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

### 通用方法

直接把视频文件放在资源文件夹里，然后直接写 HTML 代码：

```html
<video width="100%" height="400" src="movie.mp4" controls="controls">
The `<video>` tag is not supported by your browser.
</video>
```
效果如下：

<video width="100%" height="400" src="movie.mp4" controls="controls">
The `<video>` tag is not supported by your browser.
</video>

### 总结

嗯...... HTML 大法好，在 Markdown 里用 HTML 控件简直美滋滋。

---