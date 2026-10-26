(define-datatype less-than-t ()
  :indices ((j nat-t) (k nat-t))
  (zero-smallest ((n nat-t))
                 (less-than-t zero (add1 n)))
  (add1-smaller ((j nat-t)
                 (k nat-t)
                 (prev-smaller (less-than-t j k)))
                (less-than-t (add1 j) (add1 k))))

less-than-t
(less-than-t (add1 zero))
(less-than-t (add1 zero) (add1 (add1 zero)))
