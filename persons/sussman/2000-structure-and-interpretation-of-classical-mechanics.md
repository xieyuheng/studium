---
title: Structure and Interpretation of Classical Mechanics
authors: [Gerald Jay Sussman, Jack Wisdom]
date: 2000
---

# My Motive

SICM 的目的在于，
利用函数式编程中的严格的符号系统，
来介绍经典力学和微分几何。

我学习 SICM 的动机是学会经典力学，
同时也学习用 Scheme 表达知识的方式。

构拟经典力学的历史：

- 初步地观察，使人们区分出空间与空间中的物体。
- 人们观察到物体在运动，
  运动的规律被总结为物体之间有相互作用，并称之为力。
- 人们观察到的相同的力对不同物体的作用效果不同，
  人们引入质量这个物体万有的参数来度量这种不同的作用效果。

[2025-02-13] 上面的动机大概是十年前的了。
现在的动机是 simulation，即完成下面的循环：

```scheme
(define (game)
  (loop (input)
        (simulation)
        (render)))
```

[2026-11-25] 在从 "The Little Learner" 学习机器学习的原理时，
发现用来实现梯度下降的算法，真的是在模拟物理上的「滑滑梯」。

看来在机器学习领域，很多算法的灵感来自物理知识与经验。

# Preface

> Classical mechanics is deceptively simple. It is
> surprisingly easy to get the right answer with fallacious
> reasoning or without real understanding. Traditional
> mathematical notation contributes to this problem.  ... In
> order that the reasoning be clear and unambiguous, we have
> adopted a more precise mathematical notation. Our notation
> is functional and follows that of modern mathematical
> presentations.

> We require that our mathemathematical notations be
> explicit and precise enough so that they can be
> interpreted automatically, as by a computer.

> Computational algorithms are used to communicate precisely
> some of the methods used in the analysis of dynamical
> phenomena. Expressing the methods of variational mechanics
> in a computer language forces them to be unambiguous and
> computationally effective. Computation requires us to be
> precise about the representation of mechanical and
> geometric notions as computational objects and permits us
> to represent explicitly the algorithms for manipulating
> these objects. Also, once formalized as a procedure, a
> mathematical idea becomes a tool that can be used directly
> to compute results.

# 1 Lagrangian Mechanics

> The motion of a system can be described by giving the
> position of every piece of the system at each moment.
> Such a description of the motion of the system is called a
> _configuration path_; the configuration path specifies the
> configuration as a function of time.

```scheme
(define-class motion-system-t ()
  :configuration-t type-t
  :configuration-path-t (-> time-t @configuration-t))
```

例如 juggling pin：

> The juggling pin rotates as it flies through the air; the
> configuration of the juggling pin is specified by giving
> the position and orientation of the pin. The motion of the
> juggling pin is specified by giving the position and
> orientation of the pin as a function of time.

对于 juggling pin 而言，
configuration = position + orientation。

> The function that we seek takes a configuration path as an
> input and produces some output. We want this function to
> have some characteristic behavior when the input is a
> realizable path. For example, the output could be a
> number, and we could try to arrange that the number is
> zero only on realizable paths. Newton's equations of
> motion are of this form; at each moment Newton's
> differential equations must be satisfied.

> However, there is a alternate strategy that provides more
> insight and power: we could look for a path-distinguishing
> functionthat has a minimum on the realizable paths -- on
> nearby unrealizable paths the value of the function is
> higher than it is on the realizable path. This is the
> _variational strategy_: for each physical system we invent
> a path-distinguishing function that distinguishes
> realizable motions of the system by having a _stationary
> point_ foreach realizable path. For a great variety of
> systems realizable motions of the system can be formulated
> in terms of a variational principle.

两个函数如果都是 stationary point，
那么它们的线性组合显然也是 stationary point。

> In the Newtonian formulation the forces can often be
> written as derivatives of the potential energy of the
> system. The motion of the system is determined by
> considering how the individual component particles respond
> to these forces. The Newtonian formulation of the
> equations of motion is intrinsically a
> particle-by-particle description.

> In the variational formulation the equations of motion are
> formulated in terms of the difference of the kinetic
> energy and the potential energy. The potential energy is a
> number that is characteristic of the arrangement of the
> particles in the system; the kinetic energy is a number
> that is determined by the velocities of the particles in
> the system. Neither the potential energy nor the kinetic
> energy depend on how those positions and velocities are
> specified. The difference is characteristic of the system
> as a whole and does not depend on the details of how the
> system is specified.  So we are free to choose ways of
> describing the system that are easy to work with; we are
> liberated from the particle-by-particle description
> inherent in the Newtonian formulation.

## 1.1 The Principle of Stationary Action

