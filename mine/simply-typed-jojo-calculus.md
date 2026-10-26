---
title: Simply Typed JoJo Calculus
author: Xie Yuheng
keywords: [type system]
---

# Abstract

## Contains

# Introduction

# A review of lambda calculus

We assume the reader has a basic familiarity with the lambda calculus
and the concepts of bound variables, alpha-, beta- and eta-reduction.

We give a review of lambda calculus in this section,
to let the reader be familiar with our notations.

Since lambda application is not associative,
we use non-symmetrical syntax to avoid ambiguity.

- **[Definition]** Lambda expression contains,
  - Variable: `x`
  - Lambda application: `f(x)`
  - Lambda abstraction: `(x) => body`

In lambda expression,
I use `{}` to brackets to dis-ambiguity
direct application of abstraction to argument.

# A review of de Bruijn notation

The de Bruijn notation is a useful translation of lambda expression,
after which the name of a variable binding is placed near to the argument it binds.

The translation, denoted by `I( ... )`,

``` js
I( x ) = x
I( (x) => body ) = [x] I( body )
I( f(x) ) = {I( x )} I( f )
```

The effect of this translation is that function application
is translated from prefix notation to postfix notation.

For examples,

``` js
// Example abstraction
(x) => (y) => x(y)       --- [x][y]{y}x

// Example application
{(x) => (y) => x(y)}(z)  --- {z}[x][y]{y}x

// Example reduction steps
{(x) => {(y) => {(z) => z(d)}}(c)}(b)(a) --- {a}{b}[x]{c}[y][z]{d}z
{(y) => {(z) => z(d)}}(c)(a)             --- {a}{c}[y][z]{d}z
{(z) => z(d)}(a)                         --- {a}[z]{d}z
a(d)                                     --- {d}a

// Example reduction relations
(theta)   --- {(x) => N}(P)(Q)          --- {Q}{P}[x]N
          --> {(x) => N(Q)}(P)          --- {P}[x]{Q}N

(gamma)   --- {(x) => (y) => N}(P)      --- {P}[x][y]N
          --> (y) => {(x) => N}(P)      --- [y]{P}[x]N

(gamma-C) --- {(x) => (y) => N}(P)(Q)   --- {Q}{P}[x][y]N
          --> {(y) => {(x) => N}(P)}(Q) --- {Q}[y]{P}[x]N

(g)       --- {(x) => (y) => N}(P)(Q)   --- {Q}{P}[x][y]N
          --> {(x) => {(y) => N}(Q)}(P) --- {P}[x]{Q}[y]N

(beta-e)  --- /* no equivalent */       --- {Q}...[y]N
          --> /* no equivalent */       --- ...{Q}[y]N
          // where `...` is well balanced.
```

# Specification of stack machine

When postfix notation occur we can use stack machine to provide semantics.
(We learned this from the programming language Forth.)

# Adding function composition into de Bruijn notation

- **[Claim]** When translating lambda expressions to de Bruijn notation, function composition does not occur.
- **[Proof]** TODO

# The algebraic structure of the space of simple type

What is the algebraic structure of the space of simple types?

We have quotation `{ A }`, and unquote `{ A } exe == A`.

Not freely generated group, but groupoid (typed algebraic structure),
because we can not compose freely,
for example, `B (- A)` is not valid.

Thus not groupoid, because not every element has inverse, but every element has right inverse.
The element `(- A)` is right invers of `A`, but not left inverse of `A`,
`{ A (- A) } == { }` but `{ (- A) A } != { }`.

Thus we must use category theory.

Not cartesian closed category but finer,
for exponential object `(A) -> B` is farther factored into `(- A) B`.

No, it is not even category theory.
Because in category theory, to judgement whether two arrows `f` and `g` are composable,
we compare the codomain of `f` and the domain of `g`,
if these two objects are equal, we know these two arrows are composable.

In category theory, type of an arrow is its domain and codomain.

While, in jojo calculus, type of a jojo is jojo.
This way of encoding type information is scalable to higher dimensions.

Can this view enable us to understand something new?
- Cell-complex?
- Algebraic theory of two dimensional objects?

# To specify algebraic system

To specify logic deduction system, we use inference rules,
which is studied in details in Martin-Löf's type theory.

To specify algebraic system, we use algebraic equations.

The prototypical technique for specifying algebraic structure,
is the presentation of group.

The question is how to translate notions in Martin-Löf's type theory
to notions of algebraic structure.
- How to translate judgement?
- How to translate hypothetical judgement?
- How to translate the judgement form `_ prop`?
- How to translate the judgement form `_ true`?
- How to translate rules of martin-löf's type theory?

In presentation of algebraic theory, we use equation to specify relation between elements.
Equation (relation) in presentation is about computation rule (reduction, substitution, rewriting),

We can view lambda abstraction as **partial equation**,
and partial equation is first class element in the algebra.

To introduce a new element to the algebra structure,
we need to give it a name and specify its computation rule.
Take combinatory logic as a example (although it is not algebra structure), in which we can define

``` js
K(x, y) = x
S(x, y, z) = x(z, (y(z)))
```

If we are not allowed to use first class partial equation in our algebra structure, we will get a system like combinatory logic.

By using first class partial equation, we can construct anonymous element,
just like in lambda calculus, lambda abstraction construct anonymous function.

We need "arguments in stack" and "multiple return value"
to make the algebra close under composition.
- `1 2 cons` denotes one element in the stack,
  and one element in the algebra.
- `1 2` denotes two elements in the stack,
  and one element in the algebra (this kind of composition is "free").

Thus we study type theory by,
- firstly view it as algebra (maybe start from group theory),
- then extend the algebra by introducing new elements and equations
  For examples,
  - introducing lambda (first class partial equation),
  - introducing new inductive types.

Permutation group is simple to construct,
by defining functions that permute elements on top of the stack.

``` js
{ a | a a == id }

swap : { (- A) (- A) A A }
swap = { [x] [y] x y }
swap swap == id
```

# Symbolic dynamics

We can use symbolic dynamics as semantics,
because we have a simple step function.

The `cut` operator can be viewed as continuous map (respecting composition) from the `exp-space` to `type-space`.
- The shift operator and ergodic theory.
- Combinatorics on words.
  - Automata and forbidden factor (substring) of infinite word.

# Appendixes

# References
