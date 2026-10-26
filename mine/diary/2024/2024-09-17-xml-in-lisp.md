---
title: XML in lisp
date: 2024-09-17
---

想要在 lisp 的 sexp 中写 XML，
可以用如下对应：

- tag -- head
- attribute=value -- :keyword value
- children -- children
- text -- quoted string

注意，这还是没法代替 XML 作为 markup language 的作用，
因为在 XML 中，可以直接写 text，
而在 sexp 中需要 quoted string。
