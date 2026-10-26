---
title: Intro
---

# Abstract

A language to capture the concept of continuum,

- by viewing boundary as type,
- to formalize constructions in algebraic topology.

It can be viewed as higher dimensional algebraic structure in which,

- homotopy groups are it's sub-algebras,
- and it's abelianization are homology groups.

# The Plan

A group or a groupoid when defined by a presentation,
can be viewed topologically as vertexes, edges and faces,
where edges are the generators,
faces are the relations between generators,
i.e. a 2-dimensional cell complex.

And the reverse is also true,
given a 2-dimensional cell complex,
one can view it as a groupoid.

If a 2-dimensional cell complex is a manifold,
we can transform it into an equivalent cell complex
which is built from a fundamental polygon,
and the study of such groupoid can be reduce to
the study of fundamental group of fundamental polygon.

The presentations of 2-dimensional manifold
as fundamental group can be normalized,
to give a complete classification
of 2-dimension manifolds.

Can we generalize the above to 3-dimension?

Given a 3-dimensional cell complex,
we can view it as a presentation
of a 2-dimensional algebraic structure,
where elements are faces and relations are bodies.

**Conjecture: Reducing to group is not enough**

If a 3-dimensional cell complex is a manifold,
we can transform it into an equivalent cell complex
which is built from a fundamental polyhedron,
but the study of the 2-dimensional algebraic structure
can NOT be reduced to the study of a fundamental group,
because a lot of information in the fundamental polyhedron
will be lost if we insist on reduce to group.

But the algebraic structure of fundamental polyhedron
is still easier to study than that of general cell complex.

**Conjecture: Normalization of 3-dimensional manifolds exists**

The presentations of 3-dimensional manifold
as algebraic structure of fundamental polyhedron
can be normalized to given a complete classification
of 3-dimensional manifolds.

# About Algebraic Structures

The elements of a groupoid,
when viewed as a cell complex,
are (undirected) _paths_ built from _edges_.

A 3-dimensional cell complex,
when viewed as an algebraic structure,
it's elements are _shells_ built from _faces_.

For edges, there are only two ways to do composition
(this is where the inverse of groupoid elements come from),
but for faces, there are `n` ways to do composition,
where `n` is the number of edges of the a face.

Composition of two cells -- `A` and `B` must be viewed as
removing some common boundaries of `A` and `B`,
and the result must still be a cell
-- i.e. having spherical boundary.

New syntactic devices are needed to represent
the compositions easily,
the new syntactic devices are about
placeholder variables and unification,
just like the logic variables and unification of relations
in logic programming languages like Prolog.
This syntactic difficulty is the main reason,
why higher dimensional algebraic structures
are not well studied in the past.
