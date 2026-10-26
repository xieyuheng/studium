---
title: the imagination of inference rule
date: 2024-10-20
---

对 inference rule 和 proof theory 的想象，
或者用 Paul 的话来说 -- proof theory 的 mathematical reality，
其实就是 proof net，
即在已经证明的命题的空间中，
调用一个 inference rule 作用到符合条件的参数上，
就可以生成新的命题。

注意，在 dependent type 中，
以 inductive datatype 的 type constructor 为 judgment（judgment constructor），
以 data constructor 为 inference rule，
所形成的都是不带有计算的 data。

从 inet 的角度看，就是 node，
但是 inet 的 node 之间可以反应，
即可以带有计算。
这与纯粹的 inductive datatype 是不同的。

# arithmetic 的本质是 rewrite to normal form

TODO 以多项式为例子。

TODO V.V. 未来 proof 作为数学对象就像多项式，但是差别是什么？

# 如何描述 proof theory 的 mathematical reality？

TODO 用集合论的语言来描述 -- 类似几大基础数学结构。

- R(a, b): lift(R)(typeof(a), typeof(b)) -- 简单类型
- R(a, b): lift(R)(a, b, typeof(a), typeof(b)) -- 依赖类型
  或者说：
  R(a, b): lift(R)(a, b) -- 因为 typeof 总是可以在需要的时候调用。

TODO 用范畴论论的语言来描述。
