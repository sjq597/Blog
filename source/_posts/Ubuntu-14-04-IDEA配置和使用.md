title: Ubuntu 14.04 IDEA配置和使用
date: 2015-10-25 12:17:44
tags: [IDEA,开发工具]
categories: 开发环境
---
IDEA相比Eclipse的好处我这里就不多说了，反正感觉无论是界面还是与其他插件的集成都做的非常好，特别是git和数据库这块，做的时想当好。

### 安装
首先根据系统下载对应IDEA的压缩包，以Ubuntu 14.04的64位系统为例
```bash
sudo tar -xvf ideaIU-14.0.3.tar.gz -C /usr/dev
cd /usr/dev/idea-IU-139.1117.1/bin
./idea.sh
```
**注意：**正常情况下，在你启动成功之后，可以在Ubuntu应用里直接启动，但是我的总是不行，没办法，在用户根目录下建了一个脚本，内容如下：
```bash
#!/bin/sh
/usr/dev/idea-IU-139.1117.1/bin/idea.sh &
```
然后对脚本赋可执行权限

### 常用配置
必装插件:
```
Eclipse Code Formatter
Lombok plugin
Vim Emulation
```
激活服务器搭建:http://blog.lanyus.com/archives/174.html

### 无法切换中文输入法
修改启动脚本`/usr/dev/idea-IU-139.1117.1/bin/idea.sh`,头部加入下面几行:
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```
前提是你用的是`fcitx`架构的输入法.

### 序列化ID警告
I am not sure if you have an old version of IntelliJ but If I go File => Settings... => Inspections => Serialization issues =>  Serializable class without 'serialVersionUID' enabled, the class you provide give me warnings

### 文件头模板d定制
文件上右击--New--Edit File Templates--Includes--File Header
