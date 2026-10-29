　

## 简介

姓名：谢宇恒
出生：一九九二年
邮箱：[xyheme@gmail.com](mailto:xyheme@gmail.com)
主页：[xieyuheng.com](https://xieyuheng.com)
　
　

## 学历

2015：学士，数学与应用数学，中山大学。
　
　
## 经历

### 独立开发者，2025 - 2026

休息一段时间，完成自己一直想做的项目。

首先探索了基于「反应网」这个计算模型，
来设计编程语言 inet-lisp：

<https://github.com/xieyuheng/inet-lisp>

- 想要探索用到所有 CPU 核心的并行计算。
- 最终发现瓶颈在于内存带宽。
  由于内存带宽的限制，
  并行程序的运行效率没法随着 CPU
  核心数量的增长而线性增长。

然后经历了一年多，
初步完成「元语 / meta-lisp」：
<https://lisp.xieyuheng.com>
一个带有静态类型的极简 LISP。

- 编译到一个我设计的虚拟机。
- 支持 markdown 文学式编程。
- 支持纯中文编程。

　　
　

```meta-lisp
(模块 数学)

(声明 阶乘 (-> 整数型 整数型))

(定义 (阶乘 数)
  (若 (整数小于等于 数 1)
    1
    (整数乘 (阶乘 (整数减 数 1)) 数)))

(定义测试 阶乘测试
  (断言相等 1 (阶乘 0))
  (断言相等 1 (阶乘 1))
  (断言相等 2 (阶乘 2))
  (断言相等 6 (阶乘 3))
  (断言相等 24 (阶乘 4))
  (断言相等 120 (阶乘 5))
  (断言相等 720 (阶乘 6)))
```

AI 时代来临了。
学习如何实现智能体，
并实现了一个自己的编程智能体，
风铃智能体：<https://windbell.xieyuheng.com>

- 我日常开发都用自己的这个智能体。

![](https://img.xieyuheng.com/windbell/2026-10-23--13-46-55.png)
![](https://img.xieyuheng.com/windbell/2026-10-23--14-01-17.png)

### 量匠科技（Quantisan），深圳，2023 - 2024

算法工作：

- 基于逻辑式编程语言 prolog，
  实现机械零件模型的特征描述与识别。

前端工作：

- 实现工厂「工艺库」网站。
- 基于「特征识别」算法，实现工序自动分析网站。

![](https://img.xieyuheng.com/quantisan/20241014-075647.jpg)
![](https://img.xieyuheng.com/quantisan/20241014-075707.jpg)
![](https://img.xieyuheng.com/quantisan/20241014-075946.jpg)
![](https://img.xieyuheng.com/quantisan/20241014-080016.jpg)
![](https://img.xieyuheng.com/quantisan/20241014-080024.jpg)
![](https://img.xieyuheng.com/quantisan/20241014-080136.jpg)
![](https://img.xieyuheng.com/quantisan/20241014-080139.jpg)
![](https://img.xieyuheng.com/quantisan/20241014-080341.png)

### 中国科学院软件研究所 PLCT 实验室，远程，2022-08 - 2022-10

以 Cicada 项目为基础，和四名全国各地的大学生一起探索 Dependently Typed 程序语言。
通过讲解 Cicada 的实现，使得四名优秀的学员都学会了如何实现 Dependent Type。
我们一起阅读论文，探索并实现了一些高阶功能，比如 Implicit Argument 等等。

- [Cicada 项目代码](https://github.com/cicada-lang/cicada-plct)
- [项目在 PLCT 的招聘信息](https://github.com/plctlab/weloveinterns/blob/master/open-internships.md#bj61-%E8%9D%89%E8%AF%AD%E5%AE%9E%E4%B9%A0%E7%94%9F%E6%9A%82%E5%81%9C%E6%8B%9B%E8%81%98)

### 维格智数（Vikadata），深圳，2022-04 - 2022-07

- 实现维格表日常的前端需求 -- React

### XMind，深圳，2021-12 - 2021-12（两周）

完成了一个产品原型，可以以简洁优雅的语法，
将思维导图作为 List Item 的扩展，编写在 Markdown 中。
在 Demo Web App 中填写 Markdown 文件在 GitHub 的地址，
就可以拉取内容并且渲染成美丽的带有思维导图的页面，
其中的思维导图带有丰富的交互。

![](https://img.xieyuheng.com/xmind-list-item/1.png)

### 风变科技，深圳，2019-12 - 2021-11

- 设计交互式课程创作平台「课灵小馆」。
  - 交互式课程技术架构与 DSL，在线编辑与预览。
- 基于「课灵小馆」平台，设计「卡牌」系统，
  以卡牌的形式收集和检索公司运营中的转化案例。
  - 为自己设计的平台创作内容，真的很有趣。

![](https://img.xieyuheng.com/clepub/1.jpg)
![](https://img.xieyuheng.com/clepub/2.jpg)
![](https://img.xieyuheng.com/clepub/3.jpg)
![](https://img.xieyuheng.com/clepub/4.jpg)

- 设计「思索」项目，
  以探索「主动回顾」和「间隔重复」
  等等认知心理学技术在教学中的应用。

![](https://img.xieyuheng.com/sisuo/mobile/1.jpg)
![](https://img.xieyuheng.com/sisuo/mobile/2.jpg)
![](https://img.xieyuheng.com/sisuo/mobile/3.jpg)
![](https://img.xieyuheng.com/sisuo/mobile/4.jpg)

![](https://img.xieyuheng.com/sisuo/desktop/1-1.png)
![](https://img.xieyuheng.com/sisuo/desktop/1-2.png)
![](https://img.xieyuheng.com/sisuo/desktop/1-3.png)

附注：在上述项目中，我负责产品设计以及全栈开发，制定技术构架 并带领 3 - 4 人的全栈团队。

![](https://img.xieyuheng.com/forchange/xieyuheng-1.jpg)

感谢风变的同伴们，我向你们学到了很多。

### 特航航空，深圳，2019-06 - 2019-08

- 函数式编程内部培训 -- Typeclass，Monad
- 第三方数据同步 -- Scala，Akka stream，PostgresSQL
- 国际酒店 API -- Scala，Akka stream，Akka HTTP

### 长春理想科技研发部，北京，2018-11 - 2019-02

- 数据挖掘算法 研究，设计与实现 -- Python，Pandas，Numpy
- 主要研究题目包括:
  - 异常检测 / Anomaly detection
  - 根因分析 / Root cause analysis

### InBlockchain.com，北京，2018-03 - 2018-05

- Web 后端 -- Elixir，Phoenix (Web Framework)
- GraphQL API -- PostgresSQL，Absinthe GraphQL

### RiceQuant.com，深圳，2017-10 - 2018-01

- 爬虫 -- Python，Scrapy，MongoDB
- Web 后端 -- Python，Flask
- 量化交易策略 -- Python，Pandas，Numpy

## 授课

实验性的汇编语言实验，中山大学，2015。

- [课程主页](http://the-little-language-designer.github.io/cicada-nymph/course/contents.html)
- [课程小组](https://github.com/the-little-language-designer)

## 论文

2019-05-08：[A Recursive Combinatorial Description of cell-complex](https://github.com/xieyuheng/studium/blob/master/mine/a-recursive-combinatorial-description-of-cell-complex.md)

2016-06-14：Function compose, Type cut, and The algebra of logic

- ICFP，Scheme Workshop，2016，日本奈良
