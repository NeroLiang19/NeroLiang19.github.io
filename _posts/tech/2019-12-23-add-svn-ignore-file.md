---
layout: post
title: 给SVN控制的项目添加忽略文件/文件夹
category: 技术
tags: SVN
description: 
---

忽略目录其实有些像建立一个文件夹，但却不放入版本控制。如果不加入版本控制又会在`svn status`命令中显示出来，很不方便，所以可以设置本文件夹属性，让它既加入版本控制，又忽略其变化

### 未加入控制的文件夹

    svn propset svn:ignore 'test' .
    svn update
    svn commit -m "add a ignore dir"

### 已经加入版本控制的文件夹

    svn export test test_bak
    svn rm test
    svn commit -m "delete test"
    mv test_bak test
    svn propset svn:ignore 'test' .
    svn update
    svn commit -m "add a ignore dir"

如果想要忽略一个目录下多个文件夹的话，需要有一点点技巧，如下

    svn propset svn:ignore 'test
        test1
        test2' .

即每一个文件夹要单独另起一行