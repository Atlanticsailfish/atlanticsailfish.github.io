---
title: 使用Fuwari框架和GitHub Pages部署博客
published: 2025-08-13
description: '记录部署该博客过程中遇到的一些小问题'
image: './posts-images/53948770_p0_master1200.jpg'
tags: [Blog, Deploy]
category: 'Tech'
draft: false 
lang: ''
---

> Cover image source: [Source](https://www.pixiv.net/artworks/53948770)


## 第一个问题：仓库映射

我部署的base是[https://atlanticsailfish.github.io](https://atlanticsailfish.github.io)

> 使用`username/username.github.io`格式的仓库，Github会自动给予一个不含仓库名称的公网链接

在配置`astro.config.mjs`的过程中，应该将`base`置空，如果留下了`base: '/'`，那博客就会变成只有文字的空壳...hhh
```bash
import { defineConfig } from 'astro/config'

export default defineConfig({
  site: 'https://atlanticsailfish.github.io',
  base: '',
})
```

## 第二个问题：DNS映射

Github给予的公网地址[atlanticsailfish.github.io](https://atlanticsailfish.github.io)在大陆访问并不稳定，故笔者使用DNS映射自定义域名[blog.daqiyu.dpdns.org/](https://blog.daqiyu.dpdns.org/)，（相应的需要在`astro.config.mjs`修改配置为`site: 'https://blog.daqiyu.dpdns.org/'`）在映射成功后，笔者发现访问时不时会中断一段时间，经过排查发现最有可能的是，打开放在后台的GitHub Pages DNS映射页面，已保存domain，但网页仍会隔一段时间进行自动检测，且检测期间访问就会中断，保存后关闭该页面即可恢复稳定

:::note
再次感谢原作者[saicaca](https://github.com/saicaca/fuwari)提供的博客框架
:::