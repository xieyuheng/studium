---
title: 我为什么 LISP
date: 2026-10-26
cover-image:
  src: ../assets/images/logo/red-lisp.png
  alt: red lisp logo
---

# 问题

我想要设计很多形式语言，
来探索不同的形式系统与各种计算模型。

比如：

- Curry 的组合子模型（Combinatory Logic）
- Sussman 的[传播子模型（Propagator Model）](https://github.com/xieyuheng/propagator.js)
- Lafont 的[反应网模型（Interaction Nets）](https://github.com/xieyuheng/inet-lisp)
- Milner 的 π 演算（π Calculus）
- [逻辑式编程（Logic Programming）](https://github.com/xieyuheng/chimera)
- 线性逻辑（Linear Logic）
- [依值类型论（Dependent Type Theory）](https://github.com/cicada-lang/cicada-plct)
- 拓扑学中的[胞腔复形（Cell Complex）](https://github.com/xieyuheng/cell-complex)
- [形式概念分析（Formal Concept Analysis）](https://github.com/xieyuheng/conceptor)

等等等等……

长远看来，
如何让这些语言设计方面的探索更高效？
如何让语言设计的想法与实现之间的循环更短？

LISP 就是这个问题的解决方案之一。

# 什么是 LISP

LISP 不是一个单一的编程语言，
而是一个语言家族。

从 1958 年 LISP 初创到现在的几十年间，
人们不断地在 LISP 的语法框架内设计新语言。

在长期的大量的语言设计实践中，
人们摸索出了很多技巧，
方法逐渐系统化。

语言设计变得越来越简单，
形成了强大的 LISP 传统。

> 实践、认识、再实践、再认识，
> 这种形式，循环往复以至无穷，
> 而实践和认识之每一循环的内容，
> 都比较地进到了高一级的程度。
>
> ——毛泽东《实践论》

到了后期，对某些老师傅而言，
简洁优雅地设计 LISP 语法，
已经到了信手拈来的程度。

下面我带你看一下 LISP 语法设计的例子，
重点不在于理解具体的语法，
而在于体会这个「信手拈来」。

## 逻辑式语言 / MiniKanren

Dan 和 Byrd 在 "The Reasoned Schemer" 一书中，
把逻辑式语言「浅嵌入」在 Scheme 里：

```scheme
(define appendo
  (lambda (target rest out)
    (conde
      ((nullo target) (== out rest))
      ((fresh (head tail almost)
         (conso head tail target)
         (conso head almost out)
         (appendo tail rest almost))))))
```

上面的例子使用了三个新的语法关键词：

- `==` -- 两个逻辑项的 unification。
- `conde` -- 类比 Scheme 的分支语法 `cond`。
- `fresh` -- 引入新的逻辑变元。

```scheme
(== <exp> <exp>)
(conde (<exp> ...) ...)
(fresh (<name> ...) <exp> ...)
```

所谓「浅嵌入」就是让新加入语言的元素，
能够与语言中已有的元素和谐共存。
这里新加入的是「逻辑式的关系」，
语言中已有的元素是「函数」。

下面又使用了两个新语法关键词：

- `run*` -- 返回所有结果满足关系的结果。
- `run` -- 返回前几个满足关系的结果。

```scheme
(run* (<name>) <exp> ...)
(run <count> (<name>) <exp> ...)
```

```scheme
(run* (q) (appendo '(1 2) '(3 4) q))
;; => ((1 2 3 4))

(run* (q) (appendo q '(3 4) '(1 2 3 4)))
;; => ((1 2))

(run* (q)
  (fresh (a d)
    (== q (list a d))
    (appendo a d '(1 2 3))))
;; => ((() (1 2 3))
;;     ((1) (2 3))
;;     ((1 2) (3))
;;     ((1 2 3) ()))

(run 3 (q)
   (fresh (s)
     (appendo q s '(1 2 3))))
;; => (()
;;     (1)
;;     (1 2))
```

## 依值类型系统 / Pie

Dan 和 David 在 "The Little Typer" 一书中，
在 LISP 语法框架内，设计了依值类型系统的语法：

- `claim` -- 与 `define` 相对称，来声明类型。
- `check-same` -- 随手设计的语法，专门用来做测试。
- `iter-Nat` -- `Nat` 的组合子。
  类似的组合子还有：
  - `which-Nat`
  - `rec-Nat`
  - `ind-Nat`

```scheme
(claim double
  (-> Nat Nat))
(define double
  (lambda (n)
    (iter-Nat n
      zero
      (lambda (prev)
        (add1 (add1 prev))))))

(check-same Nat (double 0) 0)
(check-same Nat (double 1) 2)
(check-same Nat (double 2) 4)
(check-same Nat (double 3) 6)
```

下面用数学归纳法证明
`double` 与 `twice` 这两个函数等价：

- `->` -- 函数类型。
- `Pi` -- 依赖函数类型。
- `=` -- 等价类型。
- `the` -- 局部的类型声明语法。
  起源于 common-lisp 的语法，
  灵感来自英文的同位语结构（Apposition），
  比如："The capital of France, Paris, is a beautiful city."
- `cong` & `replace` -- 两个用来表达「等量代换」的语法。

```scheme
(claim twice
  (-> Nat Nat))
(define twice
  (lambda (n)
    (+ n n)))

(claim add1+=+add1
  (Pi ((n Nat)
       (j Nat))
    (= Nat (add1 (+ n j)) (+ n (add1 j)))))
(define add1+=+add1
  (lambda (n j)
    (ind-Nat n
      (lambda (n)
        (= Nat (add1 (+ n j)) (+ n (add1 j))))
      (the (= Nat (add1 (+ zero j)) (+ zero (add1 j)))
        (same (add1 j)))
      (the (Pi ((prev Nat))
             (-> (= Nat
                   (add1 (+ prev j))
                   (+ prev (add1 j)))
                 (= Nat
                   (add1 (+ (add1 prev) j))
                   (+ (add1 prev) (add1 j)))))
        (lambda (prev)
          (lambda (prev-add1+=+add1)
            (cong prev-add1+=+add1 (+ 1))))))))

(claim step-twice=double
  (Pi ((prev Nat))
    (-> (= Nat
          (+ prev prev)
          (double prev))
        (= Nat
          (add1 (+ prev (add1 prev)))
          (add1 (add1 (double prev)))))))
(define step-twice=double
  (lambda (prev)
    (lambda (prev-twice=double)
      (replace (add1+=+add1 prev prev)
        (lambda (n)
          (= Nat
            (add1 n)
            (add1 (add1 (double prev)))))
        (cong prev-twice=double (+ 2))))))

(claim twice=double
  (Pi ((n Nat))
    (= Nat (twice n) (double n))))
(define twice=double
  (lambda (n)
    (ind-Nat n
      (lambda (n) (= Nat (twice n) (double n)))
      (same zero)
      step-twice=double)))
```

## 代数数据类型 / Algebra Data Type

TODO

# 编程语言的设计语言

设计在于选择。

在设计领域，
我们用「设计系统」（Design System）
或者说「设计语言」（Design Language），
来把连续的选择离散化，
把繁杂的选择简单化。

LISP 对于形式语言设计，也是如此。
LISP 因此成为了一种编程语言的设计语言。

一种模式语言（Pattern Language）。

> Each pattern describes a problem which occurs over and over again in our environment, and then describes the core of the solution to that problem, in such a way that you can use this solution a million times over, without ever doing it the same way twice.
>
> -- Christopher Alexander, A Pattern Language (1977)

果真如此的话，
对于 LISP 语法设计而言，
具体的「模式」是什么呢？

首先，设计不是为了好看，是为了解决问题的。

语法设计所解决的问题是：
如何用文本的表面的形式差异，
来表达语义差异。

早期 LISP 的每一个语法设计，
都是为了解决一个新的语义差异的表达问题。

比如 TODO

以 `lambda` 为范式，
下列语法都是在引入新的约束变量（Bound Variable）：

```scheme
(lambda (<name> ...) <body>)
(fresh (<name> ...) <body>)
(run* (<name>) <body>)
(run <count> (<name>) <body>)
```

以 `let` 为范式，
下列语法都是在引入新的变量与值的绑定（Binding）：

```scheme
(let ((<name> <exp>) ...) <body>)
(let* ((<name> <exp>) ...) <body>)
(letrec ((<name> <exp>) ...) <body>)
(Pi ((<name> <exp>) ...) <body>)
(Sigma ((<name> <exp>) ...) <body>)
```
