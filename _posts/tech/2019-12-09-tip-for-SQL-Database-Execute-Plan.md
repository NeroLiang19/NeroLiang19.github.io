---
layout: post
title: Tip for SQL Database Execute Plan
category: 技术
tags: Oracle
keywords: Oracle,performance
description:
---

>Indexes（索引）

### 如果您是数据库的新手，甚至问自己“什么是SQL调优”，您应该知道索引是调整SQL数据库的有效方式，这在开发过程中经常被忽略。 在基本术语中，索引是一种数据结构，通过提供快速随机查询和高效访问有序记录来提高数据库表中数据检索操作的速度。 这意味着，一旦创建了索引，您可以比以前更快地查询。索引也用于定义主键或唯一索引，这将保证没有其他列具有相同的值。 当然，索引是一个广泛的有趣的话题，我无法通过这个简短的描述来说明。

### 基本上，目标是索引主要的搜索和排序列。

### 请注意，如果您的表不断被INSERT，UPDATE和DELETE，您应该在索引时小心 - 因为所有索引需要在这些操作之后进行修改，所以可能会降低性能。

### 此外，在执行百万次以上的批次批处理插入之前，DBA经常会丢弃它们的索引，以加速插入过程。 插入批次后，它们将重新创建索引。 但是请记住，丢弃索引将影响该表中运行的每个查询; 所以这种方法仅在使用单个大型插入时才推荐使用。

[索引详细介绍](https://baike.baidu.com/item/%E7%B4%A2%E5%BC%95/5716853?fr=aladdin)

>Oracle Performance Tuning: Execution Plans

# 执行一条SQL,按F5即可查看。

基数（Rows）：Oracle估计的当前操作的返回结果集行数

字节（Bytes）：执行该步骤后返回的字节数

耗费（COST）、CPU耗费：Oracle估计的该步骤的执行成本，用于说明SQL执行的代价，理论上越小越好（该值可能与实际有出入）

时间（Time）：Oracle估计的当前操作所需的时间

执行一条SQL,按F5即可查看。

基数（Rows）：Oracle估计的当前操作的返回结果集行数

字节（Bytes）：执行该步骤后返回的字节数

耗费（COST）、CPU耗费：Oracle估计的该步骤的执行成本，用于说明SQL执行的代价，理论上越小越好（该值可能与实际有出入）

时间（Time）：Oracle估计的当前操作所需的时间

![Image text](https://raw.githubusercontent.com/NeroLiang19/NeroLiang19.github.io/master/_src/Tech/Oracle-Tip-for-performance/Oracle-Tip-for-performance.png)

# 执行顺序：

根据Operation缩进来判断，缩进最多的最先执行；（缩进相同时，最上面的最先执行）

例：上图中 INDEX RANGE SCAN 和 INDEX UNIQUE SCAN 两个动作缩进最多，最上面的 INDEX RANGE SCAN 先执行；

同一级如果某个动作没有子ID就最先执行

同一级的动作执行时遵循最上最右先执行的原则

# 表访问的几种方式：（非全部）
TABLE ACCESS FULL（全表扫描）
TABLE ACCESS BY ROWID（通过ROWID的表存取）
TABLE ACCESS BY INDEX SCAN（索引扫描）
