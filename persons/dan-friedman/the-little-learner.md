---
title: The Little Learner
---

# Foreword by Guy L. Steele Jr.

> This book is _exactly_ right.

> Dan Friedman, with his able and expert co-authors, has
> been writing books in his unique “Little” style for over
> four decades. Every one is a gem, explaining deep and
> important ideas in computer science in bite-sized chunks.
> Dan and his co-authors have raised the “programmed
> learning” question-and-answer format to an art form, to a
> conversational style that seems almost breezy.  This very
> volume introduces two innovations, _nuggets_ and _revision
> charts_, that further streamline the presentation of
> chunks of program code and their behavior.

nuggets 就是代码块。

> The authors themselves remark: “Little” books are all
> about packaging ideas neatly into little boxes.

可否让 AI agent 为某个领域的知识生成 little books？

# Preface

> Deep learning, an emerging area of artificial
> intelligence, has revolutionized the way problems are
> solved, be it winning at Go, recognizing cats in pictures,
> or asking a smart speaker to order pizza. The most
> beautiful thing about deep learning is how simple pieces
> come together to solve large, complex problems. How can we
> understand what makes these deep learning tools work? Our
> approach is to build them, a little bit at a time, and
> watch them work.

> This book builds up a collection of concepts, with nearly
> all of them being functions.

因此「文学式编程」也应该以用函数表达的概念为元素，
而不是以任意的代码片段为元素。

> We also categorize key properties as either _rules_ or
> _laws_. Our rules are about the structure of entities,
> like their sizes. Our laws, on the other hand, are about
> the behavior of entities, like their equalities and
> invariants.

structure knowledge and behavior knowledge of entities。

# 0: Are You Schemish?

复习一下 Scheme。

# 1: The Lines Sleep Tonight

介绍 line 这个简单的函数。

```
y = w x + b
```

```scheme
(define line
  (lambda (x)
    (lambda (w b)
      (+ (* w x) b))))
```

> Here, though, we deal with a different kind of problem,
> where `x` is known, but `w` and `b` must be figured out
> from a bunch of given values of `x` and `y`.

从这个反向的参数顺序，
已经可以看到机器学习的特点了。

形式参数的分类：

- w，b -- parameters
- x -- argument

翻译建议：

- w, b 是「模型参数」，或「可学习参数」
- x 是「自变量」，或「输入」

> Functions that accept parameters after the arguments are
> known as
>
>   parameterized functions
>
> Parameterized functions are used where we must figure out
> the right values for the parameters (here, `w` and `b`)
> from given values of `x` and the corresponding values of
> `y`.

已知「带参数的函数」（parameterized functions）
和输入输出的「数据集」（dataset），
就可以推测出合理的模型参数（parameters）。

带参数的函数：

```
y = w x + b
```

数据集：

```scheme
(define line-xs [2.0 1.0 4.0 3.0])
(define line-ys [1.8 1.2 4.2 3.3])
```

找到最合适的模型参数为 `w = 1` 与 `b = 0`：

- 具体的寻找方式，我们先不讨论。

```
y = x
```

这就是「从数据集学习模型参数」的一个具体例子。

习得模型参数之后，
对于新输入我们就可以预测其输出。

比如对于 `x = 3.79` 预测 `y = 3.79`。

> We refer to this as the _predicted_ `y` for a given `x`.

一句话定义 learning：

> Finding the parameters of a function from a data set
> is known as _learning_.

结构相关的知识称作 rule，
这里给出第一条 rule：

> **The Rule of Parameters**
>    (Initial Version)
>
> Every parameter is a number.

> The parameters `w` and `b` here are collectively known as
> the parameter set, which we refer to as `θ`.
>
> So, our `θ` has two parameters. They are referred to as
> members of `θ`.

```scheme
(define line
  (lambda (x)
    (lambda (θ)
      (+ (* (ref θ 0) x) (ref θ 1)))))
```

> Can we define a function that determines `θ` for any data
> set?  Yes, we can, and such a function is an example of
> what is known as _machine learning_.

# 2: The More We Learn, the Tenser We Become

介绍 Tenser 的概念。

所有的实数称为标量（scalar）。

以标量为元素的是 tensor-1：

> [5.0 7.18 π]

- 类似向量和一维数组，但是不允许为空。

以 tensor-1 为元素的是 tensor-2：

> [[7 6 2 5] [3 8 6 9] [9 4 8 5]]

一般的，以 tensor-m 为元素的是 tensor-m+1，
但是有一个条件：所有 tensor-m 的长度必须相等。

tensor 的这个属性叫做 rank。

> The _rank_ of a tensor tells us how deeply nested its
> elements are.

> **The Rule of Rank**
>
> A tensor's rank is the number of left square brackets
> before its leftmost scalar.

如果是在 meta-lisp 中，
可能会有如下函数命名：

```meta-lisp
tensor-length
tensor-get
tensor-shape
tensor-rank
```

作为 tensor-m+1 元素的所有 tensor-m 的长度必须相等。
这个限制可以用来定义 tensor 的 shape。

比如 `[[5.2 6.3 8.0] [6.9 7.1 0.5]]`
的 shape 是 `(list 2 3)`，
因为这个 tensor-2 有 2 个 tensor-1 元素，
每个 tensor-1 有 3 个 tensor-0 元素。

又比如 `[[[5] [6] [8]] [[7] [9] [5]]]`
的 shape 是 `(list 2 3 1)`，
因为这个 tensor-3 有 2 个 tensor-2 元素，
每个 tensor-2 有 3 个 tensor-1 元素，
每个 tensor-1 有 1 个 tensor-0 元素。

scalar 的 shape 是空列表 `(list)`。

一个用来在术语上区分 list 和 tensor 的 rule：

> **The Rule of Members and Elements**
>
> Non-empty lists have members
> and non-scalar tensors have elements.

> **The Rule of Uniform Shape**
>
> All elements of a tensor must have the same shape.

> **The Law of Rank and Shape**
>
> The rank of a tensor is equal to the length of its shape.

```scheme
(define rank
  (λ (t)
    (ranked t 0)))

(define ranked
  (λ (t a)
    (cond
     ((scalar? t) a)
     (else (ranked (tref t 0) (add1 a))))))
```

> **The Law of Simple Accumulator Passing**
>
> In a simple accumulator passing function definition
> every recursive function invocation is unwrapped,
> and the definition has
> - at most one argument that _does not change_;
> - an argument that _changes towards a true_ base test;
> - andanother that _accumulates_ a result.

> Every Scheme system is required to support _tail call
> optimization_ which makes each unwrapped recursive
> invocation behave the same as a loop.

# Interlude I: The More We Extend, the Less Tensor We Get

将标量的函数，
逐点扩展（pointwise extension）成张量函数。

进一步，对于二元函数，
只要一个 tensor 的 shape
是另一个 tensor 的 shape 的后缀，
扩张就都成立。

比如：

- `(list 3)`
- `(list 2 3)`

```scheme
(+ [6 9 1] [[4 3 8] [7 4 7]])
;; =>
[(+ [6 9 1] [4 3 8])
 (+ [6 9 1] [7 4 7])]
```

接受 tenser-1 参数的 `sum-1`，
也可以被扩展为接受 tenser-1+n 参数的 `sum`。
`sum` 会消除 shape 的最后一个元素，
比如 `(list 1 2 3)` 会变为 `(list 1 2)`。

> **The Law of Sum**
>
> For a tensor `t` with rank `r > 0`,
> the rank of `(sum t)` is `r − 1`.

> If we use extended functions to form new functions, then
> the new functions also automatically work in an extended
> fashion.

