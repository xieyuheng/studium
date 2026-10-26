---
title: Substitutional pre-category
author: Xie Yuheng
date: 2018-06-20
keywords: [dependent type, categorical semantics, sequent calculus]
---

# Abstract

- In this paper I show a generalization of the structure of category,
  I call it "substitutional pre-category",
  to provide algebraic semantics for dependent type systems.

# Background

- Currently, people often use the concept of fibration
  to provide categorical model for dependent type systems.

- I wish to use a more direct approach to do the modeling,
  to guide language design and implementation.

- A generalization of the structure of category seems natural to me.

- My concern is, If we generalize the structure of category,
  then we can not directly reuse all the mathematical constructions
  that have been developed based on the structure of category.

- It seems,
  we have to re-develop all those constructions all over again,
  but I am willing to try.

# Motivating examples

## Composition of dependently typed functions

- Given two dependently typed functions :
  f : (A x -> B x)
  g : (B n -> C z)

- It is natural to unify `x` with `n',
  and to compose the two functions
  to get a function of type (A n -> C z).

## Composition in stack based concatenative language

- In a stack based concatenative language,
  the following generalized function composition is natural :
  f   : (T1, T2) -> (T3, T4)
  g   : (T, T3, T4) -> (T6, T7)
  f g : (T, T1, T2) -> (T6, T7)

- The semantics of the generalized composition are as following :
  - Suppose there are data of type (T, T1, T2) on the stack;
  - `f` takes data of type (T1, T2)
    and transform them to (T3, T4),
    leaving data of type (T, T3, T4) on the stack;
  - `g` takes data of type (T, T3, T4)
    and transform them to (T6, T7),
    leaving data of type (T6, T7) on the stack.

- The about semantics can also be explained by unification :
  [Thanks to Matt Oliveri, at HoTT group, for teaching me this.]
  - Adding extra type variable to each function :
    f   : (a, T1, T2) -> (a, T3, T4)
    g   : (b, T, T3, T4) -> (b, T6, T7)
  - Unify `a` with `(b, T)` we get :
    f   : (b, T, T1, T2) -> (b, T, T3, T4)
    g   : (b, T, T3, T4) -> (b, T6, T7)
  - Compose `f` and `g` :
    f g : (b, T, T1, T2) -> (b, T6, T7)
  - Drop the extra type variable :
    f g : (T, T1, T2) -> (T6, T7)

- Another example is :
  f   : (T1, T2) -> (T, T3, T4)
  g   : (T3, T4) -> (T6, T7)
  f g : (T1, T2) -> (T, T6, T7)

  - Similar treatment for this example
    is left to reader as exercise.

# The structure of category

- First we show the structure of category,
  to let readers be familiar with the notations I used here.

## Objects and Arrows

- To specify the structure of category,
  we need objects and arrows.

- And objects are used to type arrows,
  We use `object-t` to name the type of objects,
  And we often capitalize a name of an object.

``` agda
A : object-t
```

- We use `=>` to name the type constructor for arrows,
  and use it as an infix notation.
  [We leave `->` to be used in meta language.]

  We use `type-t` to name the type of type.

``` agda
(=>) : object-t, object-t -> type-t

A : object-t
B : object-t

f : A => B
```

- We need to be able to prove two objects are equivalent,
  thus, we need a proposition constructor to do this.

  Since proposition is type,
  a proposition constructor is also a type constructor,
  we use `object-eqv-t` to name this type constructor.

``` agda
object-eqv-t : object-t, object-t -> type-t
```

- We also need to be able to prove two arrows are equivalent,
  we use `arrow-eqv-t` to name this type constructor.

``` agda
arrow-eqv-t : (A => B), (A => B) -> type-t
```

## Identity and Compose

- For every object we have a unique identity arrow.

``` agda
identity : (A : object-t) -> (A => A)
```

- We can compose two arrows.

``` agda
compose : (A => B), (B => C) -> (A => C)
```

- Axioms of `identity` and `compose`

``` agda
identity-left :
  (f : (A => B)) ->
  (arrow-eqv-t f (compose (identity A) f))

identity-right :
  (f : (A => B)) ->
  (arrow-eqv-t f (compose f (identity B)))

compose-associative :
  (f : (A => B))
  (g : (B => C))
  (h : (C => D)) ->
  (arrow-eqv-t
    (compose f (compose g h))
    (compose (compose f g) h))
```

# The structure of substitutional pre-category

- From category to substitutional pre-category,
  I change the name `compose` to `cut`,
  in remembrance of Gentzen
  and the `cut rule` of his sequence calculus.

## Objects and Arrows

- To specify the structure of substitutional pre-category,
  we need objects and arrows as for category.

  We repeat basic constructions :

``` agda
A : object-t
B : object-t

(=>) : object-t, object-t -> type-t

f : A => B

object-eqv-t : object-t, object-t -> type-t

arrow-eqv-t : (A => B), (A => B) -> type-t
```

## A monoid of substitutions

- We also need a monoid of substitutions,
  we use `substitution-t` to name the type of substitutions.

- We need to be able to prove two substitutions are equivalent,
  we use `substitution-eqv-t` to name this type constructor.

``` agda
substitution-eqv-t : substitution-t, substitution-t -> type-t
```

- The monoid can act on objects,
  we use `substitute` to name the action.

``` agda
substitute : substitution-t, object-t -> object-t
```

- Axiom of `substitute`
  [suppose we use `(*)` to name the product in a monoid.]

``` agda
substitute-is-monoid-action :
  s1 : substitution-t
  s2 : substitution-t ->
  (A : object-t ->
    (object-eqv-t
       (substitute (* s1 s2) A)
       (substitute s2 (substitute s2 A))))
```

## Unification of Objects

- Given two objects,
  we need to be able to prove they are unifiable,
  we use `unifiable-t` to name this type constructor.

``` agda
unifiable-t : object-t, object-t -> type-t
```

- Axioms of `unifiable-t`

``` agda
unifiable-reflex : (A : object-t) -> (unifiable-t A A)

unifiable-commute : (unifiable-t A B) -> (unifiable-t B A)
```

- Given two unifiable objects,
  we can unify them to get a substitution.

``` agda
unify :
  (A : object-t)
  (B : object-t)
  (unifiable-t A B) ->
  substitution-t
```

- Axioms of `unify`

``` agda
unify-commute :
  (A : object-t)
  (B : object-t)
  (unifiable-t A B) ->
  (substitution-eqv-t
    (unify A B)
    (unify B A))

unify-respect-substitute
: (A : object-t)
  (B : object-t)
  (unifiable-t A B) ->
  (object-eqv-t
    (substitute (unify A B) A)
    (substitute (unify A B) B))
```

## Identity and Cut

- For every object we have a unique identity arrow.

``` agda
identity : (A : object-t) -> (A => A)
```

- We can cut two arrows,
  provide that,
  the codomain of the first arrow is unifiable with
  the domain of the second arrow.

``` agda
cut :
  (A => B)
  (C => D)
  (unifiable-t B C) ->
  ((substitute (unify B C) A) =>
   (substitute (unify B C) D))
```

- Axioms of `identity`

``` agda
identity-left :
  (f : (A => B)) ->
  (arrow-eqv-t f (cut (identity A) f))

identity-right :
  (f : (A => B)) ->
  (arrow-eqv-t f (cut f (identity B)))
```

- Axioms of `cut`

``` agda
cut-associative :
  (f : (A => B))
  (g : (C => D))
  (h : (E => F))
  (unifiable-t B C)
  (unifiable-t D F) ->
  (arrow-eqv-t
    (cut f (cut g h))
    (cut (cut f g) h))
```
