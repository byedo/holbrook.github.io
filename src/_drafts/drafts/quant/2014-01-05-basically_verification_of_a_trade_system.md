---
layout: post
title: "交易策略的基本检验"
description: "交易策略在其生命周期中要经历过多次检验。这些检验通常需要经过统计分析。
最基本的检验是不考虑交易成本、指令类型、突发事件、回波效应等外部因素的影响，只考虑操作信号自身的统计分析指标。
"
category: 量化金融
tags: [交易系统]
---

[交易策略在其生命周期中要经历过多次检验](/2013/12/16/trade_system.html#menuIndex6)。这些检验通常需要经过统计分析。

最基本的检验是不考虑交易成本、指令类型、突发事件、涨跌停和跳空、回波效应等外部因素的影响，只考虑操作信号自身的统计分析指标。


# 前提和假设

1. 信号发生时机

   通常，交易系统无法实时获取分笔数据，而是获取某一个时间段的[成交数据](/2013/12/18/quotation_model.html#menuIndex1)。每个时间段（比如5分钟，1天）作为一个成交周期。
   由于交易策略都是对成交数据进行事后分析，所以信号通常会滞后一个交易周期产生。

2. 成交价格

   基本检验暂时不考虑”无法成交“（比如涨跌停）的情况，但是为了检验的严格性，需要采取”最不利“的价格作为成交价格。即如果是买入信号，取交易周期内的最高价作为成交价格；如果是卖出信号取交易周期内的最低价作为成交价格。

3. 样本量

   为了减小随机误差的影响，每个样本至少要包含30对买入/卖出的交易，即至少要涵盖30个交易周期。

# 机会的度量

交易策略最根本的指标就是获利的机会。统计学中，用[概率和分布](/2013/06/07/statistics_intro_4.html)来度量机会。相应的，对交易策略的获利机会的度量需要确定以下几个指标：

<table style="border-collapse: collapse;" border="2" frame="hsides" rules="groups" cellspacing="0" cellpadding="6">
<thead>
<tr><th></th><th>总量</th><th>比率</th><th>分布</th></tr>
</thead>
<tbody>
<tr>
<td>金额类</td>
<td>净利，最大资本金损失程度</td>
<td>平均盈利额/平均亏损额</td>
<td>最大盈利/亏损额</td>
</tr>
<tr>
<td>次数类</td>
<td>交易周期</td>
<td>盈利（次数）比率</td>
<td>盈利次数分布，最大连续盈利/亏损次数</td>
</tr>
</tbody>
</table>

- 净利

  净利 = 获利额 - 亏损额 - 交易成本（在基本分析中交易成本=0）

  净利考察一个交易策略是否为盈利的策略。净利为负的策略一定不可用。但净利的大小不是最重要的指标。

- 平均盈利额/平均亏损额

  平均盈利额应大于平均亏损额，否则说明该策略不可用。

- 盈利（次数）比率

  盈利比率=盈利次数/交易周期数

  盈利比率也叫”信号成功率“，反映了策略所对应的投资理念：是依赖于偶然的巨额获利，还是依赖于多次小额获利。
  
- 盈利次数分布
  
  可以考察二项式分布、泊松(Poisson)分布或正态分布。

- 盈利标准差

  标准差越小，说明策略的稳定性越高，越不依赖于偶然的巨额获利。

- 最大盈利/亏损额

  + 如果最大盈利与平均盈利差距过大，则应视为偶然现象，去除该盈利后重新分析。
  + 如果最大亏损与平均亏损差距过大，则要慎重制定风险控制策略，避免突发事件风险（分析时 **不** 去除！）
  + 如果最大盈利/亏损所占比重过大，则应怀疑策略的盈利能力和稳定性

- 最大连续盈利/亏损次数

  连续盈利/亏损会经常出现，这是因为策略是稳定的，但是市场会发生周期性的变化。
  最大连续盈利/亏损次数可以与市场的周期性进行比对。

  结合 **当前** 盈利/亏损次数 和 **最大** 盈利/亏损次数，可以决策某次交易的额度。

- 最大资本金损失程度

  是指资本波峰、波谷间的差额。决定了需要准备的资本金。

- 交易周期

  交易周期决定了交易的频繁程度。交易越频繁则交易成本越高。




# 样本的质量

   对一个交易策略用不同的时间、不同的交易对象反复多次验证，并根据抽样分布（即各样本的统计量，如均值、中位数、标准差等的分布）的特征，剔除一些不合格的样本，重新对交易策略进行综合评价。

# 波长稳定性
  
  理论上来说，在各波长下（日线，5分钟线等），信号成功率应该保持稳定。

