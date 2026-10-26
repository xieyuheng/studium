---
title: 自类型
subtitle: Self Type
---

# Scott Encoding

```scheme
(define zero (lambda (base step) base))
(define (add1 prev) (lambda (base step) (step prev)))
(define (which-Nat n base step) (n base step))
```

## Typing

```scheme
(claim zero
  (nu (X)
    (-> X (-> Nat X) X)))

(define Nat
  (nu (X)
    (-> X (-> Nat X) X)))

(claim add1
  (-> Nat Nat)
  (nu (X)
    (-> Nat (-> X (-> Nat X) X))))

(claim which-Nat
  (nu (X)
    (-> Nat (-> X (-> Nat X) X))))
```

# Church Encoding

```scheme
(define zero (lambda (base step) base))
(define (add1 prev) (lambda (base step) (step (prev base step))))
(define (iter-Nat n base step) (n base step))
```

## Typing

The type of `zero` is the `Nat`.

We know:

```scheme
(claim zero
  (nu (X)
    (-> X (-> X X) X)))
```

Thus:

```scheme
(define Nat
  (nu (X)
    (-> X (-> X X) X)))

(claim add1
  (-> Nat Nat)
  (nu (X)
    (-> Nat (-> X (-> X X) X))))

(claim iter-Nat
  (nu (X)
    (-> Nat (-> X (-> X X) X))))
```

# Parigot Encoding

```scheme
(define zero (lambda (base step) base))
(define (add1 prev) (lambda (base step) (step prev (prev base step))))
(define (rec-Nat n base step) (n base step))
```

## Typing

```scheme
(claim zero
  (nu (X)
    (-> X (-> Nat X X) X)))

(define Nat
  (nu (X)
    (-> X (-> Nat X X) X)))

(claim add1
  (-> Nat Nat)
  (nu (X)
    (-> Nat (-> X (-> Nat X X) X))))
```

# Inductive Encoding

Remember that `ind-Nat` take `target` as an explicit argument,
because we want to infer application of `ind-Nat`.

```scheme
(claim ind-Nat
  (Pi ((target Nat)
       (motive (-> Nat Type))
       (base (motive zero))
       (step (Pi ((prev Nat))
               (-> (motive prev)
                   (motive (add1 prev))))))
    (motive target)))

(define (ind-Nat target motive base step) (target motive base step))
```

## solve zero

We already know what `(ind-Nat zero)` should be,
thus we have equation about `zero`,
let's try to solve `zero` from this equation.

```scheme
(claim (ind-Nat zero)
  (Pi ((motive (-> Nat Type))
       (base (motive zero))
       (step (Pi ((prev Nat))
               (-> (motive prev)
                   (motive (add1 prev))))))
    (motive zero)))

(assert-equal
  (ind-Nat zero motive)
  (lambda (base step) (zero motive base step))
  (lambda (base step) base))

(define zero (lambda (motive base step) base))

(claim zero
  (Pi ((motive (-> Nat Type))
       (base (motive zero))
       (step (Pi ((prev Nat))
               (-> (motive prev)
                   (motive (add1 prev))))))
    (motive zero)))
```

The type of `zero` is `Nat`, thus we have:

```scheme
(define Nat
  (Pi ((motive (-> Nat Type))
       (base (motive zero))
       (step (Pi ((prev Nat))
               (-> (motive prev)
                   (motive (add1 prev))))))
    (motive target)))
```

But target is a free variable.

```scheme
(define (add1 prev)
  (lambda (motive base step)
    (step prev (prev motive base step))))

(claim add1
  (-> Nat Nat)
  (Pi ((prev Nat)
       (motive (-> Nat Type))
       (base (motive zero))
       (step (Pi ((prev Nat))
               (-> (motive prev)
                   (motive (add1 prev))))))
    (motive (add1 prev))))
```

## define Nat as a self type

It seems we are defining one Nat for each n.
Here comes self types.

```scheme
(define Nat
  (Self (target)
    (Pi ((motive (-> Nat Type))
         (base (motive zero))
         (step (Pi ((prev Nat))
                 (-> (motive prev)
                     (motive (add1 prev))))))
      (motive target))))
```

## check zero

