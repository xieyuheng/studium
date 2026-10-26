(begin
  (= [f c] (fahrenheit-to-celsius))
  (patch f 77)
  (run)
  (assert-equal (content c) 25))

(begin
  (= [f c] (fahrenheit-celsius))
  (patch f 77)
  (run)
  (assert-equal (content c) 25))

(begin
  (= [f c] (fahrenheit-celsius))
  (patch c 25)
  (run)
  (assert-equal (content f) 77))

(begin
  (= [f c] (fahrenheit-celsius))
  (= k (celsius-kelvin c))
  (patch f 77)
  (run)
  (assert-equal (content c) 25)
  (assert-equal (content k) 298.15))
