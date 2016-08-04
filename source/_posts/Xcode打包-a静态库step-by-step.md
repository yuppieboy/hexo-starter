---
title: Xcode打包.a静态库step by step
date: 2016-07-20 13:38:56
categories: iOS
tags: Xcode

---

## 库介绍 ##
库从本质上来说是一种可执行代码的二进制格式，可以被载入内存中执行。库分静态库和动态库两种。
iOS中的静态库有 .a 和 .framework两种形式；动态库有.dylib 和 .framework 形式，后来.dylib动态库又被苹果替换成.tbd的形式。

## 静态库与动态库的区别 ##

静态库和动态库是相对编译期和运行期的：静态库在程序编译时会被链接到目标代码中，程序运行时将不再需要改静态库；而动态库在程序编译时并不会被链接到目标代码中，只是在程序运行时才被载入，因为在程序运行期间还需要动态库的存在。
总结：同一个静态库在不同程序中使用时，每一个程序中都得导入一次，打包时也被打包进去，形成一个程序。而动态库在不同程序中，打包时并没有被打包进去，只在程序运行使用时，才链接载入（如系统的框架如UIKit、Foundation等），所以程序体积会小很多，但是苹果不让使用自己的动态库，否则审核就无法通过。

## 创建.a静态库 ##
第一步，新建工程。一般使用工程名就使用库的名称，比如我这里用任意一个NSString的Category来创建静态库，我的工程名就取名为Tool，创建的.a静态库就是libTool.a。

<div align="center">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/A6D78052-62A3-4691-A638-645809A9B868.png" width = "100%" >
</div>

第二步，删除系统默认创建的【Tool.h】和【Tool.m】文件，导入需要打包的源文件。

<div align="center">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/58E46F7D-A3DF-43D8-A270-7C794C88E13A.png" width = "50%" >
</div>

第三步，修改配置 左上角加号－>【New Headers Phase】-> 添加所有需要打包的头文件，如下图：

<div align="center">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/11DDC3B9-0740-4EAE-AB1F-4203AEB595E6.png" width = "100%" >
</div>

第四步，修改导出product配置

<div align="center">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/9EFD007C-5006-436A-ABD1-50EEF4759818.png" width = "100%" >
</div>

第五步，修改编译指令集

<div align="center">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/016705F3-C687-414C-80F6-E502E3F3F106.png" width = "100%" >
</div>

模拟器：iPhone4s~5 : i386 iPhone5s~6plus : x86_64
真机：iPhone3gs~4s : armv7 iPhone5~5c : armv7s iPhone5s~6plus : arm64
如果第五步这里，设置为YES，那么编译出来的.a静态库就只包含当前设备的指令集。
举个例子：如果我们选择iPhone 5模拟器【Command+B】编译，则编译出来的.a静态库只能用iPhone4s~5模拟器跑程序，用iPhone5s~6plus，则会报找不到x86_64的libTool库。
设置为NO，则会把所有指令集的都打包合并。

第六步，编译（快捷键【Command+B】
编译时，需要用模拟器和真机各编译一次，这样Products目录下的libTool.a静态库才会变为黑色，右键show in Finder，可以进入Products目录下。

<div align="center">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/0C979DBC-5501-421F-B349-0180449D2474.png" width = "100%" >
</div>

>注意：如果步骤四中，不将Build Configuration改为Release,则打包出来的静态库会存于【Debug-iphoneos】和【Debug-iphonesimulator】两个文件夹下。
我们一般都使用Release模式，因为程序最终发布之后是Release版的，所以静态库也是在Release模式下使用。

如果想要通用需要将模拟器使用的静态库与真机使用的静态库合并成一个静态库，可以使用终端命令来实现。命令格式：
lipo -create 第一个.a文件的绝对路径 第二个.a文件的绝对路径 -output 最终的.a文件路径。
本文中使用的命令如下：

    lipo -create /Users/paul/Library/Developer/Xcode/DerivedData/Tool-fcimdkyxyorzxoafjvdaenioayfh/Build/Products/Release-iphoneos/libTool.a /Users/paul/Library/Developer/Xcode/DerivedData/Tool-fcimdkyxyorzxoafjvdaenioayfh/Build/Products/Release-iphonesimulator/libTool.a -output /Users/paul/Desktop/libTool.a


>补充：经过多次实践，第三步的操作省略，依然可以导出可正常使用的包。
如果静态库中有category类，则在使用静态库的项目配置中【Other Linker Flags】需要添加参数【-ObjC】或者【-all_load】。

最后，将生成的.a文件以及相应include文件引入即可使用。
