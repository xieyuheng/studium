(define-datatype exp-t ()
  (exp-var ((name string-t)) exp-t)
  (exp-ap ((target exp-t) (arg exp-t)) exp-t)
  (exp-fn ((name string-t) (body exp-t)) exp-t))

(define-datatype value-t ()
  ...)

(define-datatype env-t ()
  (env-empty env)
  (env-cons ((name string-t)
             (value value-t)
             (rest-env env-t))
            env-t))

(define-datatype dict-t ((element-t type-t))
  (dict-null (dict element-t))
  (dict-cons ((name string-t)
              (value element-t)
              (rest env-t))
             env-t))

(claim evaluate (-> env-t exp-t exp-t))
(define (evaluate env exp)
  (match exp
    ((exp-var name) ...)
    ((exp-ap target arg) ...)
    ((exp-fn name body) ...)))
