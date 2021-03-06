---
layout: post
title: "QQ 餐厅与系统性能模型(续)：如何评价系统的性能"
description: "作为QQ餐厅的客人，对餐厅效率的评价就是供餐“快”或者“慢”。但是对于餐厅的经营者，这样简单的考虑问题显然是不够的。
在《QQ餐厅与系统性能模型》 中提到了系统性能的很多指标，而客人感觉“快”或者“慢”仅仅对应其中的 响应时间 这一指标。本文讨论如何全面评价一个系统的性能"
category: 方法工具
tags: [测试]
lastmod: 2013-07-03
---



作为QQ餐厅的客人，对餐厅效率的评价就是供餐“快”或者“慢”。但是对于餐厅的经营者，这样简单的考虑问题显然是不够的。

在[《QQ餐厅与系统性能模型》](/2012/10/23/whats_performance.html) 中提到了系统性能的很多指标，而客人感觉“快”或者“慢”仅仅对应其中的 *响应时间* 这一指标。

对于QQ餐厅的经营者，应该如何考虑呢？



在《QQ餐厅与系统性能模型》 中已经描述了客人数量增加时餐厅中发生的一系列“故事”，让我们回顾一下：

- 资源利用率与负载

  资源利用率随负载的增加而增加，最终达到100%。此时再增加负载，资源利用率也不会增加。当然，这是对单一资源来说，现实中总是有某种资源最先达到100%而其他的资源仍有空闲。

  资源利用率达到100%时对应的并发用户数称为 **最佳并发用户数** 。

- 响应时间与负载

  当并发用户数小于前面定义的“最大并发用户数”时，系统的响应时间是一个固定的值。如果继续增加系统的负载，用户等待时间就会增加从而延长响应时 间。假设用户有一个能容忍的最长等待时间，则负载增加到一定程度时系统对某些请求的响应时间就会超过用户的容忍度。在临界状态的负载称为 最大并发用户数 。

  在最大并发用户数的情况下，系统的吞吐量应该达到最大值。超过最大并发用户数的负载会导致资源浪费在超时的请求上，从而使吞吐量下降。

（在系统负载略高于最大并发用户数时，可能吞吐量会有一定程度的增加，但此时的吞吐量已经没有意义。所以应该考量最大并发用户数下系统的吞吐量）


# 评价系统的性能

从系统建设者的角度来说，系统性能不仅仅是”快“或者”慢“，而是应该有一个全面的描述。比如对于”QQ餐厅系统“，至少要有如下的描述才能评价其性能：

- QQ餐厅的可用资源包括：2位厨师，6名服务员，30套座位
- QQ餐厅最理想情况是同时接待25位客人，此时2名厨师工作量饱和，服务员空闲1人，座位空闲5套
- 在25人就餐的情况下，没小时可以卖出8道菜，客人评价就餐时间为10分钟，99%的客人可以在30分钟完成内用餐
- 假设 客人能容忍的最长用餐时间为60分钟，则QQ餐厅最多能容纳35人就餐
- 在35人就餐的情况下，每小时可以卖出10道菜，客人平均就餐时间为20分钟，99%的客人可以在50分钟内完成用餐



# 提高系统的性能

有了前面比较系统的性能描述，就很容易知道如何提高系统的性能。提高系统性能的目标有两个级别：

- 在现有的响应时间基础上，提高最佳并发用户数和最大并发用户数。

  这两个指标的现实意义在于：最佳并发用户数要不小于系统的平均负载，最大并发用户数要不小于系统的峰值负载（通常应该达到峰值的1.5–2倍）。

  实现这个级别的目标通常比较容易，主要是增加系统的可用资源。必要时还可以横向扩展（增加服务器数量）。比如对于前面的QQ餐厅性能情况，增加厨师数量就能够有效提高最佳并发数和最大并发数。

  当然，如果能够说服用户增加所能容忍的等待时间，也可以提高最大并发数。

- 增加系统整体的响应速度

  用户感受到的整体响应时间=请求传输时间+等待时间+处理时间+响应返回时间。针对这些不同的时间片段可以分别想办法提高速度。比如：

  提高网络带宽可以减小请求传输时间和响应返回时间； 增加处理单元可以减小等待时间； 优化算法可以减小处理时间。

  上面的几种办法实现起来难度都比较大，所以提高系统性能应该优先考虑第一个级别的办法。


# 最精简的性能测试的例子

使用Apache ab进行性能测试，访问NginX上面部署的静态页面。在不同并发下测试100万次请求，结果如下：

> 并发数 失败请求数 每秒完成请求  平均响应时间ms  99%请求响应时间ms 吞吐量(kb/s)

> 100 0 13005.97  7.689 10  24132.19

> 500 0 24132.19  36.193  232 25632.78

> 600 0 13948.85  43.014  296 25881.7

> 700 0 14317.11  48.893  232 25632.78

> 800 288 14264.21  56.084  2570  26461.06

> 1000  390 14752.1 67.787  3013  27363.46

在各轮测试中，Nginx均没有发生故障切换。

从测试数据中可以得出以下结论：

- 当前的性能瓶颈在于网络带宽
- 当前系统的最佳并发数为500，最大并发为700
- 在最佳并发下，系统每秒可以处理2.4万个请求，平均响应时间36ms，99%的请求在232ms内完成
- 在最大并发下，系统每秒可以处理1.4万个请求，平均响应时间48ms，99%的请求在232ms内完成


# 负载均衡器的性能指标

对于负载均衡器，需要的性能指标与具体的应用服务器不太一样。更关注如下指标：

- 每秒的会话处理数，要区分4层和7层
- 最多保持的连接数
- 吞吐量，要区分4层和7层
- 支持的虚拟服务器的个数
- 支持的后端真实服务器的个数

