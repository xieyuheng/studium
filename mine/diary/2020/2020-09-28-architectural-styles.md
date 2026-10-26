# Architectural Styles

2020-09-28

DRAFT

## noun and verb code structure

TODO

## note about using verb as noun

- `Parser` as interface vs `Parser` as function type

## Reading the REST paper -- about API design

- 我被这个主题吸引，是因为 pretty url 类似我的项目的 directory structure。
  但是 pretty url 并不是 REST 的主旨。
  那么 REST 的主旨是什么呢？

- pretty url 包括 collection id and hierarchy
  `user/xieyuheng/repo/cicada`
  也许正确的实现方式是 `user/xieyuheng` 返回的 json 中，
  有一个 field 是 `{repos: {cicada: <url>}}`

- REST api should not dependent on url structure.
  与其 `user/xieyuheng/repo/cicada`
  也许应该 `user/xieyuheng`
  并且在返回数据中链接到 `{repos: {cicada: "repo/xieyuheng-cicada" }}`
  或者任意一个 id -- `{repos: {cicada: "repo/<id>" }}`

- 我的构架也不应该依赖 directory structure。
  所有的 noun 都可以出现在目录顶层，或者说目录的任意一个位置。

- 利用我的构架，
  1. 我可以很用以用 noun 和 verb 来构架一个 application。
  2. 我可以很容易把任意 application 变成 web application。

## Case Study: GET from

TODO

study the html standard about how to construct url from <form method="GET">

## Case Study: The Web

TODO

Web 与浏览器就是最典型的 REST 构架。

html 是典型的 hypermedia format。

REST 构架是关于 design hypermedia format 的。
