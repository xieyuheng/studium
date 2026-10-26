---
title: Good code is easy to change
---

After I read Sandi Metz's two books --
"Practical Object Oriented Design" and "99 bottles of OOP",
I **define** good code structure as easy to change.

The most important attribute about easy to change is easy to understand.

I think _SOLID principles_ are necessary
for writing code that's easy to change.

I also think the use of _Design Patterns_ is a good way
of recording knowledge that's easy to share.

# Functional Programming is orthogonal to OOP

If we disallow side-effect in an OOP language, the language became pure FP,

The key technique of OOP -- dependency injection, is still effective in FP,
injecting a dependency to a class' constructor, is just
passing a record of functions to a higher order function,
which returns a record of functions (instance of the class).

Thus dependency injection can be viewed as,
using very high order functions,
with the help of record types.

# We should not define class to get the dot syntax

Except in things like Builder Pattern,
We should not define class to get the dot syntax.

We should just use Scalable-C style OOP,
and use classes to implement real interfaces.

# Record can not be encoded by Lambda

We know that lambda can encode inductive datatypes,
but how about record types (with OOP in mind)?

We can view function as special record (object) with an `apply` method,
but how about the other way around -- encoding record by lambda?

Maybe its not possible, because record is about _polymorphism_,
i.e. sending the same _message_ to different objects might resolve different methods.

While there is not polymorphism in lambda calculus.

We can encode _list_ by lambda,
and use them to pass a group of functions to higher order function.
But doing so, we will be using index (instead of named message) to get back methods.

In record, the order does not matter, but in list, the order matters.

We might encode string by lambda and use them as message,
but such encoding will not be practical at all.
