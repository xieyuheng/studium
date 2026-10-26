---
title: My research as an adventure game
subtitle: Be explicit and clear about my goals and progress, to keep direction and help planning.
author: Xie Yuheng
---

> The idea of game, basically, is this:
> that the nature of the world is musical.
> i.e. everything that is happening is its own point.
>
> -- ["The Joker"](https://www.organism.earth/library/document/the-joker), Alan Watts

# TODO

[geometer] 为了保证 cell complex 给出的数据对于几何建模中的算法来说是充分的，
只需要写一个 cell complex 到 winged edge 的转化。

[geometer] 调研几何模型前端渲染的方案

[b-rep] 某些 b-rep 中，拓扑和几何并没有完全分离，
或者说拓扑信息不能独立于几何信息而存在，
拓扑对象之间的邻接关系是通过几何来描述的。

- 这合理吗？
  究竟应该如何表达邻接关系？
  用 generic figure？

[higher-aglebra] in category theory,
complicate algebric reasoning (1 dimensional)
is made simple by diagram chasing,
exactly because of 2 dimensional aglebric structures.

- the levels of higher aglebra should be:

  category-theory (0-groupoid), 1-groupoid, 2-groupoid, ...

  because in diagram chasing equivalences between arrows are already inversible.

  - why there are 2-category and higher-category (in which the levels are wrong)?

[topos] topos and subtype relation (i.e. OOP) in FP

[category] use category instead of groupoid for higher algebra

- 态射被理解为 process，态射之间的等价也应该被理解为 process（有方向的）。
  - 高阶代数的作用方式变得像一阶代数一样单一了。

[automath] 在语法上区分函数作用与数据构造子的作用。

- 我们在定义 datatype 的时候，是否也应该如此设计？
  - 比如 `List::null` 和 `List::cons`。

[fp] 一阶的函数式编程就是先递归地定义数据类型（集合），然后再用函数去揭示数据类型的结构。

- 在这种观点下，如何分析 Exp vs. Exp + Value？

- 可否利用 generic figure 的想法推广 cell complex 的定义？
  使得 cell 更一般，获得抽象的球面概念，
  而不是只是用具体的多边形与多面体。

  也许 generic figure 就是这种想法的推广，
  因为其书中说：

  > A few years ago, Lawvere suggested another alternative: to
  > introduce topos theory through presheaf toposes or, equivalently,
  > C-sets. These are categories whose objects result from the
  > _glueing of simpler ones_, **the generic figures**. These
  > categories are Grothendieck toposes which do not involve the
  > notion of a Grothendieck topology, making them much easier to
  > understand and work with them.

[b-rep] about polyhedral-complex -- https://www.cis.upenn.edu/~cis6100/complex1.pdf

[b-rep] polyhedral-complex vs cell-complex

- topological properties
- categorical properties

[simplicial-complex] 也是有必要，并实现，并熟练掌握的一个概念。

[cicada] 与线性的小说不同，数学是一个大厦，想要搞懂高层的概念，必须要有底层的基础。

- 比如循序渐进的，有很多例子的范畴论。
- 形式语言很适合用来构造牢固的基础。
- 但是我不能确定 sexp 还是 js-like 语法的选择。

# lambda-machine

Do a lambda-machine porject like the lambda project,
but not limited by the max call stack size of the hosting language.

We can even say the idea of SECD is to
transform a interpreter to a machine,
or transform recursion to explicit stacks.
Beside applicative lambda calculus,
maybe other interpreter can also be transformed.

# explicit-substitution

Learn from the explicit-substitution paper.

Do a project to study explicit-substitution.

Maybe just in the lambda-machine project.

We want to merge `Exp` and `Value`,
we thought about graph (inet) to achieve this goal,
but maybe explicit-substitution is the simple way.

Our finial goal about merging `Exp` and `Value`
is to make implementing dependent type easy.

Important notes:

In named variable + explicit substitution,
if we view substitution as term (first-class), we will have record,
if we add type system, we will have class,
which is typing context viewed as term (first-class).

In de Bruijn index + explicit substitution,
substitution are like infinite list, not record.

Since record is meaningful, because it can derive subtype relation.
named variable + explicit substitution
is more meaningful than
de Bruijn index + explicit substitution.

Can we view de Bruijn index as always use natural numbers as variable names?
and the simplified reduction rule as a result of this limitation?

# Postfix SECD

Learn about other variants of SECD by Dan and friends.

# A language for language design

Go back to lisp again

- Do a sexp library in C.
- Do a SECD machine in sexp and C.
- Do inet-lisp (with sexp syntax) in C (instead of JS).
- [maybe] Learn from clojure's syntax design.

我们需要有一个专门用来实验语言设计的语言。
为了避免在设计语法以及语法解析上浪费时间，
我们必须使用 lisp 的 sexp。
为了包含 record，我们学习 clojure 的语法，
在 `()` 之外，加入 `[]` 和 `{}`。

我们需要用 C 之间实现 inet，
这个版本也用 lisp 的语法，
因此需要先用 C 实现一个 sexp 的解析器。

“实验语言设计的语言” 也将用 C 实现，
并且这个 sexp 解析器处理。

我们需要的语言特性：

- sexp -- 为简化语法。

  - 也许应该用 HTTP header 的命名方式来命名类型。

- macro system 不重要，
  重要的是方便为我实验新语法，
  而不是让用户能设计新语法。

- 不用考虑 C 的 FFI，可以设计成一个封闭的系统。

- 简单类型系统，不用 dependent type。

- 纯函数，没有副作用，但是支持 Exception。

  - 也许可以将 Exception 表达在类型系统中。

  - 也许还是需要副作用的，否则就没法实现 inet 之类的东西。

实现计划：

- 模仿 lambda-machine，设计一个虚拟机。

  - 同时用 JS 和 C 实现。用 C 实现的时候自己管理内存。

- 把 sexp 语法编译到 lambda-machine 的汇编语言。

# About programming in C

- generaic array with realloc (in x-script)
- generaic stack with realloc (in x-script)

# Finish x-script

With a linear type syntax implemented in C.

- Just copy and rename type variables in quoted programs.

  - To avoid this copy, maybe we need to use closure,
    to learn more about closure, learn SECD.

# Earley parser

Implement Earley parser in JS again, and prepare to port it to C.

Maybe design Earley parser as a standalone language,
with pure postfix syntax, and return the AST in pure postfix syntax.

# iNet

Do iNet in C again.

Use linear logic as type system of inet.

Paper: Term syntax for linear logic -- simple types first.

- How does the algebra of linear logic connectives
  different from boolean lattice?

Implement optimal beta-reduction by inet.

# Learn essentials-of-compilation

learn also x86 assembly language for writing compilers.

# Mimor

During study of "Causality -- Models, Reasoning and Inference",
I found that maybe we should revisit the idea of [orbit](https://withorbit.com/).

- Repo: https://github.com/andymatuschak/orbit
- Because I keep forgetting:
  - definition of terms
  - import intuitions
  - typical examples

# FTHTP

FTHTP -- Forth Transport Protocol

A forth like language at the transport layer,
to send command to build data at the other end,
supporting conditional, loop and recursion,
especially for building record and collection.

- Using record can make the transport format
  NOT depending on the order of message
  -- such as ZMQ multi-part message.

- Property names in record can make a protocol self documenting.

# ZFC

ZFC as example first order theory -- try to formalize in logic programming.

# A browser of 3d scenes

A browser of 3d scenes,
one can use url to travel to other scenes.
A scene is a box (a tree is a nested box).

# Mo

learn about general constraint logic programming framework.

learn about general nominal logic.

implement nominal logic in `mo`.

implement alphaLeanTab -- learn this way Ray's book about first-order logic.

implement explicit-substitution

- nominal logic is important for just understanding the substitution used in paper.

# lowdmin

implement `lowdmin` again, use constraint idea to handle boundary relation.

- ctx should not just be a mapping, but be like solution.
- subdivision and surreal number

try the ideas about graph and groupoid in `lowdmin`

- the cubical version must provide a cubical-complex based model of cubical type theory

If `datatype` can be used to define infinite set,
`datatype` with higher dimensional constructors
must also be able to define infinite cell-complex,
we should start from 1-dim and try to
define infinite graph by `datatype`.

When see examples of hott or cubical type,
write them in cicada syntax.

# Path type

The rules about appling elements of `Equal` as functions,
which can be used to prove simple structural `Equal` like `equalPair`.

Might be the rules we are looking for in the design of language for cell-complex,
where are also a lots of "applying under structural".

# A language without lambda

VV said playing with expressions of proofs should be
as easy as playing with expressions of polynomials.
But bound variables and substitution made it not so easy.

What if we have a language without lambda, only top-level named functions?
Do we still need to distinguish `Value` from `Exp` and use `Closure` to handle scope?

- In normal implementation, top-level `Stmt` is different from `Exp`.
- Glued evaluation need to handle top-level name and local name (`Closure`) differently.
- With module system, such a language is useful enough.
- When writing a compiler, there is a pass which makes all closures top-level.
- Prolog is such a language.
- C is such a language.
- Maybe `mo` should be such a language.

We should implement a language to try this idea.

Maybe we should make `mo` explicitly such a language,
to simplify its implementation.

# Closed data by pure abstract classes

Closed data can be implemented by pure abstract classes.

- We should explore this.
- [Gerald Jay Sussman keynote and discussion - reClojure 2021](https://www.youtube.com/watch?v=EbzQg7R2pYU)

# JavaScript and c-family language design

Review the SICP JavaScript version.

# Get back to numbers

When I study probability theory,
I am unwilling to use just numbers.
This might be wrong, because numbers are still
the must important object in mathematics.

- draw algebraic geometry
- simulate classical mechanics
- draw differential geometry

# Graph and cell-complex

Is it easier to use hyper graph to model cell-complex?

- In Petri nets, the idea about bipartite graph is to view edge as a kind of node,
  can is be applied to cell-complex?

# Algebraic Topology

Re-study homotopy theory and algebraic topology.

- Study algebraic geometry, by which Hopf fibration are defined.

Review notes about homotopy type theory.

V.V.'s did not use `Iso` in his univalent axiom,
but changed the definition of `Iso`!

- If `Iso <-> isEquiv` why we can not use `Iso` to define univalent axiom?

Understand homotopy type theory with cell-complex as basic objects.

- Interpret type theory by cell-complex (model of a logic system)
- Revisit [Curry–Howard correspondence](https://en.wikipedia.org/wiki/Curry%E2%80%93Howard_correspondence)
  every now and then.
  - ~/persons/de-bruijn/on-the-roles-of-types-in-mathematics.pdf

# Word problems

Word problems of semigroup, monoid and group.

- About undecidable.

# A Pattern Language

Analysis Christopher Alexander's "A Pattern Language",
to use patterns to record my knowledge.

- Case study the structure of Martin Fowler's homepage.
- Readonly.Link should support writing patterns.

# Cicada Language

The goal is to design a language for foundations of mathematics.

This quest is beautifully formulated by Vladimir Voevodsky as the following:

> A formal deduction system together with a correspondence
> between its components and objects and actions in the world of mathematical thoughts
> which can be used to formalize all subject areas of mathematics
> is called a foundational system for mathematics or "Foundations of Mathematics".

The language must have enough features to formalize mathematic theorems and proofs.

- Currently we are still in lack of some important features:

  - **[side quest]** Recursion and termination-check
  - **[side quest]** Subtyping

- We already implemented inductive types,
  but we learned that it is possible to use lambda encoding
  to greatly simplify the implementation of inductive types.

  - **[side quest]** Lambda encoding and self types

    To avoid exponential lambda terms during encoding,
    we need to do graph based implementation.

We must actually use this language to formalize a lot of mathematics,
because without applications, language design project is not complete,
and applications will provide feedback to improve our design.

- **[side quest]** Formalize category theory
- **[side quest]** Formalize algebraic topology

**[paper]** Maybe the idea of fulfilling types is new and worth sharing as a paper.

- Fulfilling types make it easy to formalize some concepts of category theory.

# JoJo Calculus

Use postfix notations as syntax.

A language on which categorical semantics would fail.

To view a type system as a homomorphism between monoids.

# Boundaries as Types

Cell-complex and higher dimensional algebraic structure.

> To find an algebraic definition of infinity groupoids
> that would satisfy the Grothendieck correspondence.

Links:

- [books/boundaries-as-types](books/boundaries-as-types)
- [topics/homotopy-type-theory/cubical-type-theory.md](topics/homotopy-type-theory/cubical-type-theory.md)
- [topics/homotopy-type-theory/hott.md](topics/homotopy-type-theory/hott.md)

The only way of visualizing topological space is to give it geometry,
and one way of introducing geometry is using _algebraic geometry_.

**[problem]** From the point of view of type theory,
how "Boundaries as Types" project different from HoTT?

**[problem]** Can we use such implementation in geometric modeling?

**[problem]** Graph theory has many models,
we have many ways to interpret nodes and edges and their relations,
but how to interpret faces and bodies of cell-complex?

- Think about things can not be modeled by graph:

  - A face as a relation between relations.
  - A face as an equivalence between equivalences.
  - A face as a relation of a multiple arguments.

- The direction of an edge as many interpretions

  - Movement.
  - Causal relation.

- how to generalize this to face and body?

# Causal Inference Engine

The goal is to design a causal inference engine,
following the steps of Judea Pearl.

- by a library and its API.
- or by a probabilistic programming language.

- **[problem]** How does such a causal inference engine
  relate to logic deduction system and "model by classes and objects"?

# Recursion and termination-check

The most simple termination-check is based on the general inductive principle.
i.e. not only use the case of `n-1` as hypothesis in inductive step,
but also be able to use the case of `n-2`, `n-3`, ... (if available).

# Interaction nets and linear logic

To use interaction nets to implement Lamping's optimal beta-reduction.

- Maybe when using graph, we can also handle infinite normal form easily.

References about optimal beta-reduction:

- [Aaron Stump online lectures](https://www.youtube.com/channel/UCKQa6Ls95RhShE0kQsiXzVw).
- The Lamping algorithm paper.
- The Lambdascope paper.

# Symbolic systems

**[project]** `monoid-presentation`

- One of linear logic's model is *phase semantics*,
  in which the definition of *phase space* is based on monoid.

- Thus it is interesting to be able to play with monoid via presentation.

- **[project]** `group-presentation`

- Group presentation is much more well studied than monoid presentation.

- **[project]** `xieyuheng/algebra`

- We are good at implementing programming languages,
  which means we are good at playing with expressions.

  Thus it is beneficial to apply our skills to other problem domain
  which requires playing with expressions.

  For example, we can practice implementing a computer algebra system
  based on `bigint` of JavaScript.

  We are specially interested in polynomial and ring.

  - Remember Coq's `ring` tactic.

  Possible topics:

  - `Integer`
    - OOP API over `bigint` of JavaScript.
  - `Fraction`
  - `ContinuedFraction`

# Logic programming

Remember that inference rules of a logic are relations,
and can be formalized in logic programming languages like Prolog.

But Prolog can only do proof search,
we also want to write proof by hand,
even if we do understand the computational aspect of the logic yet,
i.e. we do not know how to evaluate terms or how to normalize terms.

**[project]** `cicada-lang/relation`

- JSON based logic programming language.
- Be able to write proof be hand.
- Learn Prolog for real.

**[side quest]** Extending Martin Löf's type theory

- There are many ways to extend Martin Löf's type theory.

  - Inductive types.
  - Higher inductive types.

- What are the general principles for introducing new types?

- Maybe we can use logic programming language to explore different inference rules.

**[problem]** Is inductive types already enough to formalize inference rules?

- Inductive type constructors are relations.

- But note that, a relation defined by inductive type constructor
  might be not reversible (not pure logic programming).

  For example, we can formalize judgments of dependent types by
  inductive type constructors, by when evaluation occurs in the it,
  it is no longer reversible.

# Lambda-cube, lambda encoding and self types

**[project]** `lambda-disco`

To implement lambda-cube with self types,
and use them to understand lambda encoding of inductive types.

# Formalize category theory

Category theory is the most basic target for formalization.

We can also try to apply the formalization
to categorical semantics and categorical logic.

- Take rules about product and sum as basic examples.
- Inversion principle of inference rule is special case of adjoint functor.
- Categorical semantics of inductive type -- F-algebra, and initial algebra.
- Categorical semantics of dependent type -- also about adjoint functor.

- **[problem]** Can we really use categorical semantics to guide
  the design of programming language's type systems?

The archetype of categorical logic is
cartesian closed category and simply typed lambda calculus.

- https://en.wikipedia.org/wiki/Cartesian_closed_category
- https://en.wikipedia.org/wiki/Simply_typed_lambda_calculus
- https://en.wikipedia.org/wiki/Categorical_logic

References:

- categorical-semantics: ~/watching/person/bartosz-milewski

  - [Bartosz's youtube channel](https://www.youtube.com/user/DrBartosz)

  Progress:

  - Category Theory 4.1: Terminal and initial objects
  - Category Theory 4.2: Products
  - Category Theory 5.1: Coproducts, sum types

- https://ncatlab.org/nlab/show/categorical+model+of+dependent+types

# Polya

inner/persons/polya/mathematics-and-plausible-reasoning/vol-2--patterns-of-plausible-inference.md

# 99 Bottles of OOP

Mimic the style of "99 Bottles of OOP",
and apply it to other programming exercises.

- Maybe use Chinese ballads as examples.

- References:

  - https://en.wikipedia.org/wiki/99_Bottles_of_Beer
  - "The Complexity of Songs", Knuth, Donald.
