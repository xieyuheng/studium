---
title: 逐步精细
module: stepwise
date: 2026-11-08
---

- [题解](#题解)
- [问题](#问题)
- [试错法](#试错法)
- [部分解与回溯](#部分解与回溯)
- [约束条件](#约束条件)
- [递归搜索](#递归搜索)
- [打印棋盘](#打印棋盘)

# 题解

本问取自 Niklaus Wirth 写于 1971 的论文
"Program Development by Stepwise Refinement"。

尝试把「逐步精细」（Stepwise Refinement）方法，
和基于 markdown 的「文学式编程」相结合。

# 问题

给定一个 8 × 8 的棋盘，放置 8 个皇后，
使得任意两个皇后都不能互相攻击：

- 不能位于同一行；
- 不能位于同一列；
- 不能位于同一条对角线。

# 试错

这个问题适合用「试错法」解决。
设候选解的集合为 `A`，约束条件为 `p`，
那么一个解就是满足 `(x in A) and p(x)` 的 `x`。

最直接的程序会不断生成 `A` 中的候选解，
直到找到一个满足 `p` 的解：

```meta-lisp skip
(define (trial-and-error)
  (unless (has-no-more-candidate)
    (let ((candidate (generate-next-candidate)))
      (cond
        ((candidate-is-solution candidate)
         (collection-solution candidate))
        (else (trial-and-error))))))
```

但是 `A` 通常非常大，穷举所有候选解并逐个检查 `p` 并不现实。

一个重要的改进是把约束条件拆成两个条件：

```text
p = q and r
```

其中 `r` 用来快速排除明显不可能成功的候选，
`q` 再检查剩余约束条件。

令：

```text
Br = { x | (x in A) and r(x) }
```

于是只需要生成 `Br` 中的元素，
而不是生成整个 `A`。

对于 8 皇后问题，`r` 可以是：

- 每一列恰好有一个皇后。

而 `q` 则是：

- 每一行至多有一个皇后；
- 每一条对角线上至多有一个皇后。

这样，候选集从所有棋盘布局，
缩小为逐列放置皇后的布局。

# 部分解与回溯

把潜在的解写成：

```text
x1 x2 ... x8
```

其中 `xi` 表示第 `i` 列皇后所在的行坐标。
我们采纳[代数记谱法](https://en.wikipedia.org/wiki/Algebraic_notation_(chess))，从下向上数行，并且从 1 开始数。

比如：

```text
4 2 7 3 6 8 5 1
```

代表：

```text
. . . . . . . Q
. Q . . . . . .
. . . Q . . . .
Q . . . . . . .
. . . . . . Q .
. . . . Q . . .
. . Q . . . . .
. . . . . Q . .
```

于是可以引入部分解：

```text
x1
x1 x2
x1 x2 ... xk
```

一个关键观察是：

> 如果一个部分解已经不满足约束条件，
> 那么无论怎样继续扩展它，
> 都不可能得到完整解。

因此，搜索可以在发现冲突时立即回退。
回溯可以用深度优先搜索实现，
而深度优先搜索可以在递归过程中自然实现，
不需要额外分配内存来保存搜索状态。

在代码中我们可以称「部分解」为 `state`：

```meta-lisp
(define-type state-t (list-t int-t))
```

这样我们需要实现的，
就是一个以 state 为节点的，
深度优先的「解法树」（solution tree）搜索。

这是 `trial-and-error` 的一个递归变体：

```meta-lisp skip
(define (search-solutions progress solutions)
  (if (is-finished progress)
    solutions
    (search-solutions
      (step-progress progress)
      (list-map-concat solutions generate-next-solutions))))
```

对于我们问题而言，
从树中的一个节点，
生成所有子节点的函数可以称作
`generate-next-states`。

除了处理 8 × 8 的棋盘外，
我们显然也想处理一般的 n x n 棋盘，
所以我们引入一个 `size` 参数。

`generate-next-states` 要用到这个 `size` 参数，
才能知道分支的个数：

```meta-lisp
(claim generate-next-states
  (-> int-t state-t (list-t state-t)))
```

初步的实现方式是：

```meta-lisp skip
(define (generate-next-states size state)
  (list-map (list-range size)
    (lambda (i) (cons (iadd 1 i) state))))
```

这里我们发现，
为了在生成新 state 的过程中，
为了能用到更高效的 `cons`，
而不是每次都用 `list-append` 来复制 list，
state 作为列表保存部分解中元素的顺序必须是反向的。

部分解：

```text
x1 x2 x3
```

需要被表示为如下 state：

```meta-lisp skip
(@list x3 x2 x1)
```

# 约束条件

判断一个新放置的皇后是满足约束条件时，
需要检查它和所有已放置皇后的关系。

同一行的检查很直接。
对角线检查可以用行列距离表示：
两个皇后如果在同一条对角线上，
它们的列距离必须等于行距离。

部分解都是暂时满足约束条件的，
所以我们需要一个函数来判断新放置的皇后，
是否与已有的部分解中的某个皇后相互冲突。

```meta-lisp
(claim is-valid-new-column
  (-> state-t int-t
      bool-t))
```

列距离可以由行坐标之差给出，
行举例可以由递归时的列表 index 给出。

```meta-lisp
(define (is-valid-new-column state new)
  (list-every
    (list-map-index state
      (lambda (i old)
        (and (not (equal new old))
             (not (equal (iabs new old) (iadd 1 i))))))
    (equal true)))

(define (iabs x y)
  (if (int-greater x y)
    (isub x y)
    (isub y x)))
```

下面我们改进 `generate-next-states`，
使其在生成分支的时候，
直接过滤掉不满足约束条件的分支：

```meta-lisp
(define (generate-next-states size state)
  (list-map-concat (list-range size)
    (lambda (i)
      (let ((new (iadd 1 i)))
        (if (is-valid-new-column state new)
          (@list (cons new state))
          (@list))))))
```

# 递归搜索

遵循之前的递归搜索的范式：

```meta-lisp skip
(define (search-solutions progress solutions)
  (if (is-finished progress)
    solutions
    (search-solutions
      (step-progress progress)
      (list-map-concat solutions generate-next-solutions))))
```

我们可以实现针对我们问题的具体递归搜索：

```meta-lisp
(claim n-queens (-> int-t (list-t state-t)))

(define (n-queens n)
  (search-queens n 0 (@list (@list))))

(claim search-queens
  (-> int-t int-t (list-t state-t)
      (list-t state-t)))

(define (search-queens size length states)
  (if (equal length size)
    states
    (search-queens
      size (iadd 1 length)
      (list-map-concat states (generate-next-states size)))))
```

我们知道 8 皇后问题有 92 个解，可以用来做测试：

```meta-lisp
(define-test n-queens-test
  (assert-equal 1 (list-length (n-queens 1)))
  (assert-equal 0 (list-length (n-queens 2)))
  (assert-equal 2 (list-length (n-queens 4)))
  (assert-equal 92 (list-length (n-queens 8))))
```

# 打印棋盘

为了更好地观察结果，可以把行号列表打印成棋盘。

例如：

```text
. . . . . . . Q
. Q . . . . . .
. . . Q . . . .
Q . . . . . . .
. . . . . . Q .
. . . . Q . . .
. . Q . . . . .
. . . . . Q . .
```

```meta-lisp
(define-type board-t (list-t (list-t text-t)))

(define (format-board board)
  (text-join (list-map board format-board-row) "\n"))

(define (format-board-row row)
  (text-join row " "))
```

下面用 `state-to-board` 把 state 转化为棋盘，
注意列的列表要转化为行的列表。

```meta-lisp
(claim state-to-board (-> state-t board-t))

(define (state-to-board state)
  (let ((size (list-length state)))
    (list-map (list-range size)
      (state-to-board-row state))))

(define (state-to-board-row state index)
  (let ((row (iadd 1 index)))
    (list-map state
      (lambda (queen-row)
        (if (equal row queen-row) "Q" ".")))))
```

```meta-lisp
(define-test n-queens-board-test
  (assert-equal
    (format-board (state-to-board (car (n-queens 4))))
    (text-join
      (@list
        ". Q . ."
        ". . . Q"
        "Q . . ."
        ". . Q .")
      "\n"))
  (println (car (n-queens 8)))
  (println (format-board (state-to-board (car (n-queens 8))))))
```

打印的结果为：

```text
4 2 7 3 6 8 5 1
```

```text
. . . . . . . Q
. Q . . . . . .
. . . Q . . . .
Q . . . . . . .
. . . . . . Q .
. . . . Q . . .
. . Q . . . . .
. . . . . Q . .
```
