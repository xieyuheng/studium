---
title: 胞腔复形
---

# syntax keywords

| dim           | cell        | complex    | spherical complex     |
|---------------|-------------|------------|-----------------------|
| 0             | vertex      |            | `==` (endpoints)      |
| 1             | edge        | `path`     | `gon` (polygon)       |
| 2             | face        | `shell`    | `hedron` (polyhedron) |
| 3             | block       | `building` | `choron` (polychoron) |
| (algebraicly) | (generator) | (element)  | (spherical element)   |

Notes:

- By "element" we mean elements in an algebraic structure.

- A spherical element can be used to specify
  the attaching map of a higher dimensional generator,
  i.e. specifying how the boundary of the generator
  is glued to the spherical element.

  After the gluing, the generator becomes part of the space,
  and the spherical element becomes the generator's boundary
  (boundaries as types).

# 0-dimensional

When we define inductive datatypes
without any higher order data constructors,
although the defined datatypes already have interesting structures,
we are only defining generators of 0-dimensional nodes.
The nodes can be recursively parameterized, thus infinity many,
and they can be parameterized in interesting ways.

When defining a complex, we can specify higher order generators,
the generators can be composed to get elements of the algebra.
The generators can also be parameterized, thus infinity many,

0-dimensional cell complex is inductively defined set.

# 1-dimensional -- logical syntax

```scheme
(define-space circle-t
  (0 (base circle-t))
  (1 (loop (== base base))))
```

# 2-dimensional -- logical syntax

Four ways to glue a square:

```scheme
(define-space sphere-t
  (0 (south sphere-t)
     (middle sphere-t)
     (north sphere-t))
  (1 (south-long (== south middle))
     (north-long (== middle north)))
  (2 (disk (gon south-long north-long north-long south-long))))
```

```scheme
(define-space torus-t
  (0 (origin torus-t))
  (1 (toro (== origin origin))
     (polo (== origin origin)))
  (2 (spoke (gon toro polo (rev toro) (rev polo)))))
```

```scheme
(define-space klein-bottle-t
  (0 (origin klein-bottle-t))
  (1 (toro (== origin origin))
     (cross (== origin origin)))
  (2 (disk (gon toro cross (rev toro) cross))))
```

```scheme
(define-space projective-plane-t
  (0 (start projective-plane-t)
     (end projective-plane-t))
  (1 (left-rim (== start end))
     (right-rim (== end start)))
  (2 (disk (gon left-rim right-rim left-rim right-rim))))
```

# 1-dimensional algebra -- logical syntax

```scheme
(the (== origin origin) (path toro toro toro polo))
(the (== origin origin) (path toro))
(the (== origin origin) (path (relf origin)))
(the (== origin origin) (path toro (relf origin)))
```

# 3-dimensional -- logical syntax

```scheme
(define-space torus3-t
  (0 (o torus3-t))
  (1 (x (== o o))
     (y (== o o))
     (z (== o o)))
  (2 (z-face (gon z y (rev z) (rev y)))
     (y-face (gon x z (rev x) (rev z)))
     (z-face (gon y x (rev y) (rev x))))
  (3 (body (hedron
             (x-face (gon ?y3 ?z2 (rev ?x2) (rev ?z0)))
             (y-face (gon ?y0 ?x2 (rev ?z1) (rev ?y3)))
             (x-face (gon ?z0 ?z1 (rev ?z2) (rev ?y0)))))))
```

The idea is that to specify the attaching map
from polyhedron to 2-skeleton of the space,
we can use a syntax like logic programming,
i.e. to use meta variable and linear unification
to specify how edges of polygons are glued together.

The same syntax can be used in polygon:

```scheme
(define-space torus-t
  (0 (origin torus-t))
  (1 (toro (== origin origin))
     (polo (== origin origin)))
  (2 (spoke (gon toro polo (rev toro) (rev polo)))))
```

可以用 `gon*` 来区分展开式与缩写：

- TODO 修改所有的例子，都用上这种区分。

```scheme
(define-space torus-t
  (0 (origin torus-t))
  (1 (toro (== origin origin))
     (polo (== origin origin)))
  (2 (spoke (gon*
              (toro (== ?0 ?1))
              (polo (== ?1 ?2))
              (toro (== ?3 ?2))
              (polo (== ?0 ?3))))))
```

或者递归地：

```scheme
(define-space torus3-t
  (0 (o torus3-t))
  (1 (x (== o o))
     (y (== o o))
     (z (== o o)))
  (2 (z-face (gon z y (rev z) (rev y)))
     (y-face (gon x z (rev x) (rev z)))
     (z-face (gon y x (rev y) (rev x))))
  (3 (body (hedron
             (x-face (gon ?y3 ?z2 (rev ?x2) (rev ?z0)))
             (y-face (gon ?y0 ?x2 (rev ?z1) (rev ?y3)))
             (x-face (gon ?z0 ?z1 (rev ?z2) (rev ?y0)))))))
```

