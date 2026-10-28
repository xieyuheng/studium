---
title: 用 propagator 来实现 elaboration
date: 2024-09-06
---

也许 propagator 可以用来解决类型检查器中的 elaboration 问题。

elaboration 是在 check 和 infer 的过程中，
利用 type 中的信息来补充当前的 exp，
这个过程本身就是 merge more information to current exp。

之前的实现中之所以需要用两个 pass 来处理 elaboration，
是因为没有 reactive programming 的功能。
在 propagator 下完全可以避免两个 pass。
