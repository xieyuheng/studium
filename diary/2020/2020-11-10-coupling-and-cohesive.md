# Coupling and Cohesive

2020-11-10

Continued Learning: The Beauty of Maintenance - Kent Beck
- https://www.youtube.com/watch?v=3gib0hKYjB0

Kent Beck 的讲解很生动很难忘记。

coupling(A, B, d) = change(A, d) -> change(B, d)

cohesive(element) = sub element all change together

// Because the code is structured so that
// elements change together are
// placed under the same super elements.

我想要一个编程风格，使得我们能够更容易地回答以下问题：
- 这个数据类型的名字应该是什么？
- 这个函数的名字应该是什么？
- 这个函数应该写在那个文件里？

尽管我们可以给出 pattern，
但是想要回答好上述问题，
还是需要具体情况具体思考。

所以不止是给出 pattern，
而是通过 value -> principle -> pattern 这三个层次的「解决方案选择指南」，
来帮助我们回答上述问题。
