---
title: breaking double links
date: 2025-04-03
---

# 问题

在实现 inet 时，最朴素的实现是用 node 和 wire 之间的，
以及 wire 和 wire 之间的 double links，
来保证从任意一个 node 或 wire 都可以遍历整个联通分支。

但是在尝试实现并行的 inet 时，double links 会导致 data race。

# 方案

读 2014-design-and-implementation-of-a-low-level-language-for-interaction-nets，
其中 "3.4.2 Directed graph encoding" 提到，
auxiliary port 和 auxiliary port 之间的连接，
可以通过生成一个 name，然后将这同一个 name 保存到两个 port 中。
这避免了 double link，因此指针所形成的有向无圈图（DAG）。

这样就可以在并行时避免 data race。

这种技巧可以称作是 "break double links"，
或者是 "break mutual reference"：

```
(A) <-> (B)
----------- break with an extra element (C)
(A) -> (C)
(B) -> (C)
```

# 类似的方案

（1）在 minikanren 中实现带有 logic variable 的
value 之间的 unification，用的也是类似的方案。
在 inet 中，也可以把 wire 所做的连接理解为 unification。

（2）同一个论文的 "4 Single link encoding method"，
再一次用了这个方案来 break double links between two unified names。
