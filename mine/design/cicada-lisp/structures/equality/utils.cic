(claim equal-map
  (implicit ((A type-t)
             (B type-t)
             (from A)
             (to A))
    (forall ((f (-> A B))
             (target (equal-t A from to)))
      (equal-t B (f from) (f to)))))

(define (equal-map (implicit A B from to) f target)
  (replace target
    (lambda (x) (equal-t B (f from) (f x)))
    refl))


(claim equal-swap
  (implicit ((A type-t)
             (x A)
             (y A))
    (-> (equal-t A x y) (equal-t A y x))))

(define (equal-swap (implicit A x y) xy-equal)
  (replace xy-equal
    (lambda (w) (equal-t A w x))
    refl))


(claim equal-compose
  (implicit ((A type-t)
             (x A)
             (y A)
             (z A))
    (-> (equal-t A x y) (equal-t A y z)
        (equal-t A x z))))

(define (equal-compose (implicit A x y z) xy-equal yz-equal)
  (replace yz-equal
    (lambda (w) (equal-t A x w))
    xy-equal))
