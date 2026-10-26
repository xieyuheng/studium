我想第一个适合用 mimor 来学习的 topic 就是 inet 与 linear logic，后者是 inet 的类型系统。

想要探究的最终问题是，如何把辅助证明语言的实现变简单。

而 inet 能帮助解决这个问题在于它的 normalization 有很好的性质。

实现它是不需要 closure 的吗？

一般的语言实现中，会有 expression 与 value 之分，前者为语法，后者是解释的结果，为语义。此时 evaluate 作为函数是从 expression 到 value 的。

在 the little typer 中，everything is expression，evaluate 作为函数是从 expression 到 expression 的。这样没有 expression 和 value 之分，是十分易于理解的，但是假设真就这么实现，真相不用 closure 和 value，那么就要在 substitution 的时候十分小心，并且 substitution 本身有很多重复的计算与生成新表达式。

在 inet 中，语法是用来以线性的方式构造 graph 的，而 reduce 作为函数是从 graph 到 graph 的。此时和上面的情况一样，在关键的地方仍然没有两个类型之分，仍然是易于理解的，但是却没有上面所说的缺点了。