> Let us suppose that for each physical system there is a
> path-distinguishing function that is stationary on
> realizable paths. We will try to deduce some of its
> properties.

通过我们对运动的直觉，推导出 path-distinguishing function。
能熟练地重复这里的推导过程，就算是学会这一节了。

### Experience of motion

根据我们的日常经验：

- 构型路径应该是连续且光滑的函数。

- 物理系统的运动不依赖于系统的历史。

- 物理系统的运动是确定性的，
  即只要知道了系统的当前状态（通常只是几个参数），
  系统的未来演化方式就是完全确定的。

### Realizable paths

对于可实现的构型路径，我们也有一些直觉上的要求：

- 如果一个路径是可实现的，
  那么这个路径的任意一个片段（segment）都是可实现的；
  反之如果一个路径被分成了很多片段，
  并且每个片段都是可实现的，
  那么这整个路径就是可实现的。

- 一个路径片段可实现与否，依赖于其上的每一个点。
  并且依赖于每一个点的方式都是一样的，
  每个点都是平等的，没有特殊的点。

- 一个路径片段可实现与否，只依赖于这个片段内的点。
  即路径片段的可实现性是一个局部属性。

因此所要找的路径区分函数，
是每一个瞬间的路径片段上的，
某种局部属性的聚合。

一种满足件的聚合方式是加法，
此时路径区分函数是就某个路径的局部属性而言，
在路径片段上的积分。

这里的注释是：

> We suspect that this argument can be promoted to a precise
> constraint on the possible ways of making this
> path-distinguishing function.

也许确实如此，可以模仿 William Lawvere 在证明 Brouwer 定理时，
原地构造了一个 category 的方式，来处理这里的推理。

> So we will try to arrange that the path-distinguishing
> function, constructed as an integral of a local property
> along the path, assumes an extreme value for any
> realizable path. Such a path-distinguishing function is
> traditionally called an _action_ for the system.

```
action := path-distinguishing function
```

> In order to pursue the agenda of variational mechanics, we
> must invent action functions that are stationary on the
> realizable trajectories of the systems we are studying. We
> will consider actions that are integrals of some local
> property of the configuration path at each moment.

> Let `γ` be the configuration-path function;

```scheme
(claim configuration-t type-t)
(define configuration-path-t (-> time-t configuration-t))
(claim γ (-> time-t configuration-t))
(claim γ configuration-path-t)
```

> `γ(t)` is the configuration at time `t`.

```scheme
(claim t time-t)
(claim (γ t) configuration-t)
```

> The action of the segment of the path `γ`
> in the time interval from `t1` to `t2` is:

```scheme
(claim t1 time-t)
(claim t2 time-t)
(claim definite-integral
  (-> (-> time-t real-t) time-t time-t real-t))
(claim F
  (-> configuration-path-t time-t real-t))
(define (S γ t1 t2)
  (definite-integral (F γ) t1 t2))
```

> where `F[γ]` is a function of time that measures some
> local property of the path. It may depend upon the value
> of the function `γ` at that time and the value of any
> derivatives of `γ` at that time.

> Traditionally, square brackets are put around functional
> arguments.  In this case, the square brackets remind us
> that the value of `S` may depend on the function `γ` in
> complicated ways, such as through its derivatives.

TODO

## 1.2 Configuration Spaces

状态空间
状态空间的维度
状态轨迹 状态路径

## 1.3 Generalized Coordinates

In order to be able to talk about specific configurations
we need to have a set of parameters
that label the configurations.

Xie:
这种对座标系的直觉理解
也指明了推广座标系这个概念的方向

Xie:
上面这句话也可以拆解成如下
- In order to be able to talk about something
  we need to have name them.
- In order to be able to talk about specific configurations
  we need to label the configurations.
- we use a set of numerical parameters
  to label the configurations.

Xie:
这里很多篇幅是用来作 座标系无关 讨论的
我想如果加入一个限制的话会更好
比如 限制流形 为 代数方程的零点

## 1.4 Computing Actions

## 1.5 The Euler-Lagrange Equations

## 1.6 How to Find Lagrangians

## 1.7 Evolution of Dynamical State

## 1.8 Conserved Quantities

## 1.9 Abstraction of Path Functions

## 1.10 Constrained Motion

# 2 Rigid Bodies
# 3 Hamiltonian Mechanics
# 4 Phase Space Structure
# 5 Canonical Transformations
# 6 Canonical Perturbation Theory
# References

- Hans Freudenthal,
  Didactical Phenomenology of Mathematical Structures,
  Kluwer Publishing Co., Boston, 1983.

- P. E. B. Jourdain,
  The Principle of Least Action,
  The Open CourtPublishing Company, Chicago, 1913.