当我们写 `(gon z y (rev z) (rev y))` 时，
其实是给出了一个连接 edge 而形成 polygon 的 pattern
-- `(gon ?0 ?1 (rev ?2) (rev ?3))`，
然后把 `z y z y` 填了进去。

- `path` 其实也是如此。
  如果 `path` 代表了 1-dim elements 的复合，
  那么 `shell` 也可以用类似 `gon` 的语法，
  给出 2-dim elements 的复合。

  - 这样就还有一个问题，
    即如何定义 cell complex 之间的连续函数。
    除了 n-cell 到 n-element 的对应关系之外，
    可能还需要把 n-element 的 boundary
    填到 n-cell 的 coordinate 中。

当我们写：

```scheme
(hedron
 (x-face (gon ?y3 ?z2 (rev ?x2) (rev ?z0)))
 (y-face (gon ?y0 ?x2 (rev ?z1) (rev ?y3)))
 (x-face (gon ?z0 ?z1 (rev ?z2) (rev ?y0))))
```

其实也是给出了一个连接 face 而形成 polygon 的 pattern，
然后把 `x-face y-face x-face` 填了进去：

```scheme
(hedron
 (?0 (gon ?y3 ?z2 (rev ?x2) (rev ?z0)))
 (?1 (gon ?y0 ?x2 (rev ?z1) (rev ?y3)))
 (?2 (gon ?z0 ?z1 (rev ?z2) (rev ?y0))))
```

即便把 `gon` 递归地展开，也是如此：

```scheme
(define-space torus3-t
  (0 (o torus3-t))
  (1 (x (== o o))
     (y (== o o))
     (z (== o o)))
  (2 (z-face (gon
               (z (== ?0 ?1))
               (y (== ?1 ?2))
               (z (== ?3 ?2))
               (y (== ?0 ?3))))
     (y-face (gon
               (x (== ?0 ?1))
               (z (== ?1 ?2))
               (x (== ?3 ?2))
               (z (== ?0 ?3))))
     (z-face (gon
               (y (== ?0 ?1))
               (x (== ?1 ?2))
               (y (== ?3 ?2))
               (x (== ?0 ?3)))))
  (3 (body (hedron
             (x-face (gon
                       (?y3 (== ...))
                       (?z2 (== ...))
                       (?x2 (== ...))
                       (?z0 (== ...))))
             (y-face (gon
                       (?y0 (== ...))
                       (?x2 (== ...))
                       (?z1 (== ...))
                       (?y3 (== ...))))
             (x-face (gon
                       (?z0 (== ...))
                       (?z1 (== ...))
                       (?z2 (== ...))
                       (?y0 (== ...))))))))
```

# 3-dimensional

To design how to introduce a (n+1)-dimensional cell `A` into a cell complex,
we need to specify an attaching map from the boundary of the cell `A`
to the n-skeleton of the cell complex.

We do this by giving the boundary of the cell `A`
a spherical coordinate space:

```scheme
(== <start> <end>)
(gon <edge> ...)
(hedron (<face> <var> ...) ...)
```

- If we always use boundary of cubes
  as the spherical coordinate space,
  we get cubical type theory.

A spherical coordinate space is a special cell complex,
thus we need to design how to define
continuous map between two cell complexes.

To define a continuous map between two cell complexes `A` and `B`,
we need to specify how each cell (say `x`) of `A` is mapped to
composition of cells (say `w * y * z` (without defining `*` for now)) of `B`
of the same dimension.

But the above is not enough,
because there are many ways (many orientations)
by which the cell `x` can be mapped to `w * y * z`.

TODO Using group as example to explain the above judgment.

To specify the orientation of the map,
we must specify another map between two cell complexes --
from the spherical coordinate space of `x`
to the boundary space of `w * y * z`.

- In the case of boundary cubes, it is enough to
  specify the mapping between all the corners.

The orienting map must not be degenerated.

Maybe recursion can stop here.

- Maybe a map between two spherical cell complexes is special,
  it is enough to just specify how cells are mapped to composition of cells,
  no need to further specify the orientation of the map,
  because it is unique.

  TODO Proof the above judgment.

  - To proof it, we must fully understand it first.

[question] Is the above judgment also true for manifolds?

- No, it is not true for the `Loop` -- `Gon(1)`.

  It is not about the space (both the domain space and the image space)
  is spherical or manifold or not,
  but about does the space have local-loop or not
  (or say, self-loop (a loop formed by one element itself)).

