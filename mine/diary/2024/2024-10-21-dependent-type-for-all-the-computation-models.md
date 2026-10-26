---
title: dependent type for all the computation models
date: 2024-10-21
---

我所研究的很多问题其实都可以归结为，
如何把依赖类型加入到各种不同的计算模型中。

# lambda calculus

首先总结一下为 lambda calculus 实现依赖类型系统的问题。

类型系统在于检查类型是否匹配，
匹配的定义方式有很多，最基础的就是类型是否等价。

当类型中可以有任意计算时，检查类型是否等价，
就需要判断 closure 在 partial evaluation 下是否等价。

之所以需要 partial evaluation，
是因为 type 可以依赖前面出现过的变量，
在类型检查阶段，只知道这些变量的类型，
而不知道这些变量的值（not yet a value）。

所以只要实现了 partial evaluation，
和对所得的 normal form 的等价判断，
就可以实现 dependent type 了。

一个函数类型 `(A, B) -> C`
的不同位置之间可能形成依赖。
形成依赖的三种方式：

- （1）没有依赖关系，
  可以直接用等价或子类型关系，
  来判断两个类型是否匹配。

- （2）通过 pattern variable 的多次出现来形成依赖关系，
  此时可以用 unification 或者 merge 和 cover 之类的方式，
  来判断两个类型是否匹配。

- （3）前面引入的变量本身，可以出现在后面的类型中
   -- `(a: A, B(a)) -> C(a)`，
   这种情况就是 dependent type 的定义。

# stack-based concatenative

stack-based concatenative 语言的类型系统对应于 sequent calculus。

直接将 sequent calculus 的 judgment
的 sequent 结构，以 stack 的形式囊括到了语言中。

适合用 two-level computation 来理解。

但是可以发现这两层计算是差异很大的，
类型这一层计算，要么判断「是否匹配」一类的计算，
要么是 eliminator 作用于 not-yet-a-value 这种平凡的计算。

可以说，真正在类型这一层实行任意计算，
还是一个没有被探索过的方向。

- 可以专门为了探索这个 two-level computation，
  而实现一个 prototype 语言试试。

# propagator

给 propagator 实现类型系统时，
可以想象 type 是 cell 所保存的数据的一部分。

可以考虑用 two-level computation 来实现类型系统。
也就是有两层 propagation：

- 数据一层：就是无类型的 propagator network 的 `run`。
- 类型一层：构造 propagator network 时进行的 propagation。

但是这两层好像是一致的，只需要一层！
构造好 propagator network，
但是还没有 patch 数据的时候，
运行 `run` 就是类型检查。

propagator model 的厉害之处是 cell 本身就带有「匹配」机制，
merge 本身就可以用于定义两个类型是否匹配。

考虑形成依赖的三种方式：

（1）和（2）很容易用 cell content 的 merge 来处理。

（3）也就是 dependent type，在此时的表现形式是什么？

好像也很简单，就是在作用 propagator 时，
让某些位置的 cell 的 type 可以依赖另外位置的 cell 本身！

# inet

对 inet 的类型系统的最直观的理解，
就是只有相互匹配的 port 才能通过 edge 相连。

node 的 port 和 edge 都可以带上 type 信息，
在构造 network 时判断是否匹配就可以了。

考虑形成依赖的三种方式：

（1）和（2）依然很简单。

（3）的表现形式是什么？

可以想象 port 和 edge 所带有的
type 信息也是 net（比如 rooted graph）。
但是对于 inet 而言这些 type 没法连接到 node！
因为 node 只有固定个数的 port，
没有专门为连接到 type 而准备的 port。

那么 inet 根本就没法实现 dependent type？
或者 node 不应该只有固定个数的 port，

比如，可以有类似这样的解决方案：

- 方案 A：在固定个数的 port 之外，
  允许每个 node 有任意多个 weak port 用来连接到 type。
  - 类似某些语言针对 GC 设计的 weak map 和 weak reference 等等。

- 方案 B：根本就不做「固定个数的 port」这个约束，
  每个 node 都可以动态地新增 port。
  - 这是要求每个 port 都有名字，
    必须用 key-value map 的方式来保存 ports。
  - TODO 这是否影响 inet 在 normalization 时的优良行为？
    一个 rule 还是要拆开并删除一对 node，然后重新做连接。
    这可能需要配合 weak port 的概念一起使用，
    weak port 被拆出来之后，可以不用遵循 linear resourece 的语义，
    直接 drop weak port 就行了。
