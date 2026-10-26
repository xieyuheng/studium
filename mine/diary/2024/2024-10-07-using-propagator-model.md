---
title: Using Propagator Model
date: 2024-10-07
---

看了一个有趣的演讲 [Jailbreaking the Simulation with George Hotz | SXSW 2019](https://www.youtube.com/watch?v=ESXOAJRdcwQ)。

其中有观众问，
如果有 upper world 的 programmer，
他们用的程序语言和编译器是什么？
George Hotz 肯定也不知道，
但是他的回答中提到了 Coq 和 Isabelle，
并且说未来的编译器可能是用搜索实现的。

我发现在程序语言这个领域，
能用 propagator 来解决的问题不只有类型检查，
可能还有编译等等。

这样我对 propagator 的兴趣就更大了，
因为它潜在的实用性更强了。

# pure propagator language

直接设计 pure propagator model 的语言如何？
不是浅嵌入在 JS 中，而是深嵌入在 JS 中，
有专门为构造 propagation network 优化的语法。
并且可以用模块系统与 JS 沟通，
比如用 JS 扩展 cell 所能 merge 的数据，
以及扩展新的 primitive propagator。

有一个问题是类型系统。
我想如果是 pure propagator model，
就算是动态类型也是可以接受的。

如果为 pure propagator model 设计类型系统，
类型系统应该描述的是什么（比如 inet 描述的是构造图时候的接口）？

- propagator 的参数类型。
- cell 所能接受的数据类型。
- cell 本身的 generic 功能，比如 supported value 和 TMS。

类型系统在于排除类型错误，
其原理是两层计算，在原有计算之上外加一层计算，
上层计算可以在运行之前，找出下层计算的错读。
那么对于 propagator network 来说，
上层计算是什么？
应该也是 propagator network，
而不是传统的基于 expression 的类型检查器。

也许与 inet 构造图时所描述的接口类型一样，
这里也描述的是构造 pnet 时的接口类型，
类型检查就是检查接口是否匹配。

- 一个 cell 可能保存 cell 和 propagator，
  这样就可能有 high-order type。

- 对于这种类型系统过来说 dependent type 是什么？
  TODO 可以先以 inet 为例子来思考。

  - [2024-10-21] 可能反而是 propagator 的 dependent type 比 inet 要简单。

# 类型作为接口

这段思考的顺序是：接口 -> 差异 -> 认知。

在考虑用图（尤其是某种二分图）作为程序语言的语法时。
类型就是接口，接口就像是真正意义上物理线缆插头的接口。

此时，简单类型与依赖类型作为接口的差异很大。
简单类型看起来更像是这种物理接口，
而依赖类型看起来更像是逻辑命题。

为什么像是逻辑命题，
因为一组接口中的后一个 port 的类型，
可能依赖前一个 port 所连接的东西，
并且代表返回值的 port
所描述的是一种需要被满足的约束条件（比如带有等词等谓词）。

简单与依赖，
人们对这两种接口的认知也不同。
对于简单接口，就是已经有的组件上面有伸出来线缆，
线缆上的 port 的类型限制了线缆连接的方式。
是先有组件，后有接口类型。
而对于依赖类型，是先有接口类型，
然后要构造满足类型的组件，
并且要满足返回 port 所代表的约束条件。

# Network 之间的等价

在用两层运算来实现 propagator 的类型系统时，
如果是依赖类型，上层类型运算也是能带有任意运算的。
此时要判断类型是否相等，
就要判断两个 propagator network 是否等价。
如何判断 network 之间的等价呢？

- [2024-10-15] 可以尝试在 inet 中先解决这个问题。
