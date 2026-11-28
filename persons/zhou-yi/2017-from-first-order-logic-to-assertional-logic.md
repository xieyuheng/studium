---
title: From First-Order Logic to Assertional Logic
author: Zhou Yi
year: 2017
---

# 动机

[2026-11-28]
学习 Assertional Logic 在知识库构建方面的潜在应用。

# Abstract

> First-Order Logic (FOL) is widely regarded as one of the
> most important foundations for knowledge
> representation. Nevertheless, in this paper, we argue that
> FOL has several critical issues for this purpose. Instead,
> we propose an alternative called _assertional logic_, in
> which all syntactic objects are categorized as set
> theoretic constructs including _individuals_, _concepts_
> and _operators_, and all kinds of knowledge are formalized
> by _equality assertions_. We first present a primitive
> form of assertional logic that uses minimal assumed
> knowledge and constructs. Then, we show how to extend it
> by definitions, which are special kinds of knowledge,
> i.e., assertions. We argue that assertional logic,
> although simpler, is more expressive and extensible than
> FOL. As a case study, we show how assertional logic can be
> used to unify logic and probability, and more building
> blocks in AI.

# 复习：一阶逻辑

一阶逻辑要配合模型论语义来理解。

一个一阶逻辑系统给定构造不同类型「表达式」的语法规则，
表达式到模型的解释器就是其「模型论语义」：

- 函数符号必须被解释为全函数，但是可能不可计算。
- 谓词符号被解释为一个关系，但是可能不可判定。

# 1 Introduction

> Also, FOL is monotonic in the sense that adding new
> knowledge into a first-order knowledge base always results
> in more consequences. However, human reasoning is
> sometimes nonmonotonic.

propagator model 在配合 lattice 使用时反而要追求单调性。

不单调的数据可以被单调化，
比如在 append-only 数据库（datomic）中，
删除数据不抹除历史，而是追加一个事实，
声明某个先前的断言「在某个时间点之后不再为真」。

> ... although FOL is considered natural for well-trained
> logicians, it is not simple and flexible enough for
> knowledge engineers with less training. One reason is the
> distinction and hierarchy between term level, predicate
> level and formula level. From my own experience as a
> teacher in this subject, although strongly emphasized in
> the classes, many students failed to understand why a
> predicate or a formula cannot be in the scope of a
> function. Another reason is the notion of free occurrences
> of variables.

指出一阶逻辑的两个问题：

- 首先是语言中有多种表达式的类型：
  term predicate function formula。
  这个也可以说有意义的差异是使得被区分的。
  毕竟在为编程语言写解释器的时候也是如此。

- 其次是「自由变元」与「约束变元」所带来的复杂性。
  这个在 lambda 演算中也会遇到。

> ... arbitrary nesting also raises issues. Although natural
> from a mathematical point of view, a nested formula is
> hard to be understood and used.

这个我不认同。
因为嵌套结构的形成，
源于语言中做组合的能力。
只要有组合，就会有表达式的嵌套。
在编程语言中可以用 abstract 和 refactor
来处理「不易理解」的问题。

下面批评说 Modus Ponens 以及数学归纳法等公理，
不能在一阶逻辑以及高阶逻辑的框架内描述。
但是考虑类型论，Modus Ponens 就是函数作用本身，
而数学归纳法可以被 Nat 上的一个内置函数捕捉，
广义的结构归纳法可以由 termination check 算法捕捉。

> We first present the primitive form of assertional logic
> that uses minimal assumed knowledge and primitive
> constructs. Then, we show how to extend it with more
> build- ing blocks by definitions, which are special kinds
> of knowledge, i.e., assertions used to define new
> individuals, concepts and operators. Once these new
> syntactic objects are defined, they can be used as a basis
> to define more.

这就是实现一个解释器的过程。

# 2 Meta Language and Prior Knowledge

这里提到了语言内置的等词作为先验的对象。

问题：这里的等词是否只处理语法意义上的相等？
也就是类型论中的 definitional equality。

# 3 Assertional Logic: the Primitive Form

## 3.1 Capturing the syntax

表达式的类型：

- I -- individuals
- C -- concepts
- O -- operators

> Concepts and operators can be nested and considered as
> individuals as well. If needed, we can have
> - concepts of concepts,
> - concepts of operators,
> - concepts of concepts of operators
> and so on.

我尝试从类型论的角度理解：

- I -- 元素
- C -- 类型
- O -- 函数

在描述一个 operator O 的类型时，
只给出了参数类型 `(C1 ... Cn)`，
而没有给出返回值类型，
只说返回值是 individuals。

下一张 schema term 会给出由函数诱导出的返回类型。

问题：是否允许没有类型的元素？

## 3.2 Representing knowledge

individuals 由 operators 的作用所形成的 term 构成。

> An _assertion_ is of the form
>
>   a = b, (1)
>
> where `a` and `b` are two terms.

> Intuitively, an assertion of the form (1) is a piece of
> knowledge in the application domain, claiming that the
> left and the right side refer to the same object. A
> _knowledge base_ is a set of assertions. Terms and
> assertions can be considered as individuals as well.

assertions 可以作为 individuals，
就给证明论提供了语法基础。

> A _schema term_ is either an atomic concept `C ∈ Concept`
> or of the form `O(C1, ..., Cn)`,

也就是说，对于某个具体的 `O ∈ Operator` 而言，
其返回类型就用具体的 `O(C1, ..., Cn)` 表示。
一个 operator 也定义了一个类型。

集合论的解释是，schema term 是 term 的集合。

> Then, a _schema assertion_ is of the same form as form (1)
> except that terms can be replaced by schema terms.

TODO

## 3.3 The semantics
# 4 Extensibility via Definitions
# 5 Embedding FOL into Assertional Logic
# 6 Incorporating Probability and More
## 6.1 Integrating assertions with probability
## 6.2 Interactions between logic and probability via assertions
## 6.3 More building blocks
# 7 Discussion, Related Work and Conclusion
