# note about "build from present" pattern

2020-09-21

A design pattern which can be used to avoid parsing.

API:
- `Target.build(present: Present): Target`
- `Target.present(target: Target): Present`

Where the `Present` can be json, yaml, sexp, xml ...