这里的例子是 `line`。

- 注意，argument 是 tensor；
  parameters 是 list。

```scheme
((line [2 7 5 11]) (list 4 6))
```

> This interlude is about how it is possible to extend
> functions that operate on fixed ranks of tensors into
> functions that also accept tensors of different ranks. We
> use the concept of _descending_ into the higher-ranked
> tensors to accomplish this.

这个扩张将会在之后用来实现自动微分，
但是实际的高效的自动微分计算，
是不能使用这种动态语言的方式的，
甚至可能也不能使用这里对于不同 shape 的 tensor 的重载。

问题：静态类型语言应该如何处理未来的自动微分问题？

# 3: Running Down a Slippery Slope

介绍最小二乘法与梯度。

> Let us now return to defining a function to automatically
> find the `θ` that best fits (from frame 27:29) the given
> data set in 24:17. We refer to this `θ` as _well fitted_.

learning 问题是根据数据集寻找模型参数，
机器学习的方法，首先要把这个问题转化为一个「求极值点」问题。
所谓「求极值点」，就是求使得目标函数取极值的模型参数。

> 数据集 + 模型 -> 目标函数

当模型参数为 well fitted 时，目标函数取极值。

> Here we introduce
>
>   successive approximations
>
> a way to find a well-fitted `θ`.

> We determine `θ[0]` and `θ[1]` by arbitrarily starting them
> at `0.0`. We then repeatedly revise `θ` to bring it as
> close as we wish to what it must finally be.

我们需要一个任意的起始 `θ`，
然后找到一个度量模型与数据拟合（fit）程度的方式，
度量的结果称为 loss，然后求解一个 loss 的极值点问题。

> In order to do that, we need to know how far away we are
> from the well-fitted `θ`.
>
> We need a _single_ scalar that tells us how close or how
> far away we are. This scalar is known as the _loss_. The
> well-fitted `θ` would be the one where this loss is as
> close to `0.0` as possible.
>
> When the loss is `0.0`, it is _ideal_.

> We determine this scalar, or loss, every time we revise
> `θ`. Since the loss shows us how far away we are from the
> well-fitted `θ`, we use it as a guide for revising `θ`.

```scheme dashed
(define l2-loss
  (lambda (xs ys)
    (lambda (θ)
      (let ((predicted-ys ((line xs) θ)))
        (sum
         (square
          (sub ys predicted-ys)))))))
```

> In mathematics, the square root of the sum of the squares
> of a tensor-1 is known as L2-norm. The L2-norm is also
> known as Euclidean distance.

我们这里没有 square root，
所以用的是 Euclidean distance 的平方。

> When used as a loss function, however, the square root is
> typically left out. Another variant of this loss function
> divides the sum of the squares by the number of elements
> in the tensor to produce a mean squared error (or MSE)
> loss function.

把对 `line` 定义的 `l2-loss` 一般化：

```scheme solid
(define l2-loss
  (lambda (target)
    (lambda (xs ys)
      (lambda (θ)
        (let ((predicted-ys ((target xs) θ)))
          (sum
           (square
            (sub ys predicted-ys))))))))
```

在带有 auto-currying 的语言中，可以直接写：

```meta-lisp
(define (l2-loss target xs ys θ)
  (let ((predicted-ys ((target xs) θ)))
    (sum (square (sub ys predicted-ys)))))
```

`(l2-loss target)` 称作「待参函数」（expectant function）。

> ... because it is _expecting_ a data set as arguments.

`(l2-loss target xs ys)` 称作「目标函数」（objective function）。

> When provided with a `θ`, the objective function produces
> a scalar representing the loss, which is a measure of how
> far away we are from the well-fitted `θ`.

也就是优化问题中的目标函数。
我们要找到目标函数的最优参数，
使得其函数值（这里的 loss）最小。

下面就要用「逐步逼近法」（successive approximations）
来寻找最优参数，这就需要求出「逐步逼近」的方向，
这就需要「梯度」。

> We have to be wary that our revision of `θ[0]` always
> moves us closer to, but does not overshoot, the ideal
> loss.

> We take a small scalar (like `0.01`), and multiply the
> rate of change by it, and revise `θ[0]` by that amount.

> This small scalar is known as the
>
>   learning rate
>
> Since we use this scalar often, we have a special symbol
> for it: `α`

> The learning rate (usually a very small number between
> `0.0` and `0.01`) is also known as the step size. We'll
> see more of how to find an appropriate learning rate in
> later chapters.
>
> The step size is sometimes written as `λ`, but because we
> already have a meaning for `λ`, we prefer to use a
> different Greek letter.

> **The Law of Revision**
>   (Initial Version)
>
> new `θ[0]` = `θ[0]` − (`α` × rate of change of `loss`
> with respect to `θ[0]`)

其中 rate of change of `loss` with respect to `θ[0]`，
就是 `loss` 函数针对第一个参数的偏导数。

这一章用一个小的扰动 0.0099，
来计算偏导数的近似值，
这种方法是「有限差分」（finite difference）。

# 预习：反向模式自动微分

「反向模式自动微分」
（Reverse-Mode Automatic Differentiation）。

```
z = (x + y) * y
```

一条链上嵌套的表达式，可以用嵌套函数的链式法则求偏导数。

为什么一个变量的多次出现，可以通过累加来计算偏导数？

因为微分研究的是「局部线性化」，
而线性系统满足叠加原理。

线性性的直接后果就是叠加：
几个小扰动一起作用，总效应等于各自单独作用之和，
交叉项是二阶小量，可以忽略。

所以当 `y` 在 `f(x, y)` 中出现两次时，
你可以假装「先只让第一个 `y` 变，再只让第二个 `y` 变」：
- 第一个位置贡献一份 `Δz1`
- 第二个位置贡献一份 `Δz2`
总变化：`Δz ≈ Δz1 + Δz2`
除以 `Δy` 取极限，就是梯度相加。

另外一种理解方式是：
把每次出现看作是不同的变量，
然后再嵌套一层恒等函数，
用恒等函数的链式法则。

```
∂z/∂y = ∂z/∂y1 * ∂y1/∂y
      + ∂z/∂y2 * ∂y2/∂y
```

机器学习的框架，就是要把这个反向传播的计算图，
编译到 GPU 的 primitive 操作（GPU kernel）。
其实机器学习所要解决的，就是一个编译与编译器优化问题。

如此看来，我的工作应该是：

- 设计一个基于 lisp 语法的模型描述语言。
- 得到反向传播计算图，并编译到 GPU kernel。
- 从而完成机器学习任务。

# 4: Slip-slidin’ Away

这一章介绍递归下降法。

上一章我们已经把 learning 问题转化为了「求极值点」问题，
并且大致给出了解决「求极值点」问题的思路。

这一章继续完善「求极值点」问题的解法。

横轴是 `θ[0]` 纵轴是 loss，
画图就可以看出这个「求极值点」问题的全貌。

在这个图中某一点的切线的斜率，
就是梯度在这一点的第一个分量。

> The rate of change that we have determined is the
>
>   slope of the tangent
>
> This slope has a different name so we don't confuse it
> with `θ[0]`. It is known as the
>
>   gradient
>
> A gradient is a general way of understanding the rate of
> change of a parameterized function with respect to _all_
> its parameters.

我们将会用自动微分算法实现一个 `gradient` 函数。

```meta-lisp
(define-type parameter-t tensor-t)
(define-type parameters-t (list-t parameter-t))

(claim gradient
  (-> (-> parameters-t scalar-t) parameters-t
      parameters-t))
```

