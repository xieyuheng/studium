---
title: type vs predicate
date: 2025-05-14
---

有了 predicate 为什么还要有 type？
主要是没法用 predicate 检查函数类型。

检查函数类型 `(check (lambda (x) ...) (-> A B))` 的时候，
首先要用参数类型扩展 context `x: A`，
然后再检查函数体，这个过程中遇到变量 `x` 时，
常常需要用判断类型之间的子类型关系。
但是 predicate 之间没法判断子类型关系，
这等价于 halting problem。

所以 type 可以被认为是一种间接地定义 predicate 的方式，
用 `(check x p-t)` 实现 `(p? x)`，
同时类型之间要能判断子类型关系。

问题： simple type 中的 generic type argument，
与 dependent type 中的 implicit type argument 有什么不同？
