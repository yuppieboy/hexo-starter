---
title: ios10相册等隐私权限设置
date: 2016-12-20 11:28:48
categories: iOS
---
__崩溃日志示例：__

This app has crashed because it attempted to access privacy-sensitive data without a usage description.  The app's Info.plist must contain an NSCameraUsageDescription key with a string value explaining to the user how the app uses this data.      


升到iOS10之后，需要在info.plist设置权限的有：

__麦克风权限__：Privacy - Microphone Usage Description 是否允许此App使用你的麦克风？

__相机权限__： Privacy - Camera Usage Description 是否允许此App使用你的相机？

__相册权限__： Privacy - Photo Library Usage Description 是否允许此App访问你的媒体资料库？

__通讯录权限__： Privacy - Contacts Usage Description 是否允许此App访问你的通讯录？

__蓝牙权限__：Privacy - Bluetooth Peripheral Usage Description 是否许允此App使用蓝牙？

__语音转文字权限__：Privacy - Speech Recognition Usage Description 是否允许此App使用语音识别？

__日历权限__：Privacy - Calendars Usage Description

__定位权限__：Privacy - Location When In Use Usage Description

__定位权限__: Privacy - Location Always Usage Description

__位置权限__：Privacy - Location Usage Description

__媒体库权限__：Privacy - Media Library Usage Description

__健康分享权限__：Privacy - Health Share Usage Description

__健康更新权限__：Privacy - Health Update Usage Description

__运动使用权限__：Privacy - Motion Usage Description

__音乐权限__：Privacy - Music Usage Description

__提醒使用权限__：Privacy - Reminders Usage Description

__Siri使用权限__：Privacy - Siri Usage Description

__电视供应商使用权限__：Privacy - TV Provider Usage Description

__视频用户账号使用权限__：Privacy - Video Subscriber Account Usage Description
