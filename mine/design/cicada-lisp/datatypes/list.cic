(define-datatype list-t (element-t type-t)
  (list-null (list-t element-t))
  (list-cons ((head element-t)
              (tail (list-t element-t)))
             (list-t element-t)))

(claim length
  (implicit ((element-t type-t))
    (-> (list-t element-t) nat-t)))
(define (length (implicit element-t) list)
  (match list
    ((list-null) zero)
    ((list-cons head tail) (add1 (length tail)))))
