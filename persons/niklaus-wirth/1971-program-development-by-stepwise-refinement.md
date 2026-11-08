---
title: program development by stepwise refinement
author: niklaus wirth
year: 1971
---

# My Motive

[2026-08-17] 把 stepwise refinement 和文学式编程，
以及语言学中的篇章学结合起来。
为 meta-lisp 设计一种可以运行 markdown 文件的机制。

"stepwise refinement" 建议翻译成「逐步精细」。

# Abstract

> The creative activity of programming -- to be
> distinguished from coding -- is usually taught by examples
> serving to exhibit certain techniques.  It is here
> considered as a sequence of design decisions concerning
> the decomposition of tasks into subtasks and of data into
> data structures. The process of successive refinement of
> specifications is illustrated by a short but nontrivial
> example, from which a number of conclusions are drawn
> regarding the art and the instruction of programming.

# 1. Introduction

> Clearly, programming courses should teach methods of
> design and construction, and the selected examples should
> be such that a gradual _development_ can be nicely
> demonstrated.

> This paper deals with a single example chosen with these
> two purposes in mind. Some well-known techniques are
> briefly demonstrated and motivated (strategy of
> preselection, stepwise construction of trial solutions,
> introduction of auxiliary data, recursion), and the
> program is gradually developed in a sequence of
> _refinement steps_.

> In each step, one or several instructions of the given
> program are decomposed into more detailed
> instructions. This successive decomposition or refinement
> of specifications terminates when all instructions are
> expressed in terms of an underlying computer or
> programming language, and must therefore be guided by the
> facilities available on that computer or language. The
> result of the execution of a program is expressed in terms
> of data, and it may be necessary to introduce further data
> for communication between the obtained subtasks or
> instructions. As tasks are refined, so the data may have
> to be refined, decomposed, or structured, and it is
> natural to _refine program and data specifications in
> parallel_.

> Every refinement step implies some design decisions.  It
> is important that these decision be made explicit, and
> that the programmer be aware of the underlying criteria
> and of the existence of alternative solutions. The
> possible solutions to a given problem emerge as the leaves
> of a tree, each node representing a point of deliberation
> and decision. Subtrees may be considered as _families of
> solutions_ with certain common characteristics and
> structures. The notion of such a tree may be particularly
> helpful in the situation of changing purpose and
> environment to which a program may sometime have to be
> adapted.

> A guideline in the process of stepwise refinement should
> be the principle to decompose decisions as much as
> possible, to untangle aspects which are only seemingly
> interdependent, and to defer those decisions which concern
> details of representation as long as possible. This will
> result in programs which are easier to adapt to different
> environments (languages and computers), where different
> representations may be required.

stepwise refinement 适合用来描述一个独立的问题的解法，
那么对于一个完整的应用程序中的模块，
是否也能用 stepwise refinement 的方法来开发呢？
也许只要用好局部的依赖注入，
通过接口来描述模块之间的依赖关系，
就可以使用 stepwise refinement。

我可以用 windbell 中的模块来尝试一下这个方法。

# 2. Notation

> For the description of programs, a slightly augmented
> Algol 60 notation will be used.

# 3. The 8-Queens Problem and an Approach to Its Solution

> Given are an 8 X 8 chessboard and 8 queens which are
> hostile to each other. Find a position for each queen (a
> configuration) such that no queen may be taken by any
> other queen (i.e. such that every row, column, and
> diagonal contains at most one queen).

> This problem is characteristic for the rather frequent
> situation where an analytical solution is not known, and
> where one has to resort to the method of trial and
> error. Typically, there exists a set `A` of candidates for
> solutions, among which one is to be selected which
> satisfies a certain condition `p`. Thus a solution is
> characterized as an `x` such that `(x in A) and p(x)`.

> A straightforward program to find a solution is:

```pseudocode
repeat Generate the next element of A and call it x
until p(x) V (no more elements in A);
if p(x) then x = solution.
```

> The difficulty with this sort of problem usually is the
> sheer size of `A`, which forbids an exhaustive generation
> of candidates on the grounds of efficiency considerations.

> It is obviously necessary to invent a "shortcut," a method
> which eliminates a large number of "obviously"
> disqualified contenders. This strategy of preselection is
> characterized as follows:

> Find a representation of `p` in the form `p = q and r`.
> Then let `Br = { x | (x in A) and r(x) }`.

> Instead of generating elements of `A`, only elements of `B`
> are produced and tested on condition `q` instead of `p`.

> Suitable candidates for a condition `r` are those which
> satisfy the following requirements:
>
> 1. `Br` is much smaller than `A`.
> 2. Elements of `Br` are easily generated.
> 3. Condition `q` is easier to test than condition `p`.

> The corresponding program then is:

```pseudocode
repeat Generate the next element of B and call it x
until q(x) V (no more elements in B);
if q(x) then x = solution
```

> A suitable condition `r` in the 8-queens problem is the
> rule that in every column of the board there must be
> exactly one queen. Condition `q` then merely specifies
> that there be at most one queen in every row and in
> every diagonal, which is evidently somewhat easier
> to test than `p`.

