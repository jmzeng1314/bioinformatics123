---
author: sam
comments: false
date: 2016-09-13 12:48:22+00:00
#layout: post
link: http://hugo.qinqianshan.com/convert-linuxs-picture/
slug: convert-linuxs-picture
title: Linux【11】-其他1-linux下的图片处理
wordpress_id: 1220
categories:
- basic_description
---

行政部门在帮我们提交申请材料的时候，难免需要上传照片，而政府部门对照片的大小往往有要求。我们可以用PS，截图等等策略来解决这个问题哈。
但作为一个有“格调”的码农，有么有什么快捷的方式能批量处理呢？今天，来了解一下Linux的一个神器convert

<!-- more -->

1.得到一个图片的尺寸
    
    identify IMG_0102.JPG
    
    IMG_0102.JPG JPEG 4074x2716 4074x2716+0+0 8-bit DirectClass 3.041MB 0.000u 0:00.000
            
2.分别得到宽和高

    identify IMG_0102.JPG | cut -d ' ' -f 3 | cut -d 'x' -f 1
    identify IMG_0102.JPG | cut -d ' ' -f 3 | cut -d 'x' -f 2
    
3.设置图片的长宽
    
    convert IMG_0102.JPG -resize 100x60 result_60.jpg
    identify result_60.jpg
    result_60.jpg JPEG 90x60 90x60+0+0 8-bit DirectClass 16.8KB 0.000u 0:00.009
    
    convert IMG_0102.JPG -resize 100x60! result_60.jpg
    identify result_60.jpg
    result_60.jpg JPEG 100x60 100x60+0+0 8-bit DirectClass 17.2KB 0.000u 0:00.000
    
注意！x为字母，不是乘法呀！！！
在尺寸后面加上"!"（感叹号）就可以了，表示强制转换！

4.按照百分比缩放
    
    convert IMG_0102.JPG -resize 50% result1.jpg
    identify result1.jpg
    result1.jpg JPEG 2037x1358 2037x1358+0+0 8-bit DirectClass 965KB 0.000u 0:00.000
    
5.拼图
    
横向拼接

    convert 1.jpg 2.jpg 3.jpg +append result.jpg

纵向拼接

    convert 1.jpg 2.jpg 3.jpg -append result.jpg

横向＋纵向

    convert /( 1.jpg 2.jpg 3.jpg +append /) /
    /( 4.jpg 5.jpg 6.jpg +append /) /
    /( 7.jpg 8.jpg 9.jpg +append /) /
    -append result.jpg

其他功能感觉用不上，暂不总结了

**参考资料：**

http://www.cnblogs.com/chenwenbiao/archive/2011/07/25/2116152.html

http://www.360doc.com/content/14/1119/14/11029609_426407658.shtml
