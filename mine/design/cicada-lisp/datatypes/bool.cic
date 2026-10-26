(define-datatype bool-t ()
  (true bool-t)
  (false bool-t))

(claim not (-> bool-t bool-t))
(define (not x)
  (match x
    (true false)
    (false true)))

(claim add (-> bool-t bool-t bool-t))
(define (add x y)
  (match* (x y)
    ((true true) true)
    ((true false) false)
    ((false true) false)
    ((false false) false)))
