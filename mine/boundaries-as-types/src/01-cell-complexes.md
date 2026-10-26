---
title: Cell Complexes
---

# Cell complex as a generalization of graph theory

Note that graph (with nodes and edges),
and cell complex (with faces and bodies and higher),
are all only syntax, i.e. without interpretion.

They are NOT model.

For example, we can interpret a directed graph as a causal model.

# Different meanings of higher-order-ness

Higher order inductive type is one way of
introducing higher-order-ness into our type system,
another way is higher-order functions.

Is univalent axiom a relation between this two orders?

Is it true that,
if we can view other higher-order-ness
as models of higher order inductive type,
we can use the algebra of the later to simplify our reasoning?

- Higher category theory already provides an example.

- One problem is that a function type is directed
  (we do not have general method to reverse any function),
  but in a cell complex, the a cell has no direction

  - But we used minus sign when composing edges in `path`,
    is it not about direction?

    It is not.
    We can only say a cell has "orientation",
    in the sense that, for example, when composing an edge,
    we can use one of it's endpoint,
    the minus sign in `path` only means
    to change the layout of an edge
    to compose it by another endpoint.

# Path type

[question] Why using path type we can view `Equal` as a `Pi` type?

- Thus proving equal under structure like `Pair` and `class` really easy.

```cicada
i: I |- A: Type
i: I |- M: A
|- M[0/i] = x : A[0/i]
|- M[1/i] = y : A[1/i]
-----------------------
|- (i: I) => M: (i: I) -> A with {
  case (0) => x
  case (1) => y
}
```

```cicada
i: I |- A: Type
i: I |- M: A
-----------------------
|- (i: I) => M: (i: I) -> A
```

Note that, we use `with` to add extra constraints to a type,
an element of the type must satisfy the constraints.

- Why this can not be expressed by record type?
  Maybe it can, and maybe it should.

- Note that, when introducing constructor to higher inductive type,
  we also plan use `with`, maybe we should not,
  because it means providing more information
  (beside the name of the constructor)
  instead of adding constraints on elements the type.

- What if we do not use substitution?
  but use function instead:

  ```cicada
  A : (I) -> Type
  m : (i: I) -> A(p)
  m(0) = x : A(0)
  m(1) = y : A(1)
  ---------
  m : Path(A, x, y)
  ```

  no nominal-typing is needed here, `I` is a normal type of endpoints.

  - TODO Is this idea enough to be used as the path type
    where structural equivalence is easy to prove?

    If it is, we can already back to cicada without cell complex!

[question] What is the general relation
between topological (homotopical) spaces
and the `Equal` type?

If we ignore the idea of nominal-typing for a moment,
the idea of `Path` type is to
enrich a function of certain Pi type,
with constraints about the return value of the function.

Type checking of such enrichment is simple.

```cicada
function Path(A, x, y): Type {
  return (i: I) -> A with {
    case (0) => x
    case (1) => y
  }
}
```

If we view function type as special object with `apply` property,
we can express the enrichment by other properties.

```cicada
function Path(A, x, y): Type {
  return class {
    apply: (i: I) -> A,
    start: Equal(A, apply(0), x),
    end: Equal(A, apply(1), y),
  }
}
```

# Limitation of our construction

The following concepts are not built-in our language:

- Subset.
- Quotient.

# Naming and syntax keywords

| dim           | cell        | complex    | spherical complex   |
|---------------|-------------|------------|---------------------|
| 0             | vertex      |            | `endpoint`          |
| 1             | edge        | `path`     | `polygon`           |
| 2             | face        | `shell`    | `polyhedron`        |
| 3             | block       | `building` | `polychoron`        |
| (algebraicly) | (generator) | (element)  | (spherical element) |

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

[question] How to understand cell complex as inductively defined set?

- 0-dimensional cell complex is inductively defined set.

# 1-dimensional -- logical syntax

```cicada
datatype Circle {
  base: Circle
  loop: endpoint [ base, base ]
}
```

# 2-dimensional -- logical syntax

Four ways to glue a square:

```cicada
datatype Sphere {
  south: Sphere
  middle: Sphere
  north: Sphere
  southLong: endpoint [ south, middle ]
  northLong: endpoint [ middle, north ]
  disk: polygon [
    southLong, northLong, -northLong, -southLong
  ]
}
```

```cicada
datatype Torus {
  origin: Torus
  toro: endpoint [ origin, origin ]
  polo: endpoint [ origin, origin ]
  spoke: polygon [ toro, polo, -toro, -polo ]
}
```

```cicada
datatype KleinBottle {
  origin: KleinBottle
  toro: endpoint [ origin, origin ]
  cross: endpoint [ origin, origin ]
  disk: polygon [ toro, cross, -toro, cross ]
}
```

```cicada
datatype ProjectivePlane {
  start: ProjectivePlane
  end: ProjectivePlane
  leftRim: endpoint [ start, end ]
  rightRim: endpoint [ end, start ]
  disk: polygon [ leftRim, rightRim, leftRim, rightRim ]
}
```

# 1-dimensional algebra -- logical syntax

```cicada
space Torus {
  check path [ toro, toro, toro, polo ]: endpoint [ origin, origin ]
  check path [ toro ]: endpoint [ origin, origin ]
  check path [ relf(origin) ]: endpoint [ origin, origin ]
  check path [ toro, relf(origin) ]: endpoint [ origin, origin ]
}
```

# 3-dimensional -- logical syntax

```cicada
datatype Torus3 {
  o: Torus3
  x: endpoint [ o, o ]
  y: endpoint [ o, o ]
  z: endpoint [ o, o ]
  zFace: polygon [ z, y, -z, -y ]
  yFace: polygon [ x, z, -x, -z ]
  zFace: polygon [ y, x, -y, -x ]
  body: polyhedron {
    xFace [ Y3, Z2, -X2, -Z0 ]
    yFace [ Y0, X2, -Z1, -Y3 ]
    xFace [ Z0, Z1, -Z2, -Y0 ]
  }
}
```

The idea is that to specify the attaching map
from polyhedron to 2-skeleton of the space,
we can use a syntax like logic programming,
i.e. to use meta variable and linear unification
to specify how edges of polygons are glued together.

- Note that, polygons are elements of the boundary of polyhedron.

# 3-dimensional

To design how to introduce a (n+1)-dimensional cell `A` into a cell complex,
we need to specify an attaching map from the boundary of the cell `A`
to the n-skeleton of the cell complex.

We do this by giving the boundary of the cell `A`
a spherical coordinate space -- endpoint, polygon, polyhedron, ...

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

# Hopf fibration

<https://en.wikipedia.org/wiki/Hopf_fibration>
