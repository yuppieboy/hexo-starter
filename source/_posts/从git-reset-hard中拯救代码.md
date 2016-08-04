---
title: 从git reset --hard中拯救代码
date: 2016-08-02 13:46:24
categories: iOS
tags: Git
---

## 场景 ##
如果```git add . ```后还未```commit```输入了```git reset --hard```,会发现缓存区和本地都没有了这些改动的纪录，因为用了```--hard```。

## 挽救 ##
由于每次git命令进行操作时git都会对相关文件进行快照,并通过一定形式把信息保存再```.git/```目录下。

由于此前我使用过```git add .```命令,因此当文件被放进暂存区时,快照信息对象就已经保存了，而实用```git reset --hard```之后,这些对象就变成了悬空文件对象(```dangling blob```)。

我们可以实用```git fsck```命令显示他们

[git fsck](https://git-scm.com/docs/git-fsck)：用于验证当前git仓库数据的有效性和一致性，能够显示那些"丢失"的```commit```、```blob(文件)```、```tree```等。

我们可以通过以下命令

    git fsck --cache --unreachable $(git for-each-ref --format="%(objectname)")

我们得到一大堆blob的hash ID


    unreachable blob 907b308167f0880fb2a5c0e1614bb0c7620f9dc3
    unreachable blob 72663d3adcf67548b9e0f0b2eeef62bce3d53e03
    ...


接下来使用```git show```就能显示这些对象的内容了，例如```git show 907b308```。