一个没法在类型中表达的不变量是：
在某个 parameters 上的 gradient，
其形状和 parameters 一致。

注意「自动微分」与「符号微分」的区别，
「符号微分」要求给出某个函数的导数的一般表达式，
而「自动微分」只要求能够计算函数在每一点的具体导数。

先实现一个一般的，用来迭代 `f` 多次的辅助函数：

```scheme
(define revise
  (lambda (f revs θ)
    (cond
     ((zero? revs) θ)
     (else
      (revise f (sub1 revs) (f θ))))))
```

> Now let's get back to defining a (revision) function that
> guides us from one `θ` to the next.

在 revision 中：

> We multiply the gradient for a given parameter by the
> learning rate α and subtract it from that parameter.

如何理解这里的「减法」而不是「加法」？
因为导数度量的是增加的速度，
因此梯度指向损失函数上升最快的方向，

- 如果梯度为正：`∂L/∂θ[i] > 0`，
  说明增大 `θ[i]` 会让损失变大，所以应该减小 `θ[i]`。
  减去一个正数，正好让它变小。

- 如果梯度为负：`∂L/∂θ[i] < 0`，
  说明增大 `θ[i]` 会让损失变小，所以应该增大 `θ[i]`。
  减去一个负数，等于加上一个正数，正好让它变大。

所以统一写成「减去梯度」，
就能自动沿着损失下降最快的方向走，
这叫做「梯度下降」。

> **The Law of Revision**
>   (Final Version)
>
> new `θ[i]` = `θ[i]`
>   − (`α` × rate of change of `loss` w.r.t. `θ[0]`)

> This algorithm for finding parameters is known as
>
>   optimization by gradient descent
>
> Who knew sliding down a slippery slope would be this much
> fun!

# Interlude II: Too Many Toys Make Us Hyperactive

介绍 dynamic scope，
可以用来处理递归下降法中的「超参数」（hyperparameters）。

「超参数」的特点是必须就具体情况通过实验来确定。

我们在练习实现的时候可以不用 dynamic scope，
因为这是一个程序语言级别的功能，
而一般的程序语言没有 dynamic scope 这个功能。

在一般的程序语言中，我们可以使用的备选方案是：

- 方案 A：直接多带一个参数 `options`，
  把需要的所有参数都传到函数里。
  缺点是所有函数都会多一个参数。

- 方案 B：用 stack 来模拟 dynamic scope。

> **The Rule of Hyperparameters**
>
> Every hyperparameter either is a scalar or has no value.

```scheme
(declare-hyper smaller)
(declare-hyper larger)

(with-hypers
    ((smaller 1)
     (larger 2000))
  (+ smaller larger))
```

> A `let`-expression creates a new local name for a value,
> but a `with-hypers` provides a new value for a
> pre-existing name that has been declared as a
> hyperparameter.

为了避免大量传递参数，可能还是需要「方案 B」。
只不过我们可以完全用 stack 实现，
让取 hyperparameter 的表达式变得更 explicit。

比如：

- `with-hyper` 和 `hyper-get` 都实为函数。

```scheme
(with-hyper smaller 1
  (lambda ()
    (with-hyper larger 2000
      (lambda ()
        (+ (hyper-get smaller)
           (hyper-get larger))))))
```

看来这样是不合理的，因为语法太啰嗦了，
还是需要专门实现 dynamic scope 的语法。

scheme 标准中，
直接把 dynamic scoped variable 当作零元函数：

```scheme
(define smaller (make-parameter 0))
(define larger (make-parameter 0))

(parameters
    ((smaller 1)
     (larger 2000))
  (+ (smaller) (larger)))
```

早期 scheme 有 `fluid-let`：

```scheme
(fluid-let
    ((smaller 1)
     (larger 2000))
  (+ smaller larger))
```

其实现方式是先用 `set!` 临时改变局部变量，
在结束之后再 `set!` 回之前的值。

这样实现的好处是：

- 不用声明动态作用域变量。
- 不用额外设置 stack。

缺点是不能正确处理 `call/cc`。

scheme 中有一个专门和 `call/cc`
配合使用的函数 `dynamic-wind`，
利用这个函数，可以实现支持 `call/cc` 的
`make-parameter` 和 `parameters`。

# 5: Target Practice

之前用 line 测试了递归下降法，
line 被称为 target，
这章找更多的 target 函数来练习一下。

target 包括：

- 一个非线性函数 -- 二次函数 quad，
- 一个多元函数 -- 平面 plane。

这里再次需要用「溯因推理」的方式，
猜测出这些 target 的模型。

需要介绍线性与非线性函数，
需要介绍二次函数，等等新概念。
方式是给出简要的精准的定义，
然后再次拆解定义中没有定义的概念。
这最终会像一个树状图一样，
把所有概念的定义产开成常识。

```scheme
(nonlinear-function
 "a nonlinear function is not linear in its arguments."
 (linear-function
  "a linear function one that uses only addition
   and scaling to find its result."
  (scaling
   "scaling multiplies its argument
    by a fixed value or by a parameter.")))

(quadratic-data-set
  "it can be predicted by a quadratic function."
  (quadratic-function
   "Here is a general definition of a quadratic function: ..."))
```

```scheme
(define quad
  (lambda (t)
    (lambda (θ)
      (+ (* (ref θ 0) (square t))
         (+ (* (ref θ 1) t)
            (ref θ 2))))))
```

这个概念树的展开过程中，
可以辅助例子和解释。

另外我们可以发现，
某些概念是用大量的例子来引入的，
比如 "The Little Schemer" 的开头，
对原子性概念的定义。

因此我们可以总结：

- 原子型概念，用大量例子与操作来建立直觉。
- 复合型概念，通过精准简洁的定义来展开概念树。

这是自上而下的讲授方法，
也有自下而上的，比如对 learning 的精确定义之前，
先预习了 function 和 parameters 以及 data set 的概念。

与其说是「概念树」，
不如说是由概念的定义所引出的依赖关系所形成的「概念格」，
底层是假设读者已知「基础概念」，
上层是需要学习的「新概念」。

每个章节，其实都是以线性讲述的方式，
在这个「概念格」中探索了一部分。

介绍 plane 的模型：

```scheme
(define plane
  (lambda (t)
    (lambda (θ)
      (+ (dot-product-1-1 (ref θ 0) t)
         (ref θ 1)))))

(define dot-product-1-1
  (lambda (w t)
    (sum-1 (* w t))))
```

此时 `θ[0]` 和 `t` 都是 tensor-1，
从而扩展了 data sets 和 parameters 的例子。

> **The Rule of Data Sets**
>
> In a data set `(xs, ys)` both `xs` and `ys` must have the
> same number of elements.  The elements of `xs`, however,
> can have a different shape from the elements of `ys`.

数据集是 tensor，比如：

- 尽管最外层的 tensor 只是为了表达「数据集」中的「集」。
  由于集合中的数据形状相等，所以满足 tensor 的要求。

```scheme
(define plane-xs
  [[1.0 2.05]
   [1.0 3.0]
   [2.0 2.0]
   [2.0 3.91]
   [3.0 6.13]
   [4.0 8.09]])

(define plane-ys
  [13.99
   15.99
   18.0
   22.4
   30.2
   37.94])
```

> **The Rule of Parameters**
>   (Final Version)
>
> Every parameter is a tensor.

> **The Rule of θ**
>
> `θ` is a list of parameters
> that can have different shapes.

parameters 的具体形状，
要看 parameters 在模型中的具体用法才能确定。
比如在 plane 这个例子中，
可以根据「类型推导」找出 parameters 的形状，
当然这不是真的类型推导，
因为 shape 信息不再类型中。

