---
title: python图片转字符串画
date: 2016-11-11 12:57:52
categories : Python
tags: Python
---

## 原理
把图片各像素根据灰度值转成不同的自定义字符串然后显示出来。

## 依赖库
使用第三方库`pillow`:

    sudo -H pip --default-timeout=100 install pillow

## 实现

    #!/usr/bin/python
    # -*- coding: utf-8 -*-
    from PIL import Image

    ascii_char = list("##$&      ")
    count = len(ascii_char)

    def toText(image_file):
    string = ''
    for h in range(0, image_file.size[1]):  # h
        for w in range(0, image_file.size[0]):  # w
            r, g, b = image_file.getpixel((w, h))
            gray = int(0.2126 * r + 0.7152 * g + 0.0722 * b)
            unit = 255.0 / len(ascii_char)
            string = string + ascii_char[int(gray / unit)]
        string = string + '\n'
    return string


    image_file = Image.open("lks.jpg")
    image_file = image_file.resize((int(image_file.size[0]*0.2), int(image_file.size[1] * 0.1)))  # 调整图片大小

    print u'Info:', image_file.size[0], ' ', image_file.size[1], ' ', count

    with open('lks.txt','w') as f:
    f.write(toText(image_file))


## 效果图

<div align="left">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/lks.jpg" width = "50%" >
</div>

<div align="left">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/2D11EE12-6E56-4C21-87DD-B49631AF6A71.png" width = "100%" >
</div>
