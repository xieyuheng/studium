---
title: cs 6120 advanced compilers
subtitle: the self-guided online course
sources:
- "https://www.cs.cornell.edu/courses/cs6120/2020fa"
- "https://www.cs.cornell.edu/courses/cs6120/2025fa"
---

# info

老师：https://www.cs.cornell.edu/~asampson
课程：https://www.cs.cornell.edu/courses/cs6120/2020fa/self-guided

# lesson 1 -- welcome and overview

[ [Webpage](https://www.cs.cornell.edu/courses/cs6120/2020fa/lesson/1/) ]

[2025-10-04] 我第一次听说 Proebsting’s Law，
也就是说编译器优化对软件运行速度的提升，
远远小于硬件对软件运行速度的提升。

> **Proebsting's Law: Compiler Advances Double Computing Power Every
> 18 Years**
>
> I claim the following simple experiment supports this depressing
> claim. Run your favorite set of benchmarks with your favorite
> state-of-the-art optimizing compiler. Run the benchmarks both with
> and without optimizations enabled. The ratio of of those numbers
> represents the entirety of the contribution of compiler
> optimizations to speeding up those benchmarks. Let's assume that
> this ratio is about 4X for typical real-world applications, and
> let's further assume that compiler optimization work has been going
> on for about 36 years. These assumptions lead to the conclusion that
> compiler optimization advances double computing power every 18
> years. QED.
>
> This means that while hardware computing horsepower increases at
> roughly 60%/year, compiler optimizations contribute only
> 4%. Basically, compiler optimization work makes only marginal
> contributions.
>
> Perhaps this means Programming Language Research should be
> concentrating on something other than optimizations. Perhaps
> programmer productivity is a more fruitful arena.

source: https://proebsting.cs.arizona.edu/law.html
info: https://oleksii.shmalko.com/20211028115609/

老师说，但是因为摩尔定律正在减慢，
并且有很多新的特殊硬件，
所以编译器写手还是有市场的。

# lesson 2.1 -- representing programs

[ [Webpage](https://www.cs.cornell.edu/courses/cs6120/2020fa/lesson/2/) ]

[2025-10-05]

- 不用 AST 而使用 list of instructions 来表示程序。
  就是类似 LLVM IR 的一种语言。

  ```c
  <dest>: <type> = <operator> <operand> ...;
  ```

  必须要求 `<operator>` 出现，
  甚至不能直接写 literal 和 variable：

  ```c
  v1: int = const 1;
  result: int = id v1;
  ```

  其中 `id` 是 identity operator，
  `const` 是 constant operator。

  operator 不一定对应汇编指令的 opcode，
  比如下面的例子中有 `print` 这个 operator。

  也许通过扩展 operator 和 type，
  我们可以有一个相对 high level 的这种语言，
  适合直接把 lisp 代码翻译成 instruction list。

  而不是只在中间语言才用这种 instruction list 语言。

  这类 instruction list 语言应该叫什么名字？
  SSA -- static single-assignment form 吗？
  但是目前老师所描述的语言好像还没有 single assignment 这个限制。

- CFG -- control flow graph

  是我们从 instruction list 所表示的 program 中提取出来的信息。

  作为 graph 的性质：

  - directed graph
  - vertex = instruction
  - edge = possible control flow
  - one entry vertex, one exit vertex

- 介绍一些从 instruction list 构造 CFG 的例子。

- 介绍 CFG 的一种自然演化，
  就是为最常见的 chain of instructions
  而优化 graph 的表示方式。

  就是引入 basic block 的概念，
  使得 vertex 从 instruction 变成 basic block。

  注意老师如何渐进地引入概念，
  而不是一开始就介绍 basic block。

  一个 basic block：

  - jump 与 branch -- 称作 terminator，
    只能出现在 basic block 的结尾。
    注意 call 不是 terminator。

  - label 的对象是（也就是 jump 或 branch 的 target）
    只能 basic block 而不是 instruction。

- 可以设想设计 lisp 语法，
  直接表示 basic block 所构成的 CFG。

  也许可以叫做 b-lisp，
  或者 baby-lisp -- basic-block-lisp。

- 下面介绍如何设计类似汇编的语言，
  也就是从线性的 instruction list 开始，
  解析出来 basic block 的 CFG。

  最好直接把 label 保存在 block 中：

  ```scheme
  (define-data block?
    (basic-block (label symbol?) (instrs (list? instr?))))
  ```

  所需要的函数类型是：

  ```scheme
  (-> (list? (union instr? label?)) (list? block?))
  ```

  其实既然是在设计新的中间语言，
  就可以直接设计表示 CFG 的语言，
  而不必再从 instruction list 解析。

  这与 lisp 直接用 sexp 而避免语法解析是一样的道理。

- 然后介绍如个构造 CFG。

  所需要的函数类型是：

  ```scheme
  (-> (list? block?) (digraph? block?))
  ```

  有一个特殊情况是不带 terminator 的 block，
  要有 edge 到下一个 block。

  当然，设计语言的时候，也可以考虑完全禁止这种情况。

- 我发现这位老师好像很擅长写类似 python 的伪代码。

  可能在现代的 CS 专业，上了很多算法课之后就会如此。

# lesson 2.2 -- introduction to bril

[ [Webpage](https://www.cs.cornell.edu/courses/cs6120/2020fa/lesson/2/) ]

[2025-10-05]

- bril 是老师的 IR 项目：

  - https://capra.cs.cornell.edu/bril/
  - https://github.com/sampsyo/bril

  动机：

  - bril 是为教学而设计的，保持简单。
  - bril 可以随便扩展 operator 和 type。

- 直接用 JSON 作为 AST。

  具体语法：

  ```c
  @main {
    v0: int = const 1;
    v1: int = const 2;
    v2: int = add v0 v1;
    print v2;
    v3: ptr<int> = alloc v0;
    free v3;
  }
  ```

  翻译成 JSON AST：

  ```js
  {
    "functions": [{
      "name": "main",
      "instrs": [
        { "op": "const", "type": "int", "dest": "v0", "value": 1 },
        { "op": "const", "type": "int", "dest": "v1", "value": 2 },
        { "op": "add", "type": "int", "dest": "v2", "args": ["v0", "v1"] },
        { "op": "print", "args": ["v2"] },
        { "op": "alloc", "type": { "ptr" : "int" }, "dest": "v3", "args": ["v0"] },
        { "op": "free", "args": ["v3"] },
      ]
    }]
  }
  ```

  其实用 lisp 就可以兼顾具体语法和 JSON AST 的优点：

  ```scheme
  (define (main)
    (= v0 int (const 1))
    (= v1 int (const 2))
    (= v2 int (add v0 v1))
    (print v2)
    (= v3 (ptr int) (alloc v0))
    (free v3))
  ```

- 老师所用的很多工具都是 rust 系的，比如：

  - deno instead of node
  - uv instead of pip

  不同工具是用不同语言写的，想要跑起来，
  就要安装一些列 rust 系的工具，很麻烦。

  老师解释故意用很多语言写是为了证明 JSON AST 的普适性。
  但是这其实不用什么证明，大家都了解。

  正确的策略也许是用单一的语言写一套方便的工具，
  如果 bril 真 的有价值，
  别的开发者会根据需要用各自喜欢的语言来写新的工具的。

- 由于 bril 的 JSON AST 是描述 instruction list 的，
  所以这里老师展示如何用 python 把 instruction list 转化为 CFG。

  这里展示了 unix pipe 可以很方便地用来测试 JSON AST 的处理与转化程序。

  老师直接在课堂中现场写代码，这种教学方式也不错。
  重点也是学生需要暂停视频，先独立给出自己的实现。

  与 IU 的 homework + code review 的方式相比，
  现场写代码的方式只能用于视频课程，
  因为一般的课程没法暂停。

- 老师介绍了一个自己写的 snapshot testing 工具，
  叫 turnt：https://github.com/cucapra/turnt

  实际上按照我的经验，
  snapshot testing 用 unix pipe + git diff 就足够了，
  根本没必要再依赖额外的工具。

[2025-10-22]

- 我已经在 x-lisp 项目中增加了 basic-lisp 子项目，
  作为直接描述 basic block 的中间语言。

# lesson 3.1 -- local optimization & dead code elimination

[2025-10-22]

- 优化分为三类：

  - local optimization

    就是针对某个 basic block 而言的，
    而不是针对整个 function 的。

  - function optimization

  - inter function optimization

- 先从简单的 local optimization 开始。

- 首先实现的是 dead code elimination。

  注意，这是一个 function optimization。

  可以发现，虽然我们的数据结构叫做 control flow graph，
  其中 graph 是依据 control flow 而建立的，
  但是其实 instruction 之间，
  按照 variable 的依赖关系，
  也能构成一个 graph。

  dead code elimination 就是要删除没有被依赖的 instruction。

  这个优化非常像 GC！

- 可以看出这个优化应该在 SSA 之后，
  否则对变量的 assignment 会让问题变复杂。

- 介绍了一个在 SSA 之前，
  介绍两种 dead code elimination 算法：

  - function 内完全没有被用到的变量。
    这个可以用类似 GC 的算法，但是老师没用。

  - 局部 block 内被 overwrite 但是之前的值没有被用到的变量。

  这里两个算法的缺点都是一次只能消除一层 dead code，
  要迭代到收敛才能消除所有 dead code。

  这样效率会很低，先转化到 SSA 显然是更好的方案。

- 老师在展示优化的时候指出，
  可以通过让解释器记录所运行的 instruction 个数，
  来做简单的 profiling。
  以证明优化确实有效。

  另外证明优化有效的方式是，
  直接把优化之后的代码打印出来，

- 好消息的 bril 的代码仓库中有很多测试和例子程序可以用。

# lesson 3.1 -- local value numbering

[2025-10-23]

- 这节课讲如何用一个方法处理三个局部（block）优化：

  - dead code elimination
  - 消除连串的 copy（id）
  - 消除相同的 sub-expression

- 问题是，给出两个 variable，
  以它们为 root 的 expression graph
  可能代表了完全相同的计算过程。

  local value numbering 就是要发现这一点。
  其中计算过程被称为 value。
  每个被分配以不同 number 的 value，
  代表了可能产生不同 value 的计算过程。

  | #  | value   | canonical home |
  |----|---------|----------------|
  | #1 |         | x              |
  | #2 | #1 + #1 | a              |
  | #3 | #2 * #1 | d              |
  | #4 | #2 + #3 | c              |

  就是说，对于每个变量 rhs 的表达式，
  要用 partial evaluaton 找到其正规形式，
  这样就能比较两个 rhs 是否相等。

  - 这里的 numbering 类似 partial evaluaton 中的 neutral。
    说这些 numbering 是 value 也没问题，
    因为 neutral 可以通过 not-yet-value 被视为 value。

  - 消除相同的 sub-expression 需要做这种判断，
    因为消除的不是语法上相同的 sub-expression，
    而是计算效果相同的 sub-expression。

  其实现方式类似解释器，
  但是 env 是关于 numbering 的 table。

- 老师给出一个具体列表的例子。

  老师在讲例子的时候，总是说 "in LVN"，
  是 LVN 是 local value numbering 的缩写，
  我经常错听成 LLVM。

  ```c
  main {
    a: int = const 4;
    b: int = const 2;
    sum1: int = add a b;
    sum2: int = add a b;
    prod: int = mul sum1 sum2;
    print prod;
  }
  ```

  | #  | value     | canonical home |
  |----|-----------|----------------|
  | #1 | const 4   | a              |
  | #2 | const 2   | b              |
  | #3 | add #1 #2 | sum1           |
  | #4 | mul #3 #3 | prod           |

  注意 canonical home 只记录一个 variable，
  我们还需要一个 variable 到 numbering 的 hash。

- 这种方法比 partial evaluaton 更有趣，
  partial evaluaton 显然也可以解决发现相同 sub-expression 的问题，
  但是这里的方法类似于给所有可能出现的计算建立了 hash table，
  给出一个表达式（atom-operand-exp），
  通过查表就可以知道这个表达式是否代表未出现过的新的计算。

  表达式的 operand 总能在 table 中找到，
  因为 operand 是已经出现过的计算。

- 下面介绍如何利用这个 table 来进行优化。

  - 首先把 expression 代替为 partial evaluaton 之后的 expression。
    这一步要利用到 canonical home。

  - 其次遇到完全相同的计算比如上面的 sum2，可以直接删除。
    或者按老师说的，用 identity instruction 来做一个 copy。
    - 老师的意思是说，后续再运行 dead code elimination pass 来删除 dead code。
    其实没有出现在 canonical home 中的 variable 都可以删除。

- local value numbering (LVN) 的变体：

  - 一般的消除 sub-expression 不需要知道 instruction 的语义，
    只是需要知道它们没有副作用。

    而想要消除连串的 copy，就需要用到 `id` 的语义。

  - 在判断 value 是否相等的时候，考虑到交换律，
    -- `add x y = add y x`。

    老师处理这个问题的方案是：
    让 value 列的 operands 按照递增的顺序排列。
    这种 normalization 可以让我们直接用最简单的，
    语言内置的 hash 来实现 table。

    显然有很多方案能处理这种等价关系的问题。

  - 与 `id` 的特殊性类似，特殊处理 `const` 的语义，
    才能实现对 `const` 的 partial evaluaton。

  看来不同的优化需要知道 instruction 的不同信息，
  比如有没有副作用，是否具有交换性，等等。
  如何在扩展 instruction 的时候扩展这些信息？

- 介绍 constant folding。

  其实就是 partial evaluaton 的最简单形式。

- 强调 local value numbering 是 local 于 block 的。

- 老师介绍自己的写的类似 python 的伪代码。

  我发 python 可以在 if 中定义变量，
  然后在 if 外面使用。

  考虑 basic block 之间的使用变量的关系，这是合理的。
  但是 scheme 做不到这一点。

- 介绍了由于没有 SSA 导致 LVN 算法变复杂了。
  预示了 SSA 的重要性。

# lesson 4.1 -- data flow

[2025-10-23]

- data flow 是一种框架类的 idea，
  可以把很多 block 内的局部分析，
  变成 control flow graph 上的全局分析。

- 这里先考虑一个一般的 reaching definition problem。

  对于一个 variable 的 definition（assignment），
  和所有引用到这个 variable 的地方（use），
  判断这个 definition 是否 reach 到这个 use 位置。
  或者说，判断所 use 的是否是这个 variable 的这个 definition。

  注意，这与 liveness 分析不同，
  liveness 是针对 variable 的，
  而这里的问题是针对 variable 的某个 definition 的。

- 这里已经开始给一个 variable 的多个 assignment 分配不同的名字了，
  这也预示着 SSA。

- reaching definition problem 就是可以用 data flow 解决的例子。

- data flow framework：

  - 首先确定想要分析的信息是什么，
    这个信息应该是就每个 block 的 entry 和 exit 而言的。

    - 比如，就 reaching definition problem 问题而言，
      所关心的信息就是 the set of reaching definitions。

  - 就所关心的信息而言，
    列方程表达出来 block 所导致的，
    entry 位置和 exit 位置之间的关系。

    这个方程经常叫做 transfer function。

  - 就所关心的信息而言，
    根据 control flow graph 中的 edge，
    列方程表示 block 之间的关系。

    - 比如，就 reaching definition problem 而言，
      两个 in-edge 就需要 set union。

  - 解方程。

    这显然非常适合用 propagator model 实现！
    每个 block 的 in 和 out，也就是 entry 和 exit，都是一个 cell。
    方程就是 cell 之间的 propagator。

- 介绍如何对 reaching definition problem 列方程。

- 介绍 data flow 的 worklist 算法。

  这也可以看作是 propagator model 的一种单线程实现方式。
  只不过这里的 propagator 形式非常固定。

  注意，worklist 算法有 forward 和 backward 两个版本。

- 老师强调了这个算功支持 loop。

- 下面介绍 data flow 的前提，也就是 propagator 的前提，
  cell 里面保存的东西在 merge 的时候要符合 lattice 公理。

- 带有 loop 的 graph 可以被理解为无穷多 path 的 merge。

- 介绍其他适用于 data flow framework 的问题：

  | 问题                  | 信息               | 方向     | merge |
  |-----------------------|--------------------|----------|-------|
  | reaching definitions  | set of definitions | forward  | union |
  | liveness variables    | sef of variables   | backward | union |
  | constant propagation  |                    |          |       |
  | available expressions | set of expressions |          |       |
  | interval analysis     | interval           |          |       |

  我能理解 reaching definitions 和 liveness variables，
  constant propagation 就是把所有的 constant variable 找出来。
  老师提到的 interval analysis 已经完全是一般的 interval 算术了。

- 这么说来，人们对 data flow 的研究，
  也可以用于对 propagator 的研究！

- 与 EOC 课程中个单独的 uncover-live pass 相比，
  这里把类似的分析都放到一个框架内了，
  并且这个框架还和 propagator model 有非常强的联系，
  这很不错。

- propagator model 厉害之处在于它适用于任何需要解方程的地方。

# lesson 4.2 -- implementation task

[2025-10-23]

- 这是一个非常短的视频，补充介绍 data flow 这一节课的作业。

# lesson 5.1 -- global analysis & optimization

[2025-10-23]

- 这节课继续 function 层次的 optimization。

- 先介绍 control flow graph 中的一些概念：

  - predecessor 和 successor -- 这就是 digraph 中的概念。

  - path -- 就是 digraph 中的 path。

- dominator -- A block dominates B block，
  如果从 entry block 到 B block 的所有 path 都经过 A block。

  也就是说，A 一定会在 B 之前被运行。
  或者说，如果运行了 B 那么，A 一定已经运行过了。
  即 A 在 B 的必经之路上。

  domination 关系是自反的，一个 block dominates 自身。

  这个关系当然也是可以在一般的 digraph 上定义的，
  只不过在 control flow graph 上，这个意义很重要。

  注意，在 tree 这种特殊的 digraph 中，
  domination relation 就是 predecessor relation。

  反过来对于任意 digraph 我们可以定义 domination tree。
  来完整地表示其 domination relation。
  domination tree 中的每个节点，
  要 dominate 它的子树（自身和所有后代节点）。

  我们可以定义 strict dominate
  和 immediate dominate 这两种关系：

  - strict dominate -- 取消 dominate 的自反性。
  - immediate dominate -- strict dominate 并且没有中间 node。

  一个 node 在 dominate tree 中的 children
  就是这个 node immediate dominate 的 nodes。

- domination frontier -- A 的 domination frontier，
  就是以 A 为 root 的 domination tree，
  再向前 successor 一步，所多出来的 vertices。

- post-domination -- 与 domination 关系类似，
  但是是就 exit 而言的。

  也就是与 domination 是对偶的。

  也就是说如果 A post-domination B，
  那么想要从 B 到 exit，就一定要经过 A。

  类似的也有 post-domination tree。

  注意，control flow graph 总是有一个 entry 和一个 exit，
  其实是因为，函数被调用时总是从某个固定的点开始运行，
  并且总是要通过 return 把 control 还给 caller。

  正是因为有了这个 one entry 和 one exit，
  function 所代表的 control flow graph 才能 compose，
  function 也才能 compose。

- 给出计算 domination relation 的算法。

  计算的结果 domination relation，
  可以用 vertex 到 vertex set 的 map 表示。

- 暂停，自己想一下。
  好像可以一层一层地从 entry 开始构造 domination tree。

  ```python
  processed = {}
  frontier = {entry}
  # 通过找到每个 vertex 在 tree 中的唯一 parent 来完成
  parent_relation = {}
  while all_vertices != processed
    next_level = progress(frontier) - processed
    for vertex in next_level
      parent_relation[vertex] =
        least_common_predecessor(direct_predecessors(vertex), processed)
    processed += next_level
    frontier = next_level

  def progress
    # 找到一个集合的 vertex 的 successor 的集合
    # 排除这个集合中的 vertex

  # 在已经构造了的 tree 中找 least common predecessor
  def least_common_predecessor(vertices, processed)
    if length(vertices) == 1
      return vertices[0]
    else
      # 一定是收敛的因为有唯一的 entry 作为 tree 的 root
      vertices = intersection(back_progress(vertices), processed)
      return least_common_predecessor(vertices, processed)
  ```

  这种类 python 的伪代码一点也不好。
  不如类 scheme 的伪代码。

- 老师给出的循环直到收敛算法，
  在每个循环中要对所有的 vertex 更新当前所估计的 domination set。

  这看起来很简单，但是如何分析其复杂性呢？

  复杂性是 O(n^2)。

  另外，如果 control flow graph 是 reducible 的，
  那么在遍历所有 vertex 的时候用 reverse post-order，
  就可以 O(n) -- 外层的循环是常数。

  这是怎么证明的？
  是不是所有的拓扑排序都可以？

  老师说大部分程序语言的 CFG 都是 reducible 的。

- 下面就要定义什么是 CFG 的 reducible。

  先定义什么是 natural loop，
  或者说一个 vertex 的集合，
  何时算是 natural loop：

  - 整体是个 circle，也就是说 strongly connected。

    强联通就是说：任意两个顶点都在一个公共的环上。
    其必要条件是：对于图中的每一个顶点，都必须存在于至少一个环中。

  - 只有一个入口。

    就是说不能有 goto 从外面跳到 loop 中间。

  讽刺的是昨天刚刚看了 knuth 的一个问答演讲，
  其中 knuth 吹嘘他如何用 goto 跳到一个 loop 中间的。
  读 knuth 一定要带着批判的眼光去读，
  他目标是写编译器的书，但是一直拖延，
  拖延出来了几大本关于算法的书。
  并不是什么值得学习的对象。

- 关于强联通与环的关系 deepseek：

  强连通性要求图中任意两个顶点都在一个公共的环上，
  或者说，整个图是由多个环紧密嵌套、连接而成的。
  单个的环是构成强连通图的基本单元，
  但一个环本身只是一个最小的强连通子图。

  从强连通分量（SCC）的角度理解：

  这是理解两者关系最深刻的方式。

  - 强连通分量（SCC） 是有向图的一个极大强连通子图。
    所谓“极大”，意味着不能再添加任何其他顶点而不破坏其强连通性。

  - 关键定理：一个有向图中的每一个顶点和每一条边，都恰好属于一个强连通分量。

  现在，来看环和SCC的关系：

  - 每个环都包含在一个SCC内：
    考虑环上的任意两个顶点 u 和 v。
    因为这是一个环，所以存在从 u 到 v 的路径，也存在从 v 到 u 的路径。
    这正好满足了强连通性的定义。因此，这个环上的所有顶点都属于同一个强连通分量。

  - 一个SCC可以由多个环“组成”：
    一个SCC内部的结构非常复杂，
    它包含了所有顶点之间互相可达的路径。
    这些路径交织在一起，形成了许多大大小小的环。
    可以说，SCC就是一个由无数环紧密耦合而成的结构。

  - 最小的非平凡SCC就是一个环：
    最简单的、包含多于1个顶点的强连通图是什么？
    就是一个环。例如 A -> B -> A。
    它本身就是一个SCC，并且它本身就是一个环。

- 关于 loop 还有 backedge 的概念：

  A edge A -> B, where B dominates A.

- 给出找 backedge 的算法。

  就是先用 breadth-first search，
  找到所有 forward edge，
  然后是删除所有 forward edge 剩下的就是 backedge。

  好像任何遍历算法都可以，只要是从 entry 开始的就行。

- 可以说 natural loop 是围绕 backedge 而产生的。
  找包含 backedge 的最小强联通分支，
  应该就是 natural loop 了。

- reducible control flow graph 就是说，
  每个 backedge 都有一个 natural loop。

  包含一个 loop 的最小强联通分支总是存在的，
  那么这里需要考虑的额外条件就是唯一 entry 了。

  reducible 没有说 reduce 成什么？

- structured programming 显然会给出 reducible control flow graph。

- 给出 irreducible control flow graph 的例子。

  只需要三个节点：

  ```dot
  A -> B
  A -> C
  B -> C
  C -> B
  ```

  从这种简单的情况看来，
  goto 也是合理的。

- 下面介绍有了关于 CFG 的更多知识之后，
  可以进行的优化。

- loop-invariant code motion。

  就是把没必在 loop 中反复计算的表达式，
  从 loop 中提出来。

- natural loop 相关的术语：

  - header -- 就是 loop 的 header。
  - tail -- 就是 backedge 的起点。
  - preheader -- 就是进入 loop 的 block，可能有多个。

- 这个优化就是要找到唯一 preheader，
  如果不是唯一，可以构造一个新的 block。
  然后把 loop-invariant code 从 loop 中提到这个 preheader 中。

- 这里暂停思考：

  - 怎么识别 loop-invariant code？
  - 如何完成 move？

  首先要通过 backedge 找到 natural loop。

[2025-10-24]

- 老师又给出一个 loop until no change 的算法。

  iterate to convertgence:
  - for every instruction in a given loop:
    - mark it as loop-invariant iff:
      forall operands of x, either:
      - all reaching definitions of x are outside the loop
      - there is exactly one definition,
        and it is already marked as loop-invariant.

  注意，这里如用到了 reaching definitions。

- 另外需要给出判断什么时候可以 move
  一个被标记为 loop-invariant 的 instruction：

  - definition dominates all its uses.

    否则第一次循环的时候，这个变量不是由这个 definition 定义的，
    而其他循环中，这个 definition 是由这个 definition 定义的。
    此时把这个 definition 放到外面，会导致第一次循环时，
    这个变量也是由这个 definition 定义的。

  - 没有对这个变量的其他定义。

  - dominates all loop exits.

    natural loop 只能有一个 entry，
    但是是可以有多个 exits 的。

    因为如果不 dominates 某个 exit，
    那么这个 definition 就有可能在循环中运行不到。

    这里的问题是，甚至对于一般的 for loop 而言，
    loop 的 body 都不会 dominates exit，
    因为 for loop 可能会 loop 0 次。

    do while loop，如果没有 break 的话，
    可以保证 loop body dominates exit。

    - 这是我第一次知道 do while loop 的优点。
      我几乎没有用过这种 loop。

      之后遇到这种情况，可以通过 do while loop，
      来把 loop body 代码至少运行一次，
      明显表达出来。

    对于 while loop 而言，
    有时 loop 不能被运行 0 次，
    但是我还是用 while loop 来表达了。
    如果能发现不能运行 0 次，
    就可以用这个优化。

- 上面关于 move 的这第三个条件可以放宽：

  如果提出这个 definition，
  让它在 zero loop 的情况下也会额外运行一次，
  不会影响后续的代码。

  显然这要求这个 instruction 不能带有副作用。

  其次后续代码对这个 definition 不能有依赖，
  就是说所定义的 variable is dead after the loop。

  - 别忘了，我们是用 liveness 来描述对于变量的实际依赖的。

- 由于所有 sub-expression 都被 unnest 成 definitions 了，
  所以这里提出 definition 的过程可以做到极限。

# lesson 5.2 -- static single assignment

[2025-10-24]

- 老师说 SSA 是一种哲学。

  SSA 可以完全改变我们对 instruction 的理解，
  使得一个 function 内的 basic block，
  可以被理解为以 variable 为 key 的 key-value map。

- 老师指出，之前很多优化中的难点，
  都来自于对同一个 variable 的多次 assignment 所产生的副作用。

- 首先要把一般函数翻译成 SSA，
  这需要用到 phi instruction 或者类似的技巧。

  最后还要从 SSA 翻译回来，
  翻译回来这一步感觉就是寄存器分配。

- 介绍 phi instruction。

- 给出对带有循环的代码，添加 phi instruction 的例子。

- phi instruction 所定义的 variable，
  是会随着循环而变化的，
  所有依赖这个 variable 的 instruction，
  也会跟着变化。

  在循环过程中，对变量的副作用（re-assignment）还是存在的，
  只不过副作用出现的起点，被限制在了 phi instruction。

- SSA 哲学，或者说 SSA 视角：

  - variable == definition
  - instruction == value
  - argument == data flow graph edge

  其实也有 variable == instruction。

- 上面所提到的 data flow graph，
  不是之前以 block 为 vertex 的 graph，
  而是更细节地，以 variable 为 vertex 的 graph。

  也就是说 variable 可以被理解为 propagator model 中的 cell。
  并且每个 value instruction 都只有一个简单的，
  以这个 variable 为 output cell 的 propagator。

- 考虑 phi instruction 如何被实现为 propagator 中的 cell。

  可以理解为 phi instruction 的 variable，
  依赖于两个 in-block 的 cell。

  注意，构造 propagator 的时候，
  其实只需要描述 cell 之间的依赖关系，
  而不需要描述 block 之间的依赖关系。
  这也预示着，`put!` + `use` 风格的 SSA。

- 考虑 `put!` + `use` 风格的 SSA。

  此时对 propagator 的构造很自然，
  其构造与使用 phi 时是完全相同的。

  并且可以发现，jump 其实是多余的，
  就 cell 之间已经有了 reaction 关系，
  没必要用 jump 和 label 来描述的额外的信息！

- 可以发现，有了 propagator model 这个通用的计算模型之后，
  可以方便理解很多东西。

- 下面要把一般的 block 转化为 SSA。

  注意，新的课程：https://www.cs.cornell.edu/courses/cs6120/2025fa/lesson/6/
  已经在使用新的 SSA 格式了：https://capra.cs.cornell.edu/bril/lang/ssa2.html

  但是我们还是先学习 phi instruction。

- 转化成 SSA 过程中的重命名很简单，
  重点是如何增加 phi node。

  重点就是之前提到的 dominance frontier 的概念。
  phi node 应该被加入到作为 dominance frontier 的 block 中。

  在实现 basic-lisp 时，
  要实现很方便的 API，
  使得我们可以 query 这些 block 之间依赖关系，
  以及 instruction 之间的依赖关系。

- TODO 我的编译器在开头就会对所有的 let 做 uniquify，
  并且没有对变量的 assignment。
  如此会对翻译成 SSA 的步骤有什么影响？
  是否不用做 renaming 了？
  是否还需要 phi node？

- 算法分两步：

  - 首先找到所有 dominance frontier 并在其中增加 phi node。
  - 然后在 renaming 的过程中修改 phi node，使得它们具有正确的 SSA 形式。

  注意，使用 put + use 的 SSA 算法可能不一样。
  TODO 这个我之后再研究。

- 暂停想一下如何实现上面的算法。

  其中第一步是简单的。
  所插入的 phi node 已经带有 label 了，
  知识需要知道 renaming 之后的名字。

  第二步，可以是记录 rename 之前原来的名字，
  发现 phi node 中对原来名字的依赖时，
  根据 block 的名字来修改 phi node 中的 label。

- 老师给出的算法：

  insert phi node:

  - forall `v` that is a variable:
    - forall `d` that is a block where v is defined:
      注意，这里找的是 `v` 的 definition block，而不是 definition instruction。
      - forall `b` that is a block in the dominance frontier of `d`:
        别忘了 dominance frontier 是就某个 definition 而言的。
        - insert phi node to `b` if not already
        - add block `b` to definition blocks of `v` if not already
          因为现在这个 phi node 也在定义 v 了！
          但是这就是在循环中修改所循环的 collection 了，没有问题吗？

  rename variables:

  - 给每个 variable 分配一个 stack -- `stack[v]`
    用来保存 `v` 的新名字。

  - 这里的 rename 函数，
    既是递归函数，又用到了 stack。
    不是很好理解。

    注意，递归的时候取的是 dominance tree 的 children。

    需要实现的时候再来看这个算法。
    另外，转化到 SSA 的算法应该有很多，
    都可以参考一下。

- 下面讲如何从 SSA 转化成不带 phi node 的形式。

  这里要解决两个问题：

  - basic block 的解释器不能解释 phi node。
  - SSA 使用的变量太多了。

  这第一个问题，如果使用 put + use，这一步好像是平凡的！

  第二个问题好像可以直接通过寄存器分配来解决。

- 这里老师给出的算法是把 phi node
  变成两个 direct predecessor block。

  这个新增的 direct predecessor block，
  其也暗示了 put + use 风格的 SSA。

# lesson 6 -- llvm

[ [Webpage](https://www.cs.cornell.edu/courses/cs6120/2020fa/lesson/6/) ]

[2025-10-15]

这节课的内容是用 llvm 的 cpp api，
来写一个简单的 pass：

- 把所有的 binary operator 都改成 mul。

- 实现这个 pass 的方式不是修改已有的 binary instruction，
  而是创造一个修改了的新的 instruction 节点，
  然后找到所有用到了原来 binary instruction 结果的地方，
  把变量换成新的变量。

- 实现过程中展现了 SSA 哲学，下面三个东西被视为是等同的：

  - instruction
  - 所生成的结果
  - 保存结果的 variable

SSA 这么简单的东西，
被 llvm 包装成这么复杂的 OOP api，
这太荒谬了。

就像 clojure 经常说的那样，
应该用数据，而不应该用 OOP。

bril 就是数据驱动的例子。

# lesson 9 -- alias analysis

[2025-11-03]

- alias analysis 也叫 pointer analysis。

- 强调不光是 C 中有 explicit pointer，
  动态类型语言中的 object 也是 pointer。

- TODO

# lesson 10 -- memory management

[2025-10-19]

[ [Webpage](https://www.cs.cornell.edu/courses/cs6120/2020fa/lesson/10/) ]

- 这节课要讲的主题是 GC。

- 介绍手动管理内存可能会出现的 bug。

  这是人的问题不是语言的问题：

  - Eskil Steenberg：

    - [How I program C](https://www.youtube.com/watch?v=443UNeGrFoM)
    - [Advanced C: The UB and optimizations that trick good programmers](https://www.youtube.com/watch?v=w3_e9vZj7D8)
    - [Architecting LARGE software projects](https://www.youtube.com/watch?v=sSpULGNHyoI)
    - [Debugging and the art of avoiding bugs](https://www.youtube.com/watch?v=sfrnU3-EpPI)

- 介绍为什么静态分析很难完全解决内存分配问题。

- 介绍 GC 的基本原理。

- 介绍 GC 中的术语：

  - collector -- 就是 runtime 的 GC 代码。
  - mutator -- 从 GC 角度看，你的代码就是在修改 graph。
  - live/dead -- 是否 reachable from root。

- 解决 GC 问题就是要找到 dead data 然后 free。

  解决方案有两大类：「引用计数」和「标记清扫」。

  - 引用计数：

    就是在数据中记录有向图意义上的 in-degree，
    每次做 side-effect 都要更新这个记录。

  - 标记清扫：

    如果数据是有 runtime tag 的话，
    就不需要额外的 metadata。

    即使只是思考标记的过程（不是真的实现三染色算法），
    也可以有三染色的概念：

    - 白色：未访问
    - 灰色：在 queue 中
    - 黑色：已经访问过

    对于图的遍历算法，
    这种用染色辅助思考的方式都适用。

- 分析两种方案的优点和缺点：

  - 引用计数：

    - 不用 stop the world。
    - 实现简单。
    - 但是没法处理 circle。

  - 标记清扫

    - 可以处理 circle。
    - 有很大的设计空间可以探索各种算法。
    - fast mutator，不用处理 metadata。

- 下面主要讲不同类型的标记清扫 GC。

  - 保守 GC：

    主要是在没有 runtime tag 的情况下，
    很难区分 int 和 pointer，导致了有些「保守」。
    也就是有些 int 看起来像是 from-space 中的 pointer，
    也会被当成 pointer。

    老师说这是 GC 发展的后期才提出的方案，
    这个方案之后 GC 才有「精确」与「保守」之分。

    我觉得这个方案是完全可以接受的，
    因为为了精确地找到所有的 root，
    所付出的复杂度是非常高的。
    另外可以通过经常更换 to-space 的位置，
    让每次标记的过程所误判的 int 不一样。

    老师说保守 GC 的优点就是，
    适合在 C 和 C++ 这种没有 runtime tag 的语言中工作。

    - 这个前提是，你能知道 malloc 返回的值的区间。

    但是其实我们知道，有 runtime tag 的语言中，
    可能会为了优化而混用 tagged value 和 untagged value。
    所以也是需要保守 GC 的。

    老师举例说，
    apple 的 webkit js runtime 就是使用保守 GC 的。
    尽管 js 是需要 runtime tag 的语言。

    这里老师还引用了 boehm 的论文：

    > The wasted memory seems bad, but "Bounding Space Usage of
    > Conservative Garbage Collectors" by Hans-J. Boehm shows how data
    > structures often don't have such a big problem.

    boehm 的主页，以及 GC 有关的工作：

    - https://www.hboehm.info
    - https://www.hboehm.info/gc
    - https://www.hboehm.info/gc/bounds.html

    另外保守 GC 在 64-bits 机器上工作良好，
    但是可以想象在 32-bits 机器上就会有更多的误判了，
    真实的语言中确实出现过类似的情况，
    导致在 32-bits 机器上 GC 根本就没法正常工作。

    这其实是个挺令人担忧的 attack surface。
    所以要谨慎使用保守 GC。

    另外有一个重点是 保守 GC 不能是 copying GC，
    因为 copying GC 需要更新 root，
    而有可能误判 root 的时候是不能更新 root 的。

- 介绍并行 GC。

  首先是简单的 collector 自己并行。

  然后是 collector 和 mutator 并行。

  这种方案可以避免 stop the world，
  但是一个 collector thread
  可能会成为所有其他 thread 的瓶颈。

  老师介绍这种 GC 非常难实现。

- 介绍渐进 GC。

  比如三染色 GC。

- 介绍 copying GC。

  这是另一个区分 GC 的维度。

  这很适合用来分配长度可变的内存，
  因为在解决自动 free 问题的同时，
  还解决了内存碎片化的问题。

- 介绍 generational GC。

- 画一个树状图总结 GC 的类型。

# lesson 11.1 -- dynamic compilers

[2025-10-19]

[ [Webpage](https://www.cs.cornell.edu/courses/cs6120/2020fa/lesson/11/) ]

- 这节课的主题是 JIT。

- 介绍 ahead of time 和 just in time 的区别。

  我觉得传统的编译器才是神奇所在。
  JIT 像是为了解决动态类型语言而做的 ad-hoc 设计。

- AOT 的优点是简单。

  JIT 的最重要优点是有更多优化的机会。

  - 那么 JIT 可否解决 tagged value 的 inject 和 project 优化问题？

  但是主要还是因为动态语言太难静态编译了。

  毕竟，如果想要利用程序运行时的行为来优化程序，
  可以用测试，不一定要用 production。

- 这里老师澄清了解释器与编译器只是实现方式，
  语言本身经常可以用多种方式实现。

- 介绍 JIT 的实现方式，
  要同时实现解释器与编译器，
  然后在二者之间切换。

  但是不学习具体的实现方式，
  我还是不知道如何从编译器好的程序切换回到解释器。

  难道是类似用 predicate 实现 generic dispatching？

- 给出一个 tracing JIT 的例子。

  tracing 的意思就是只是为 control flow graph 中的一个 path 而做编译，
  在这个过程中，可以假设所有使得 flow 选择了这个 path 的条件都满足，
  利用这些条件来优化编译器。

  control flow graph 中的 branch 会被翻译为 guard，
  如果偏离这个 path 了，就直接退出，然后重新回到解释器。

  这样看好像确实有点道理。

- 这看起来好像可以解决 tag 的 inject 和 project 问题，
  因为很多 control flow graph 中的 branch 都是根据类型来的。

- 这么看 JIT 对于我之后想要给 x-lisp 实现的
  generic dispatching 而言也很重要。

# lesson 11.2 -- Tracing via Speculation

[2025-10-20]

- 这节课的作业就是为 bril 写 tracing JIT。

- 这用到了我之前看 bril 文档没看懂的 speculation 功能！
  原来是为了实现 JIT 而设计的。

- [Bril / Speculative Execution](https://capra.cs.cornell.edu/bril/lang/spec.html#speculative-execution)

   - `(speculate)` 进入 speculative execution context，
     也就是说实现的时候可能需要一个新的 frame，
     像函数的 context 一样。

     对于寄存器和栈中的变量可以如此，
     但是对于内存的 side-effect 就不能用类似函数的 context 来实现了。

     可能需要模仿 CPU 的 write buffer。

   - `(commit)` 结束 execution context，commit side-effects。

   - `(guard condition label-on-abort)`
     如果 condition 失败就不要 commit，
     直接 jump 到 about。

- 老师说这个功能是在模仿 CPU 中的 speculative execution，
  尽管一般的 ISA 不会暴露相关的 instruction，
  我们还是可以在 IR 中实现这个功能来方便 frontend 实现 JIT。

- 介绍 bril 的 speculative execution 扩展。

  我还没有实现 bril（或者说我的 basic-lisp），
  但是这里可以看出来 bril 可以让我们轻易地实验一些新的 idea。
  这非常不错。

- 关于 speculative execution 的实现：

  - 要支持 speculative context 的嵌套。
  - 不支持 rollback 对内存的修改。

- 下面讲如何做 tracing。

  直接修改 bril 的解释器，
  使得它可以在运行时记录下来运行的 instruction 就可以了。

  也就是说 JIT 可以完全在 backend 实现！

- 这节课的作业就是实现基于 tracing 的优化。

  tracing 出来的 instruction list 本身可能就可以被已有的 pass 优化了，
  只不过之前在 control flow graph 中这些 pass 没机会运行。
