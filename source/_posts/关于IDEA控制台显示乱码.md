---
title: 关于控制台的乱码
date: 2021-10-18 17:01:20
author: lsy
readmore: true
tags: 
  - JAVA
  - Bug
    
categories:
  -JAVA学习
---

记录IDEA产生乱码的原因以及解决方案。
<!-- more -->
在用IDEA运行项目的时候，日志永远都是乱码。以前因为没遇到什么bug，懒得处理这个问题，因为日志乱码也无伤大雅。这次，因为无法定位到bug，所以想着先把控制台输出的日志显示正常。

## 乱码产生的原因
首先我们要知道什么是乱码, 简而言之乱码就是文件打开的编码方式和文件本身编码方式不对, 注意这个地方有两个编码, 一个是文件本身的编码, 一个是用什么编码打开文件, 两个编码不对应, 就会出现乱码。如图。
![1](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/控制台乱码.png)

>这里是控制台打印tomcat日志报错。

UTF-8大家都知道，这是国际比较通用的编码格式。但是在国内，我们用的最多的是GBK编码，它是windows中国区的默认编码。
查阅资料可以知道，tomcat输出日志到控制台是utf—8格式，但是IDEA控制台显示的是GBK编码，文件本身的编码方式和打开文件的编码方式不对，所以产生了乱码。

## 解决思路
那解决思路就很明显了。两种，改变文件的编码方式或者改变idea控制台的编码方式。
这里我就只展示改变idea控制台的编码方式把。
>即把idea的编码也改成UTF-8。

在jvm 启动参数 VM options 加个配置 -Dfile.encoding=UTF-8。
![1](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/控制台乱码步骤0.png)
![1](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/控制台乱码步骤1.png)
![1](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/控制台乱码步骤2.png)


配置完之后果然问题解决了