把一个潜在的解 `x` 分解为 `[x1, x2, ..., xn]`。
这样就可以引入 "partial trial solution" 的概念：
从 `[x1]` 到 `[x1, x2]` 一直到 `[x1, x2, ..., xn]`。

并且观察到：

> ... a full solution can never be obtained by extending a
> partial trial solution which does not satisfy the
> predicate `q`

这样就可以提前发现错误。

另外，目前没没有出错的「部分解」（partial solution），
可能没法被扩展到完整的解，这样就需要 backtracking 的技巧。

backtracking 可以用深度优先搜索实现，
而深度优先搜索本身可以在递归过程中自然实现，
不需要额外分配内存来保存搜索状态。

# 4. Development of the Program

> We now formulate the stepwise generation of partial
> solutions to the 8-queens problem by the following first
> version of a program:

```pseudocode
variable board, pointer, safe;
consider-first-column;
repeat try-column;
  if safe then
  begin set-queen; consider-next-column
  end else regress
until last-column-done V regress-out-of-first-column
```

TODO 理解这里的每个伪代码步骤。

> In order to refine these instructions and predicates
> further in the direction of instructions and predicates
> available in common programming languages, it becomes
> necessary to express them in terms of data representable
> in those languages. A decision on how to represent the
> relevant facts in terms of data can therefore no longer be
> postponed.

> A common difficulty in program design lies in the
> unfortunate fact that at the stage where decisions about
> data representations have to be made, it often is still
> difficult to foresee the details of the necessary
> instructions operating on the data, and often quite
> impossible to estimate the advantages of one possible
> representation over another. In general, it is therefore
> advisable to delay decisions about data representation as
> long as possible (but not until it becomes obvious that no
> realizable solution will suit the chosen algorithm).

TODO

# 5. The Generalized 8-Queens Problem

> The generalized problem is formulated as follows:
>
> Find all possible configurations of 8 hostile queens on an
> 8 x 8 chessboard, such that no queen may be taken by any
> other queen.

> The new problem essentially consists of two parts:
>
> 1. Finding a method to generate further solutions.
> 2. Determining whether all solutions were generated or not.

> It is evidently necessary to generate and test candidates
> for solutions in some _systematic manner_. A common
> technique is to find an _ordering of candidates_ and a
> condition to identify the last candidate. If an ordering
> is found, the solutions can be mapped onto the integers.
> A condition limiting the numeric values associated with
> the solutions then yields a criterion for termination of
> the algorithm, if the chosen method generates solutions
> strictly in increasing order.

一种给出的编码方式是：

`M(x) = sum([0, 7], i => x[i] * 10^i)`

此时最小的编码是 00000000，
最大的编码是 77777777。

# 6. The Extended Program

这里讨论在递归函数运行的过程中，
打印结果时的顺序问题。

# 7. Conclusions

> The lessons which the described example was supposed to
> illustrate can be summarized by the following points.

> 1. Program construction consists of a sequence of
> _refinement steps_. In each step a given task is broken up
> into a number of subtasks. Each refinement in the
> description of a task may be accompanied by a refinement
> of the description of the data which constitute the means
> of communication between the subtasks. Refinement of the
> description of program and data structures should proceed
> in parallel.

> 2. The degree of _modularity_ obtained in this way will
> determine the ease or difficulty with which a program can
> be adapted to changes or extensions of the purpose or
> changes in the environment (language, computer) in which
> it is executed.

> 3. During the process of stepwise refinement, a _notation_
> which is natural to the problem in hand should be used as
> long as possible. The direction in which the notation
> develops during the process of refinement is determined by
> the language in which the program must ultimately be
> specified, i.e. with which the notation ultimately becomes
> identical. This language should therefore allow us to
> express as naturally and clearly as possine the structures
> of program and data which emerge during the design
> process. At the same time, it must give guidance in the
> refinement process by exhibiting those basic features and
> structuring principles which arc natural to the machine by
> which programs are supposed to be executed. It is
> remarkable that it would be difficult to find a language
> that would meet these important requirements to a lesser
> degree than the one language still used most widely in
> teaching programming: Fortran.

从 stepwise refinement 的角度，
给出了「好的编程语言」的评判方法。

> 4. Each refinement implies a number of _design decisions_
> based upon a set of design criteria. Among these criteria
> are efficiency, storage economy, clarity, and regularity
> of structure. Students must be taught to be conscious of
> the involved decisions and to critically examine and to
> reject solutions, sometimes even if they are correct as
> far as the result is concerned; they must learn to weigh
> the various aspects of design alternatives in the light of
> these criteria. In particular, they must be taught to
> revoke earlier decisions, and to back up, if necessary
> even to the top. Relatively short sample problems will
> often suffice to illustrate this important point; it is
> not necessary to construct an operating system for this
> purpose.

这里提到了设计过程中的选择本身也会形成一个搜索树，
也需要 backtracking，正如这篇文章举例所用的 8-queens 问题。

> 5. The detailed elaborations on the development of even a
> short program form a long story, indicating that careful
> programming is not a trivial subject. If this paper has
> helped to dispel the widespread belief that programming is
> easy as long as the programming language is powerful
> enough and the available computer is fast enough, then it
> has achieved one of its purposes.

也许这最后一个结论在 AI coding 时代也适用。
