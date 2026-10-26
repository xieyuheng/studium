# JSON vs XML

2020-09-28

## 类型系统差异

JSON 是 structural typing，其类型为 record type 的形式，可以不包含「类型名」信息。

XML 是 nominal typing，其 tag 就是「类型名」。

## Self Describtive

JSON 的 structural typing (record type) 是 self describtive 的，
即，其类型中包含了 field 的名字，
而 ADT 中的 product type 不包含 field 的名字。

## 「架构化程度」

XML 的架构化程度比 JSON 更低，可以这样理解这一事实：
- XML 更容易写易读，比如列表时，不用像 JSON 一样用 Array，只需要列举同类 element。

JSON 的架构化程度比 XML 高，可以这样理解这一事实：
- 想要设计 XML 的 schema 语言，需要用到正则表达式；
- 想要设计 JSON 的 schema 语言，只需要简单的 record type system。

「易写易读」是相对主观的不重要的性质，
用 JSON 和 YAML 也不对「易写易读」之特性有太大损失。

「结构化程度高」是更重要的性质，
因为这样设计的 API 程序员使用起来更方便。
我们要优先考虑程序员的需求与体验。

## 「结构」与「排版」两种 markup

XML 的错误在于，它想要一次解决两个问题。
一、结构 markup；
二、排版 markup。

用基于 JSON 的解决方案时，
可以只用 JSON 做结构 markup，
将排版 markup 留给专门的 inline 语言。
