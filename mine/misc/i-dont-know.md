---
title: Things I don't know
subtitle: Explicitly spoken out to urge myself to learn.
author: Xie Yuheng
---

# Galois Theory

- cover space and galois theory
- formal concept analysis and galois connection

# Formal Concept Analysis

# Linear Logic

# Probabilistic Programming

# Lambda Encoding of inductive types

> Be really good at using lambda to encode inductive datatypes

- learn Aaron Stump's cedille: https://cedille.github.io
- learn Fu Peng's gottlob: https://github.com/Fermat/gottlob
  - https://fermat.github.io
- Alternative encoding: https://gist.github.com/zmactep/c5e167c86fb8d80dcd5532792371863f

I have learned lambda encoding from Fu Peng's works.

- https://github.com/cicada-lang/lambda/tree/master/docs/tests

How to use lambda encoding to understand the duality between constructor and eliminator?

- Using function application as eliminator is the key?

# Homotopy type theory

# Equivalence between Recursive functions

Maybe impossible.

If impossible, why?

Also learn about using `fixpoint` as keyword.

https://en.wikipedia.org/wiki/Fixed-point_combinator

https://docs.google.com/viewer?url=https%3A%2F%2Fwww.ps.uni-saarland.de%2Fcourses%2Fsem-prog97%2Fmaterial%2FYYWorks.ps&embedded=true&chrome=false&dov=1

infinite normal forms:

- boehm tree
  https://en.wikipedia.org/wiki/Continued_fraction
- read henk barendregt's lambda book

# Practical usage of Coq and Lean for proving theorems

Also learn CoC when learning Coq.

https://en.wikipedia.org/wiki/Calculus_of_constructions
https://ncatlab.org/nlab/show/calculus+of+constructions

# Cubical Type Theory

# Coinductive datatypes

Not just record type?

# Graph based implementation of Lambda Calculus

To solve the following problem:

> Normal forms of Parigot numerals are exponential in size,
> but a reasonable term-graph implementation
> should be able to keep them linear via sharing.

I already know how to implement interaction nets,
which are linear, but a good starting point.

Examples of formal semantics -- Knuth, 1970
PhD theses -- C P wadsworth, 1971
PhD theses -- R Statman, 1974

# A unified language

as I learn these, I found that
I'd better have a language to help me
unify the different notations used by different authors
-- a sexp-based lisp language.

Note that, implementing is the unifying force.

- Let's start from Lambda Cube and CoC.

I feel most papers are hard to understand.

Maybe I am stupid, but I have some thinking about this recently.

I think if I have a syntax-ly unified language,
and I can transcript the notations of hard to understand papers
into my unified language,
understanding will be easier.

because i will be actively testing my understanding during transcripting.
