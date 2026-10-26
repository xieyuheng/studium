# Unified Modeling Language

2020-11-14

## Two ways to express property -- attribute and association

attribute -- inbox slot
association -- outbox arrow
- can avoid nested boxes

## Unified syntax to express dependency

UML 的箭头表示 "depends on"，
不同的箭头表示 "depends on because of ____"。
例如：
- class diagram
  - association (solid arrow) -- depends on because of used as field
  - subclass (solid arrow with triangle end) -- depends on because of used as superclass
    - the arrow reads as "generalization"
  - dependency (dashed arrow) -- depends on because of used in some way