TODO Why the above judgment is not true for general cell complexes?

TODO For the above design to work, we must be able to get
the spherical coordinate space of the boundary
of the composite element -- `w * y * z`.

Maybe composition should be defined by composition of coordinate spaces.

Whenever we introduce a new type constructor,
we need to design it's type checking rules
-- what are it's elements.
We need to do this for `Cell` and `Skeleton` and `Boundary`.

```cicada
datatype Endpoint {
  start: Cell(0, Endpoint)
  end: Cell(0, Endpoint)
}

function Cell(
  dim: Nat,
  Space: Type,
  implicit Coordinate: SphericalType(Nat::sub1(dim)),
  attachment: (Coordinate) -> Skeleton(Nat::sub1(dim), Space)
): Type {
  return {
    // Overload function application,
    // to get a cell's boundary in the Skeleton
    // by applying a cell to element of Coordinate.
    apply: attachment,
    // TODO
    ...
  }
}

datatype Gon(n: Nat) {
  // `Fin(n)` has 0, 1, ..., n-1.
  vertex(i: Fin(n)): Cell(0, Gon(n))
  edge(i: Fin(n)): Cell(1, Gon(n), {
    // Suppose we can write case-lambda with `type` attribute.
    type (Endpoint) -> Skeleton(0, Gon(n))
    case (Endpoint::start) => vertex(i)
    case (Endpoint::end) => vertex(Fin::add1Mod(i))
  })
}

datatype Torus3 {
  o: Cell(0, Torus3)

  // To introduce a 1-dimensional element,
  // we map `Endpoint` to the 0-skeleton,
  // i.e. previously introduced 0-dimensional elements.

  x: Cell(1, Torus3, {
    // - `x` is part of `Skeleton(1, Torus3)`, which is a subspace of `Torus3`.
    // - To introduce a 1-dimensional element, we need to use a 0-spherical complex
    //   as the coordinate system of the 1-dimensional element's boundary.
    // - The coordinate system is the domain of the attaching map.
    // - The 0-spherical complex we will use is `Endpoint`.
    // - We can use a case function to specify the attaching map.
    type (Endpoint) -> Skeleton(0, Torus3)
    case (Endpoint::start) => o
    case (Endpoint::end) => o
  })

  // By using `Endpoint` as coordinate,
  // we can get `x`'s boundary image by applying `x`
  // to elements of `Endpoint` -- boundary coordinate.
  // (This is not used in the definition of datatype yet.)

  y: Cell(1, Torus3, {
    type (Endpoint) -> Skeleton(0, Torus3)
    case (Endpoint::start) => o
    case (Endpoint::end) => o
  })

  z: Cell(1, Torus3, {
    type (Endpoint) -> Skeleton(0, Torus3)
    case (Endpoint::start) => o
    case (Endpoint::end) => o
  })

  // To introduce a 2-dimensional element,
  // we map `Gon(4)` (polygon) to 1-skeleton,
  // i.e. previously introduced 1-dimensional elements.

  xFace: Cell(2, Torus3, {
    type (Gon(4)) -> Skeleton(1, Torus3)
    case (Gon::edge(0)) => z with {
      type (Gon::edge(0).Coordinate) -> Boundary(z)
      type (Endpoint) -> Boundary(z)
      case (Endpoint::start) => z(Endpoint::start)
      case (Endpoint::end) => z(Endpoint::end)
    }

    case (Gon::edge(1)) => z with { ... }
    case (Gon::edge(2)) => z with { ... }
    case (Gon::edge(3)) => y with { ... }
  })

  yFace: Cell(2, Torus3, {
    ...
  })

  zFace: Cell(2, Torus3, {
    ...
  })

  body: Cell(3, Torus3, {
    type (Cube) -> Skeleton(2, Torus3)
    case (Cube::xFaceStart) => xFace with {
      type (Cube::xFaceStart.Coordinate) -> Boundary(xFace)
      type (Gon(4)) -> Boundary(xFace)
      case (Gon::edge(0)) => xFace(Gon::edge(0))
      case (Gon::edge(1)) => xFace(Gon::edge(1))
      case (Gon::edge(2)) => xFace(Gon::edge(2))
      case (Gon::edge(3)) => xFace(Gon::edge(3))
    }
    ...
  })
}
```

# Generalization of Cell

[question] In what sense "boundaries as types"?

Maybe in the sense that, when defining a function between cell complexes,
the **boundary relation** between elements of the cell complexes,
are used to check whether the function is continuous
-- checking orientation map.

Note that there is a _boundary relation_ between cells of a cell complex,
a n-cell is boundary of a (n+1)-cell if it is
part of the image of the attaching map of the (n+1)-cell.

[question] Should we have a `Boundary` type constructor?

