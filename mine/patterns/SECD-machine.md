---
title: SECD Machine
---

The operating principle of this machine centers around the idea of
delayed substitutions. Rather than performing β-reductions as atomic
operations, the machine partitions them, as a measure to improve
runtime eﬃciency, into two steps that are distributed over time. When
encountering β-redices, it just collects mappings of bound variables
to (operand) expressions in a runtime structure called the
environment. All substitutions are subsequently done while traversing
the body expressions only once in search of bound-variable
occurrences.

Closely related to delayed substitutions is the notion of
closures. They pair abstractions that generally include occurrences of
free variables with environments containing the (evaluated)
expressions that may have to be substituted for them later on,
whenever it becomes possible and necessary to actually evaluate these
closures.

-- Abstract Computing Machines: A Lambda Calculus Perspective
-- W. Kluge

```
((λ (u v w) ((u v) w)) v w u)
((((λ (u) (λ (v) (λ (w) ((u v) w)))) v) w) u)
```