在没有 dependent type 的情况下，
也许可以直接把带有 shape 的 tensor 类型内置到语言中。

进一步，也许通过给 meta-lisp 加一个 macro system，
就可以让 meta-lisp 成为 machine learning 的 meta language。

# Interlude III: The Shape of Things to Come

tensor-2 是矩阵。

比如下面的矩阵呢 shape 是 `(2 3)`：

```scheme
[[2 4 5]
 [6 7 8]]
```

比如 shape 为 `(3 1)` 的是 column matrix：

```scheme
[[5]
 [7]
 [8]]
```

而 shape 为 `(1 3)` 的是 row matrix：

```scheme
[[5 7 8]]
```

这一节介绍新的 Tensor 的缩写，
并且要标注 shape 来区分 row vector -- 比如 shape 为 `(3)`，
与 row matrix -- 比如 shape 为 `(1 3)`。

# 笔记：函数类型总结

解释写在代码块之后。

```meta-lisp
(define-type parameters-t (list-t tensor-t))

(define-type target-fn-t
  (-> tensor-t parameters-t tensor-t))
```

`target-fn-t`
也可以称为「模型函数」，从输入预测输出。
- 第一个参数是「输入数据」；
- 第二个参数是「模型参数」或「模型权重」；
- 返回值是「输出数据」。

```meta-lisp
(define-type loss-fn-t
  (-> target-fn-t
      tensor-t tensor-t
      parameters-t
      scalar-t))
```

`loss-fn-t`
通过引入度量，把「寻找模型参数」问题，
转化为一个「求极值点」问题。
- 第一个参数是「模型函数」；
- 第二和第三个参数是「输入数据」和「输入数据」，
  这两个参数构成了「数据集」；
- 第四个参数是「模型参数」；
- 返回值是 loss，即对「预测数据」
  与实际「输出数据」之间差距的度量。

```meta-lisp
(define-type expectant-fn-t
  (-> tensor-t tensor-t
      parameters-t
      scalar-t))

(define-type objective-fn-t
  (-> parameters-t
      scalar-t))
```

`expectant-fn-t`
是 `loss-fn-t` 的 currying，
代表一个在等待数据集。

`objective-fn-t`
是 `expectant-fn-t` 的 currying，
代表「求极值点」问题的「目标函数」。

```meta-lisp
(claim gradient (-> objective-fn-t parameters-t parameters-t))
```

`gradient` 通过梯度下降来解决「求极值点」问题。

# 6: An Apple a Day

本章介绍一个机器学习技巧：
不要每次计算梯度的时候都用完整的数据集，
而是从数据集中取一个小的随机样本。

取样的另外一个原因是，
需要留一些真实数据不用于寻找参数，
而是用于检验模型的预测的数据是否符合真实数据。

> Using a small random sample of a few points from the data
> set produces a good enough approximation of loss, which
> can be used to revise `θ`. We refer to this sample as a
> _batch_ and its generation as _sampling a batch from the
> data set_.

> So, to sample a batch, we generate a few random indices,
> and then select the corresponding elements from `xs` and
> `ys`. We refer to these random indices as
>
>   _a batch of indices_

> **The Rule of Batches**
>
> A batch of indices consists of random indices that are
> natural numbers smaller than `(tlen t)`.

```scheme
(define samples
  (lambda (n s)
    (sampled n s (list))))

(define sampled
  (lambda (n i a)
    (cond
      ((zero? i) a)
      (else
       (sampled n (sub1 i)
         (cons (random n) a))))))
```

> So, now that we know how to randomly sample indices, how
> do we use these indices to get a batch from the data set?
>
> We need a new toy for this!

这里要引入一个新的 primitive 函数 `(trefs t b)`，
看来 primitive 函数都叫做 toy。

```scheme
(declare-hyper batch-size)

(define sampling-obj
  (lambda (expectant xs ys)
    (let ((n (tlen xs)))
      (lambda (θ)
        ;; b -- batch-indices
        (let ((b (samples n batch-size)))
          ((expectant (trefs xs b) (trefs ys b)) θ))))))
```

注意这里函数复合式的设计，
实现 `sampling-obj` 之后，
并不用修改 `gradient-descent` 的实现，
只需要在调用时把 `(expectant xs ys)`
改为 `(sampling-obj expectant xs ys)`。

```scheme
(with-hypers
   ((revs 1000)
    (α 0.01)
    (batch-size 4))
 (gradient-descent
  (sampling-obj
   (l2-loss line) line-xs line-ys)
  (list 0.0 0.0)))
```

如果考虑 currying，这其实是一个函数修饰器：

- 在 Sussman 关于如何灵活地编程的新书中，
  也有提到类似的技巧。

```meta-lisp
(define-type expectant-fn-t
  (-> tensor-t tensor-t
      parameters-t
      scalar-t))

(claim sampling-obj
  (-> expectant-fn-t expectant-fn-t))
```

> This kind of gradient descent where the
> objective function uses sampling is known as
>
>   _stochastic gradient descent_
>
> _Stochastic_ is another way of saying we use random
> numbers to determine our results. Using _samples_ as part
> of our objective function is what makes it stochastic.

> **The Law of Batch Sizes**
>
> Each revision in stochastic gradient descent uses only a
> batch of size `batch-size` from the data set and the ranks
> of the tensors in the batch are the same as the ranks of
> the tensors in the data set.

# 7: The Crazy “ates”

这一章讨论一个关于如何组织代码的工程问题：
即如何在 `revise` 的过程中，
给每个 parameter 增加附加信息来辅助 `revise`。

回顾 `gradient-descent`：

```meta-lisp dashed
(define-type objective-fn-t (-> parameters-t scalar-t))

(claim gradient
  (-> objective-fn-t parameters-t parameters-t))

(claim gradient-descent
  (-> objective-fn-t parameters-t parameters-t))

(define gradient-descent
  (lambda (obj θ)
    (let ((f (lambda (Θ)
               (map (lambda (p g)
                      (− p (* α g)))
                 Θ
                 (gradient obj Θ)))))
      (revise f revs θ))))
```

> One of the reasons why `gradient-descent` from frame 99:5
> is unable to get to its well-fitted `θ` with fewer
> revisions is because it has very little information to
> work from.
>
> Before we get to the details of that, what matters here is
> that we must find a way to hold this information and bring
> it up to date as the parameter is revised.
>
> The first thing to remember is that this extra information
> is associated with each parameter in `θ`.

> **The Law of Revisions**
>
> As long as we make sure that `gradient-descent` accepts an
> initial `θ` and results in a well-fitted `θ`, any
> reasonable way of revising it from the first to the last
> revision is okay.

首先引入 accompanied parameter，
用来保存对应于一个 parameter 的额外信息：

> Whenever a parameter is wrapped in a list, we refer to it as
>
>   _an accompanied parameter_

```scheme
(list p1 p2 p3 ...)
```

改为：

```scheme
(list (list p1) (list p2) (list p3) ...)
```

未来可能有：

```scheme
(list (list p1 ...) (list p2 ...) (list p3 ...) ...)
```

这就是为什么在参数命名上要区分小写的 `θ` 代表 parameters，
与大写的 `Θ` 代表 accompanied parameters。

由于小写的 theta 到大写的 theta 像是「吹气球」，
所以两个方向的转化函数分别叫做 "inflate" 和 "deflate"。

注意这里在函数式语言中实现「接口」（interface）的方式，
由于我们的语言中并没有 record 与 subtype，
所以我们并没有真的定义一个 interface：

