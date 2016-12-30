---
title: 利用Python给图片打马赛克
date: 2016-12-02 16:40:48
tags: PIL
categories: Python
---

## 思路
利用PIL(Python Image Library)获取图片相应点位的像素值，然后以这些像素为基础重新绘制一张新图。

## 实现

    #!/Library/anaconda/bin/python
    # -*- coding: utf-8 -*-

    # 实现功能: 给图片打马赛克

    from PIL import Image, ImageDraw, ImageFont
    import sys

    width = 600
    height = 600
    granularity = 25  # 颗粒度

    image = Image.new('RGB', (width, height), (255, 255, 255))
    # 创建Font对象:
    font = ImageFont.truetype('Arial.ttf', 36)
    # 创建Draw对象:
    draw = ImageDraw.Draw(image)

    # image_name = raw_input('Entering Input ImageName:  ')
    image_name = sys.argv[1]
    image_file = Image.open(image_name)
    image_file = image_file.resize((width, height))  # 调整图片大小


    def to_mosaic(file_name):
        for x in range(0, width, granularity):
            for y in range(0, height, granularity):
                r, g, b = file_name.getpixel((x, y))
                draw.rectangle([(x, y), (x + granularity, y + granularity)], fill=(r, g, b), outline=None)  # None即是不加网格


    to_mosaic(image_file)
    image.save('Mosaic.jpg', 'jpeg')


## 效果图
<div align="left">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/Mosaic.jpg" width = "50%" >
</div>
