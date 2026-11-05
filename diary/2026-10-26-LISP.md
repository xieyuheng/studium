---
title: 我为什么 LISP？
date: 2026-10-26
cover-image:
  src: ../assets/images/logo/red-lisp.png
  alt: red lisp logo
---

- [问题](#问题)
- [什么是 LISP？](#什么是-lisp)
  - [寄存器机](#寄存器机-register-machine)
  - [逻辑式语言](#逻辑式语言-minikanren)
  - [依值类型系统](#依值类型系统-pie)
  - [代数数据类型](#代数数据类型-algebra-data-type)
- [编程语言的设计语言](#编程语言的设计语言)
- [美学流派](#美学流派)
  - [极简](#极简-scheme)
  - [现代](#现代-clojure)
  - [形式极简](#形式极简-calcit)
- [尾声](#尾声)

# 问题

我想要设计很多形式语言，
来探索不同的形式系统与各种计算模型。

比如：

- Lafont 的[反应网模型（Interaction Nets）](https://github.com/xieyuheng/inet-lisp)
- Sussman 的[传播子模型（Propagator Model）](https://github.com/xieyuheng/propagator.js)
- Curry 的组合子模型（Combinatory Logic）
- [机器学习（Machine Learning）](https://github.com/xieyuheng/learner.js)
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

# 什么是 LISP？

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

下面我带你走马观花，
看一下 LISP 语法设计的例子。
重点不在于理解具体的语法，
而在于体会这个「信手拈来」。

## 寄存器机 / Register Machine

Sussman 和 Abelson 在 SICP 的第五章
"Computing with Register Machines" 中，
设计了一个描述 Register Machine 的语言。

接受输入并打印最大公约数（GCD）结果的机器：

```scheme
(controller
 gcd-loop
   (assign a (op read))
   (assign b (op read))
 test-b
   (test (op =) (reg b) (const 0))
   (branch (label gcd-done))
   (assign t (op rem) (reg a) (reg b))
   (assign a (reg b))
   (assign b (reg t))
   (goto (label test-b))
 gcd-done
   (perform (op print) (reg a))
   (goto (label gcd-loop)))
```

使用寄存器机语言描述用迭代实现的阶乘：

```scheme
(define (factorial n)
  (define (iter product counter)
    (if (> counter n)
      product
      (iter (* counter product)
            (+ counter 1))))
  (iter 1 1))
```

所对应的寄存器机语是：

```scheme
(define-machine factorial
  (register n product counter)
  (controller
    test-counter
      (test (op > (reg counter) (reg n)))
      (branch (label factorial-done))
      (assign product (op *) (reg counter) (reg product))
      (assign counter (op +) (reg counter) (const 1))
      (goto (label test-counter))
    factorial-done))
```

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
`double` 与 `twice` 这两个函数，
虽然定义方式不同，但是行为等价：

- `->` -- 函数类型。
- `Pi` -- 依赖函数类型。
- `=` -- 等价类型。
- `the` -- 局部的类型声明语法。
  起源于 common-lisp 的语法，
  灵感来自英文的「同位语」（Apposition），
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

在 "The Little Typer" 一书的附录 "The Way Forward" 中，
有几页纸最能体现「信手拈来」的感觉。
在这篇附录里作者想要介绍，
如何实现正文之外的更多语言功能。

想要实现「归纳数据类型」（Inductive Datatypes），
可以这样设计语法：

```scheme
(data List ((E U)) ()
  (nil ()
    (List E))
  (:: ((e E) (es (List E)))
    (List E))
  ind-List)

(data Less-Than () ((j Nat) (k Nat))
  (zero-smallest ((n Nat))
    (Less-Than zero (add1 n)))
  (add1-smaller ((j Nat)
                 (k Nat)
                 (j<k (Less-Than j k))))
    (Less-Than (add1 j) (add1 k))
  ind-Less-Than)
```

想要实现带有「模式匹配」（Pattern Matching）
的「递归函数」（Recursive Functions），
可以这样设计语法：

```scheme
(claim length
  (Pi ((E U))
      (-> (List E) Nat)))
(define length
  (lambda (E es)
    (match es
      (nil zero)
      ((:: x xs) (add1 (length xs))))))

(claim front
  (Pi ((E U)
       (n Nat))
      (-> (Vec E (add1 n)) E)))
(define front
  (lambda (E n es)
    (match es
      ((vec:: x xs) x))))
```

想要实现「隐式参数」（Implicit Arguments），
可以这样设计语法：

- 其中 `Pi*` 和 `lambda*` 用来引入隐式参数。

```scheme
(claim length
  (Pi* ((E U))
      (-> (List E) Nat)))
(define length
  (lambda* (E)
    (lambda (es)
      (rec-List es
        0
        (lambda (e es l)
           (add1 l))))))
```

不写第一个隐式参数的表达式：

```scheme
(length (:: 'potato (:: 'gravy nil)))
```

等价于：

- 在不使用隐式参数的情况下，完整的函数作用表达式。

```scheme
(length Atom (:: 'potato (:: 'gravy nil)))
```

表达式：

- 这里 `implicitly` 是专门给函数提供隐式参数的新语法。

```scheme
(implicitly length Atom)
```

等价于：

```scheme
(-> (List Atom) Nat)
```

想要支持「证明策略」（Proof Tactics），
可以这样设计语法：

```scheme
(claim even-or-odd
  (Pi ((n Nat))
      (Either (Even n) (Odd n))))
(define-tactically even-or-odd
  (intro n)
  (elim n)
  (apply zero-is-even)
  (intro n-1 e-or-o.n-i)
  (elim e-or-o.n-i)
  (then
   right
   (apply add1-odd->odd)
   auto)
  (then
   left
   (apply add1-odd->even)
   auto))
```

> Each pattern sits at the center of a network of connections which connect it to certain other patterns that help to complete it... And it is the network of these connections that forms the language.
>
> -- Christopher Alexander, The Timeless Way of Building (1979)

## 代数数据类型 / Algebra Data Type

`define-record-type` 是 scheme 语法设计的典范。
其根源可以追溯到 [Scheme 48](https://s48.org)，
通过 [SRFI-9](https://srfi.schemers.org/srfi-9/srfi-9.html) 实现标准化。

例如：

```scheme
(define-record-type point
  (make-point x y)
  point?
  (x point-x point-set-x!)
  (y point-y point-set-y!))
```

其语法设计为：

```scheme
(define-record-type <record-name>
  (<constructor> <field-tag> ...)
  <predicate>
  (<field-tag> <accessor> [<modifier>])
  ...)
```

这种设计避免了把所有信息都一股脑地表达出来，
而是用 `<field-tag>` 作为名字，
把 field 的信息串起来。

`(<constructor> <field-tag> ...)`
相当于定义了一些名为 `<field-tag>` 的变量。
而后续的 `(<field-tag> <accessor> [<modifier>])`
定义了这些变量的属性。

具体来说，就是先声明 `x` `y`
是两个 record 内部使用的变量，
然后再在后续的语句中定义这两个变量的属性：

```scheme
  (x point-x point-set-x!)
  (y point-y point-set-y!)
```

以 `define-record-type` 为范式，
可以推广出定义代数数据类型的显式（Explicit）语法：

```meta-lisp
(define-algebraic-type (my-list-t E)
  ((nil)
   nil?)
  ((li (head E) (tail (my-list-t E)))
   li?
   (head li-head li-put-head!)
   (tail li-tail li-put-tail!)))
```

对应的隐式（Implicit）语法可以设计为：

```meta-lisp
(define-enum (my-list-t E)
  (nil)
  (li (head E) (tail (my-list-t E))))
```

> Thus, as in the case of natural languages, the pattern language is generative.
>
> -- Christopher Alexander, The Timeless Way of Building (1979)

# 编程语言的设计语言

设计在于选择。

在设计领域，
我们用「设计系统」（Design System）
或者说「设计语言」（Design Language），
来把连续的选择离散化，
把繁杂的选择简单化。

LISP 对于形式语言设计，也是如此。
LISP 因此成为了一种编程语言的设计语言。

一种模式语言（Pattern Language）：

> Each pattern describes a problem which occurs over and over again in our environment, and then describes the core of the solution to that problem, in such a way that you can use this solution a million times over, without ever doing it the same way twice.
>
> -- Christopher Alexander, A Pattern Language (1977)

果真如此的话，
对于 LISP 语法设计而言，
具体的「模式」是什么呢？

首先，设计不是为了好看，是为了解决问题的。

语法设计所解决的问题是：

> 如何用文本形式上的差异，
> 来表达语义差异。

早期 LISP 的每一个语法设计，
都是为了解决一个「表达语义差异」问题。

比如，`define` 解决了在程序顶层做定义的问题，
下列语法就都有样学样，来在顶层做定义：

```scheme
define
claim
define-machine
define-tactically
define-record-type
define-algebraic-type
define-struct
define-enum
```

`lambda` 解决了在匿名表达式中，
引入约束变量（Bound Variable）的问题：

```scheme
(lambda (n) (add n 1))
```

以 `lambda` 为范式，
下列语法都是在引入新的约束变量：

```scheme
(lambda (<name> ...) <body>)
(fresh (<name> ...) <body>)
(run* (<name>) <body>)
(run <count> (<name>) <body>)
```

`let` 解决了引入变量名与值的绑定（Binding）问题：

```scheme
(let ((x 1)
      (y 2))
  (add x y))
```

以 `let` 为范式，
下列语法都是在引入变量名与值的绑定：

```scheme
(let ((<name> <exp>) ...) <body>)
(let* ((<name> <exp>) ...) <body>)
(letrec ((<name> <exp>) ...) <body>)
(Pi ((<name> <exp>) ...) <body>)
(Sigma ((<name> <exp>) ...) <body>)
```

> There is a central quality which is the root criterion of life and spirit in a man, a town, a building, or a wilderness. This quality is objective and precise, but it cannot be named.
>
> -- Christopher Alexander

# 美学流派

哪里有人群，哪里就有分歧。
人们在设计方面的分歧可以被称为「美学流派」。
在语法设计方面也是如此。

上面在介绍基于 LISP 的语法设计时，
其实我只介绍了其中一个美学流派。
也就是我所遵从的「极简主义」流派。

下面我多介绍几个。
给我所说的「历史上有大量的 LISP 语言设计实践」，
多添一些佐证。

## 极简 / Scheme

这个流派的特点是：

- 追求高度一致性。
- 保持克制，不使用 `()` 之外的其他括号，
  比如 `[]` 和 `{}`。
- 使用易读的词。
  比如：
  - `define` 而不是 `def`
  - `lambda` 而不是 `fn`
  - `add1` 而不是 `succ`

这个流派由于化到了最简，
所以很适合中文编程：

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

## 现代 / Clojure

在 `()` 之外，也利用 `[]` 和 `{}` 来表达语义差异：

```clojure
[1 2 3]

{:host "localhost"
 :port 8080}
```

尽量减少括号。

比如相比 scheme `let` 和 `cond`：

```scheme
(let ((a 1)
      (b 2))
  (+ a b))

(cond
  ((> x 0) "positive")
  ((< x 0) "negative")
  (else    "zero"))
```

少了用来表达「对子」的括号：

- 这与使用 `{}` 来表达键值对相呼应。

```clojure
(let [a 1
      b 2]
  (+ a b))

(cond
  (> x 0) "positive"
  (< x 0) "negative"
  :else   "zero")
```

大胆使用缩写：

- `def`
- `fn`
- `defn`
- `str`
- `conj`
- `disj`

大胆融合 Java 语法：

```clojure
(java.util.Date.)
(java.net.URI. "http://clojure.org")

(doto (JFrame. "Celsius Converter")
  (.setSize 300 80)
  (.setVisible true))
```

## 形式极简 / Calcit

在 [Calcit](https://github.com/calcit-lang/calcit) 语言中，
设计出了一种很好的基于缩进的 LISP 语法。

几乎能够去掉所有括号。

规则只有两条：

- 「换行 + 缩进」表示「向下嵌套一层列表，
  更深的行是上一行尾部的子表达式」；
- `$` 在行内起到同样的作用，
  等价于「开括号并把后续内容放到下一层」。

例如：

```calcit-lang
defn fib (n)
  if (< n 2) 1 $ +
    fib $ - n 1
    fib $ - n 2
```

等价于：

```calcit-lang
defn fib (n)
  if (< n 2)
    1
    +
      fib $ - n 1
      fib $ - n 2
```

等价于：

```meta-lisp
(defn fib (n)
  (if (< n 2)
    1
    (+
      (fib (- n 1))
      (fib (- n 2)))))
```

# 尾声

> Syntax without representation is tyranny.
>
> -- Sussman

我为什么 LISP？

因为 LISP 是一种设计语言，
它能够把语言设计的循环缩短。

> 设计、实现、再设计、再实现……

我的 LISP 语言集合：[x-lisp](https://github.com/xieyuheng/x-lisp)。