```js
interface Representation {
  inflate,
  deflate,
  update,
}
```

而是把每个接口函数作为普通参数，
来在参数层面实现「依赖注入」（dependency injection）。

由于函数可以 currying，
所以这种实现方式用起来也是能在代码中体现意图的，
即「这一组函数，属于一个接口」这个意图。
并且，这样分散的参数传递是更灵活的。

- 唯一的缺点就是「依赖参数顺序」，
  当参数的数量很多时，我们需要能不依赖参数顺序来调用函数。
  这个问题可以通过设计新语法来解决，
  新语法可以专门处理「依赖参数顺序」问题。

> We use these three functions
>
>     inflate deflate update
>
> to generalize `gradient-descent`.
>
> Rather than scattering the three “ates” throughout the
> `gradient-descent`, we pass them to a more general
> `gradient-descent`:

```meta-lisp dashed
(define gradient-descent
  (lambda (inflate deflate update)
    (lambda (obj θ)
      (let ((f (lambda (Θ)
                 (update
                   Θ
                   (gradient obj (deflate Θ))))))
        (deflate
          (revise f revs
            (inflate θ)))))))
```

> We're going to simplify them.
> To do that, let's consider something
>
>   crazy

这里所说的 "crazy" 其实是一个 code smell，
也就是发现这些接口函数中都重复调用了 `map`，
应该把 `map` 抽出来，使得接口函数更简单：

- 注意，尽管 `update` 函数简化为了针对某一个 parameter 而计算。
  但是梯度 `gradient` 依然是就整个 parameters 计算的，
  即 objective 函数对每个 parameter 的偏导数。

```meta-lisp solid
(define gradient-descent
  (lambda (inflate deflate update)
    (lambda (obj θ)
      (let ((f (lambda (Θ)
                 (map update
                   Θ
                   (gradient obj
                     (map deflate Θ))))))
        (map deflate
          (revise f revs
            (map inflate θ)))))))
```

> **The Law of the Crazy “ates”**
>
> For any representation, the three “ates” are concerned
> with only one parameter and its accompaniments, and are
> not directly concerned with either `θ` or `Θ`.

用函数类型来描述「接口」就是：

```meta-lisp
(define-type parameter-t tensor-t)
(define-type parameters-t (list-t parameter-t))

(define-type (accompanied-parameter-t A)
  (pair-t parameter-t A))

(define-type (inflate-fn-t A)
  (-> parameter-t (accompanied-parameter-t A)))

(define-type (deflate-fn-t A)
  (-> (accompanied-parameter-t A) parameter-t))
```

这些函数其实是为 `update-fn-t` 服务的，
目的是把：

```meta-lisp
(define-type update-fn-t
  (-> parameter-t parameter-t
      parameter-t))
```

转化为：

- 第二个 `parameter-t` 参数为 gradient。

```meta-lisp
(define-type (update-fn-t A)
  (-> (accompanied-parameter-t A) parameter-t
      (accompanied-parameter-t A)))
```

这里可以看到，
每个 `update` 函数在做更新的时候已经有更多信息了，
扩展了 state passing 中的 state 的结构。

例子：

- `θ` 为 parameters，元素记为 `p`；
- `Θ` 为 accompanied-parameter，元素记为 `P`。

```scheme
(define naked-i (lambda (p) (let ((P p)) P)))
(define naked-d (lambda (P) (let ((p P)) p)))
(define naked-u (lambda (P g) (let ((p P)) (- p (* α g)))))
```

```scheme
(define lonely-i (lambda (p) (list p)))
(define lonely-d (lambda (P) (ref P 0)))
(define lonely-u (lambda (P g) (list (- (ref P 0) (* α g)))))
```

# 8: The Nearer Your Destination, the Slower You Become

上一章和这一章，完美的展示了，
如何先把代码打开，然后在修改代码。

> For each desired change,
> make the change easy (warning: this may be hard),
> then make the easy change.
>
> -- Kent Beck

由于接近极值点时梯度会逐渐减小到零，
所以如果 learning rate 适合开始阶段的梯度下降，
在接近极值点的时候，下降的速度就变慢了。

> The change that we make to a given parameter at each
> revision is known as the _velocity_ of descent.

注意，velocity 作为 change 是「加」到 parameter 上的。
「梯度下降」公式中的减号，应该被移到 velocity 中。

这里的方案是去积累整个梯度下降路径上的 velocity，
每次都乘上一个因子来减少积累的程度。

本章开头用「接力赛」中累积速度的比喻，
来解释这个 velocity 累加的方案。

> we should boost our velocity by adding some fraction `μ`
> of the velocity `v`, of the previous revision, to the
> change we expect to make in the current revision.

```scheme
(declare-hyper μ)
```

> The hyperparameter `μ` is between 0.0 and 1.0 and
> represents the decimal fraction of the previous velocity
> we want to retain for the next velocity.
>
> The recommended scalar for `μ` is usually about 0.9.

由于我们抽出来了接口函数，
所以实现新算法就是实现新接口：

```scheme
(define velocity-i (lambda (p) (list p (zeros p))))
(define velocity-d (lambda (P) (ref P 0)))
(define velocity-u
  (lambda (P g)
    (let ((v (- (* μ (ref P 1))
                (* α g))))
      (list (+ (ref P 0) v) v))))
```

> this form of gradient descent is known as
>
>  _momentum gradient descent_
>
> That's because we multiply the velocity `v` by a constant
> `μ`. The resulting expression is analogous to the formula
> of momentum in physics.

用 meta-lisp 实现机器学习框架时，
我们可以实现一些画图和动画函数，
这样可以更直观地看到不同算法的效果。

# Interlude IV: Smooth Operator

> So, smooth seems to be a way of
> incorporating historical information that
> is less relevant as we move forward.

# 9: Be Adamant

> Because α (learning rate) represents the fraction of the
> gradient we're going to use as our velocity,
> another approach to addressing this
> problem is to make this fraction _adaptive_.

> Adaptive here means that the fraction is
> decided based on the gradient and its
> historical values.

不用在纠结 `learningRate` 这个常数了，
因为我们讲用历史数据类计算 `adaptiveLearningRate`。

并且可以把这里修改 `learningRate` 的技巧，
和前一章的 momentum gradient descent 结合起来用，
得到 ADMA 算法。

> NOTE ADAM is short for adaptive moment estimation.
> The accompaniment `v` is known as
> the gradient's 1st moment and `r` its 2nd moment.

注意，
这里两个算法的复合并没有那么优雅了，
必须要修改算法定义的细节才能做到。

# Interlude V: Extensio Magnifico!

这里学习如何实现 extended functions。

我不理解为什么不用函数复合，
而是重新从头定义 `of-rank?` 之类的函数。
是为了优化？难道经过测试这里的函数是瓶颈？

我在实现这里的 `extend1` 和 `extend2` 时，
没有先看这章，但是我只是实现了对 Scalar 函数的 extend，
这里实现了对任意 base rank 的函数 extend，
技巧就是把 base rank 作为参数传进来。

```typescript
const mul21 = extend2(mul, 2, 1)
```

`mul21` 与 `mul` 作用于两个 rank 为 2 的 Tensor 的效果并不一样，
`mul21` 可以用来实现 matrix-vector multiplication。

注意，对于 `extend2` 之类的高阶 tensor 函数，
只有当考虑 tensor 的 shape 的时候，
其类型才能有效地约束函数的范围。

比如：

