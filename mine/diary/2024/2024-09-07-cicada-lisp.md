---
title: cicada-lisp
date: 2024-09-07
---

# cicada-lisp 的动机

想要用一个新的语言来实现 propagator，
就需要先实现 actor model，
然后把 propagator 和 cell 都实现为 actor。
这是最明智的选择。

这种项目要同时探索很多新东西，比如：

- actor model
- typed actor model
- generic
- propagator
- typed propagator

因此可以把这种项目分类为探索类的语言项目，
这种项目适合用 lisp 来做，
因此我想实现一个 cicada-lisp。

想要实现带有朴素 OOP 和静态类型的 scheme-like lisp，
既然已经要实现静态类型了，最简单的就是直接实现 dependent type。

关于，静态类型的 scheme，
最重要的是一个关于 type 的合理 naming convenient，
首字母大写是不行的。

Martin Fowler 在 "Refactoring" 一书中，
用到的带有不定冠词的变量名，
是我目前知道的唯一解决方案。
如果这个方案尝试可行，那么 cicada-lisp 的想法，就也是可行的。

关于 cicada-lisp 的实现方式：

- 先用 JS 实现 cicada-lisp 的解释器。
- 然后用 cicada-lisp 实现自身的编译器，编译到 XVM。
- XVM 支持 actor model，类似 erlang 虚拟机。

# 关于 lisp 的 naming convenient

在开始学 lisp 和 scheme 的时候，
我就觉得与 underscore_case 和 camelCase 相比，
lisp-case 要好看很多。
比如命名文件和命令行程序的时候，
大多数 linux/unix 程序员还是用 lisp-case。
