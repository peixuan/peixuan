---
title: 使用CMake搭建OpenCV开发环境
date: 2016-01-02 20:20:15
lastmod: 2017-05-01 11:22:10
tags:
- 圖像處理
- OpenCV
categories:
- 技術縱橫
- 學習手記
- 環境構建
---

> <p align="left">本篇博文最初发布于2016年元旦，当时项目组有十多个人，工作在不同的操作系统、不同的开发环境下，写这篇博文，其目的只是希望同事们能够通过它快速搭建好OpenCV的开发环境，并不指望此文能快速传播出去——不过此文后来为我节省了好多时间，因为我再也不用教别人怎么设置OpenCV了。从最初发布本文至今，OpenCV已经有了很多更新，我决定也持续更新本文，希望它能帮助更多的人。</p>
> <p align="left" style="color:red">最近一次更新：2017年4月</p>

OpenCV的全称是Open Source Computer Vision，是非常著名的跨平台计算机视觉库，由于它是基于BSD协议可商用的，因此在计算机视觉领域OpenCV是非常流行的。

手头一个刚刚开始的项目需要使用OpenCV，因此首先要搭建一个OpenCV开发环境。为了找到一个最简洁的搭建方案，我在互联网上仔仔细细检索了一番——然而很失望，我并没有找到一个高效的方法。
互联网上描述的方法，基本上都比较接近：Windows下将include目录和lib库手动添加到Visual Studio工程中，Linux下就给gcc加一个路径参数……总之，这些方法还基本上属于手工配置的范畴，对于Windows，如果再新建一个工程，便要重新配置，而对于Linux，如果你makefile写得熟练还好，否则的话，这种方法就过于繁琐了。
而且，如果这样手工配置的话，Windows、Linux、OSX、甚至不同版本的Visual Studio都需要反复做相应的配置，非常麻烦。那么，到底有没有一个好的方法呢？

OpenCV使用CMake作为编译工具，支持跨平台。CMake是个好东西，我们这里也利用CMake来搭建OpenCV的开发环境，这样不管是在Windows、Linux还是OSX下，都可以使用同一套配置了。

# *废话少说，直接上步骤。*

## 首先是一些准备工作。

### 在`Windows`下：
1. 从`OpenCV`官网下载对应的`Windows`版本`OpenCV`，我这里用的是`OpenCV-3.0.0.exe`。
2. 这是一个`7-Zip`自解压程序，解压到某一位置，比如我解压到了：`D:\Software`,
解压程序会在路径上自己加上`opencv`目录，即：`D:\Software\OpenCV`。
（我自己改的大小写，强迫症，请理解）
3. 在系统属性->高级系统设置中，设置环境变量：
变量：`OPENCV_DIR`
数值：`D:\Software\OpenCV\build`
4. 设置另一个环境变量：
变量：`OpenCV_BIN_DIR`
数值：`%OPENCV_DIR%\x64\vc12\bin`
注意一下，这里的值要根据你的需要来设置，比如你要编译32位的程序，这里就不要用x64而要用x86；而如果你是用的不是VS2013而是VS2012，那么这里就要用vc11，当然VS2015就是vc14。
5. 在系统变量Path中添加一项：`%OpenCV_BIN_DIR%`
6. 下载一个`CMake`并安装。
这样`Windows`下的准备工作就完成了。

### 在`Linux`下：
1. 对于`Ubuntu 14.04.3 x64`，请安装下面的组件：

``` bash
sudo apt-get install -y tortoisehg
sudo apt-get install -y libsdl1.2-dev libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev gfx1.2-dev
sudo apt-get install -y build-essential subversion git-core checkinstall yasm texi2html
sudo apt-get install -y libfaac-dev libfaad-dev libmp3lame-dev libsdl1.2-dev libtheora-dev libx11-dev
sudo apt-get install -y libxvidcore-dev zlib1g-dev
sudo apt-get install -y libx264-dev
sudo apt-get install -y cmake-curses-gui
sudo apt-get install -y libjasper-dev libgtk-3-dev libdc1394-22-dev libgphoto2-dev
```
2. 从`OpenCV`官网下载对应的`Linux`版本`OpenCV`，我这里用的是`OpenCV-3.0.0.zip`。
3. 解压：

``` bash
unzip OpenCV-3.0.0.zip
```
4. 进入目录：

``` bash
cd opencv
```
5. 构建`makefile`：

``` bash
cmake .
```
注意上面的命令，`cmake`之后一个空格，加个点，`cmake`会下载一个`intel ipp`库，然后生成相应的`makefile`。
6. 编译和安装`OpenCV`

``` bash
make
sudo make install
```
这样`Linux`的准备工作也完成了。

## 剩下的工作就简单了，
1. 在你的代码目录，新建立一个`CMakeLists.txt`文件，内容如下：

``` cmake
# 文件开始
cmake_minimum_required(VERSION 2.8)
project(YourProjectName)

if(MSVC)
  option(OpenCV_STATIC "Use static OpenCV libraries" OFF)
endif()

find_package(OpenCV REQUIRED)

include_directories(. ${OpenCV_INCLUDE_DIRS})

add_executable(YourProgramName
               main.cpp
               main.h
               prog.cpp
               prog.h)
target_link_libraries(YourProgramName ${OpenCV_LIBS})
# 文件结束
```
2. 根据你的项目情况，更改`YourProjectName`、`YourProgramName`以及添加对应的cpp和h文件，这个文件就写好了。

3. 如何使用呢？
  + `Linux`下，还是`cmake .`生成`makefile`，执行`make`来编译。
  + `Windows`下，我建议你再写一个批处理`make-solutions.bat`，里面有这一行就行了：

``` bat
cmake -G "Visual Studio 12 Win64" ..\..\source && cmake-gui .
```
把这个文件和`CMakeLists.txt`放在同一目录下，根据你的需要进行修改，如果是编译32位程序，就去掉`Win64`，另外`Visual Studio 12`对应`VS2013`，11对应2012，14对应2015，10对应2010，以此类推。
双击就可以生成VS工程了，打开`sln`文件，其它应该就不需要我教了。

以上就是整个过程了，如果有任何疑问请留言，我会一点点完善补充这篇教程。
