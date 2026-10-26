---
title: Semantic of de Bruijn Notation
author: Xie Yuheng
date: 2018-07-22
keywords: [language design, syntax]
---

# Semantic

The direct semantic of de Bruijn notation,
is stack based postfix programming language.

# Translating

- Notation
  | {...} | quotation              |
  | []    | local variable binding |

- Suppose `trans( ... )` is a syntax translating function,
  which translate normal lambda notation to de Bruijn notation.

- The rules :

  1. trans( v ) => v

  2. trans( (lambda (v) M) ) => [v] trans( M )

  3. trans( (M N) ) => { trans( N ) } trans( M )

# Example

- in de Bruijn notation :
  { M } { N } [u] { P } [v] [w] { Q } z

- which is equal to (for example) :
  { N } [u] { P } [v] { M } [w] { Q } z

# Advantage

- More equivalence between functions
  are explicitly revealed by the syntax.

- Syntax A is better than syntax B,
  if, when using A, it is easier to manipulate
  the underlying semantics.

- Or say, the semantics will be easier to imagine,
  when expressed in syntax A.
