---
title: Closure
---

# Problem

Closure is a technique to solve
the problem of consistent substitution
(or say the problem of lexical scope).

- **Lexical scope**:

  With lexical scope, a name always refers to its lexical context.
  This is a property of the program text and is made independent of
  the runtime call stack by the language implementation.  Because this
  matching only requires analysis of the static program text, this
  type of scope is also called static scope.

Consistent substitution can be implemented by
recursively renaming, which is not as performant as closure.

# Solution

```
closure = { env, function }
```

To apply a closure to arguments,
is to extend the env with the new bindings,
and evaluate the function body in the new env.

# Problem introduced by closure

In an interpreter, using closure
will make `Value` different from `Exp`.

# Example: Type variables

```
[ :a list_t [ :b ] [ :a list_t :a -- :b ] -- :b ]

=> create quote (closure),
   get current env (from frame?),
   and bind unbound type variables.

{ :a = :a1, :b = :b1 }
[ :a list_t [ :b ] [ :a list_t :a -- :b ] -- :b ]

=> create inner quote, get current env,
   and bind unbound type variables (there is non).

{ :a = :a1, :b = :b1 }
[ :a list_t :a -- :b ]
```

How about type variable should be the same, but only occurs in nested quotes?

```
[ [ :a ] [ :a list_t ] -- int_t ]

=> create quote, get current env,
   and bind unbound type variables,
   including those in nested quotes.

{ :a = :a1 }
[ [ :a ] [ :a list_t ] -- int_t ]
```

- Note that, we do not explicit write the variable in the outer quote.

  ```
  [ <:a> [ :a ] [ :a list_t ] -- int_t ]
  ```

So top-level functions are different from inner functions.
Because for type variables, each top-level function is a whole scope.
