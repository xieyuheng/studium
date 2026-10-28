# c4model

2020-09-16

今天看了：https://c4model.com

C4 provides a model to describe the static structures that make up a software system.

它将构架分为了四个层次的 diagram 来描述，
分别是：
- context -- software system in context
- container -- separately runnable/deployable unit
- component -- major structural building blocks (of container) and their interactions
  - 也许可以作为工作的单元，分配给团队成员。
- code -- class or datatype and function
  - 作者认为 object oriented programming
    与 functional programming 之间的术语对应如下：
    - component -- module
    - class ------ function

我想作者所描述的方法中，很有道理的一点可以用一句话总结，即：
想要描述复杂的构造，可以利用层次地叙述方式。
层次可以控制复杂性。

这里的复杂性指的是认知的复杂性，
正如度量算法复杂性的，是算法所消耗的资源（时间和空间），
度量这里所说的复杂性的，也可以称为「资源」，
即人的认知资源。

情报：
- 4+1 architectural view model: https://en.wikipedia.org/wiki/4%2B1_architectural_view_model
  - C4 model 的先驱
- Risk-storming: https://riskstorming.com
  - A visual and collaborative risk identification technique
- Notation, notation, notation: https://c4model.com/review
  - A software architecture diagram review checklist
- Structurizr: https://structurizr.com
  - a collection of tooling to create software architecture diagrams and documentation
    based upon the C4 model

## 只用 class 来做抽象

其中描述 code 这个层次的 diagram 时，
作者用到了 java 中的 interface 与 impl-class，
类似 typescript 中的 interface 与 class implement，
或者 typescript 中的 abstract class 与 class extends。

这种关系其实类似 class 与 instance。
语言中有很多形成这种关系的方式，
我们尝试只用 class 与 instance，
或者更极端地，我们用 higher function 与 function。

## 「属于」与「子类型」关系

其中描述 Container 与 Component 元素的时候，
没一个元素有 name 还有 technology。

比如：
- Web Application [Container: Nodejs]
- API Application [Container: Nodejs]
- Single-Page Application [Container: Sveltejs]
- Database [Container: PostgreSQL]

如何用类型与元素之间的「属于」关系，
以及类型与类型之间的「子类型」关系，
来更精确地描述上面的信息呢？

或者是：
- Web Application : Nodejs <: Container
- API Application : Container <: Nodejs
- Single-Page Application : Container <: Sveltejs
- Database : Container <: PostgreSQL

又或者是：
- Web Application : Nodejs : Container
- API Application : Container : Nodejs
- Single-Page Application : Container : Sveltejs
- Database : Container : PostgreSQL

「属于」关系可否带有传递性，
而被视为是特殊的「子类型」关系呢？

例如 cumulative universe type 就是这种情况的例子。
如果 U0 : U1，
那么 U0 的元素也被认为是 U1 的元素，
也就是说 U0 <: U1。

在 fulfilling type system 中这是自然的。

In fulfilling type system,
Every partly fulfilled object is a type,
to fulfill the object is to construct instance of the type.

## YAML and JSON based DSL for modelling

读 "Diagramming vs modelling" 一节：https://c4model.com/#Modelling
我们可以设计基于 YAML 与 JSON 的更好用的 DSL。
比如，我们可以用我们的 schema 为这里的 modelling 语言提供一个 model。
