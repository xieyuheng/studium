# About Implementing Arrow and Pair Directly

2020-10-05

当已经有了 Pi 这个类型，
Arrow 就应该被实现为它的语法糖，
而不应该再直接实现。
（Sigma 与 Pair 也一样）

因为直接实现 Arrow，就需要处理 Arrow 与特殊的 Pi 之间的等价。

这里发现的原则是：
- 设计一组 core types；
- 利用 core types 来表示其他 types。

这样可以简化类型之间等价关系，因为需要 normalize 的东西更少了。

在实现 fulfilling type system 的时候，我们也应该注意这一点。
