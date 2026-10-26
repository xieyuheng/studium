---
title: 实现 Truth Maintenance System
date: 2024-08-11
---

读了 Truth Maintenance System 的论文之后，
发现这里用的是与 datalog 类似的正向推理系统，
我还没实现过类似的系统（之前只实现过 prolog 的反向推理系统）。

现在很有必要实现一个自己的正向推理系统。

记得之前看一个 linear logic 的演讲，
用的也是类似 datalog 的正向推理。

[Extremely interesting talk about logic programming](https://www.youtube.com/watch?v=rICThUCtJ0k)

- backend chaining + persistent facts = prolog
- forward chaining + persistent facts = datalog
- forward chaining + resource = evolving state (linear logic!)
- forward chaining + resource (can be user input) = interactive state

所以一旦实现了自己的系统，
就可以实验很多新的东西。

------

一个问题是我现在实现语言的策略是什么？

- 如果用 JS 实现 sexp 语法的解释器，
  那么由于需要抽象机制，就需要带有 lambda calculus。

  - 在 inet 的实现中就遇到了类似的问题。

- 也许这就是为什么 Sussman 可以快速实验很多想法的原因，
  因为他有自己满意的语言，
  这个语言有 macro system 可以很快将别的语言嵌入进去。

  - 我不能实现动态类型语言，
    是否就不能做到 Sussman 的效果呢？

    我不一定要用 sexp 与 macro system，
    还是可以用 JavaScript 语法。

    只需要有一个基础的带有 lambda calculus 抽象机制的基础语言，
    然后在上面加东西，否则每次都要实现 lambda calculus 太累了。

    是否应该每次都实现 lambda calculus？
