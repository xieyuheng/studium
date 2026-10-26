---
title: class vs. interface
---

在看 "Building Problem Solvers" 这本书时，
发现像是 `ClassicalSolver` 一类的 `Solver`，
因为它带有当前 solving 的状态，
所以好像非常适合用 class 来实现。

但是其实我选择用更朴素的
interface + function 的方式来实现，
原因在于如下利弊分析：

class 好于 interface 的地方：

- 有 dot 这种更简洁的语法
  -- `a.f(x)` vs. `f(a, x)`
  真的简洁吗？
  至少可以少重复声明一些类型参数。

interface 好于 class 的地方：

- 方便增加新的处理函数，
  用户所增加的处理函数，
  是和作者所提供的处理函数一个级别的。

- 方便区分哪些数据是核心的，
  比如可以把核心数据作为 JSON 保存在文件中，
  （尽管 interface 中的函数没法简单地保存成 JSON）。

- 当某些处理函数不依赖于 inference 中的所有属性时，
  可以只依赖部分属性。