```cicada
mul21: ([m, n], [n]) -> [m, n]
mul21: ([a, m, n], [n]) -> [a, m, n]
mul21: ([m, n], [b, n]) -> [b, m, n]

// 由于 extend2 的定义中两个参数 rank 相等时，
// 被特殊处理为 zip + map 了，
// 这也许是不合理的，因为下面两个作用返回的 shape 一样了。
mul21: ([a, m, n], [a, b, n]) -> [a, b, m, n]
mul21: ([a, m, n], [b, n]) -> [a, b, m, n]
// 也许应该要求相对于两个 base rank 的 extra rank 相等时，
// 才使用 zip + map，从而达到下面的效果：
mul21: ([a, m, n], [a, n]) -> [a, m, n]
// 而按照当前的定义，我们将得到：
mul21: ([a, m, n], [a, n]) -> [a, a, m, n]
```

TODO 用 dependent type 描述 `extend2` 的类型将会是很有趣的挑战。

```cicada
claim extend2: (
  fn: (
    Tensor(firstShape),
    Tensor(secondShape),
  ) -> Tensor(resultShape)
) -> (
  Tensor(???),
  Tensor(???),
) -> Tensor(???)
```

# 10: Doing the Neuron Dance

如何构造更复杂的，模仿人类神经网络的函数，作为 target 函数呢？

用函数复合就可以吗？

称 activation functions 为 deciders，
activation 来自神经网络的比喻，
但是 decider 这样的命名更接地气。

> Functions like relu are known
> as artificial neurons. Each
> neuron has a linear part, like
> linear, and a nonlinear
> decider like rectify.

引入 relu 函数。

> **The Rule of Artificial Neurons**
>
> An artificial neuron is a parameterized linear function
> composed with a nonlinear decider function.

> Let's see an illustration of
> how multiple uses of relus
> can be combined to do more interesting things.

获得用 relu 的复合模拟更复杂的函数的直觉。

用 `halfStrip` 和 `fullStrip` 为例子，
因为根据勒贝格（Lebesgue）积分的方案，
任何函数都可以被分解为 `halfStrip` 和 `fullStrip`，
即 piecewise-linear approximation。

这类方案叫做 universal approximation。

# 11: In Love with the Shape of Relu

这一章学习神经网络的 layer，
layer function 是特殊的 target function，
有一样的类型声明。

有不同类型的 layer：

- dense layers (fully-connected layers)

  In general, the layer function of a dense
  layer with `m` neurons takes one input tensor of shape `[n]`,
  and parameters weight of shape `[m, n]` and bias of shape `[m]`,
  and invokes each of those `m` neurons on input,
  to produce output tensor of shape `[m]`.

  We say that the width of this layer is `m`.

> **The Law of Dense Layers**
>     (Initial Version)
>
> A dense layer function invokes m neurons on an n-element
> input tensor-1 and produces an m-element output tensor-1.

> **The Law of Dense Layers**
>     (Final Version)
>
> A dense layer function invokes m neurons on an n-element
> input tensor-1 that produces an m-element output tensor-1
> in a single invocation of *-2-1.

注意，这里对 `linear` 的定义要用 `matrixVactorMul`，而不能用 `dot`。

观察这里 `matrixVactorMul` 和 `dot` 的定义，
也许我们在处理 tensor 的函数的时候，
应该放弃传统线性代数中，只有行向量与列向量才能 `dot` 的限制，
也许行列向量的发明只是为了书写矩阵乘向量的时候方便。

> The list of shapes of the tensor2 and
> tensor1 parameters necessary for a layer is
> known as the _shape list_ of the layer.

在带有 dependent type 的语言中，
shape list 就是 layer 的类型参数。

这里很多篇幅用来处理 `k-relu` 的 currying，
看来带有自动 currying 的语言是必要的。

> The problem is that network functions, as
> in 1-relu, 2-relu, and 3-relu above, must
> take their arguments t and θ one at a time.

# 12: Rock Around the Block

前一章定义的 `k-relu`，
给出了 `k` 之后，
对所接受的 parameters 完全没有约束。

parameters 的 shape list 必须作为分离的 metadata 给出，
这一章我们改进这一点。

> We begin by introducing blocks.
> (A block is short for network building block.)
> A block associates a layer function with its shape list.

可以说是带有一些运行时的类型信息了，
也许可以在 compose block 的时候用来检查 shape 是否符合？

- 其实不是，在做 composition 的时候，
  shape list 只是简单地被 append 到一起，
  shape list 之间并没有什么相互的约束（constraints）。

> Now let's see the real magic in these blocks.
> A neural network is fully described by a
> network function and a shape list.

> Oh, that means a neural
> network can also be a block!
> So stacking blocks together
> also produces another block.

找到了！什么？
范畴的态射。
那是带有 shape list 的 layer functions。

有 composition 的地方，就有范畴与它的态射，
而上一章的 `k-relu` 显然是在做 composition，
因此要找范畴与态射。

但是，如果真的是范畴的话，
单位元是什么？
就是对 输入 tensor 而言的 identity function，
并且忽略 parameters。

想要成为范畴，
还需要检查复合是满足结合律的，
这是显然的。

> **The Law of Blocks**
> Blocks can be stacked to form bigger blocks
> and complete networks.

# 13: An Eye for an Iris

> Degrees of belief is the confidence we have
> about a certain statement.

> In general, the layers closer to the input are
> wider than the layers closer to the output.

> The layers closer to the input are responsible
> for learning some of the more primitive
> characteristics of the data set, and the layers
> closer to the output learn more advanced
> characteristics based on the output produced
> by the earlier layers.

Breaking the symmetry between neurons
-- 使用随即的初始 weights。

He initialization for networks that use rectify:

> ... that formula makes sure that in deep
> networks, the weights stay in a very tight
> cluster around 0.0 so that when a scalar from
> the input tensor is multiplied with weights in
> each layer, the result neither explodes nor
> vanishes.

> **The Rule of Layer Initialization**
>        (Initial Version)
> The bias tensor-1 of a layer is initialized to contain only 0.0
> The weight tensor-2 of a layer is initialized to random scalars
> with a central value of 0.0 and a variance of 2/n where n is the
> length of the input to the layer.

用部分数据来训练，用另一部分数据来测试。

> On larger data sets, the general guideline
> is to use 20% of the data set for testing.
> Since our data set here is quite small, we
> limit the test set to 10%.

> A model is an approximation of an
> _idealized_ function represented by the data
> set. This idealized function yields, for
> every x in the xs of the data set, the
> corresponding y from ys, but also
> produces a y for any given x, even if it is
> not in xs.

这一章的 `randomTensor` 需要用到正态分布。
要写一个能生成符合正态分布的随机数的函数。

引入 model 的概念，
每次 training 都会获得一个 model，
但是只是用函数来实现 model 可能是不合适的，
因为好的 model 的 parameters 应该被保存下来。

# Interlude VI: How the Model Trains

> [for classifiers]
> We say that the model is accurate for
> those inputs where the two classes are
> the same.

> When the classes are not the same,
> we call them _classification errors_.

> The ratio of the number of accurate
> classifications to the total number of
> test inputs we have is known as the
> _accuracy_ of the model.

当决定算法的合适参数（options）时，
可以尝试不同的参数组合，
然后用 accurate 来判断参数是否合适。

> This way of testing different
> combinations for the best one is known as
>
>   _grid search_.

尽管这里把 `grid-search` 定义为了一个语法关键词，
但是也有用函数实现 `grid-search` 的方案，
并且不依赖 dynamic scope。

这里像是 `grid-search` 这种基于实验的方案，
让我感到很不舒服，也许是因为与（可以被机器学习的） parameters 相比，
人们对这些 hyperparameters 的理解并没有那么透彻，
也许科学的探索就是需要这些实验。

