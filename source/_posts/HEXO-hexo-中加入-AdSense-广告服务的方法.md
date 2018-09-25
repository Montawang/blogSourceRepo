---
title: '[HEXO] hexo 中加入 AdSense 广告服务的方法'
mathjax: true
copyright: true
comment: true
date: 2018-09-25 17:56:35
tags:
  - hexo
categories: hexo
photo: /2018/09/25/HEXO-hexo-中加入-AdSense-广告服务的方法/photo1.png
---

{% note default %}

就在昨天！我的 AdSense 申请终于过了！这里来总结一下 hexo 上 AdSense 通过的经验。

{% endnote %}

<!-- more -->

### 注册 AdSense 账号

这个不多说了，直接去[这里](https://www.google.com/adsense/start/#/?modal_active=none)

<div> <img style="align: center" src="pic1.jpg" /> </div>

### 更改自己 hexo 主题文件

此处以 NexT 主题为例（注：可能不是最简方式，自行斟酌）：
1. 新建 `theme/next/layout/_custom/google_adsense.swig`，将 AdSense 上的代码粘贴进去
2. 在 `theme/next/layout/_custom/head.swig` 中也粘贴一份
3. 如果在每篇博客里也想看到广告的话，在 `theme/next/layout/post.swig` 里中在希望看到的地方加上:

```swig
{% include '_custom/google_adsense.swig' %}
```
例如：在 `<div id="posts" class="posts-expand"> </div>` 中间插入，总代码如下：

```swig
{% block content %}
  <div id="posts" class="posts-expand">
    {{ post_template.render(page) }}
    {% include '_custom/google_adsense.swig' %}
  </div>
{% endblock %}
```

此时如果 `hexo server` 的话是看不到广告的，因为 AdSense 的显示是 Google 控制的。

### 疯狂更新博客

你需要使你的网站内容充足以通过 AdSense 的审核（我写了 21 篇左右才通过，其实算上所有网页其实还不止），如果被拒绝了，就再重新申请一次，说不定就过了🤣。

### 总结

使用 AdSense 来赚外块，美滋滋~~

---