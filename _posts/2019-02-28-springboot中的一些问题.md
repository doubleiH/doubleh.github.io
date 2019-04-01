---
layout: post
title:  spring boot
date:   2019-02-28 08:00:00 +0800
categories: spring boot
tag:  
excerpt: spring在使用过程中出现的一些坑
---


* content
{:toc}


---------------------------------

1、Invalid bound statement (not found)

这个原因主要是由于包没有扫描到。网上有好多种办法，我的原因主要是在启动类中添加的MapperScan值使用`com.example.*`，导致后面的service识别不了一直报错。
解决办法，将`com.example.*``改为`com.example.dao`即可解决