# Interlude VII: Are Your Signals Crossed?

利用神经网络，来解码带有噪声的信号。

介绍莫尔斯码，以及用 tensor 表示莫尔斯码的方式。

为了在书中给出 signal 例子时方便，
还设计了一种代表 signal 的 tensor 的缩写，
这也是一种简单的压缩数据的方案。

`tenserZip` -- `[d, n] -> [n, d]` -- 与矩阵转置类似。

> **The Law of Zipped Signals**
> A signal-2 is formed by zipping signal-1s, and the signal-2 as well
> as its constituent signal-1s all have the same number of segments.

由于信息开始的时间可能是任意的，
所以需要使用卷积来消除信号中开始时间的差异。

# 14: It's Really Not That Convoluted

> To decode Alice and Bob's messages,
> we need to now learn about _correlation_
> between two 1-dimensional signals.

> Correlation is a way to detect the
> occurrence of a pattern anywhere within
> a signal.

> We detect a pattern by _scanning_ the
> signal from beginning to end and
> measuring the similarity between a
> portion of the source signal and the
> pattern.

假设有一个已知的信号模式（pattern signal），
将它在需要被识别的对象信号（target signal）上扫过（scan），
然后逐个度量当前信号和信号模式的耦合程度（correlation），
就能在对象信号上找到最符合信号模式的位置。

可以想像，在带有卷积网络中，
开始的时候 pattern signal 是未知的，
需要在学习的过程中找到，因此它们是 parameters。

target signal 在书中被称做 source signal，
pattern single 也叫做 filter 或者 kernel。

注意在 scan 的时候，
要用一半 pattern 从边界开始对比，
这样能够使得结果信号（result signal）
与 target signal 的长度相等，
也能够囊括边界上的 partial match。

> **The Law of Correlation**
>   (Single Filter Version)
> The correlation of a filter of length m with a source signal1 of
> length n, where m is odd (given by 2p + 1), is a signal1 of
> length n obtained by sliding the filter from overlap position −p
> to overlap position n − p − 1, where each segment of the result
> signal1 is obtained by taking the dot product of the filter and the
> overlap in the source at each overlap position.

> What are features?
> Features are specific kinds of patterns
> that we look for in the source.

刚好我最近的工作在处理属性图（attributed graph）
中的特征识别（feature recognition），
很巧在这里遇到 feature 一词。
也许属性图中的特征也可以用卷积神经网络来识别。

> **The Rule of Filters**
> Filters are tensor parameters in a θ.

> In a typical neural network classifier,
> we're simultaneously trying to detect
> multiple features in a single source.

多个 shape 相同的 filter
可以组成一个 filter bank tensor (or just bank)。

> **The Law of Correlation**
>    (Filter Bank Version)
> The correlation of a filter bank of shape (list b m d) with a
> source signal2 of shape (list n d) is a signal2 of shape (list n b)
> resulting from zipping the b signals1 resulting from correlating
> the b filters2 in the bank with the source.

为了保持多个卷积 layer 可以复合，
对结果做了一次 sum。

TODO 以图片识别为例，从直觉上解释高维度 single 的 correlation。

# 15: …But It Is Correlated!

这两章的标题很棒。

> It's Really Not That Convoluted.
> But It Is Correlated!

因为 CNN 的 C 在原始的论文中代表 convolution（mirrored correlation），
但是在现代的实现中，没有必要做 mirror。

> Since filters are learned during training of
> the network, it does not matter if we mirror
> them or not -- they are learned in the
> appropriate direction. So we can avoid the
> mirroring, leaving just the correlation.

TODO

# Epilogue - We've Only Just Begun

## 1 Mathematical foundations

The most relevant mathematical fields:

- Linear Algebra
- Probability and Statistics
- Vector Calculus

Condensed presentation of the necessary mathematics:

- Deep Learning [^1]

Learning from first principles:

- Coding the Matrix [^2]
- Bayesian Statistics the Fun Way [^3]

## 2 Data-generating distributions

> Data sets are a curious entity. They appear like there's nothing
> particularly interesting about them. They just contain a large
> number of points. But in reality, data sets provide us with a
> sampling of the outputs of a _data-generating process_.

> These data-generating processes can be understood in probabilistic
> terms. We associate them with an ideal _probability distribution_,
> which maps each point being generated by the process to the
> probability of its occurrence. We call this the _data generating
> distribution_.

这里对 probability distribution 的定义很棒，

> [it] maps each point being generated by the process
> to the probability of its occurrence.

> The process of learning the θ of a neural network constructs an
> internal approximation of this data generating distribution, and
> applies it to the specific task at hand.

有了所学习到的参数 θ，
就确定了所学习到的函数，
就能重新用来生成数据了。

## 3 Tasks

> Deep learning is all about doing interesting things. The cognoscenti
> have given a somewhat boring name to these interesting things. They
> call them _tasks_.

给机器学习所能处理的问题简单分类：

- Regression（通过函数拟合来做预测）
- Classification（比如鸢尾花的分类问题）
- Translation（比如莫尔斯电码到拉丁字母的的翻译）
  - Style Transfer（比如图片美化，比如自动生成梵高风格的画）
- Generation（比如现在最火的 ChatGPT，生成图片，自动谱曲，等等）

> Neural networks are always designed for a specific task that drives
> the choice of underlying functions and structures. Here we discuss
> some of the ideas that influence the design space.

## 4 Other loss functions

