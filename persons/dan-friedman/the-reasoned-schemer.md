---
title: the reasoned schemer
---

# [note]

## to learn how to teach

- to learn how to teach
  by naming some discussions

## in lack of algebraic data type

- minikanren 中 既有 cons 又有 conso

  而 在 prolog 中 類似 cons 的構造子是沒有對應的 goal 的
  直接用 unification 處理

  不論是 minikanren 還是 prolog
  都沒有 algebraic data type
  也就沒有 judgment 和 inference rule

# minikanren implementation

## typing about unification

- term =
  - var :: term
  - non-var :: term

- substitution = var term dict

- walk ::
     (term substitution -> term)
- extend-substitution ::
     (var term substitution -> substitution)
- unify ::
     (term term substitution -> (substitution or #f))

- occurs-check ::
     (var term substitution -> bool)
- extend-substitution-check ::
     (var term substitution -> (substitution or #f))
- unify-check ::
     (term term substitution -> (substitution or #f))

- walk* ::
     (term substitution -> term)

## var

```scheme :tangle the-reasoned-schemer.scm
(define-syntax var
  (syntax-rules ()
    [(var w) (vector w)]))

(define-syntax var?
  (syntax-rules ()
    [(var? w) (vector? w)]))
```

## unification

- substitution = var term dict

```scheme :tangle the-reasoned-schemer.scm
(define walk
  ;; (term substitution -> term)
  (lambda (v s)
    (cond [(var? v)
           (cond [(assq v s) =>
                  (lambda (a)
                    ;; cdr as right-hand-side
                    (let ([v^ (cdr a)])
                      (walk v^ s)))]
                 [else v])]
          [else v])))

;; extend-substitution
(define ext-s
  ;; (var term substitution -> substitution)
  (lambda (x v s)
    (cons (cons x v) s)))

(define unify
  ;; (term term substitution -> (substitution or #f))
  (lambda (v w s)
    (let ([v (walk v s)]
          [w (walk w s)])
      (cond [(eq? v w) s]
            [(var? v) (ext-s v w s)]
            [(var? w) (ext-s w v s)]
            [(and (pair? v) (pair? w))
             (cond [(unify (car v) (car w) s) =>
                    (lambda (s)
                      (unify (cdr v) (cdr w) s))]
                   [else #f])]
            [(equal? v w) s]
            [else #f]))))
```

## unification with occurs-check

```scheme :tangle the-reasoned-schemer.scm
(define occurs-check
  ;; (var term substitution -> bool)
  (lambda (x v s)
    (let ((v (walk v s)))
      (cond [(var? v) (eq? v x)]
            [(pair? v)
             (or
              (occurs-check x (car v) s)
              (occurs-check x (cdr v) s))]
            [else #f]))))

(define ext-s-check
  ;; (var term substitution -> (substitution or #f))
  (lambda (x v s)
    (cond [(occurs-check x v s) #f]
          [else (ext-s x v s)])))

(define unify-check
  ;; (term term substitution -> (substitution or #f))
  (lambda (v w s)
    (let ([v (walk v s)]
          [w (walk w s)])
      (cond [(eq? v w) s]
            [(var? v) (ext-s-check v w s)]
            [(var? w) (ext-s-check w v s)]
            [(and (pair? v) (pair? w))
             (cond [(unify-check (car v) (car w) s) =>
                    (lambda (s)
                      (unify-check (cdr v) (cdr w) s))]
                   [else #f])]
            [(equal? v w) s]
            [else #f]))))
```

## walk* & reify

```scheme :tangle the-reasoned-schemer.scm
(define walk*
  ;; (term substitution -> term)
  (lambda (v s)
    (let ([v (walk v s)])
      (cond [(var? v) v]
            [(pair? v)
             (cons
              (walk* (car v) s)
              (walk* (cdr v) s))]
            [else v]))))

;; reify-substitution
(define reify-s
  (lambda (v s)
    (let ([v (walk v s)])
      (cond
       ((var? v) (ext-s v (reify-name (length s)) s))
       ((pair? v) (reify-s (cdr v) (reify-s (car v) s)))
       (else s)))))

(define reify-name
  (lambda (n)
    (string->symbol
     (string-append "_." (number->string n)))))

(define empty-s '())

(define reify
  (lambda (v)
    (walk* v (reify-s v empty-s))))
```

## trunk

- (trunk _) is trunk constructor
  it is just (lambda () _)

- for example
  [string] denote a trunk when applied
  return a value of type string

```scheme :tangle the-reasoned-schemer.scm
(define-syntax trunk
  ;; _ -> [_]
  (syntax-rules ()
    [(_ e) (lambda () e)]))
```

## stream constructor

- the dependent type 'substitution _ stream' can be
  substitution 0 stream
  substitution 1 stream
  substitution more stream

```scheme :tangle the-reasoned-schemer.scm
;; measure-zero
(define-syntax mzero
  ;; _ 0 stream
  (syntax-rules ()
    [(_) #f]))

(define-syntax unit
  ;; (_ -> _ 1 stream)
  (syntax-rules ()
    [(_ a) a]))

(define-syntax choice
  ;; (_ [_ _ stream] -> _ more stream)
  (syntax-rules ()
    [(_ a f) (cons a f)]))
```

## streams processing

```scheme :tangle the-reasoned-schemer.scm
(define-syntax case-inf
  (syntax-rules ()
    [(_ e
        [() on-zero]
        [(a^) on-one]
        [(a f) on-choice])
     (let ([a-inf e])
       (cond [(not a-inf)
              on-zero]
             [(not (and (pair? a-inf)
                        (procedure? (cdr a-inf))))
              (let ([a^ a-inf])
                on-one)]
             [else
              (let ([a (car a-inf)]
                    [f (cdr a-inf)])
                on-choice)]))]))

;; stream-concatenate
(define mplus
  ;; ([_ _ stream] [_ _ stream] -> [_ _ stream])
  (lambda (a-inf f)
    (case-inf a-inf
      [() (f)]
      [(a) (choice a f)]
      [(a f0) (choice a (trunk (mplus (f0) f)))])))

;; stream-interleave
(define mplusi
  ;; ([_ _ stream] [_ _ stream] -> [_ _ stream])
  (lambda (a-inf f)
    (case-inf a-inf
      [() (f)]
      [(a) (choice a f)]
      [(a f0) (choice a (trunk
                          ;; swap
                          ;; thus the next value
                          ;; would be taken form the second stream
                          (mplusi (f) f0)))])))

;; stream-map
(define bind
  ;; ([t1 _ stream] (t1 -> t2) -> [t2 _ stream])
  (lambda (a-inf g)
    (case-inf a-inf
      [() (mzero)]
      [(a) (g a)]
      [(a f) (mplus (g a) (trunk (bind (f) g)))])))

(define bindi
  ;; ([t1 _ stream] (t1 -> t2) -> [t2 _ stream])
  (lambda (a-inf g)
    (case-inf a-inf
      [() (mzero)]
      [(a) (g a)]
      [(a f) (mplusi (g a) (trunk (bindi (f) g)))])))
```

## goal constructor

- _ goal = (substitution -> substitution _ stream)

```scheme :tangle the-reasoned-schemer.scm
(define fail
  ;; 0 goal
  ;; (substitution -> substitution 0 stream)
  (lambda (s) (mzero)))

(define succeed
  ;; 1 goal
  ;; (substitution -> substitution 1 stream)
  (lambda (s) (unit s)))

(define ==
  ;; (term term -> (0 or 1) goal)
  ;; (term term -> (substitution -> substitution (0 or 1) stream))
  (lambda (v w)
    (lambda (s)
      (cond [(unify v w s) => succeed]
            [else (fail s)]))))

(define ==-check
  ;; (term term -> (0 or 1) goal)
  ;; (term term -> (substitution -> substitution (0 or 1) stream))
  (lambda (v w)
    (lambda (s)
      (cond [(unify-check v w s) => succeed]
            [else (fail s)]))))
```

## goal conjunction

```scheme :tangle the-reasoned-schemer.scm
(define-syntax all
  (syntax-rules ()
    [(_) succeed]
    [(_ g) g]
    [(_ g^ g ...)
     (lambda (s)
       (bind (g^ s) (all g ...)))]))

(define-syntax alli
  (syntax-rules ()
    [(_) succeed]
    [(_ g) g]
    [(_ g^ g ...)
     (lambda (s)
       (bindi (g^ s) (alli g ...)))]))

(define-syntax fresh
  (syntax-rules ()
    [(_ (x ...)
        g ...)
     (lambda (s)
       (let ([x (var 'x)]
             ...)
         ((all g ...) s)))]))
```

## goal disjunction

### conde

```scheme :tangle the-reasoned-schemer.scm
(define-syntax anye
  (syntax-rules ()
    [(_ g1 g2)
     ;; substitution -> substitution _ stream
     (lambda (s)
       (mplus (g1 s)
              (trunk (g2 s))))]))

(define-syntax conde
  (syntax-rules (else)
    [(_) fail]
    [(_ (else g0 g ...)) (all g0 g ...)]
    [(_ (g0 g ...) c ...)
     (anye (all g0 g ...) (conde c ...))]))
```

### condi

```scheme :tangle the-reasoned-schemer.scm
(define-syntax anyi
  (syntax-rules ()
    [(_ g1 g2)
     ;; substitution -> substitution _ stream
     (lambda (s)
       (mplusi (g1 s)
               (trunk (g2 s))))]))

(define-syntax condi
  (syntax-rules (else)
    [(_) fail]
    [(_ (else g0 g ...)) (all g0 g ...)]
    [(_ (g0 g ...) c ...)
     (anyi (all g0 g ...) (condi c ...))]))
```

### conda

```scheme :tangle the-reasoned-schemer.scm
(define-syntax ifa
  (syntax-rules ()
    [(_ g0 g1 g2)
     ;; substitution -> substitution _ stream
     (lambda (s)
       (let ([s-inf (g0 s)]
             [g^ g1])
         (case-inf s-inf
           [() (g2 s)]
           [(s) (g^ s)]
           [(s f) (bind s-inf g^)])))]))

(define-syntax conda
  (syntax-rules (else)
    [(_) fail]
    [(_ (else g0 g ...)) (all g0 g ...)]
    [(_ (g0 g ...) c ...)
     (ifa g0 (all g ...) (conda c ...))]))
```

### condu

```scheme :tangle the-reasoned-schemer.scm
(define-syntax ifu
  (syntax-rules ()
    [(_ g0 g1 g2)
     ;; substitution -> substitution _ stream
     (lambda (s)
       (let ([s-inf (g0 s)]
             [g^ g1])
         (case-inf s-inf
           [() (g2 s)]
           [(s) (g^ s)]
           [(s f) (g^ s)])))]))

(define-syntax condu
  (syntax-rules (else)
    [(_) fail]
    [(_ (else g0 g ...)) (all g0 g ...)]
    [(_ (g0 g ...) c ...)
     (ifu g0 (all g ...) (condu c ...))]))
```

## run

```scheme :tangle the-reasoned-schemer.scm
;; run converts a stream of substitutions
;; to a list of values using map-inf

(define map-inf
  (lambda (n p a-inf)
    (case-inf a-inf
      [() '()]
      [(a)
       (cons (p a) '())]
      [(a f)
       (cons (p a)
             (cond [(not n) (map-inf n p (f))]
                   [(> n 1) (map-inf (- n 1) p (f))]
                   [else '()]))])))

(define-syntax run
  (syntax-rules ()
    [(_ n^ (x) g ...)
     (let ((n n^) (x (var 'x)))
       (if (or (not n) (> n 0))
         (map-inf n
                  (lambda (s) (reify (walk* x s)))
                  ((all g ...) empty-s))
         '()))]))

(define-syntax run*
  (syntax-rules ()
    [(_ (x) g ...)
     (run #f (x) g ...)]))
```

## misc

```scheme :tangle the-reasoned-schemer.scm
(define-syntax lambda-limited
  (syntax-rules ()
    ((_ n formals g)
     (let ([x (var 'x)])
       (lambda formals
         (ll n x g))))))

(define ll
  (lambda (n x g)
    ;; substitution -> substitution _ stream
    (lambda (s)
      (let ([v (walk x s)])
        (cond [(var? v) (g (ext-s x 1 s))]
              [(< v n) (g (ext-s x (+ v 1) s))]
              [else (fail s)])))))

(define-syntax project
  (syntax-rules ()
    [(_ (x ...) g ...)
     ;; substitution -> substitution _ stream
     (lambda (s)
       (let ((x (walk* x s)) ...)
         ((all g ...) s)))]))
```

# minikanren library

## list processing

```scheme :tangle the-reasoned-schemer.scm
(define conso
  (lambda (a d p)
    (== (cons a d) p)))

(define caro
  (lambda (p a)
    (fresh (d)
      (== (cons a d) p))))

(define cdro
  (lambda (p d)
    (fresh (a)
      (== (cons a d) p))))

(define pairo
  (lambda (p)
    (fresh (a d)
      (== (cons a d) p))))

(define nullo
  (lambda (x)
    (== x '())))

(define listo
  (lambda (l)
    (conde
      [(nullo l)]
      [(fresh (d)
         (pairo l)
         (cdro l d)
         (listo d))])))

(define appendo
  (lambda (l s out)
    (conde [(nullo l)
            (== out s)]
           [(fresh (a d rec)
              (conso a d l)
              (appendo d s rec)
              (conso a rec out))])))

(define unwarpo
  (lambda (x out)
    (conde
      [(== x out)]
      [(fresh (a)
         (pairo x)
         (caro x a)
         (unwarpo a out))])))
```

# 1 playthings

## [note]

- the law of fresh
  if x is fresh
  then (v x) succeeds and associates x with v

- the law of ==
  (== v w) is the same as (== w v)

- the law of conde
  to get more values from conde
  pretend that the successful conde line has failed
  refreshing all variables that got an association from that line

## run*

```scheme
(run* (q)
  fail)

(run* (x)
  succeed)

(run* (q)
  (== 1 q))

(run* (x)
  (let ([x1 1])
    (== x1 2)))
```

## reified variable

- 也許是 Thoralf Albert Skolem 強調
  要給返回的不定元做系統地重命名
  以便閱讀

```scheme
(run* (x)
  (let ((x #f))
    (fresh (x)
      (== #t x))))

(run* (x)
  (let ((x #f))
    (== #f x)))

(run* (x)
  (fresh (x)
    (== #t x)))

(run* (x)
  (== #f x))

(run* (x)
  (fresh (a d)
    (== x
        (cons a (cons d '())))))

(run* (r)
  (fresh (x)
    (let ([y x])
      (fresh (x)
        (== (list y x y) r)))))

(run* (r)
  (fresh (x)
    (fresh (y)
      (== (list y x y) r))))

(run* (r)
  (fresh ()
    (fresh (y)
      (== (list y y) r))))
```

## 底層等詞

- unification [==] 與 等詞 [eq?] 有別
  eq? 是用以實現 unification 的底層等詞

```scheme
(run* (q)
  (fresh (x)
    (== (eq? x q) q)))

(run* (q)
  (let ([x q])
    (fresh (q)
      (== (eq? x q) x))))

(let ([q 1])
  (let ([x q])
    (eq? x q)))

;; 不定元都被 unify 到了相同的值
;; 等詞 eq? 還是要能夠區分它們
(run* (q)
  (fresh (x)
    (== x #f)
    (== q #f)
    (== (eq? x q) q)))

(run* (q)
  (fresh (z)
    (fresh (x)
      (== x z)
      (== q z)
      (== (eq? x q) q))))

(run* (z)
  (fresh (q)
    (fresh (x)
      (== x z)
      (== q z)
      (== (eq? x q) q))))
```

## conde

- e for every line

- undo[refreshes][backtracing] happens here

```scheme
(run* (x)
  (conde
   [(== 1 x) (== 1 x)]
   [(== 2 x) (== 3 x)]))

(run* (x)
  (conde
    [(== 'kkk x) fail]
    [(== 'kk x) succeed]
    [(== 'k x) succeed]))

(run* (x)
  (conde
    [(== 'kkk x) succeed]
    [(== 'kk x) succeed]
    [(== 'k x) succeed]))

(run* (x)
  (conde
    [(== 'kkk x) succeed]
    [(== 'kk x) succeed]
    [(== 'k x) succeed]
    [succeed succeed]))

;; 這裏第一次體現出來了 backtracing
(run* (r)
  (fresh (x y)
    (conde
      [(== 'kkk x) (== 'aaa y)]
      [(== 'kk x) (== 'aa y)] )
    (== (list x y) r)))

(define teacupo
  (lambda (x)
    (conde
      ((== 'tea x))
      ((== 'cup x)))))

(run* (x)
  (teacupo x))
```

## the scope of reification

```scheme
(run* (r)
  (fresh (x y z)
    (conde
      [(== y x) (fresh (x)
                  (== z x))]
      [(fresh (x)
         (== y x)) (== z x)])
    (== (list y z) r)))

(run* (r)
  (fresh (x y z)
    (conde
      [(== y x) (fresh (x)
                  (== z x))]
      [(fresh (x)
         (== y x)) (== z x)])
    (== 1 x)
    (== (list y z) r)))
```

## expression have value

```scheme
(run* (q)
  (let ([a (== 1 q)]
        [b (== 2 q)])
    a))

(run* (q)
  (let ([a (== 1 q)]
        [b (== 2 q)])
    b))

(run* (q)
  (let ([a (== #t q)]
        [b (fresh (x)
             (== x q)
             (== #f x))]
        [c (conde
             ((== #t q) succeed)
             (else (== #f q)))])
    b))
```

# 2 teaching old toys new tricks

## define

- 用 == 和 數據構造子 cons [任何可逆函數都可以]
  來定義新的關係 [== 是最基礎的關係]
  這就是組合方式之一

- 另外的組合方式是
  1. 關係在 conde 內複合在 形成帶有分支結構新關係
     conde 內的的語義是 or
  2. 用 fresh 構造新的 scope 引入新的 變元
     fresh 內的語義是 and

- x -
  後面會講到各種 function 如何被轉化爲 relation
  這裏首先考慮的就是 數據構造子 [cons]
  和 數據域提取子 [car cdr]

```scheme
(define caro
  (lambda (p a)
    (fresh (d)
      (== (cons a d) p))))

(run* (r)
  (fresh (x y)
    (caro (list r y) x)
    (== 1 x)))

(define cdro
  (lambda (p d)
    (fresh (a)
      (== (cons a d) p))))

(run* (r)
  (fresh (v)
    (cdro '(1 2 3) v)
    (caro v r)))

(run* (x)
  (cdro '(1 2 3) (list x 3)))

(run* (x)
  (fresh (a b)
    (== '(1 2) `(,a ,b))
    (== `(,a ,b) x)))
(run* (l)
  (fresh (x)
    (cdro l '(1 2 3 4))
    (caro l x)
    (== 0 x)))

(define conso
  (lambda (a d p)
    (== (cons a d) p)))

(run* (l)
  (conso 1 2 l))
(run* (x)
  (conso x '(1 2) '(0 1 2)))
(run* (x)
  (conso x `(1 ,x 3) `(0 1 ,x 3)))
(run* (l)
  (fresh (x)
    (== `(0 1 ,x 3) l)
    (conso x `(1 ,x 3) l)))
(run* (l)
  (fresh (x)
    (conso x `(1 ,x 3) l)
    (== `(0 1 ,x 3) l)))

;; 函數式的謂詞本身也可轉化爲關係
;; 並且不用添加新的參數
(define nullo
  (lambda (x)
    (== x '())))
(run* (x)
  (nullo x))

(define pairo
  (lambda (p)
    (fresh (a d)
      (conso a d p))))

;; 用 conso 可以定義 caro cdro 和 pairo
```

# 3 seeing old friends in new ways

## predicate to unifier

- THE FIRST COMMANDMENT
  to transform a function whose value is a boolean
  into a function whose value is a goal
  replace cond with conde and unnest each question and answer
  unnest the answer #t (or #f) by replacing it with #s (or #u)

```scheme
(define list?
  (lambda (l)
    (cond
     [(null? l) #t]
     [(pair? l) (list? (cdr l))]
     [else #f])))

(define listo
  (lambda (l)
    (conde
      [(nullo l) succeed]
      [(pairo l) (fresh (d)
                   (cdro l d)
                   (listo d))]
      [else fail])))

(run* (x)
  (listo (cons 1 2)))

(run* (x)
  (conso 1 x (cons 1 2)))

(run* (x)
  (listo (list 'a 'b x 'd)))

;; loop
(run 6 (x)
  (listo `(1 2 3 . ,x)))
```

## lolo

```scheme
(define lol?
  ;; list-of-lists?
  (lambda (l)
    (cond
     [(null? l) #t]
     [(list? (car l))
      (lol? (cdr l))]
     [else #f])))

(define lolo
  (lambda (l)
    (conde
      [(nullo l)]
      [(fresh (a)
         (caro l a)
         (listo a))
       (fresh (d)
         (cdro l d)
         (lolo d))])))

(define lolo
  (lambda (l)
    (conde
      [(nullo l)]
      [(fresh (a)
        (caro l a)
        (listo a))]
      [(fresh (d)
        (cdro l d)
        (lolo d))])))

(define lolo
  (lambda (l)
    (conde
      [(nullo l)]
      [(fresh (a d)
        (caro l a)
        (listo a)
        (cdro l d)
        (lolo d))])))

(run 10 (l)
  (lolo l))

(run 1 (q)
  (fresh (x y)
    (lolo `((1 2) (,x 3) (4 ,y)))
    (== 'ok q)))

(run 10 (x)
  (lolo `((1) . ,x)))
```

## loto

```scheme
(define twinso
  (lambda (s)
    (fresh (x y)
      (conso x y s)
      (conso x '() y))))

(define twinso
  (lambda (s)
    (fresh (x)
      (== (list x x) s))))

(run* (q)
  (twinso '(1 1))
  (== #t q))

(run* (q)
  (twinso `(1 ,q)))

(define loto
  ;; list-of-twins
  (lambda (l)
    (conde
     [(nullo l)]
     [(fresh (a)
        (caro l a)
        (twinso a))
      (fresh (d)
        (cdro l d)
        (loto d))])))

(define loto
  ;; list-of-twins
  (lambda (l)
    (conde
      [(nullo l)]
      [(fresh (a d)
        (caro l a)
        (twinso a)
        (cdro l d)
        (loto d))])))

(run 10 (z)
  (loto `((1 1) . ,z)))
```

## listofo

```scheme
(define listofo
  (lambda (predo l)
    ;; pred denote predicate
    (conde
      [(nullo l)]
      [(fresh (a d)
         (caro l a)
         (predo a)
         (cdro l d)
         (listofo predo d))])))

(run 10 (out)
  (fresh (w x y z)
    (== `((1 1) (2 ,w) (,x ,y) . ,z) out)
    (listofo twinso out)))

(run 1 (out)
  (fresh (w x y z)
    (== z out)
    (listofo nullo out)))

(define loto
  (lambda (l)
    (listofo twinso l)))

(run 10 (z)
  (loto `((1 1) . ,z)))
```

## membero

```scheme
(define member?
  (lambda (x l)
    (cond
     [(null? l) #f]
     [(eq-car? l x) #t]
     [else (member? x (cdr l))])))

(define eq-car?
  (lambda (l x)
    (eq? (car l) x)))

;; note that
;; Whenever a conde line is guaranteed to fail
;; it is unnecessary
;; 不必用謂詞檢查 以保證參數在函數的定義域內
;; 因爲施行計算的不是函數 而是關係
(define membero
  (lambda (x l)
    (conde
      [(caro l x)]
      [(fresh (d)
         (cdro l d)
         (membero x d))])))

(run 10 (l)
  (membero 1 (cons l 1)))

(run 10 (l)
  (membero 1 (cons 1 l)))

(run 10 (l)
  (membero 1 (list l 1)))

(run 10 (l)
  (membero 1 (list 1 l)))

(run* (x)
  (membero 2 (list 1 x 3)))

(run* (x)
  (membero 2 (list 1 2 x 3)))

(run* (x)
  (membero 2 (list 1 x 2 3)))

(run* (out)
  (fresh (x y)
    (membero 2 (list 1 x 3 y))
    (== (list x y) out)))

(run* (x)
  (membero x (list 1 2 3)))

(run 10 (l)
  (membero 'k l))
```

## pmembero

- 這個變體在於 控制解的形狀
  縮小了解集

```scheme
(define pmembero
  ;; p denotes proper-list
  (lambda (x l)
    (conde
      [(caro l x) (cdro l '())]
      [(fresh (d)
         (cdro l d)
         (pmembero x d))])))

(run 10 (l)
  (pmembero 'k l))

;; mistake
(run* (out)
  (pmembero 1 '(a b 1 c d 1))
  (== #f out))

(run* (out)
  (membero 1 '(a b 1 c d 1))
  (== #f out))

(define pmembero
  (lambda (x l)
    (conde
      [(caro l x) (cdro l '())]
      [(caro l x)]
      [(fresh (d)
         (cdro l d)
         (pmembero x d))])))

;; mistake
(run* (out)
  (pmembero 1 '(a b 1 c d 1))
  (== #f out))

(define pmembero
  (lambda (x l)
    (conde
      [(caro l x) (cdro l '())]
      [(caro l x)
       (fresh (a d)
         (cdro l (cons a d)))]
      [(fresh (d)
         (cdro l d)
         (pmembero x d))])))

(run* (out)
  (pmembero 1 '(a b 1 c d 1))
  (== #f out))

(run 10 (l)
  (pmembero 'k l))

(define pmembero
  (lambda (x l)
    (conde
      [(caro l x)
       (fresh (a d)
         (cdro l (cons a d)))]
      [(caro l x) (cdro l '())]
      [(fresh (d)
         (cdro l d)
         (pmembero x d))])))

(run 10 (l)
  (pmembero 'k l))

(define pmembero
  (lambda (x l)
    (fresh () (membero x l)
          (listo l))))

(run 10 (l)
  (pmembero 'k l))

(run* (out)
  (membero 1 '(a b 1 c d 1))
  (== #f out))
```

## memberrevo

- 注意回溯的順序

```scheme
(define memberrevo
  ;; rev denotes reverse
  (lambda (x l)
    (conde
      [(fresh (d)
         (cdro l d)
         (memberrevo x d))]
      [(caro l x)])))

(run* (x)
  (memberrevo x '(1 2 3 4 5)))

;; 嵌套在 oro 裏順序當然也被保持
(run 3 (k)
     (conde
       [(== k 1)]
       [(conde
          [(== k 2)])]
       [(== k 3)]))

(define list-reverse
  (lambda (l)
    (run* (y)
      (memberrevo y l))))

(list-reverse '(1 2 3))
```

# 4 members only

## function to unifier

- THE SECOND COMMANDMENT
  to transform a function whose value is not a boolean into
  a function whose value is a goal
  add an extra argument to hold its value
  replace cond with conde
  and unnest each question and answer

- 注意
  在一次 unification 中
  memo 的 out 只可能有一種結果

```scheme
(define memo
  (lambda (x l out)
    (conde
      [(caro l x) (== l out)]
      [(fresh (d)
        (cdro l d)
        (memo x d out))])))

(run 1 (out)
  (memo 1 '(0 0 1 2 3) out))

(run* (r)
  (memo r
         '(0 0 1 2 3)
         '(1 2 3)))

(run* (r)
  (memo 1 '(1 2 3) '(1 2 3))
  (== #f r))

(run* (r)
  (memo 1 '(1 2 3) '(1 2))
  (== #f r))

(run* (r)
  (memo 1 '(1 2 3) '(1 2))
  (== #f r))

(run* (x)
  (memo 1 `(1 2) `(7 ,x)))

(run* (out)
  (fresh (x)
    (memo 1 `(0 0 ,x 0 1 2) out)))

(run 10 (z)
  (fresh (u)
    (memo 'k `(0 0 k 0 k 2 . ,z) u)))
```

## rembero

```scheme
(define rembero
  (lambda (x l out)
    (conde
      [(nullo l)
       (== '() out)]
      [(conso x out l)]
      [(fresh (res a d)
        (conso a d l)
        (rembero x d res)
        (conso a res out))])))

(run* (out)
  (rembero 1 '(1 2 3) out))

(run* (out)
  (fresh (y)
    (rembero 0 `(1 2 ,y 3 0 4) out)))

(run* (out)
  (fresh (y z)
    (rembero y `(a b ,y d ,z e) out)))

(run* (r)
  (fresh (y z)
    (rembero y `(,y d ,z e) `(,y d e))
    (== `(,y ,z) r)))

(run 13 (w)
  (fresh (y z out)
    (rembero y `(a b ,y d ,z . ,w) out)))

(run 13 (r)
  (fresh (w y z out)
    (rembero y `(a b ,y d ,z . ,w) out)
    (== (list w y z out) r)))

;; 以爲不移除任何東西是允許的
;; 而且 在轉向下個 oro 分支的時候
;; 之前 unification 都被忘記了
;; 所以處理語義的時候要小心
(define surpriseo
  (lambda (s)
    (rembero s '(a b c) '(a b c))))

;; 下面的語義是正常的
(run* (r)
  (== 'd r)
  (surpriseo r))

;; 而下面的三個例子中 語義可能就是有偏差的了
(run* (r)
  (surpriseo r))
(run* (r)
  (== 'b r)
  (surpriseo r))
(run* (r)
  (surpriseo r)
  (== 'b r))

;; 下面可以讓 移除 的語義更加嚴格
;; 不接受沒有移除任何元素的情況
;; 這種東西在 一般的函數式編程中是表達不出來的
(define rembero
  (lambda (x l out)
    (conde
      ;; without the following
      ;; it can not succeed by remove nothing
      ;; (fresh ()
      ;;   (nullo l)
      ;;   (== '() out))
      [(conso x out l)]
      [(fresh (res a d)
        (conso a d l)
        (rembero x d res)
        (conso a res out))])))
```

# 5 double your fun

## appendo

- order of fresh ()
  make recursive call as late as possible
  thus recursive call is not on fresh variables

```scheme
(define appendo
  (lambda (l s out)
    (conde
      [(nullo l)
       (== out s)]
      [(fresh (a d rec)
        (conso a d l)
        (appendo d s rec)
        (conso a rec out))])))

(run* (out)
  (appendo `(1 2 3) `(a b c) out))

(run 10 (x)
  (fresh (y)
    (appendo `(a b c . ,y) `(d e) x)))

(run 10 (x)
  (fresh (y)
    (appendo `(a b c . ,y) `(d e . ,y) x)))

(run 10 (x)
  (fresh (y)
    (appendo `(a b c) `(d e . ,y) x)))

;; can't run 12
;; because on the 12th times
;; where you pretend that the first branch does not unify
;; l & s are fresh
;; thus d s rec are all fresh in the recursive call
(run 11 (x)
  (fresh (y)
    (appendo x y `(1 2 3 4 5 6 7 8 9 10))))

(run 11 (y)
  (fresh (x)
    (appendo x y `(1 2 3 4 5 6 7 8 9 10))))

(run 11 (out)
  (fresh (x y)
    (appendo x y `(1 2 3 4 5 6 7 8 9 10))
    (== `(,x ,y) out)))

(define appendo
  (lambda (l s out)
    (conde
      [(nullo l)
       (== out s)]
      [(fresh (a d rec)
         (conso a d l)
         (conso a rec out)
         (appendo d s rec))])))

;; can run 12
(run 12 (x)
  (fresh (y)
    (appendo x y `(1 2 3 4 5 6 7 8 9 10))))

(run 12 (y)
  (fresh (x)
    (appendo x y `(1 2 3 4 5 6 7 8 9 10))))

(run 12 (out)
  (fresh (x y)
    (appendo x y `(1 2 3 4 5 6 7 8 9 10))
    (== `(,x ,y) out)))


(run 3 (x)
  (fresh (y z)
    (appendo x y z)))

(run 3 (y)
  (fresh (x z)
    (appendo x y z)))

(run 3 (z)
  (fresh (x y)
    (appendo x y z)))

(run 3 (out)
  (fresh (x y z)
    (appendo x y z)
    (== `(,x ,y ,z) out)))

(define swappendo
  (lambda (l s out)
    (conde
      [(fresh (a d rec)
        (conso a d l)
        (conso a rec out)
        ;; on recursive unification
        ;; all variables are fresh
        (swappendo d s rec))]
      [(and (nullo l)
           (== out s))])))

;; forever
;; (run 1 (z)
;;   (fresh (x y)
;;     (swappendo x y z)))
```

## [note] 思想模型

- 在對例子的觀察中
  獨立建立起自己的思想模型
  來理解語言的語義

## unwarp

- order of oro

```scheme
(define unwarpo
  (lambda (x out)
    (conde
      [(fresh (a)
        (pairo x)
        (caro x a)
        (unwarpo a out))]
      [(== x out)])))

(run* (x)
  (unwarpo '((((((1)))))) x))

;; loop
;; (run 1 (x)
;;   (unwarpo x 1))
;; for recursive call on 'a' 'out'
;; but after
;;   (pairo x)
;;   (caro x a)
;; 'a' 'out' are still fresh variables

(define unwarpo
  (lambda (x out)
    (conde
      [(== x out)]
      [(fresh (a)
        (pairo x)
        (caro x a)
        (unwarpo a out))])))

(run* (x)
  (unwarpo '((((((1)))))) x))

(run 3 (x)
  (unwarpo x 1))

(run 3 (x)
  (unwarpo `((,x)) 1))
```

## flatteno

- 注意解的列表如何循環

```scheme
(define flatten
  (lambda (s)
    (cond [(null? s) '()]
          [(pair? s)
           (append (flatten (car s))
                   (flatten (cdr s)))]
          [else (cons s '())])))

(flatten '((1 (2)) 3))
(flatten '(1))
(flatten '(1 . ()))
(flatten '(1 . 2))
(flatten '1)

(define flatteno
  (lambda (s out)
    (conde
      [(nullo s) (== '() out)]
      [(fresh (a d res-a res-d)
         (conso a d s)
         (flatteno a res-a)
         (flatteno d res-d)
         (appendo res-a res-d out))]
      [(conso s '() out)])))

(run* (out)
  (flatteno '((1 (2)) 3) out))

(run* (out)
  (flatteno '((1 2) 3) out))

(run* (out)
  (flatteno '(1) out))

;; loop
;; (run 3 (x)
;;   (flatteno x '(1)))


(define flattenrevo
  (lambda (s out)
    (conde
      [(nullo s) (== '() out)]
      [(conso s '() out)]
      [(fresh (a d res-a res-d)
         (conso a d s)
         (flattenrevo a res-a)
         (flattenrevo d res-d)
         (appendo res-a res-d out))])))

(run 2 (x)
  (flattenrevo x '(1)))

(run 3 (x)
  (flattenrevo x '(1)))

(run 10 (x)
  (flattenrevo x '(1)))

(equal?
 (run* (out)
   (flattenrevo '((1 2) 3) out))
 (reverse
  (run* (out)
    (flatteno '((1 2) 3) out))))


(define flattenrevo
  (lambda (s out)
    (conde
      [(conso s '() out)]
      [(nullo s) (== '() out)]
      [(fresh (a d res-a res-d)
         (conso a d s)
         (flattenrevo a res-a)
         (flattenrevo d res-d)
         (appendo res-a res-d out))])))

;; can not run 3
(run 2 (x)
  (flattenrevo x '(1)))

(equal?
 (run* (out)
   (flattenrevo '((1 2) 3) out))
 (reverse
  (run* (out)
    (flatteno '((1 2) 3) out))))
```

# 6 the fun never ends

## 思想模型

- 還是用 or-list and-list
  還有 or-cursor and-cursor
  還有 undo-info
  來考慮
  [儘管這是 prolog VM 的實現方式]

- the law of condi
  condi behaves like conde
  except that its values are interleaved

```scheme
(define anyo
  (lambda (g)
    (conde
      [g]
      [(anyo g)])))

(run 3 (x)
  (anyo succeed))

;; loop
;; (run 1 (x)
;;   (anyo fail))

(define nevero (anyo fail))
(define alwayso (anyo succeed))

;; loop
;; (run 1 (q)
;;   nevero
;;   (== q #t))

(run 1 (q)
  fail
  nevero)

(run 1 (q)
  succeed
  (== q #t))

(run 1 (q)
  alwayso
  (== q #t))

(run 3 (q)
  succeed
  (== q #t))

;; different from succeed
;; alwayso can succeed any number of times
(run 3 (q)
  alwayso
  (== q #t))

(define salo
  ;; succeeds at least onec
  (lambda (g)
    (conde
      [succeed]
      [g])))

(run 3 (g)
  (salo alwayso)
  (== #t g))

(run 1 (g)
  (salo nevero)
  (== #t g))

;; loop
;; (run 2 (g)
;;   (salo nevero)
;;   (== #t g))

;; loop
;; (run 1 (g)
;;   (salo nevero)
;;   fail
;;   (== #t g))

;; loop
;; (run 1 (g)
;;   alwayso
;;   fail
;;   (== #t g))

;; loop
;; (run 1 (g)
;;      (conde [(== #f g) alwayso]
;;             [(== #t g)])
;;      (== #t g))

;; no loop
;; because after the first failure
;; instead of staying on the first line
;; we try the second condi line
(run 1 (g)
  (condi [(== #f g) alwayso]
         [(== #t g)])
  (== #t g))

;; it has no value
;; since the second condi line is out of values
;; (run 2 (g)
;;   (condi [(== #f g) alwayso]
;;          [(== #t g)])
;;   (== #t g))

(run 5 (g)
  (condi [(== #f g) alwayso]
         [(anyo (== #t g))])
  (== #t g))

(define teacupo
  (lambda (x)
    (conde
      [(== 'tea x)]
      [(== 'cup x)])))

(run 5 (r)
  (condi [(teacupo r)]
         [(== #t r)]))

(run 5 (r)
  (conde [(teacupo r)]
         [(== #t r)]))


(run 5 (q)
  (condi
    [(== #f q) alwayso]
    [(== #t q) alwayso])
  (== #t q))

;; loop
;; (run 5 (q)
;;   (conde
;;     [(== #f q) alwayso]
;;     [(== #t q) alwayso])
;;   (== #t q))


(run 4 (q)
  (condi
    [(== 0 q) alwayso]
    [(== 1 q) alwayso]))

(run 4 (q)
  (conde
    [(== 0 q) alwayso]
    [(== 1 q) alwayso]))


;; loop
;; (run 4 (q)
;;   (condi
;;     [alwayso]
;;     [nevero]))

(run 4 (q)
  (conde
    [alwayso]
    [nevero]))

;; loop
;; (run 1 (q)
;;   (all (conde
;;          [(== #f q)]
;;          [(== #t q)])
;;        alwayso)
;;   (== #t q))

(run 1 (q)
  (alli (conde
          [(== #f q)]
          [(== #t q)])
        alwayso)
  (== #t q))

(run 5 (q)
  (alli (conde
          [(== #f q)]
          [(== #t q)])
        alwayso)
  (== #t q))

(run 5 (q)
  (alli (conde
          [(== #t q)]
          [(== #f q)])
        alwayso)
  (== #t q))


(run 5 (q)
  (all (conde
         [succeed]
         [nevero])
       alwayso)
  (== #t q))

;; loop
;; (run 5 (q)
;;   (alli (conde
;;          [succeed]
;;          [nevero])
;;        alwayso)
;;   (== #t q))
```

# 7 a bit too much

## bit

```scheme
(define bit-xoro
  (lambda (x y r)
    (conde
      [(== 0 x) (== 0 y) (== 0 r)]
      [(== 1 x) (== 0 y) (== 1 r)]
      [(== 0 x) (== 1 y) (== 1 r)]
      [(== 1 x) (== 1 y) (== 0 r)])))

(run* (s)
  (fresh (x y)
    (bit-xoro x y 0)
    (== (list x y) s)))

(run* (s)
  (fresh (x y)
    (bit-xoro x y 1)
    (== (list x y) s)))

(run* (s)
  (fresh (x y r)
    (bit-xoro x y r)
    (== (list x y r) s)))

(define bit-ando
  (lambda (x y r)
    (conde
      [(== 0 x) (== 0 y) (== 0 r)]
      [(== 1 x) (== 0 y) (== 0 r)]
      [(== 0 x) (== 1 y) (== 0 r)]
      [(== 1 x) (== 1 y) (== 1 r)])))

(run* (s)
  (fresh (x y)
    (bit-ando x y 1)
    (== (list x y) s)))

(define half-addero
  (lambda (x y r c)
    (all
     (bit-xoro x y r)
     (bit-ando x y c))))
```

## number

```scheme
(define build-num
  (lambda (n)
    (cond
     [(zero? n) '()]
     [(and (not (zero? n)) (even? n))
      (cons 0
            (build-num (/ n 2)))]
     [(odd? n)
      (cons 1
            (build-num (/ (- n 1) 2)))])))

(build-num 6)

(define poso
  (lambda (n)
    (fresh (a d)
      (== (cons a d) n))))

(run* (r)
  (poso r))
```

# >< 8 just a bit more

# 9 under the hood

## [note] unification

- 變元之間的融合關係有向圖
  首先想到的有兩種實現方式
  1. 以點爲實現實體
     每個點中儲存它於其他點的關係
     - 再限制每個點內只能保存一個點
       再對圖加以等價變形
       就得到 prolog 的實現方式
  2. 以邊爲實現實體
     每個邊中保存與他相關的點的關係

- 後者實現起來簡單
  設想要給三維拓撲圖形編碼
  那麼可能以最高維的幾何體作爲實現實體
  實現起來最簡單

## circular

- minikanren 加上 check 之後
  ==-check 就不接受 循環的 term 了
  這樣 walk* 的時候就不會有無線循環

- 而 prolog 總是接受有循環的 term
  但是在 walk* 的時候
  發現循環的 term 並處理他們
  也就是有個 walk*-check

- 目的都是爲了避免運行時的無線循環

```scheme
(run 1 (x)
  (fresh (y z)
    (== y z)
    (== z x)
    (== x y)))

;; loop
;; (run 1 (x)
;;   (fresh (y z)
;;     (== (cons x y) z)
;;     (== (cons z x) y)
;;     (== (cons y z) x)))

(run 1 (x)
  (fresh (y)
    (== y x)
    (== x y)))

;; loop
;; (run 1 (x)
;;   (fresh (y)
;;     (== x (list y))
;;     (== y (list x))))

;; loop
;; (run 1 (x)
;;   (== (list x) x))

(run 1 (q)
  (fresh (x)
    (== (list x) x)
    (== #t q)))

(run 1 (q)
  (fresh (x y)
    (== (list x) y)
    (== (list y) x)
    (== #t q)))

(run 1 (x)
  (==-check (list x) x))

(run 1 (x)
  (==-check x (list x)))
```

## [note] 表達方式

- 當你把觀察後的理解成果 表達地特別機械化時
  你大概就知道如何實現這些想法了

# >< 10 thin ice

## conda

- 'a' denotes a single line

- The Law of conda
  If the question of a conda line succeeds
  pretend that the remaining conda lines
  have been not there

  - like the cut of prolog

- only if the question of a conda line fails
  do we consider the remaining conda lines

- THE THIRD COMMANDMENT
  If prior to determining the question of a conda line
  a variable is fresh
  it must remain fresh in the question of that line

```scheme
(run* (x)
  (conda
   [(== 'olive x)]
   [(== 'oil x)]))

(define not-pastao
  (lambda (x)
    (conda
     [(== 'pasta x) fail]
     [else succeed])))

(run* (x)
  (conda
   [(not-pastao x) fail]
   [else (== 'spaghetti x)]))

(run* (x)
  (== 'spaghetti x)
  (conda
   [(not-pastao x ) fail]
   [else (== 'spaghetti x)]))
```
