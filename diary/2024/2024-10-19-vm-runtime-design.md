---
title: vm runtime design
date: 2024-10-19
---

实现 vm runtime value 的实现方式：

- （A）每个 value 都是一个 pointer，
  要符合 value 的 interfece，
  比如带有代表类型的数据。
  此时 int 和 float 也要包在 pointer 内。

- （B）将 value 定义为 tag + content 的 struct，
  content 可以是一个 int 和 float 之类的直接值，
  也可以是 pointer。

- （C）value 就是 64 bits 的 word，
  在其中给出部分 tag 来编码 value 的类型。

- （D）value 64 bits 的 word，
  不用任何编码，
  操作 value 的函数需要知道 value 的正确类型。
  这种设计没法实现 GC。

需要进一步学习 runtime 和 GC 的设计：

- 2023-essentials-of-compilation-racket.pdf
