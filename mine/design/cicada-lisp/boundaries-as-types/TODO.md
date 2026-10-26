separate cubical notes from cell-complexes

# examples

Example algebra of 1-dimension

- Take presentation of group as example
- Take interesting groups as example (fundamental polygon)

Example algebra of 2-dimension

Example algebra of 3-dimension -- for `Pi(3)(S(2))`

About implementation:

- If we think about how to implement higher order incidence relation
  by objects and pointers,
  and we store pointers at both direction at every order,
  the implementation will be a graph model of cell-complex.

- Should we use hypergraph to encode higher order incidence relation?

Product space -- and boundary operator over it.

Review old notes:

- topics/mathematics/algebraic-topology
- topics/mathematics/combinatorial-group-theory
- langs/cell-complex
  - a-language-for-equivalence
  - fibration
  - holding
  - square
  - at1-note
  - aeqea
  - main
  - note

# Problems

## Problem of Hopf fibration

We are trying to find a way to generalize algebraic structure to higher dimension,
how can 2-dimensional sphere has non trivial 3-dimensional algebraic structure?

- Hopf fibration describe partition of the ball of `S(3)`,
  but in our language, we can not describe this detailed partition.

Maybe we can define partition on cell-complex.

When defining a map between two spaces,
we must also define the map for all possible partitions

- Maybe Like [surreal number](https://en.wikipedia.org/wiki/Surreal_number).

When we define map between the same dimension,
our way of definition already give a definition
for all possible partitions.

We can map element cross dimension,
but must also define the map for all possible partitions.

Then we can define Hopf fibration in a
intuitive and pure topological way.

## Problem of the separation between fixed parameters and varied indexes

In inductive datatypes, a type constructor can take arguments
and the arguments are to separated into two groups:

- **fixed (parameters)** which do NOT vary between the data constructors.
- **varied (indexes)** which can vary between the data constructors.

When we introduce arguments to type constructor for complex,
what is the meaning of the above separation?

What is the topological interpretion
of "fixed vs. varied arguments"
for inductive datatype?
