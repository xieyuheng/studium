---
title: return value by output parameter
date: 2024-12-30
---

在读 "Programming a problem-oriented-language" 时，
发现 Charles H. Moore 关于 stack 有如下观点：

> It is important not to attempt to combine the return stack and the
> parameter stack. They are not synchronized.

其实 C 的 calling convention 确实是
combine the return stack and the parameter stack，
并且这种设计对于实现局部变量而言很有用，
可以在实现局部的 memory allocator，而不用垃圾回收器。

这么看来 C 的设计也是很好的。

设想实现一个类似 C 的语言，
没有 malloc，通过额外的 pointer 参数来实现返回值。
如果给函数标记出来 input 和 output 参数，
就可以实现下面的效果：

```c
infer: (ctx: ctx_t, exp: exp_t) -> (type: value_t);

// given:
// ctx: ctx_t
// exp: exp_t

type: value_t = infer(ctx, exp);

// the same as:

type: value_t;
infer(ctx, exp, type);
```

好像甚至不需要 `*` 和 `&`，
所有的参数都是 reference 而不是 value。

在设计 propagator 和 inet 的函数作用语义时，
我都用到了类似的技巧。
