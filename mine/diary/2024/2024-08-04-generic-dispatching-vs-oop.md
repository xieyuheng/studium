在实现 propagator 的时候，
Sussman 同时介绍了大量使用 generic dispatching 的编程风格，
使用这种风格看来是必要的，
因为 merge 之类的二元函数要根据参数类型的不同来 dispatching，
OOP 中一个 message 发给不同类型的 object 效果不同，
是一元函数的 dispatching，根本就不是为多元函数的情狂设计的。

如何设计一个简单类型的带有 generic dispatching 的语言呢？

- 如何 import 一个 generic 的不同 handlers？

cicada 是否应该设计为 generic dispatching 而不是 OOP 呢？

- 如果是，也许就应该回到 sexp 的语法。
