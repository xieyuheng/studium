# Programming Language Keywords

2020-09-16

关于程序语言 keyword 的选择，
假设用 keyword 来做前缀，
不同的 keyword 前缀后面可以跟着不同的语法。

## 类型声明与定义

要选取长度一样的英文词，以保持排版优美，
为了这个目的而列表如下：
|----------|---|----|-----|------|-------|--------|---------|----------|
| 长度     | 1 | 2  | 3   | 4    | 5     | 6      | 7       | 8        |
|----------|---|----|-----|------|-------|--------|---------|----------|
| 函数定义 | = | fn | def | make | proof | define | meaning | function |
|          |   |    | let |      | prove | create |         |          |
|          |   |    | val |      | value |        |         |          |
|          |   |    | var |      | build |        |         |          |
|----------|---|----|-----|------|-------|--------|---------|----------|
| 类型声明 | : | ty |     | type | claim | affirm | theorem | proclaim |
|          |   |    |     | prop | judge | assert | confirm | announce |
|          |   |    |     | spec | state | verify | declare |          |
|          |   |    |     | anno |       | belief |         |          |
|          |   |    |     | aver |       |        |         |          |
|----------|---|----|-----|------|-------|--------|---------|----------|

## 分支与循环

2020-09-19

分支与循环相关的关键词，可能不需要对齐，但是也列表如下：

|------|---|---|-----|------|-------|--------|---|---|
| 长度 | 1 | 2 | 3   | 4    | 5     | 6      | 7 | 8 |
|------|---|---|-----|------|-------|--------|---|---|
| 分支 |   |   |     | case | match | branch |   |   |
|      |   |   |     |      |       | choice |   |   |
|      |   |   |     |      |       |        |   |   |
|      |   |   |     |      |       |        |   |   |
|------|---|---|-----|------|-------|--------|---|---|
| 循环 |   |   | rec | loop |       |        |   |   |
|      |   |   | jmp | goto |       |        |   |   |
|      |   |   |     | jump |       |        |   |   |
|------|---|---|-----|------|-------|--------|---|---|

## About `:` and reversed `:`

2020-10-09

We use `--` for reversed `:`.

`:` might be read as
- is an element of
- is a member of
- belongs to
- is in

In set theory, the symbol "∈" is a stylized lowercase Greek letter epsilon ("ϵ"),
the first letter of the word ἐστί, which means "is".

`--` might be read as
- contains
- includes (also used for subset[superset] relation)

- We call a string of more then one dash `-` a "dashline",
  for example `--`, `---`, `----`, ...
  - https://en.wikipedia.org/wiki/Dash
  - https://en.wikipedia.org/wiki/Hyphen

## 关于「属性」的术语

2020-11-13

「对象的属性」中的「属性」

- property, properties
- attr, attribute -- used in formal concept analysis
- field -- only for non-function properties
- method -- only for function properties
- feature -- used in UML
- operation -- used in UML -- only for function properties
- association -- used in UML -- another way of drawing attributes
