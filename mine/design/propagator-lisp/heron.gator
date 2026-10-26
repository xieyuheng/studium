;; h = (g + x/g) / 2

(define-propagator (heron-step x g h)
  (divider (adder g (divider x g)) 2 h))