[question] How should we implement boundary?

We can apply `Boundary` to element of space -- like `Boundary(xFace)`.
We can get an element of `Boundary(xFace)`
by referring to an element of the coordinate space.
Two elements of `Boundary(xFace)` are equal if they have the same image.

Note that, the above definition of `Boundary` gives a boundary relation
between elements of a space, which feels much like
the "belongs to" relation of set theory,
and the "belongs to" relation of type theory,
-- the type of a cell is it's boundary,
which will be used to do type checking
when defining mapping between cells.

Boundary relation between elements of product space is easy to define,
just apply the boundary operator on one element of the tuple,
for example, `[x, z]` is part of the boundary of `[x, y]`,
if `z` is part of the boundary of `y`,
or say, `[x, z]: Boundary([x, y])`,
if and only if `z: Boundary(y)`.

[question] It seems to implement higher inductive type, we need two features:

- Data constructors are of type `Cell`.
- Later data constructors can depend on previous data constructors.

Can we generalize this?

Maybe `Cell` are only special kinds of datatype modifier.

[question] What is the generalization (or say, a generalization) of `Cell`?

[keyword] maybe we should call `Cell` an **inductive constraint**
that specifies the inductive definition of cell complex,
and we can also have other inductive constraint like `Cubical`.

[keyword] **static properties** of data constructor,
-- data constructor of record type.

[keyword] **constraints on return value** of Pi type
-- the idea of `Path` type.

# 2-dimensional algebra

How to compose two cells?

What operations we need to do on composed elements?

- We need to get it's coordinate,
  because the definition of map between two cell complexes
  always require the map to be continuous,
  in the sense that mapping `x` to `y`
  must be oriented with a map between `x.Coordinate` and `y.Coordinate`.

- Maybe coordinate space of `a * b`
  are the sum space of
  the coordinate space of `a`
  and coordinate space of `b`.

- Maybe definition of continuous map should not require
  map between coordinate spaces,
  but require map from coordinate space to image of coordinate space.

When we define a continuous map
between two cell complexes,
what should we check?

- Suppose we are defining a continuous map between two cell complexes
  -- `X` and `Y`, and two cells `a` and `b` of `X` are listed in the case-lambda,
  if `f` is an element of coordinate space of `a`
  and `g` is an element of coordinate space of `b`
  and `f` has the same image as `g` in `X` (defined by attaching maps),
  then in the case-lambda they must also have the same image.
  i.e. adjacent cell must map to adjacent elements.
  i.e. the adjacency relation must be respected,
  which is the principle of defining continuous map.

# 2-dimensional algebra -- logical syntax

```cicada
space Torus3 {
  check shell { refl(refl(o)) [ ... ] }: polygon [ refl(o), -refl(o) ]
  check shell { refl(x) [ ... ] }: polygon [ x, -x ]
  check shell { refl(x) [ ... ] refl(x) [ ... ] }: polygon [ x, x, -x, -x ]
  check shell {
    xFace [ B3, C2, -A2, -C0 ]
    yFace [ B0, A2, -C1, -B3 ]
    refl(x) [  ... ]
  }: polygon [ ... ]
}
```

# Homotopy group `S3`

<https://en.wikipedia.org/wiki/Homotopy_groups_of_spheres>

For `n >= 2`, `Pi(n)` is abelian.

- [Eckmann–Hilton argument](https://en.wikipedia.org/wiki/Eckmann%E2%80%93Hilton_argument).

```cicada
datatype S1 {
  base: S1
  rim: endpoint [ base, base ]
}

datatype S2 {
  south: S2
  north: S2
  meridian: endpoint [ south, north ]
  disk: polygon [ meridian, meridian ]
}

datatype S3 {
  // TODO
}
```

TODO How to do 3-dimensional algebra?

```cicada
space S3 {
  check building { ball }: polyhedron S3 {
    disk [ X, -Y ]
    disk [ X, -Y ]
  }
}
```

TODO How come a sphere `S2` has an 3-dimensional algebraic structure in it?

- Degenerated 3-dimensional elements in `S2` has non-trivial structure?

```cicada
space S2 {
  check building { relf(disk) }: polyhedron {
    TODO
  }

  check building { relf(relf(rim)) }: polyhedron {
    TODO
  }
}

```

TODO How to define `Pi(3)(S(2))`?

```cicada
function Pi3S2 (s3: S3): S2 {
  case (S3::base) => S2::base
  case (S3::rim) => S2::rim
  case (S3::disk) => S2::disk
  case (S3::ball) => TODO
  // It seems there is almost not information in definition of `S3`,
  //   thus `Pi3S2` must be about the structure of
  //   the higher dimensional algebra itself.
}
```
