---
title: reverse little book
date: 2026-11-15
---

# 动机

[2026-11-15] 重读 "The Little Learner" 过程中，
尝试逆向工程 dan 写 little book 的过程。

- 提出假说，构建思想模型
- 推理，验证模型的结论
- 实践检验，看能否利用模型写出新的 little book

# 结构分析

在 "The Little Learner" 的前言中，
作者介绍了 little book 的部分结构：

> We also categorize key properties as either _rules_ or
> _laws_. Our rules are about the structure of entities,
> like their sizes. Our laws, on the other hand, are about
> the behavior of entities, like their equalities and
> invariants.

- 结构性的知识被总结为 rule
- 行为性的知识被总结为 law

分析 little book 也可以如此。
首先识别并命名构成 little book 的元素是什么，
元素的属性以及元素之间的关系就是结构性的知识。

最显而易见的结构是：

- chapter
  - frame
    - question
    - answer
  - rule
  - law
- interlude -- where we temporarily shift our focus.
- appendix

除了这些还有别的文本内的元素，
比如：

- nuggets（代码块）
- same-as-chart

另外还有一个在 little book 中，
被反复强调的关于篇章形式的重点：

> Because you may find that either this topic or our
> exposition method is new, you should read each chapter
> until you fully understand it. This might lead to reading
> some chapters more than once. Do not move forward until
> the chapter being read is completely clear. Be
> determined. Work with others and discuss with them what
> has been unclear until each little piece falls into place.
>
> It is futile to read ahead in a “Little” book because
> everything is structured in _exactly_ the right order as a
> sequence of numbered _frames_ separated by horizontal
> lines. Each chapter is fairly short and has lots of _white
> space_, so rereading an entire chapter carefully to regain
> a lost thread is not unreasonable.

也就是：

- 顺序性：
  所有文本必须按照顺序阅读，
  因为后面的文本依赖于前面的文本。

- 局部性：
  比如，用到 cons 才解释 cons。
  当然也有在一开始就给出一个术语对照表的情况。
  比如 "Transcribing to Scheme" 中，
  书中符号与 scheme 代码的对照表。

我们可以把 frame 序列当成一个符号过程来分析，
并且给 interpretant 分类，
也就是给「从已有内容生成新内容」的方式分类。

# 文字游戏

尽量找教学内容可以自然引出的文字游戏。

比如，在参数命名上，`θ` 代表 parameters，
`Θ` 代表 accompanied parameters。

由于小写的 theta 到大写的 theta 像是「吹气球」，
所以两个方向的转化函数分别叫做 "inflate" 和 "deflate"。

所设计的接口函数是 "inflate" "deflate" "update"，
都有后缀 "-ate"，而这个接口参数是需要「先引入，后解释」的，
读者总是在「期待」这个解释，所以就引入了 "grate expectations"，
另一个以 "-ate" 为后缀的词组（来自狄更斯的小说《远大前程》），
来描述读者的期待。

又比如介绍梯度下降的章节叫做 "Slip-slidin’ Away"，
是 保罗·西蒙 一首歌的名字，
而后续的介绍梯度下降的技巧的章节叫做
"The Nearer Your Destination, the Slower You Become"
是这首歌的一句歌词的变体。

这些文字游戏对于教学内容而言是装饰性的，
但是又像是教学旅途中的有趣风景，
让读者能回忆起这次旅行。

每一章的标题都与其内容有关，
但是单单看标题，一般猜不出这一章的内容是什么。
每一章的标题都是一个小谜题。

这些文字游戏也让人想起 Dan 是
"Gödel, Escher, Bach: an Eternal Golden Braid"
的作者 Douglas Hofstadter 的朋友。

而 GEB 的核心概念是「怪圈」（Strange Loop）：

> 一个系统如何通过自身的规则，
> 反过来谈论或指涉自身，
> 从而创造出超越规则本身的意义。
