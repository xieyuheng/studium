(begin
  (= [x guess better-guess] (heron-step))
  (patch x 2)
  (patch guess 1.4)
  (run)
  (assert-equal (content better-guess) 1.4142857142857141))


(begin
  (= better-guess (heron-step (cell 2) (cell 1.4)))
  (run)
  (assert-equal (content better-guess) 1.4142857142857141))
