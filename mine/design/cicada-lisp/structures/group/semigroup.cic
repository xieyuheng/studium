(define-class semigroup-t ()
  (claim element-t type-t)
  (claim compose (-> element-t element-t element-t))
  (claim compose-associative
    (forall ((x element-t)
             (y element-t)
             (z element-t))
      (equal-t element-t
        (compose x (compose y z))
        (compose (compose x y) z)))))
