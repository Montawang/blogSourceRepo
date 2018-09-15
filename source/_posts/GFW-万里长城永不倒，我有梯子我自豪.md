---
title: '[GFW] 万里长城永不倒，我有梯子我自豪'
mathjax: true
copyright: true
comment: true
date: 2018-08-31 19:20:34
tags:
	- GFW
categories: GFW
photo: /2018/08/31/GFW-万里长城永不倒，我有梯子我自豪/photo1.jpg
---

{% note default %}
嗯，本篇文章来普及一下科学上网，当然先只讲一个方法~~一次讲太多影响不好~~。为什么会想写这篇文章呢？主要是因为最近发生了很多事，而且很多消息国内被封锁了，所以这里为了让更多人能够看到墙外的世界，看到一个事情的更多面，并且将富强民主文明和谐的社会主义核心价值观播撒到世界各地，专门开了这么一篇文章。
{% endnote %}

<!-- more -->

### 什么是 GFW？

下面这段话摘自维基百科上对 [GFW 的解释](https://zh.wikipedia.org/wiki/%E9%98%B2%E7%81%AB%E9%95%BF%E5%9F%8E)

<div class="note info">
防火长城（英语：Great Firewall (of China)，常用简称：GFW，中文也称中国国家防火墙，中国大陆民众俗称墙、防火墙、功夫网等等），是对中华人民共和国政府在其互联网边界审查系统（包括相关行政审查系统）的统称。此系统起步于1998年，其英文名称得自于2002年5月17日Charles R. Smith所写的一篇关于中国网络审查的文章《The Great Firewall of China》，取与Great Wall（长城）相谐的效果，简写为Great Firewall，缩写GFW。随着使用的拓广，中文“墙”和英文“GFW”有时也被用作动词，网友所说的“被墙”即指网站内容被防火长城所屏蔽或者指服务器的通讯被封阻，“翻墙”也被引申为突破网络审查浏览境内外被屏蔽的网站或使用服务（如被GFW屏蔽的网盘Dropbox）的行为。
</div>

所以这个 GFW 出发点还是挺好的，而且这个墙其实是很多国家都有的，维基百科上的解释欠妥。如果想知道到底有哪些网站被墙了可以看[这个](https://github.com/gfwlist/gfwlist)。 当然 GFW 在一定程度上也保证了国家安全不被侵犯，利大于弊。然而还是有很多东西我们想访问或者必须访问却无法访问，这个时候就是梯子也就是科学上网工具发挥作用的时候了。

### 使用 Psiphon 来突破网络审查

#### 直接下载

因为 Lantern 太坑，而且并不是很稳定，所以我最后选择了免费而且**开源**的 Psiphon。Psiphon 的网站在[这里](https://www.psiphon3.com/)，不过似乎要翻墙🤣，所以可以：
<br />
<center>
{% btn https://s3.amazonaws.com/psiphon/web/l99h-ony2-bspu/zh/download.html#direct, <strong>直接到这里下载</strong>, download fa-lg fa-fw %}
</center>
<br />
安装完成之后点击连接，第一次会比较慢，连接好之后会跳出一个网页，告诉你当前网络的地理位置，一般来说可以直接忽略了。当然设置里也可以设置分通道代理之类的。最后一点提醒，像我这样的 iOS 用户要使用美区号才能在 AppStore 里下载。

#### 极客方法

极客那当然是自己编译安装，直接用命令行了。这个感兴趣的同学可以看[这篇文章](https://medium.com/@rampage_router/psiphon3-%E5%91%BD%E4%BB%A4%E8%A1%8C%E7%89%88%E6%9C%AC%E7%BC%96%E8%AF%91%E6%8C%87%E5%8D%97-f45c7846dc2b)

### 总结

~~由于自己比较穷~~，所以只能用个开源软件勉强糊口，有钱的同学可以自己买 VPN 或 Shadowsocks，[甚至可以自己搭](https://medium.com/@zoomyale/%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91%E7%9A%84%E7%BB%88%E6%9E%81%E5%A7%BF%E5%8A%BF-%E5%9C%A8-vultr-vps-%E4%B8%8A%E6%90%AD%E5%BB%BA-shadowsocks-fd57c807d97e)。


---