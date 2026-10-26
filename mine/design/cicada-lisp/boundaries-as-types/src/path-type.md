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
