---
title: FFmpeg的安装与使用
date: 2016-10-31 15:19:46
categories: Tool
tags: FFmpeg
---

## 简介
```FFmpeg```是一个自由软件，可以运行音频和视频多种格式的录影、转换、流功能，包含了```libavcodec```——这是一个用于多个项目中音频和视频的解码器库，以及```libavformat```——一个音频与视频格式转换库。
“FFmpeg”这个单词中的“FF”指的是“Fast Forward”。有些新手写信给“FFmpeg”的项目负责人，询问FF是不是代表“Fast Free”或者“Fast Fourier”等意思，“FFmpeg”的项目负责人回信说：“Just for the record, the original meaning of "FF" in FFmpeg is "Fast Forward"...”
这个项目最初是由Fabrice Bellard发起的，而现在是由Michael Niedermayer在进行维护。许多FFmpeg的开发者同时也是MPlayer项目的成员，FFmpeg在MPlayer项目中是被设计为服务器版本进行开发。
2011年3月13日，FFmpeg部分开发人士决定另组```Libav```，同时制定了一套关于项目继续发展和维护的规则。(From [FFmpeg Wiki](https://zh.wikipedia.org/wiki/FFmpeg))

## 安装

    brew install ffmpeg

由于license的原因，```libvo-aacenc```被默认安装，如果想要安装```libfaac```或者```libfdk-aac```可以通过以下命令：

    brew reinstall ffmpeg --with-faac

    brew reinstall ffmpeg --with-fdk-aac


<div align="center">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/0A976D62-F7E7-47BB-8AF4-CDE0B8BDADCD.png" width = "100%" >
</div>

## 项目组成

```libavformat```：用于各种音视频封装格式的生成和解析，包括获取解码所需信息以生成解码上下文结构和读取音视频帧等功能

```libavcodec```：用于各种类型声音/图像编解码

```libavutil```：包含一些公共的工具函数

```libswscale```：用于视频场景比例缩放、色彩映射转换

```libpostproc```：用于后期效果处理

```ffmpeg```：该项目提供的一个工具，可用于格式转换、解码或电视卡即时编码等

```ffsever```：一个 HTTP 多媒体即时广播串流服务器

```ffplay```：是一个简单的播放器，使用ffmpeg 库解析和解码，通过SDL显示


## 命令集

ffmpeg 命令集举例

1.获取视频的信息

    ffmpeg -i video.avi

2.将图片序列合成视频

    ffmpeg -f image2 -i image%d.jpg video.mpg

上面的命令会把当前目录下的图片（名字如：image1.jpg. image2.jpg. 等...）合并成video.mpg

3.将视频分解成图片序列

    ffmpeg -i video.mpg image%d.jpg

上面的命令会生成image1.jpg. image2.jpg. ...
支持的图片格式有：PGM. PPM. PAM. PGMYUV. JPEG. GIF. PNG. TIFF. SGI

4.为视频重新编码以适合在iPod/iPhone上播放

    ffmpeg -i source_video.avi input -acodec aac -ab 128kb -vcodec mpeg4 -b 1200kb -mbd 2 -flags +4mv+trell -aic 2 -cmp 2 -subcmp 2 -s 320x180 -title X final_video.mp4
说明：
* 源视频：source_video.avi
* 音频编码：aac
* 音频位率：128kb/s
* 视频编码：mpeg4
* 视频位率：1200kb/s
* 视频尺寸：320 X 180
* 生成的视频：final_video.mp4

5.为视频重新编码以适合在PSP上播放

    ffmpeg -i source_video.avi -b 300 -s 320x240 -vcodec xvid -ab 32 -ar 24000 -acodec aac final_video.mp4

说明：
* 源视频：source_video.avi
* 音频编码：aac
* 音频位率：32kb/s
* 视频编码：xvid
* 视频位率：1200kb/s
* 视频尺寸：320 X 180
* 生成的视频：final_video.mp4

6.从视频抽出声音.并存为Mp3

    ffmpeg -i source_video.avi -vn -ar 44100 -ac 2 -ab 192 -f mp3 sound.mp3

说明：
* 源视频：source_video.avi
* 音频位率：192kb/s
* 输出格式：mp3
* 生成的声音：sound.mp3

7.将wav文件转成Mp3

    ffmpeg -i son_origine.avi -vn -ar 44100 -ac 2 -ab 192 -f mp3 son_final.mp3

8.将.avi视频转成.mpg

    ffmpeg -i video_origine.avi video_finale.mpg

9.将.mpg转成.avi

    ffmpeg -i video_origine.mpg video_finale.avi

10.将.avi转成gif动画（未压缩）

    ffmpeg -i video_origine.avi gif_anime.gif

11.合成视频和音频

    ffmpeg -i son.wav -i video_origine.avi video_finale.mpg

12.将.avi转成.flv

    ffmpeg -i video_origine.avi -ab 56 -ar 44100 -b 200 -r 15 -s 320x240 -f flv video_finale.flv

13.将.avi转成dv

    ffmpeg -i video_origine.avi -s pal -r pal -aspect 4:3 -ar 48000 -ac 2 video_finale.dv

或者：

    ffmpeg -i video_origine.avi -target pal-dv video_finale.dv

14.将.avi压缩成divx

    ffmpeg -i video_origine.avi -s 320x240 -vcodec msmpeg4v2 video_finale.avi

15.将Ogg Theora压缩成Mpeg dvd

    ffmpeg -i film_sortie_cinelerra.ogm -s 720x576 -vcodec mpeg2video -acodec mp3 film_terminate.mpg

16.将.avi压缩成SVCD mpeg2
NTSC格式：

    ffmpeg -i video_origine.avi -target ntsc-svcd video_finale.mpg

PAL格式：

    ffmpeg -i video_origine.avi -target pal-svcd video_finale.mpg

17.将.avi压缩成VCD mpeg2
NTSC格式：

    ffmpeg -i video_origine.avi -target ntsc-vcd video_finale.mpg

PAL格式：

    ffmpeg -i video_origine.avi -target pal-vcd video_finale.mpg

18.多通道编码

    ffmpeg -i fichierentree -pass 2 -passlogfile ffmpeg2pass fichiersortie-2

19.从flv提取mp3

    ffmpeg -i source.flv -ab 128k dest.mp3
