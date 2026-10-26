---
title: dense array sparse index
date: 2025-04-04
---

# 问题

TODO

# 方案

就是多引入一层 indirection，
在 value array 之外维护一个 index array。

dense array 因此 iteration 很快。

通过 index 查找多了一层 indirection，
需要先从 index array 找到 real index。

# 例子

在可以用来实现 ECS (Entity Component System)。