```scheme
(claim zero Nat)
(define zero (lambda (motive base step) base))

(type-checking-chart
 (check () zero Nat)
 (check ()
   zero
   (Self (target)
     (Pi ((motive (-> Nat Type))
          (base (motive zero))
          (step (Pi ((prev Nat))
                  (-> (motive prev)
                      (motive (add1 prev))))))
       (motive target))))
 (check ()
   (lambda (motive base step) base)
   (Self (target)
     (Pi ((motive (-> Nat Type))
          (base (motive zero))
          (step (Pi ((prev Nat))
                  (-> (motive prev)
                      (motive (add1 prev))))))
       (motive target))))
 (check ((target Nat zero))
   (lambda (motive base step) base)
   (Pi ((motive (-> Nat Type))
        (base (motive zero))
        (step (Pi ((prev Nat))
                (-> (motive prev)
                    (motive (add1 prev))))))
     (motive target)))
 (check ((target Nat zero)
         (motive (-> Nat Type))
         (base (motive zero))
         (step (Pi ((prev Nat))
                 (-> (motive prev)
                     (motive (add1 prev))))))
   base
   (motive target))
 (lookup-and-equal-type
  ((target Nat zero)
   (motive (-> Nat Type))
   (base (motive zero))
   (step (Pi ((prev Nat))
           (-> (motive prev)
               (motive (add1 prev))))))
  base
  (motive target))
 (equal-type
  ((target Nat zero)
   (motive (-> Nat Type))
   (base (motive zero))
   (step (Pi ((prev Nat))
           (-> (motive prev)
               (motive (add1 prev))))))
  (motive zero)
  (motive target))
 (equal-type
  ((target Nat zero)
   (motive (-> Nat Type))
   (base (motive zero))
   (step (Pi ((prev Nat))
           (-> (motive prev)
               (motive (add1 prev))))))
  (motive zero)
  (motive zero)))
```

## check add1

```scheme
(claim add1 (-> Nat Nat))
(define add1
  (lambda (prev)
    (lambda (motive base step)
      (step prev (prev motive base step)))))

(type-checking-chart
 (check ()
   add1
   (-> Nat Nat))
 (check ()
   (lambda (prev)
     (lambda (motive base step)
       (step prev (prev motive base step))))
   (-> Nat Nat))
 (check ((prev Nat))
   (lambda (motive base step)
     (step prev (prev motive base step)))
   Nat)
 (check ((prev Nat))
   (lambda (motive base step)
     (step prev (prev motive base step)))
   (Self (target)
     (Pi ((motive (-> Nat Type))
          (base (motive zero))
          (step (Pi ((prev Nat))
                  (-> (motive prev)
                      (motive (add1 prev))))))
       (motive target))))
 (check ((prev Nat)
         (target Nat (lambda (motive base step)
                       (step prev (prev motive base step)))))
   (lambda (motive base step)
     (step prev (prev motive base step)))
   (Pi ((motive (-> Nat Type))
        (base (motive zero))
        (step (Pi ((prev Nat))
                (-> (motive prev)
                    (motive (add1 prev))))))
     (motive target)))
 (check ((prev Nat)
         (target Nat (lambda (motive base step)
                       (step prev (prev motive base step))))
         (motive (-> Nat Type))
         (base (motive zero))
         (step (Pi ((prev Nat))
                 (-> (motive prev)
                     (motive (add1 prev))))))
   (step prev (prev motive base step))
   (motive target))
 (check ((prev Nat)
         (target Nat (lambda (motive base step)
                       (step prev (prev motive base step))))
         (motive (-> Nat Type))
         (base (motive zero))
         (step (Pi ((prev Nat))
                 (-> (motive prev)
                     (motive (add1 prev))))))
   ;; (check (...) (prev motive base step) (motive prev))
   (motive (add1 prev))
   (motive target))
 (check ((prev Nat)
         (target Nat (lambda (motive base step)
                       (step prev (prev motive base step))))
         (motive (-> Nat Type))
         (base (motive zero))
         (step (Pi ((prev Nat))
                 (-> (motive prev)
                     (motive (add1 prev))))))
   (motive ((lambda (prev)
              (lambda (motive base step)
                (step prev (prev motive base step))))
            prev))
   (motive (lambda (motive base step)
             (step prev (prev motive base step)))))
 (check ((prev Nat)
         (target Nat (lambda (motive base step)
                       (step prev (prev motive base step))))
         (motive (-> Nat Type))
         (base (motive zero))
         (step (Pi ((prev Nat))
                 (-> (motive prev)
                     (motive (add1 prev))))))
   (motive (lambda (motive base step)
             (step prev (prev motive base step))))
   (motive (lambda (motive base step)
             (step prev (prev motive base step))))))
```
