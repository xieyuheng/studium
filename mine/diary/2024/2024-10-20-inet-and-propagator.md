---
title: inet and propagator
date: 2024-10-20
---

inet 应该可以用 propagator 来实现。

inet 与 propagator 的差异是，
每个 node 都有固定个数的 port。
但是 propagator network 中，
每个 cell 可以连接任意多各 propagator。

反过来 propagator 所构成的网络结构是固定的，
而 inet 中网络结构是不断变化的，
正是网络结构的变化编码了计算。

# c and actor

我们需要用 C 写的，
可能不是 xvm 或 inet，
而是 actor model。

# inet and IO

inet 的 IO 可能可以用
ECS（entity component system）式的数据库来实现。
