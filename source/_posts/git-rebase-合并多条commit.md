---
title: git rebase 合并多条commit
date: 2016-08-18 14:31:17
categories: iOS
tags: Git
---

## 目的 ##
合并多次commit，保留一次commit信息

## 步骤 ##

查看git log

<div align="left">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/294EE683-9521-496A-A624-D72932D9DA42.png" width = "80%" >
</div>

合并commit2,3,4, ```226b17```为commit1的```hash```值

    git rebase -i 226b17


<div align="left">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/ECB7C279-FD24-4918-8E56-F593BC595C6A.png" width = "80%" >
</div>

`:wq`

<div align="left">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/96EA2F8E-A318-44C0-88BF-21FE4158C095.png" width = "80%" >
</div>

`:wq`

commit合并成功

<div align="left">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/805B17D6-FFEB-499F-AA03-2913A66D7110.png" width = "80%" >
</div>
