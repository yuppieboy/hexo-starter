---
title: Mac OS启动服务浅析
date: 2016-11-29 14:42:03
tags: Mac OS
---

## 起因
之所以分析Mac OS启动项，主要原因是我下载visio for Mac时中了一招，下载到了``李鬼``，是个`pkg`文件，安装时提示我可能损害电脑，但是我还是授权了，结果悲剧了。

## 后果
`chrome`和`firefox`首页全部被篡改为名叫`snowbitt`的搜索引擎，开机后`safari`和`firefox`都被默认启动。

## 解决办法
首页篡改还是比较容易，比如chrome中 `setting->重制设置`即可，不过它会关闭所有插件，重新打勾开启，然后把启动时网页改过来即可。firefox中直接删相应搜索引擎项无用，只能卸载掉并把·`／Library/Application Support/Firefox` 文件删掉，然后下载新的dmg文件重装即可。

比较麻烦的是开机启动项，系统偏好设置里明明没有启动项，可是开机时还是会启动，于是就深究了下去，发现和Mac OS启动服务有关系。

我们先来看一下__Mac OS X__的启动原理：

1. mac固件激活，初始化硬件，加载`BootX`引导器。

2. `BootX`加载内核与内核扩展(kext)。

3. 内核启动`launchd`进程。

4. launchd根据
/System/Library/LaunchAgents, /System/Library/LaunchDaemons, /Library/LaunchDaemons,
/Library/LaunchAgents,
~/Library/LaunchAgents,里的plist配置，启动服务守护进程。

下面再来理解几个基础概念：

__/System/Library和/Library和~/Library目录的区别？__

/System/Library目录是存放Apple自己开发的软件。
/Library目录是系统管理员存放的第三方软件。
~/Library/是用户自己存放的第三方软件。

__LaunchDaemons和LaunchAgents的区别？__

`LaunchDaemons`是用户未登陆前就启动的服务（守护进程）。
`LaunchAgents`是用户登陆后启动的服务（守护进程）。

以上所说的plist文件大致格式如下：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC -//Apple Computer//DTD PLIST 1.0//EN
          http://www.apple.com/DTDs/PropertyList-1.0.dtd >
          <plist version="1.0">
          <dict>
               <key>Label</key>
               <string>com.example.exampled</string>
               <key>ProgramArguments</key>
               <array>
                    <string>exampled</string>
               </array>
               <key>KeepAlive</key>
               <true/>
          </dict>
          </plist>

集体参数含义参照Apple官方文档[`launchd.plist`](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man5/launchd.plist.5.html)

我进入相应目录，根据最新更新日期，发现新增了几个plist文件，cat下，其中关注到了两个关键参数

`KeepAlive`: True 进程一直存在

`RunAtLoad`: True 进程开机就启动

`ProgramArguments`: /etc/run_app.sh

查看了`run_app.sh`也是个新增的脚本文件，并存在于`/etc`目录下面

看了代码大概意思就是10s后开启了某些应用，并篡改了`www.google.com`

到此破案了，我删除了相关多余plist文件和`run_app.sh`，重启电脑就正常了，当然把相应参数改为false也是可以的，由于我不需要`run_app.sh`这个脚本，所以索性彻底删除。