> Cross-entropy loss is usually used in conjunction with a softmax
> decider (we'll see this one soon). In combination, these two
> functions enable training a network with more accuracy and using
> fewer revisions.

> This function [l1-loss] can be used when we don't want outliers in
> the dataset to disproportionately influence the loss.

## 5 More deciders

> The only decider in previous chapters is _rectify_. While it has a
> number of very useful properties and is extremely simple, _rectify_
> is a rather recent development. Earlier neural networks relied on
> nonlinear functions, for example, the _logistic sigmoid_ and the
> hyperbolic _tanh_.

leaky rectify 对于避免某些训练时出现大量 0 的情况。

有方案[^6]可以通过学习获得 leaky rectify 中的反向倾斜角的角度，
而不是作为一个在训练过程中不变的 hyperparameter。

> This function [softmax] always produces a tensor1 that meets the
> criterion of a probability distribution -- each element in it is
> between 0.0 and 1.0, and the elements sum to 1.0. Because of this
> property, a softmax decider is used in conjunction with the
> cross-entropy loss function (which expects a probability
> distribution), making it ideal for the last layer of a classifier
> neural network.

## 6 Higher-dimensional signals

一维的 signal 包括：

- sounds
- seismic vibrations
- variability of temperature

高维的 signal 包括：

- images
- videos

> Higher-dimensional signals give rise to networks that have much
> larger θs since we're now dealing with a much larger amount of
> data. It is not uncommon to see θs containing hundreds of millions
> of scalar parameters in them.

Examples of these networks that make for
extremely interesting architectural explorations:

- AlexNet [^7]
- ResNet [^8]
- VGG [^9]

## 7 Natural language systems

> Text-based tasks such as translation and text classification require
> the processing of natural languages. A neural network designed for
> natural language processing usually relies on very specific layer
> structures that allow the network to learn the relationship between
> words in different parts of a piece of text.

> These networks can learn how ideas are threaded through a sequence
> of sentences and reproduce them as necessary in tasks like
> translation and summarization. Networks such as GPT-3 [^10] carry
> billions of scalars in their θs and are able to handle very complex
> tasks.

> There are two main kinds of architectures used for text processing.
> There is a _recurrent_ neural network architecture that uses an
> output associated with a prior token for processing a future token.
> This allows the network to develop information about the context in
> which a given token appears.

> Recurrent networks are rapidly being replaced by a newer form of
> network based on the idea of _attention_ [^11]. The difference in
> attention-based networks is that they work on whole sequences rather
> than one token at a time. They include a processing block known as a
> _transformer_ that uses attention to provide a mechanism with which
> the network can learn how each token in the input sequence
> influences other tokens.

构造一个简单的使用 attention 和 transformer 的网络，
可以看作是 The Little Learner 的有趣练习，
毕竟现在 ChatGPT 非常火。

## 8 Generative networks

有两种主要的 generative network architectures：

- variational autoencoder [^12]
- generative adversarial network, or GAN [^13]

variational autoencoder 有 encoder 和 decoder 两部分：

- encoder 压缩数据成 code，
- decoder 可以反过来把随机的 code
  还原成类似原数据的数据。

这听起来比 ChatGPT 还有趣，感觉类似于人类的想象力，
比如发现新的图片，乐曲，字体等等。

要知道，人们发现新的数学也在于想象力，
可否用这种 network 来发现新的数学呢？

generative adversarial network 也有两部分：

- generator -- 把随机数变为带有想要属性的数据，
- discriminator -- 用来筛选所生成的数据。

> The clever bit about GANs is that their training is designed in a
> way that both penalizes the discriminator if it is wrong in making a
> judgment and penalizes the generator if it generates the wrong kind
> of output.

generative adversarial network（生成对抗网络），
中的 adversarial（对抗） 就在于此。

这个 clever bit 听起来也是有趣的 idea，具体如何实现呢？

> Training the discriminator and generator together ensures that the
> generator learns how to produce samples that the discriminator will
> accept and the discriminator learns to accept only samples that
> satisfy the application requirements.

## 9 Practical things

> One of the most important elements of the design of a neural network
> is its _capacity_, which can roughly be thought of as the number of
> scalars in its θ.

> If the network has too few, then it won't learn enough about the
> training set, a condition known as _underfitting_.  If it has too
> many, then it will learn to be very specific to the training set,
> a condition known as _overfitting_.

> Even if a network has the appropriate capacity, training it with too
> few revs can lead to underfitting, while training it with too many
> can lead to overfitting.

> Either underfitting or overfitting or both might cause the network
> to not perform optimally. The network will fail to _generalize_ to
> points that lie outside the training set. Selecting networks with
> the right capacity and choosing the hyperparameters carefully is a
> very critical part of the network training process.

这里，可能是因为我们还欠缺足够强大的数学理论来理解 hyperparameters，
因此实验就很重要，最好不要满足于找到合适的 hyperparameters，
而是要勇于做关于 hyperparameters 的假设，
然后用实验数据来验证假设。

这样就和科学研究非常类似了。

实用的技巧：

> First, it always helps to properly track the metrics of a network as
> it trains.  Tracking the training loss, i.e., the value produced by
> the loss function at each of the revs, helps follow how well the
> network is training.

设计结构化的 log 来记录实验过程。

> Second, using a proper validation set to track the relevant
> performance metric associated with the network (such as accuracy of
> predictions) helps determine if the training is proceeding
> correctly.

比如之前用到的 grid-search。

> Third, techniques like _regularization_ are used to manage the
> effectiveness of training. Regularization is the name given to a
> technique of adding an additional term to the loss function that
> influences the direction the gradient descent takes. See Good-fellow
> et al. [^1] for examples of regularization.  This helps train the
> network without under- or over-training it, which improves the
> overall performance of the network for its designated task.

> Fourth, initialization of weights is a significant part of
> training. He et al. [^6] proposed the initialization scheme used in
> this book. This scheme is useful with rectifier-based networks.
> Other schemes such as the one proposed by Glorot et al. [^14] help
> with other types of networks.

## 10 Onwards, little learners!

> This epilogue is a whirlwind tour of the topics and
> sources for curious readers to pursue. With the
> understanding of neural networks gained from this book, we
> hope these topics are less formidable than they would
> otherwise have been.

> À bientôt

# Appendix A: Ghost in the Machine

介绍了利用 Dual Number 进行自动微分的方法，
不用对函数复合而成的表达式做符号处理，
只需要扩展 Number 的定义，
并且再扩展 primitive function，
不需要改变 compound function 的定义，
就能完成自动微分。

据说这个方法是 Clifford 发明的，
Sussman 在讲经典力学的 Scheme 书中也用了这种方法。

在无类型语言中，
可以自由地扩展 primitive function
（需要小心避免递归定义）。
在带有类型检查的语言中，
可能需要重新定义一套 primitive function
（正如我们在 JS/TS 实现中做的那样）。

我们在什么地方可以用类似的技巧呢？

注意不用修改已有的定义就能做扩展，
也是 propagator 的主题。

# Appendix B: I Could Have Raced All Day

TODO

# References

[^1]: I. Goodfellow, Y. Bengio, and A. Courville, Deep Learning. MIT Press, 2016.

[^2]: P. N. Klein, Coding the Matrix: Linear Algebra Through Applications to Computer Science. Newtonian Press, 2013.

[^3]: W. Kurt, Bayesian Statistics the Fun Way. No Starch Press, Inc., 2019.

[^4]: L. Hui and M. Belkin, “Evaluation of neural architectures trained with square loss vs cross-entropy in classification tasks,” in 9th International Conference on Learning Representations, ICLR 2021, OpenReview.net, 2021.

[^5]: A. L. Maas, “Rectifier nonlinearities improve neural network acoustic models,” 2013.

[^6]: K. He, X. Zhang, S. Ren, and J. Sun, “Delving deep into rectifiers: Surpassing human-level performance on imagenet classification,” in Proceedings of the IEEE International Conference on Computer Vision (ICCV), December 2015.

[^7]: A. Krizhevsky, I. Sutskever, and G. E. Hinton, “Imagenet classification with deep convolutional neural networks,” in Advances in neural information processing systems, 2012.

[^8]: K. He, X. Zhang, S. Ren, and J. Sun, “Deep residual learning for image recognition,” in IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2016.

[^9]: K. Simonyan and A. Zisserman, “Very deep convolutional networks for large-scale image recognition,” in International Conference on Learning Representations, 2015.

[^10]: T. Brown, B. Mann, N. Ryder, et al., “Language models are few-shot learners,” in Advances in Neural Information Processing Systems (H. Larochelle et al., eds.), vol. 33, Curran Associates, Inc., 2020.

[^11]: A. Vaswani, N. Shazeer, N. Parmar, et al., “Attention is all you need,” in Advances in Neural Information Processing Systems (I. Guyon et al., eds.), vol. 30, Curran Associates, Inc., 2017.

[^12]: D. P. Kingma and M. Welling, “Auto-Encoding Variational Bayes,” in 2nd International Conference on Learning Representations, ICLR 2014, Banff, AB, Canada, April 14-16, 2014, Conference Track Proceedings, 2014.

[^13]: I. Goodfellow, J. Pouget-Abadie, et al., “Generative adversarial nets,” in Advances in Neural Information Processing Systems (Z. Ghahramani, M. Welling, et al., eds.), vol. 27, Curran Associates, Inc., 2014.

[^14]: X. Glorot and Y. Bengio, “Understanding the difficulty of training deep feedforward neural networks,” in Proceedings of the Thirteenth International Conference on Artificial Intelligence and Statistics, PMLR, 2010.
