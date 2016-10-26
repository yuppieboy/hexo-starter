---
title: python打包可执行文件
date: 2016-10-20 14:02:39
categories: Python
tags: Archive
---

[PyInstaller](https://github.com/pyinstaller/pyinstaller/) 是一个用来将 Python 程序打包成一个独立可执行软件包，支持 Windows、Linux 和 Mac OS X。

## Installation ##
PyInstaller is available on PyPI. You can install it through pip:

    pip install pyinstaller


Tips: 必须翻墙，不然会因为timeout报错,可以加一些参数:

    sudo -H pip --default-timeout=100 install pyinstaller


## usage ##
安装完pyinstaller之后就可以开始使用了。


使用pyinstaller生成可执行文件的方法
- 将依赖文件集中到一个文件夹：


    pyinstaller -D -w main.py   



- 将所有依赖文件都打包到同一个可执行文件中：


    pyinstaller -F -w  main.py  



把main.py替换成你的主入口python文件即可。
```-w```参数代表main.py是一个窗体程序。
