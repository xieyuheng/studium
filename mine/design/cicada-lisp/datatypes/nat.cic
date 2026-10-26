(define-datatype nat-t ()
  (zero nat-t)
  (add1 ((prev nat-t)) nat-t))

(check (add1 zero) nat-t)
(check (add1 (add1 zero)) nat-t)

(the nat-t (add1 zero))
(the nat-t (add1 (add1 zero)))

(claim add (-> nat-t nat-t nat-t))
(define (add x y)
  (match x
    (zero y)
    ((add1 prev) (add1 (add prev)))))
