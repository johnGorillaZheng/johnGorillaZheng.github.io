---
title: 数据挖掘小知识 - 关联规则学习 Associate mining
date: 2020-04-17 22:56:05
tags:
---

## 动机

频繁模式挖掘（Frequent pattern mining）在交易或关系型数据中进行数据挖掘。试图在数据集中挖掘频繁出现的模式，例如购物中顾客更有可能买的物品集合。实际中的例子：沃尔玛发现买尿不湿的客人更有可能买啤酒。虽然不理解为什么，但客观存在。^_^

## 基本概念

- 初步定义
  - itemset: 物品集合， $I=\\{I_1, I_2, ..., Im\\}$
  - k-itemset: $k=|I|$, 势为k的itemset
  - T -> **transaction**，为非空itemset, $T\in D$, D是一个任务相关的transaction（task-relevant）
  - 每一个transaction有一个TID

- 滋瓷（Support）
  - **support count** of itemset A in dataset $D = |\\{T | A \subseteq T \land T \subseteq D \\}|$。D中包含A的transacion的数量
  - support = $\frac{|support\ count|}{|D|}$
  - 当$support(A) \geq min\\_sup$，称A是频繁的，其中$min\\_sup$是人为定义的最小支持度(minimum support threshold)

- 关联规则 (Associate rule)
  - 定义关联规则形如$A \implies B$，其中$A \subset I, B \subset I, A \neq \emptyset, A \neq \emptyset$ and $A \cap B = \emptyset$
  - support: 在D中成立的规则$A \implies B$的$support=support(A \cup B)$
  - 可信度：在D中成立的规则$A \implies B$的$confidence = \frac{support(A \cup B)}{support(A)}$
  - 当$A\cup B$是频繁的，我们称$A \implies B$为强关联
  - lift of $A \implies B = \frac{support(A \cup B)}{support(A) * support(B)}$
    - $lift = 1$， A， B相互独立
    - $lift \geq 1$，A，B正相关 (positively correlated)
    - $lift \leq 1$，A，B负相关（negatively correlataed）

## Apriori算法

