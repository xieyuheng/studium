---
title: to do arithmetic is to rewrite to normal form
date: 2024-10-21
---

看了 Paul 的 "Arithmetic" 这本书，
很明显地认识到 arithmetic 就是在于 rewrite to normal form。

罗马计算版就是 multiset rewriting。

进位制下的 7 + 8 = 15 也是 rewrite 到每一位所能容下的符号，
也是 multiset rewriting。

Paul 的 "Measurement" 这本书中，
解代数方程 d*(d-1)=1 的过程，
也是 rewrite to normal form 的过程。
但是这里涉及的可能是 constraint processing
中的 hypergraph rewriting 了。

读 "Measurement" 知道 measurement 在于比较，
而 Arithmetic 也是为了比较，
rewrite to normal form 的目的就是去比较。

大多数计算模型，
比如 lambda calculus，
都是用 rewrite to normal form 来定义的。
难怪计算机在开始的时候，是用来做 arithmetic 中的计算的。
难怪计算机叫「计算」机。

# proof theory

与这种 "rewrite to normal form" 的结构形成对比的，
就是 proof theory 中搜索的感觉，
给定目标类型，然后构造满足目标类型的证明，
像是搜索而不是计算。

但是在实现 proof theory 的时候，
要能够检查所构造的对象是否满足条件，
就是类型检查。
这个过程如果被理解为是判断是否等价，
那就还是需要比较。

注意，考虑 propagator network 中的 `merge`，
这比 `equal` 要丰富很多，
但是 arithmetic 和 normal form
都是为了实现 `equal`。
所以说 propagator network
确实是把人们对计算的理解向前推进了一大步。

另外，算术系统的对象的类型是固定的，
而程序语言中对象是可以通过定义来扩展的。
比如：
- lambda calculus 的类型是固定的 lambda expression。
- interaction net 的类型是可以通过定义新的 node 来扩展的。
这种感受也很不一样。
