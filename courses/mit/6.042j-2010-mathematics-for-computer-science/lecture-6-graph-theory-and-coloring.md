---
title: lecture 6 -- graph theory and coloring
video: "https://www.youtube.com/watch?v=h9wxtqoa1jY"
---

# My Motive

[2025-10-01] 在 "Essentials of Compilation"，
看到了寄存器分配与 graph coloring 问题。
感觉不过瘾，所以来学习一下。

与这个课程类似的 6.1200j-2024-mathematics-for-computer-science，
的 Lecture 1 叫做 "Predicates, Sets, and Proofs"。

也就是说这个课程十分符合 x-lisp 这种动态类型语言对计算的理解，
也就是 predicates，sets and functions。

# Lecture

- 以男女性伴侣的个数比例为例子，介绍 graph theory 的基本知识。

  批判了美国新闻机构和社会科学中，
  使用调查与统计的方法所得到的错误结论。

- 以 MIT 的考试时间分配为例子，介绍 graph coloring。

  很多带有 constraint 的资源分配问题，
  都可以化归为 graph coloring 问题。

  这里老师还给出了他自己本科在普利斯顿的时候，
  用 graph coloring 算法来优化学校考试的时间表的例子，
  是的所有考试都能在圣诞节前完成，
  这样学生不必在过节的时候还想着考试。

- 介绍 basic coloring 算法。

  这类算法称为「贪心算法」，也就是：

  - 按照一定规则 order 所有 vertex。
  - 每次挑选最前面的 vertex 处理。
  - 一遍过，不回头看。

  最基础的算法是任意 order。

- 对于 basic coloring 算法（任意 order）来说，
  可以证明：如果图的最大 degree 为 d，
  则可算法的结果最多用到 d + 1 个 color。

  这个定理是惊人的，因为条件是任意 order。
  而结果与最好的结果只相差 1。

- 证明定理的方式是对 graph vertex 的个数 n 用归纳法。

  老师在这里提醒到，
  很多同学会首先想到对命题中的 d 做归纳法，
  但是这是不对的，因为所有最大 degree 为 d 的 graph 的集合还是很复杂。

  找出 n 作为归纳法的对象，而不是 d，类似于做辅助线。

  对于图论问题而言，最常用的就是，
  对 vertex 的个数或者 edge 的个数用归纳法。

- 在对上面命题的归纳法证明中，
  basic color 算法中的 ordering 可以自然给出归纳推步的条件，
  也就是 ordering 中最后一个 vertex 还没被处理时的情况。

- 这个命题很令人惊讶，
  因为一个 graph 可能包含很多 d-degree complete subgraph，
  此时确实给出比最好的 coloring 多用一个 color 的结果。

  但是，degree 最大为 d 的 graph，
  可能没有 complete subgraph，
  比如二分图（bipartite graph）中的 degree 可能很高，
  但是只需要用两个 color 就可以完成 coloring，
  因此也这类图也叫 bi-color graph。

- 这里老师停下问同学，
  能否找出算法给出的结果，
  与最佳结果相差很远的例子。

  答案是 bipartite graph，
  也就是 callback 了课程开始时候的 man and woman 的例子！
  在课程中设计出来例子与问题之间的 callback，
  其精巧程度可想而知。

  当然，star graph 是个更极端（更简单）的例子。

- 尽管定理给出的上界很大，
  但是 basic coloring 算法对于
  star graph 和 complete bipartite graph
  这两类特殊的 graph，都能给出 2 coloring。

- 一般的 bipartite graph 就不行了。
  这里以 complete bipartite graph 为基础，
  构造了 basic coloring 会大失败的例子。

- 这里老师才给出 bipartite graph 的定义。
  这也是巧妙的课程设计。

  也就是说，一个 graph 能否被 2 coloring 是已经解决的问题。
  但是一个 graph 能否被 3 coloring 就是 NP-complete 的问题了，
  和找出任意 graph 的最小 coloring 一样是 NP-complete。

- dsatur 算法只是在 basic coloring 算法的基础上，
  用 heuristic 给出了一种 ordering。

- 这里老师介绍了 graph coloring 问题在自己公司的应用。
  老师 Tom Leighton 是起源于 MIT 的公司 Akamai Technologies 的创始人之一，
  公司用 Consistent hashing 技术做 CDN。
  具体的应用是：
  - 公司有很多（75000 多个）分布式的 server 需要更新软件版本。
  - 更新软件需要 stop server。
  - 限制是某些 server 同时运行着某类服务，
    它们不能同时被 stop，因为要保持服务在线。
  - 需要把更新时间分配给这些 servers，
    使得它们可以在最短时间内分批更新。

  最终的计算结果是可以用 8 coloring，
  分 8 批更新完所有 server 上的软件。
  这可以在一天内完成。

- 老师还介绍了其他 graph coloring 应用的例子：

  - 编译器的寄存器分配问题。
  - 地图的染色问题 -- 最著名的 graph coloring 问题。
  - 通信理论中通信频率的分配问题：
    如果两个信号塔所覆盖的区域相重叠，
    就不能使用同一通讯频率。
