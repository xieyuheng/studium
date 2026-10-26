(define-datatype fin-t ()
  :indices ((n nat-t))
  (fin-zero ((k nat-t)) (fin-t (add1 k)))
  (fin-add1 ((k nat-t)) (-> (fin-t k) (fin-t (add1 k)))))
