---
title: opcode difference between scheme and forth
date: 2025-01-21
---

在实现 forth 时，查找 word 的地方称为 dictionary，
我称 dictionary entry 为 definition 简称 def。
在引用一个全局名字时，用 CALL 这个 opcode，
CALL 先在 dictionary 中 LOOKUP，找到 definition，
然后 APPLY 这个 definition。

```
CALL = LOOKUP + APPLY
```

类似 EOPL 中的 IMPLICIT-REFS，
我们可以称这种解释器为 IMPLICIT-CALL。
在 forth-like 语言中，
我们显然是没法承受 EXPLICIT-CALL 的，
因为这样太啰嗦了。

在实现 scheme 时，runtime env 就是简单的 name 到 value 的 kv map。
没有 definition 的概念，所有东西都是 value。
value 可以被 apply。
一个函数调用可以被编译为，
从 mod 中 LOOKUP value 然后再 APPLY。

- 在实现 inet-lisp 时，一个问题是每次 LOOKUP 函数时得到的是同一个 function，
  但是每次 LOOKUP node 时得到的是一个新的 node。

  - 在逻辑式语言中，当使用 logic variable 来实现 rule 时，
    每次 LOOKUP 一个 clause 可能需要 refresh 其中的所有 logic variable。
    所以这种每次 LOOKUP 出来的东西不一样的行为是有先例的。

其实在 scheme 中，
一旦要求实现副作用 `(set! <name> <value>)`，
也是需要 IMPLICIT-REFS 的，
反而是 C 的 pointer 才是 EXPLICIT-REFS。

这两种实现方式在 EOPL 中有讨论过，
在 EOPL 中 def 被称为 den 代表 denotation。

> An important part of the specification of any programming language is
> the set of values that the language manipulates. Each language has
> at least two such sets:
>
> - **expressed values** -- the possible values of expressions.
> - **denoted values** -- the values bound to variables.

注意，与 EOPL 不同，我现在正在编译到 opcode。
