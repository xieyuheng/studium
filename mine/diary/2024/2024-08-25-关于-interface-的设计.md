---
title: 关于 interface 的设计
date: 2024-08-25
---

interface 是越简单越好，
但是什么是简单呢？

在实现 "Building Problem Solvers" 一书中的 Problem Space Model 时，
我发现，为了设计简单的 interface，不应该指定其中函数 refactoring 的方式。

比如，想要的是：

```typescript
sprout: (node: Node) => Array<[Edge, Node]>
```

不应该分解为：

```typescript
sproutEdge: (node: Node) => Array<Edge>
edgeApply: (edge: Edge, node: Node) => Node
```
