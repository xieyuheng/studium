---
title: concrete pointer graph
date: 2025-04-03
---

# 问题

在实现复杂的拓扑数据结构时
-- 比如 interaction nets 和 cell complex，
人们通常用高层的概念来画图
-- 比如 node、edge、face 等等。

但是这些高层的概念不能包含所有信息，
对程序具体行为的理解不充分会导致 bug。

# 方案

用具体的指针来画图就能观察到所有的信息，
我称之为 concrete pointer graph。

因为 pointer 代表着最底层的最具体的依赖关系，
因此所画的图也是 concrete dependancy graph。

# 例子

比如开始实现 inet 的时候，
我总是画 node 和 port，
或者 node 和 wire 之间的连接图。
但是在尝试并行实现的时候，
发现因此忽视了很多 data race 的细节。

而 Shinya Sato 在 2014 年的论文中，
经常用 concrete pointer graph，
因此能把很多细节研究清楚。

cell complex 也是类似。

# drawing

```
+---+---+---+
|   |   |   |
+---+---+---+
```
