---
title: Essential concepts
---

# Continuous function

All function can be defined in our language is continuous.

If we view spaces as algebras,
a function between two spaces `A` and `B`
is a homomorphism between the two algebras.

To define a function from `A` to `B`,
we must specify how to map each generators of `A` to an element of `B`,
we can check a function is well defined by viewing boundaries as types.

Take _subdivision_ as an example,
if `B` is a subdivision of `A`,
we know `A` and `B` are equivalent,
if we view the equivalence as two functions
-- `f: A -> B` and `g: B -> A`
-- it is easy to define `f`,
while to define `g` we must map some generators
to trivial elements (`refl`) of `A`.

# Product space

TODO How to understand `Torus` as `Circle * Circle`?

```cicada
space Pair(Circle, Circle) {
  check path [ cons(base, loop) ]: endpoint [ cons(base, base), cons(base, base) ]
  check shell { cons(loop, loop) }: polygon [ ... ]
}
```

# Fibration

Every map can be viewed as a fibration,
the homotopy lifting property also hold.

```cicada
function Fiber(
  implicit E: Type,
  implicit B: Type,
  f: (E) -> B,
  y: B,
): Type {
  return exists (x: E) Id(B, f(x), y)
}
```

# Manifold

A closed manifold is defined as a connected finite homogeneous complex.

TODO
