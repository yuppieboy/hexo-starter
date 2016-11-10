---
title: 【转载】Ruby China的RubyGems镜像上线
date: 2016-11-01 13:14:33
categories: Ruby
tags: RubyGems
---

### 公告

`huacnlee for Ruby China · 发布于 2016年3月09日 · 最后由 huopo125 回复于 2016年5月12日`

几年前，由我起头，我们搭建了 `ruby.taobao.org` 为国内开发者解决 Gem 安装的问题，现在应该国内绝大多数，甚至所有（国内服务器，国内网络）的 Ruby 开发者企业，应该都在用它。

但最近越来越发现之前 `ruby.taobao.org` 很难同步了，原因有很多，之前的实现方式过于复杂，服务器在国内，同步困难，阿里的网络安全要求维护服务器得是公司员工.... 各种限制导致我们在维护的时候困难重重，尤其是当 `@fsword` `@qhwa` 离职以后，根本无法再继续维护了。

为了有一个可持续发展未来，我们考虑由 `Ruby China` 来负责这个事情，这样可以像现在 Ruby China 社区，`RubyConf China` 一样，由社区的很多参与者来共同维护。

这件事情我们已经考虑了一年了，之前一直没有找到一个好的方式来解决，以及一些其他因素，比如资金的问题。

这周，我们终于找到了解决方案，也找到了赞助商：`腾讯云`

### 整个 gems.ruby-china.org 的架构:

>2016-05-18 Updated: Gems 存储放到了 UpYun 上面，通过 UpYun 的 CDN 来解决，同时配合 UpYun 的镜像存储功能，减少回源到国外的次数。

>过程: [https://ruby-china.org/topics/29978](https://ruby-china.org/topics/29978)

<div align="center">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/3F00C78D-9939-44FE-BE0A-B380479B50AC.png" width = "100%" >
</div>

### 新的方案的特点
- 实时的，不再有同步耗时的问题；

- 全球 `400+ CDN` 节点（据腾讯官方所说 ~~）为 Gem 下载加速；

- 更加简单有效的架构，稳定性更高；

- 背后有两台国外服务器，确保稳定性，确保 `CDN` 汇源能获取到数据；

[原文链接](https://ruby-china.org/topics/29250)
