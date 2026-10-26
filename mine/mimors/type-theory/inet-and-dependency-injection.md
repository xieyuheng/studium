如何在 inet 中实践 OOP 的 dependency injection？

可能要模仿 FP 的 Algebraic effect，
一个模块 M 只能处理 a，
不是给 b 和 c 实现 M，
而是先把 b 和 c 转化成 a，
再用 M 处理。
