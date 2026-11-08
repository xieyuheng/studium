---
title: to be the little schemer
---

# >< recursion
# 8 lambda the ultimate
## rember-f

```scheme
(define rember-f
  (lambda (test?)
    (lambda (a l)
      (letrec
          ([R (lambda (l)
                (cond [(null? l) '()]
                      [(test? (car l) a)
                       (cdr l)]
                      [else
                       (cons (car l)
                             (R (cdr l)))]
                      ))])
        (R l)))))

((rember-f eq?)  1 '(11 1 1 1))


(rember-f eq?)
==
((lambda (test?)
   (lambda (a l)
     (letrec
         ([R (lambda (l)
               (cond [(null? l) '()]
                     [(test? (car l) a)
                      (cdr l)]
                     [else
                      (cons (car l)
                            (R (cdr l)))]
                     ))])
       (R l))))
 eq?)
==
(lambda (a l)
  (letrec
      ([R (lambda (l)
            (cond [(null? l) '()]
                  [(eq? (car l) a)
                   (cdr l)]
                  [else
                   (cons (car l)
                         (R (cdr l)))]
                  ))])
    (R l)))

((rember-f eq?) 1 '(11 1 1 1))
==
((lambda (a l)
   (letrec
       ([R (lambda (l)
             (cond [(null? l) '()]
                   [(eq? (car l) a)
                    (cdr l)]
                   [else
                    (cons (car l)
                          (R (cdr l)))]
                   ))])
     (R l)))
 1 '(2 1 3))
==
list :== '()



(letrec
    ([R (lambda (l)
          (cond [(null? l) '()]
                [(eq? (car l) a)
                 (cdr l)]
                [else
                 (cons (car l)
                       (R (cdr l)))]
                ))])
  (R l))
```

## insert-g

```scheme
(define insert-g
  (lambda (test?)
    (lambda (g)
      (lambda (new old l)
        (letrec
            ([I (lambda (l)
                  (cond
                   [(null? l) '()]
                   [(test? (car l) old)
                    (g new old (cdr l))]
                   [else
                    (cons (car l)
                          (I (cdr l)))]))])
          (I l))))))
(define seqL
  (lambda (new old l)
    (cons new (cons old l))))
(define seqR
  (lambda (new old l)
    (cons old (cons new l))))
(define seqS
  (lambda (new old l)
    (cons new l)))
(define seqrem
  (lambda (new old l)
    l))
(((insert-g eq?) seqR) 'k 2 '(1 2 3))
(((insert-g eq?) seqL) 'k 2 '(1 2 3))
(((insert-g eq?) seqS) 'k 2 '(1 2 3))
(((insert-g eq?) seqrem) 'k 2 '(1 2 3))
```

abstract common patterns with a new function

## >< multirember&co

```scheme
(define multiinsertLR&co
  (lambda (new oldL oldR lat col) ;;col denote collector
    (cond [(null? lat)
           (col '() 0 0)]
          [(eq? (car lat) oldL)
           (multiinsertLR&co new oldL oldR
                             (cdr lat)
                             (lambda (newlat L R)
                               (col (cons new (cons oldL newlat))
                                    (add1 L)
                                    R)))]
          [(eq? (car lat) oldR)
           (multiinsertLR&co new oldL oldR
                             (cdr lat)
                             (lambda (newlat L R)
                               (col (cons oldR (cons new newlat))
                                    L
                                    (add1 R))))]
          [else
           (multiinsertLR&co new oldL oldR
                             (cdr lat)
                             (lambda (newlat L R)
                               (col (cons (car lat) newlat)
                                    L
                                    R)))]
          )))


;; test:
(multiinsertLR&co 'k '<l 'r> '(<l r> <l r>)
                  (lambda (newlat L R)
                    newlat))
```

# 9 ... and again, and again, and again, ... (Ycombinator)
## partial function

指這樣的遞歸函數（考慮遞歸函數的工作方式）
對一部分輸入值它會在有限次層展開後停止
而對其他部分的輸入值 遞歸函數就這些值而展開時 不會停止
（相反的術語是：total function）
+ 在明確地定義拓撲結構之前
  儘量避免使用拓撲學術語 去討論類似收斂性的性質
``the recursion step gives the value in terms of
the value of the procedure applied to a different argument
in order for the recursion to terminate
the different argument must be
closer to the base argument in some way''
--kent dybvig

想用遞歸的方式來實現某個函數(procedure)
如果能找到某個對輸入值的某一方面的度量（用自然數）
使這一度量隨着遞歸函數展開的層次的深入而嚴格遞減
那麼就能保證這個遞歸函數是一個total function

## inperfectness, bad and eternity

假設有一個探究函數性質的謂詞名叫will-stop?
它能判斷一個函數是否是total的
往證這樣的謂詞的存在性將導出矛盾
首先作簡化 不妨假設它判斷的是作用於'()的函數
考慮它作用於函數bad的值 即考慮bad作用於'()是否會停止
就展示了不協調性 即矛盾

```scheme
(define bad
  (lambda (x)
    (and (will-stop? bad)
         (eternity x))))

(define eternity
  (lambda (x)
    (eternity x)))
```

## Y
### lambda表達式的作用：

通常只有當我命名一個非遞歸函數後
我才能呼喚它讓它作用於自變量
而lambda表達式使得我可以換直接寫出一個非遞歸函數
然後指示它讓它作用於自變量 而不用給函數命名

### Ycombinator的作用：

可是代碼對於遞歸函數的描述是表面上的循環定義
需要命名函數後才能在定義中循環呼喚它
爲了不給函數命名而可以直接使用它 人們必須另想辦法
辦法之一就是使用Ycombinator
一個可以描述遞歸函數作用的λ-term

### 下面這一段總結自sicp：

#### 遞歸函數的看似循環的定義 可以被看作由函數方程確定函數
通常人們說循環定義是錯誤的
然而其實人們經常把一個方程變形爲等價的迭代方程
這個迭代方程就是循環定義的 但它並不是錯誤的 而是有精確語義的
人們對方程作這樣變形 就是爲了解原方程 或者近似地解原方程
sicp中通過反過來考慮而把循環定義理解爲方程
使得一個人首先在觀念上不再排斥循環定義

任意一個方程的一個未知元
都可以看作一個約束變元
而一個慣例是用這個變元來命名方程的解

在一個遞歸函數的定義（一個方程）中 用define命名函數時使用的名稱
可以看作一個約束變元
只不過之後把所求得的遞歸函數存入了這個名稱所對應的存儲空間
#### Ycombinator是以遞歸函數爲不動點的算子
>< 有了λ-cal的知識之後就發現我在這部分說的很多內容並不正確
>< 但是作爲一個求解Y的heuristic 這些文本還是有很大意義的
可以說它把上面的函數方程解了出來
即把函數方程的解用函數顯式表示出來
這個解甚至可以在scheme的解釋器中進行計算
+ 這裏體現了人們對函數的追求 或者更本質地說 是對計算的追求
  因爲儘管函數並不總是能用顯式計算
  或更進一步 高效地用顯式計算
  但是一個關係是函數關係可能暗示着這個關係的宜計算性
+ 這裏“不動點”是一個拓撲學術語 精確的討論需要明確一個拓撲結構
  並且考慮算子的收斂性 不動點的存在性與唯一性 等等
用例子來解釋如下：
```scheme
(define ^
  (lambda (x n)
    (cond ((= n 0)
           1)
          (else
           (* x (^ x (- n 1)))))))
(define F
  (lambda (g)
    (lambda (x n)
      (cond ((= n 0)
             1)
            (else
             (* x (g x (- n 1))))))))
```
把^變爲F
而F可以被Y作用而得到^
+ 可以這樣來理解：
  F中g的第二次出現標出了^的位置
  而g的第一次出現表明這個位置就是被循環調用的位置
有限次迭代F就發現
對於任意起始函數g
F的N次迭代對於所有x和小於N的n與^有相同的值
所以如果用一個可以作無窮循環的函數來迭代F就可以生成^
考慮下面的獲得無限循環的方式：
```scheme
((lambda (x) (x x)) (lambda (x) (x x)))
(define Y
  (lambda (f)
    ((lambda (x) (f (x x)))
     (lambda (x) (f (x x))))))
```
(Y F)=(F (Y F))
Y爲函數空間中的算子
但是這個函數沒法被實際調用 因爲它的遞歸層次的加深不會停止
要想寫出可以被實際調用的Y就需要很好地理解解釋器的解釋方式
### 回到the little schemer
目的是寫出一個可以在scheme中被實際調用的Ycombinator
(首先要弄清解釋器的行爲方式)
下面用兩個平行的例子來作說明
其中第一個很容易在我寫的解釋器中被求值
而要對第二個求值就需要先給我寫的解釋器增加關於數值計算的內建函數
#### 用define定義一個遞歸函數（用set!定義的也是一樣）
```scheme
(define length
  (lambda (l)
    (cond ((null? l)
           0)
          (else
           (add1 (length (cdr l)))))))
(define ^
  (lambda (x n)
    (cond ((= n 0)
           1)
          (else
           (* x (^ x (- n 1)))))))
```
下面我們的目標就是不用define來定義遞歸函數
#### 有限層遞歸函數
對於小的參數給出的結果與遞歸函數給出的結果相同
*每增加一層都要把函數的定義重新寫一遍*
```scheme
;length0
(lambda (l)
  (cond ((null? l)
         0)
        (else
         (add1 (eternity (cdr l))))))
;length1
(lambda (l)
  (cond
   ((null? l)
    0)
   (else
    (add1 ((lambda (l)
             (cond
            ((null? l)
             0)
            (else
             (add1 (eternity (cdr l))))))
           (cdr l))))))
;length2
(lambda (l)
  (cond
   ((null? l)
    0)
   (else
    (add1 ((lambda (l)
             (cond
              ((null? l)
               0)
              (else
               (add1 ((lambda (l)
                        (cond
                         ((null? l)
                          0)
                         (else
                          (add1 (eternity (cdr l))))))
                      (cdr l))))))
           (cdr l))))))

;^0
(lambda (x n)
  (cond ((= n 0)
         1)
        (else
         (* x (whatever x (- n 1))))))
;^1
(lambda (x n)
  (cond ((= n 0)
         1)
        (else
         (* x ((lambda (x n)
                 (cond ((= n 0)
                        1)
                       (else
                        (* x (whatever x (- n 1))))))
               x (- n 1))))))
;^2
(lambda (x n)
  (cond ((= n 0)
         1)
        (else
         (* x ((lambda (x n)
                 (cond ((= n 0)
                        1)
                       (else
                        (* x ((lambda (x n)
                                (cond ((= n 0)
                                       1)
                                      (else
                                       (* x (whatever x (- n 1))))))
                              x (- n 1))))))
               x (- n 1))))))
```
#### 用算子L的迭代來寫有限層遞歸函數
算子指 以procedure爲參數 以procedure爲值 的procedure
*每增加一層都要多寫一個L*
```scheme
;length0
((lambda (length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 (length (cdr l)))))))
 eternity)
;length1
((lambda (length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 (length (cdr l)))))))
 ((lambda (length)
    (lambda (l)
      (cond ((null? l)
             0)
            (else
             (add1 (length (cdr l)))))))
  eternity))
;length2
((lambda (length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 (length (cdr l)))))))
 ((lambda (length)
    (lambda (l)
      (cond ((null? l)
             0)
            (else
             (add1 (length (cdr l)))))))
  ((lambda (length)
     (lambda (l)
       (cond ((null? l)
              0)
             (else
              (add1 (length (cdr l)))))))
   eternity)))

;^0
((lambda (g)
   (lambda (x n)
     (cond ((= n 0)
            1)
           (else
            (* x (g x (- n 1)))))))
 eternity)
;^1
((lambda (g)
   (lambda (x n)
     (cond ((= n 0)
            1)
           (else
            (* x (g x (- n 1)))))))
 ((lambda (g)
   (lambda (x n)
     (cond ((= n 0)
            1)
           (else
            (* x (g x (- n 1)))))))
  eternity))
;^2
((lambda (g)
   (lambda (x n)
     (cond ((= n 0)
            1)
           (else
            (* x (g x (- n 1)))))))
 ((lambda (g)
    (lambda (x n)
     (cond ((= n 0)
            1)
           (else
            (* x (g x (- n 1)))))))
  ((lambda (g)
     (lambda (x n)
       (cond ((= n 0)
              1)
             (else
              (* x (g x (- n 1)))))))
   eternity)))
```
#### 用作用於算子L的procedure 來迭代算子L 以得到有限層遞歸函數
*每增加一層都要重新寫一個用作用於算子L的procedure*
```scheme
;length0
((lambda (mk-length)
   (mk-length eternity))
 (lambda (length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 (length (cdr l))))))))
;length1
((lambda (mk-length)
   (mk-length
    (mk-length eternity)))
 (lambda (length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 (length (cdr l))))))))
;length2
((lambda (mk-length)
   (mk-length
    (mk-length
     (mk-length eternity))))
 (lambda (length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 (length (cdr l))))))))
;^0
((lambda (mk^)
   (mk^ eternity))
 (lambda (x n)
   (cond ((= n 0)
          1)
         (else
          (* x (g x (- n 1)))))))
;^1
((lambda (mk^)
   (mk^
    (mk^ eternity)))
 (lambda (x n)
   (cond ((= n 0)
          1)
         (else
          (* x (g x (- n 1)))))))
;^2
((lambda (mk^)
   (mk^
    (mk^
     (mk^ eternity))))
 (lambda (x n)
   (cond ((= n 0)
          1)
         (else
          (* x (g x (- n 1)))))))
```
#### ``all names are equal, but some names are more equal than others.''
更換命名後
下面這個還可以作爲length0
```scheme
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 (mk-length (cdr l))))))))
(define length0
  ((lambda (mk-length)
     (mk-length mk-length))
   (lambda (mk-length)
     (lambda (l)
       (cond ((null? l)
              0)
             (else
              (add1 (mk-length (cdr l)))))))))
(length0 '())
(length0 '(1))
```
下面這個也可以作爲length1
並且我們知道對於長度大於1的l
(eternity eternity)會被求值 然後作用於(cddr l) 然後被add1作用
所以我就可以使得(eternity eternity)被求值的時候跳出來和我玩
```scheme
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 ((mk-length eternity) (cdr l))))))))
(define length1
  ((lambda (mk-length)
     (mk-length mk-length))
   (lambda (mk-length)
     (lambda (l)
       (cond ((null? l)
              0)
             (else
              (add1 ((mk-length eternity) (cdr l)))))))))
(define eternity)
(define i-want-to-play-with-length1
  (lambda ()
    ((lambda (k)
       (cond ((string? k)
              (begin
                (display k)
                (newline)))
             ((procedure? k)
              (begin (display "笨length1~~~")
                     (newline)
                     (display "碰到了長度超過1的list他就處理不了了！")
                     (newline)))
             (else
              (begin (display "nothing-else")
                     (newline)))))
     (letcc play-with-me
            (set! eternity play-with-me)
            "ready to play with length1, haha."))))
(i-want-to-play-with-length1)
(eternity '())
(length1 '())
(length1 '(1))
(length1 '(1 2))
```
#### ``all names are equal, but some names are more equal than others.''
*最後終於得到了遞歸函數length*
```scheme
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 ((mk-length mk-length) (cdr l))))))))
(define length
  ((lambda (mk-length)
     (mk-length mk-length))
   (lambda (mk-length)
     (lambda (l)
       (cond ((null? l)
              0)
             (else
              (add1 ((mk-length mk-length) (cdr l)))))))))
(length '(1 2 3 4 5 6 7 8 9 10))
```
這是
L*是L的變形 這個變形在於把L的recursion從L變爲(L L)
記這個變形的結果爲L*
L*就是我們所向往的東西 它作用於自身就得到遞歸函數
(L* L*)就是length
(lambda (mk-length) (mk-length mk-length))只是簡單地作用於L*而得到(L* L*)而已
而(L* L*)會給出一個closure
稱爲<closure-of-length>
*這個就是遞歸函數length*
當傳入參數l的是非空list而需要遞歸時
在recursion位置 就會用(L* L*)再造出一個<closure-of-length>
而傳入這個<closure-of-length>的參數就是(car l)的值了
+ 下一節想要提取出L時所犯的錯誤強調了下面這一點：
  必須是當需要遞歸時才用(L* L*)造出一個<closure-of-length>
  這個特性是可以實現的
  從下面的表格中可以看到
  當<closure-of-length> action時
  <body>就在擴展後的<environment>下用meaning問自己存在的意義是什麼
  然後如果不需要遞歸
  <closure-of-L*>就安安靜靜的呆在名字<environment>中mk-length的後面
```scheme
------------------------------------------------
<closure-of-length>
------------------------------------------------
<environment>
(((mk-length) (<closure-of-L*>))
 ...)
<formals>
(l)
<body>
(cond ((null? l)
       0)
      (else
       (add1 ((mk-length mk-length) (cdr l)))))
-------------------------------------------------
-------------------------------------------------
<closure-of-L*>
-------------------------------------------------
<environment>
(...)
<formals>
(mk-length)
<body>
(lambda (l)
  (cond ((null? l)
         0)
        (else
         (add1 ((mk-length mk-length) (cdr l))))))
--------------------------------------------------
```
#### to extract a value and give it a name

for to get back the function that looks like length

##### 可怕的錯誤
現在既然已經知道該如何得到遞歸函數length了
那麼就該考慮如何得到這個能得到遞歸函數length的表達式了
即 想要求一個procedure 稱爲Y
它作用於L會得到遞歸函數length
並且這個L可以是其他類似於L的用來定義單變量遞歸函數的算子
首先應該讓上面的可以得到遞歸函數length的表達式中出現算子L
換一種問法
如何從L得到L*呢？
+ 注意 因爲是被(lambda (mk-length) (mk-length mk-length))作用
  所以下面L*的參數mk-length所接受的值將總是L*本身
+ 可以發現雖然let是語義清晰的語法糖
  但是卻不能完成把L提取出來的任務
  所以還是得回到lambda表達式
只要把L*中的(mk-length mk-length)部分
當作是從L的recursion位置的length代換而來的就行了嗎？
把(mk-length mk-length)的值傳入L的參數就行了嗎？
這就犯了一個可怕的讓機器崩潰的錯誤！！！
+ 因爲(mk-length mk-length)的值必須要等到需要遞歸調用的時候再求才行
  否則就沒完沒了陷入了循環
  因爲在求值(mk-length mk-length)對l的作用的時候
  首先要求值(mk-length mk-length)
  而求值(mk-length mk-length)的時候
  馬上就需要求值同樣的(mk-length mk-length)
  ><遺失的show函數是不是可以探測這種循環？
*千萬不要執行下面的代碼塊中的任何表達式*
>< org-babel如何限制可執行性呢？
```scheme
(lambda (mk-length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 ((mk-length mk-length) (cdr l)))))))
=/={這裏上下兩個表示是不相等的 上面的是對的 下面的是錯的}
(lambda (mk-length)
 (let ((length (mk-length mk-length)))
   (cond ((null? l)
          0)
         (else
          (add1 (length (cdr l)))))))
==
(lambda (mk-length)
  ((lambda (length)
     (lambda (l)
       (cond ((null? l)
              0)
             (else
              (add1 (length (cdr l)))))))
   (mk-length mk-length)))

錯的length：
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   ((lambda (length)
      (lambda (l)
        (cond ((null? l)
               0)
              (else
               (add1 (length (cdr l)))))))
    (mk-length mk-length))))

錯的length：
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   (L (mk-length mk-length))))
(define L
  (lambda (length)
    (lambda (l)
      (cond ((null? l)
             0)
            (else
             (add1 (length (cdr l))))))))

這樣寫出來的錯的Y如下：
(define Y
  (lambda (F)
    ((lambda (f) (f f))
     (lambda (f) (F (f f))))))
```

##### 正確的東西
那麼 如何正確地從L得到L*呢？
只要把(mk-length mk-length)放到lambda表達式中保護起來如下
(lambda (x) ((mk-length mk-length) x))
然後再傳入L的參數length
傳入時lambda表達式會被求值成爲closure
這樣((mk-length mk-length) x)作爲closure的body只有被調用的時候纔會被求值
下面的代碼塊中
第1個等號表示等號下面的東西與L*的效果相同（仍然稱之爲L*）
第2個等號表示等號上面的東西是等號下面的東西的語法糖
之後的東西就是把Y寫出來的簡單過程
+ 雖然我的目的好像是不使用define
  但是爲了清晰性我還是定義出L來
```scheme
;L*
(lambda (mk-length)
  (lambda (l)
    (cond ((null? l)
           0)
          (else
           (add1 ((mk-length mk-length) (cdr l)))))))
;==
(lambda (mk-length)
  (let ((length (lambda (x) ((mk-length mk-length) x))))
    (lambda (l)
      (cond ((null? l)
             0)
            (else
             (add1 (length (cdr l))))))))
;==
(lambda (mk-length)
  ((lambda (length)
     (lambda (l)
       (cond ((null? l)
              0)
             (else
              (add1 (length (cdr l)))))))
   (lambda (x) ((mk-length mk-length) x))))
;----------------------------------------------
;this is length
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   ((lambda (length)
      (lambda (l)
        (cond ((null? l)
               0)
              (else
               (add1 (length (cdr l)))))))
    (lambda (x) ((mk-length mk-length) x)))))
;----------------------------------------------
;this is length
((lambda (mk-length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 ((mk-length mk-length) (cdr l)))))))
 (lambda (mk-length)
   (lambda (l)
     (cond ((null? l)
            0)
           (else
            (add1 ((mk-length mk-length) (cdr l))))))))
;don't believe me?
(((lambda (mk-length)
    (lambda (l)
      (cond ((null? l)
             0)
            (else
             (add1 ((mk-length mk-length) (cdr l)))))))
  (lambda (mk-length)
    (lambda (l)
      (cond ((null? l)
             0)
            (else
             (add1 ((mk-length mk-length) (cdr l))))))))
 '(1 2 3 4 5 6 7 8 9 10))
;----------------------------------------------
(define L
  (lambda (length)
    (lambda (l)
      (cond ((null? l)
             0)
            (else
             (add1 (length (cdr l))))))))
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   (L
    (lambda (x) ((mk-length mk-length) x)))))
;---------------------------------------------
;most clear version
(define Y
  (lambda (F)
    (let ((F* (Y-help F)))
      (F* F*))))
(define Y-help
  (lambda (F)
    (lambda (F*)
      (F (lambda (x) ((F* F*) x))))))
;---------------------------------------------
(define Y
  (lambda (F)
    ((lambda (F*) (F (lambda (x) ((F* F*) x))))
     (lambda (F*) (F (lambda (x) ((F* F*) x)))))))
(define Y
  (lambda (F)
    (let ((F* (lambda (F*) (F (lambda (x) ((F* F*) x))))))
      (F* F*))))
;---------------------------------------------
;most simple version
(define Y
  (lambda (F)
    ((lambda (F*) (F* F*))
     (lambda (F*) (F (lambda (x) ((F* F*) x)))))))
;---------------------------------------------
(define length

  (Y (lambda (length)
       (lambda (l)
         (cond ((null? l)
                0)
               (else
                (add1 (length (cdr l))))))))


  )
(length '(1 2 3 4 5 6 7 8 9 10))


((Y (lambda (length)
      (lambda (l)
        (cond ((null? l)
               0)
              (else
               (add1 (length (cdr l))))))))

 '(1 2 3 4 5 6 7 8 9 10))
```

# 10 what is the value of all this? (interpreter)

## note

徹底弄清某些東西的最好方法就是實現它們
因爲當一個人解釋一個被他實現了的東西 而不說明他是如何實現它的時候
他是在蒙着你的眼睛給你描述一幅畫

這裏“寫出一個scheme解釋器”是指寫出一個value函數
在元解釋器中以如下方式調用value函數：（合法輸入記爲<sexp>）
> (value '<sexp>)
1. 因爲對於不同<sexp>函數value有不同的作用方式
   所以需要分類 再按類型求值
   理想的處理是讓分類方式清晰地表達出這種作用方式上的不同
2. 觀念上 需要嚴格區分解釋前的<sexp>與解釋後的<sexp>
   即作爲輸入的<sexp>與作爲輸出的<sexp>
3. 爲了能夠在元解釋器中遞歸地處理<sexp>
   首先需要分爲<atom>與<list>兩類
   + 因爲我的解釋器中的<sexp>
     對元解釋器來說是<*sexp*>
     遞歸地在元解釋器中處理<*sexp*>要遵從前面的誡律
   並且要區分<list>是否是null
   還有<list>的car是否是<atom>
   同時這些也區分了value的作用方式
   而之後的更細的分類更是按value的作用方式的分類
   + 因此這裏的<atom>指元解釋器中的謂詞``atom?''下的東西
     而我的解釋器中的謂詞``atom?''
     需要用元解釋器中的謂詞``:atom?''來特殊處理
``it is necessary for a scheme implementation to distinguish
between core forms and syntactic extensions
a scheme implementation expands syntactic extensions into core forms
as the first step of compilation or interpretation
allowing the rest of the compiler or interpreter
to focus only on the core forms''
--kent dybvig
#+name: <<分類>>
```scheme
<sexp> ::= <atom> | <list>

  <atom> ::= <const> | <identifer>
    <const> ::= <name-of-primitive-procedure> | <*number*> | <*bool*>

  <list> ::= () | (<head-sexp> <sexp> ...)
    <head-sexp> ::= <atom-head-sexp> | <list-head-sexp>
      <list-head-sexp> ::= <lambda-exp>
       :此時原list被解釋爲non-primitive-procedure的action
      <atom-head-sexp> ::= <name-of-primitive-procedure>
                            :此時原list被解釋爲primitive-procedure的action
                           | quote
                            :此時原list屬於數據類型 <*sexp*>
                           | lambda
                            :此時原list是一個lambda-exp
                           | cond
                            :此時原list是一個控制結構

note:
1. 這個代碼塊的使用只是爲了語法高亮
2. ``::=''符號用來表示歸納定義
3. ``<sexp> ...''的出現代表這裏可以出現零個或一個或多個<sexp>
4. 在分類的末端
   我用符號``:''來對語義作簡單的說明
5. 在分類的末端
   我用符號<*...*>來記那些在我的解釋中代表一類獨立的數據結構的東西
   它們會被我的解釋器中的atom?判斷爲真
   它們目前有：
   <*number*> <*bool*> <*sexp*>
   <*procedure*> == <*primitive*> | <*non-primitive*>
   <*non-primitive*> == <*closure*>
   數據結構指能夠以特定的方式存儲在內存的東西
   當然對於我寫的解釋器來說內存是想象出來的
   <var> 與 <obj>的綁定是在table中實現的
   而table是元解釋器中的一個list
6. 術語``action''與``application''等同
   但是更接近數學
7. 書中原來的解釋器不處理'()
   對'()的處理是我加的
   我想在讀第18章的時候人們可以體會到書中不處理'()的原因
```

### little change

1. use `myapply' to rename `apply'
2. add *null
3. change a way of [[*identifer and notfound][error report]]

### important terms

1. 我寫的解釋器直接被稱作“我寫的解釋器”
   （當然這其實是Friedman寫的解釋器）
   其中的符號表達式記爲<sexp>
2. 我寫的解釋器所嵌入的解釋器被稱作“元解釋器”
   其中的符號表達式記爲sexp

### typesetting

1. 需要被引用的代碼塊會被加上標題

## lexical scope, entry and table of environment

``the scope of a binding is the block
in which the bound identifier is visible
minus any portions of the block
in which the identifier is shadowed''

``a variable that occurs free in a lambda expression
should always be bound
otherwise when the procedure is apply
there will be an error about the unbound variable''

``to support lexical scoping
a procedure carries the lexical context (environment)
along with its code''

``so that
when the procedure is applied somewhere outside the scope
of the bindings for variables that occur free within the procedure
the same bindings
that were in effect when the procedure was created
are in effect again when the procedure is applied''

--kent dybvig

lambda表達式 表達procedure語義 在解釋器中會被轉化爲closure
closure會把當時的environment包入其內
每次closure的action會用<arg>s與<val>s組成的new-entry擴展environment
然後在擴展的environment中apply
apply時 不是這個procedure的參數的<arg>s 與 是這個procedure的參數的<arg>s
都以一致的方式 由內層到外層地 在table的entry中查找它們的<val>s
這種方式就是*identifer

entry是用來記錄一組names與一組values之間的命名關係的數據結構
考慮怎樣一個樹適合用來完成這個任務就明白entry的構造了
即一個names的list和一個同等長度的values的list作成的pair
+ pair這個術語被濫用了
  這裏指只含有兩個元素的list
  其他地方又指形如(A . B)的東西
```scheme
(define new-entry
  (lambda (a b)
    (cons a (cons b '()))))
(define first
  (lambda (l)
    (car l)))
(define second
  (lambda (l)
    (cadr l)))
```

lookup-in-entry是*identifer類型的作用的實現方式
+ 當在entry中找不到name時返回(entry-f name) ``f'' denote function
  考慮下面的environment數據結構就知道
  當在一個entry中找不到name時不把這個name扔掉
  是因爲還要在environment中的下一個entry中找name
+ 先把entry中的names與values拆開 然後交給輔助函數處理
```scheme
(define lookup-in-entry
  (lambda (name entry entry-f)
    (lookup-in-entry-help name
                          (first entry)
                          (second entry)
                          entry-f)))
(define lookup-in-entry-help
  (lambda (name names values entry-f)
    (cond
     ((null? names)
      (entry-f name))
     ((eq? name (car names))
      (car values))
     (else
      (lookup-in-entry-help name (cdr names) (cdr values) entry-f)))))
```

table (of environment) is a list of entries
考慮在environment中查找name的方式就知道
這個數據結構使得一個name所對應的新value可以覆蓋它所對應的舊value
```scheme
(define extend-table cons)
(define lookup-in-table
  (lambda (name table table-f)
    (cond ((null? table)
           (table-f name))
          (else
           (lookup-in-entry name
                            (car table)
                            (lambda (name)
                              (lookup-in-table name (cdr table) table-f)))))))
```

## value and meaning

按作用類型對<sexp>[[分類]]
一共僅有7種作用方式
atom-to-action: *const *identifer
list-to-action: *null *quote *lambda *cond *application

```scheme
(define value
  (lambda (e)
    (meaning e '())))

(define meaning
  (lambda (e table)
    ((expression-to-action e) e table)))

(define expression-to-action
  (lambda (e)
    (cond
     ((atom? e)
      (atom-to-action e))
     (else
      (list-to-action e)))))

(define atom-to-action
  (lambda (e)
    (cond
     ((number? e)
      *const)
     ((eq? e #t)
      *const)
     ((eq? e #f)
      *const)
     ((eq? e 'cons)
      *const)
     ((eq? e 'car)
      *const)
     ((eq? e 'cdr)
      *const)
     ((eq? e 'null?)
      *const)
     ((eq? e 'eq?)
      *const)
     ((eq? e 'atom?)
      *const)
     ((eq? e 'zero?)
      *const)
     ((eq? e 'add1)
      *const)
     ((eq? e 'sub1)
      *const)
     ((eq? e 'number?)
      *const)
     (else
      *identifer))))

(define list-to-action
  (lambda (e)
    (cond ((null? e)
           *null)
          ((atom? (car e))
           (cond ((eq? (car e) 'quote)
                  *quote)
                 ((eq? (car e) 'lambda)
                  *lambda)
                 ((eq? (car e) 'cond)
                  *cond)
                 (else
                  *application)))
          (else
           *application))))
```

## after classification

下面是不同類型的作用的細節
注意 它們與上面執行分類功能的代碼是分離的
而上面的代碼單純的執行分類的任務沒有遞歸
所以遞歸的任務全在作用的細節中完成

### dependence

```scheme
(define atom?
  (lambda (x)
    (and (not (pair? x))
         (not (null? x)))))
(define add1
  (lambda (x)
    (+ 1 x)))
(define sub1
  (lambda (x)
    (- x 1)))
```

### *const

最簡單的是*const
它把數字與邏輯值按原樣輸出
而給<name-of-primitive-procedure>貼上primitive的標籤
以表明是這個解釋器中所配備的基本函數與謂詞
```scheme
(define *const
  (lambda (e table)
    (cond ((number? e)
           e)
          ((eq? e #t)
           #t)
          ((eq? e #f)
           #f)
          (else
           (cons 'primitive (cons e '()))))))
```

### *identifer

1. 前面所敘述的enrty與environment數據結構以及相關函數
   是爲且僅爲*identifer這種類型的作用而準備的
2. 當需要用到lookup-in-table的第四個參數的時候
   就是找不到某個名稱所對應的值的時候
   書中使用(car '()))) 這樣就使用戶得到一個元解釋器中的錯誤信息
   而這裏使用一個字符串
   好處是 這個字符串標記了錯誤之後還可以被儘量地處理
   直到實在錯上加錯了爲止
   這個特性究竟是好是壞就因理解方式而異了
3. 這寫問題只有在實踐中才會被處理 在純理論討論中是不作處理的
   因爲就這裏的認識論而言“錯誤永遠都是非本質的”
```scheme
(define *identifer
  (lambda (e table)
    (lookup-in-table e table notfound)))
(define notfound
  (lambda (name)
    "error: at least one name is unbound"))
```

### *null

```scheme
;最最簡單的*null
(define *null
  (lambda (e table)
    '()))
```

### *quote

*quote用來標識<*sexp*>數據類型
加quote的<sexp>在解釋時會被認爲是<*sexp*>
之所以有<sexp>與<*sexp*>之間的相互轉換
得益於McCarthy對LISP的設計
``scheme programs share a common printed representation
with scheme data structures
as a result
any scheme program has a natural and obvious
internal representation as a Scheme object''--kent dybvig
這個特性使得我在我寫的這個解釋器裏再寫一個解釋器成爲可能
```scheme
(define *quote
  (lambda (e table)
    (text-of e)))
(define text-of second)
```

### *lambda

被*lambda來作用的是<lambda-exp> 它的語義是non-primitive
<lambda-exp>經*lambda作用被轉化爲closure
它前有一個``non-primitive''標籤
以方便之後按作用方式的不同來分別處理primitive與non-primitive
```scheme
<closure> == (non-primitive (<environment> <formals> <body>))
```
non-primitive就是以這種方式在我的解釋器中被視爲一種數據類型的
```scheme
(define *lambda
  (lambda (e table)
    (list 'non-primitive
          (cons table (cdr e)))))
(define table-of
  (lambda (non-primitive)
    (car non-primitive)))
(define formals-of
  (lambda (non-primitive)
    (cadr non-primitive)))
(define body-of
  (lambda (non-primitive)
    (caddr non-primitive)))
```

### *cond

cond的功能是分支控制
它把謂詞的結果轉化成其他的東西
有點像“反謂詞”
+ 如果沒有一個條件是真的
  那麼最後一層遞歸的cond-line爲'() 會出現(car '())的錯誤
  保持cond-lines的結尾是else 就不會出現這種錯誤
+ 這裏遞歸調用了meaning
```scheme
(define *cond
  (lambda (e table)
    (evcon (cond-lines-of e) table)))
(define cond-lines-of cdr)
(define evcon
  (lambda (lines table)
    (cond ((else? (question-of (car lines)))
           (meaning (answer-of (car lines)) table))
          ((meaning (question-of (car lines)) table)
           (meaning (answer-of (car lines)) table))
          (else
           (evcon (cdr lines) table)))))
(define question-of
  (lambda (x)
    (car x)))
(define answer-of
  (lambda (x)
    (cadr x)))
(define else?
  (lambda (x)
    (cond ((atom? x)
           (eq? x 'else))
          (else
           #f))))
```

### *application

只剩最後一個作用方式*application了
它的任務繁重 它是使用遞歸調用的主要部分 另外的使用遞歸調用的部分是*cond
1. 被*application作用的<sexp>的car爲<*procedure*>
   這個<*procedure*>是通過遞歸調用meaning
   作用於出現在car位置的<sexp>而求得的
   羅嗦地說<*procedure*>是
   以我定義的方式 在我寫的解釋器裏 表達函數語義的 元解釋器中的sexp
   即 前面貼着primitive或non-primitive標籤的sexp
2. 被*application作用的<sexp>的cdr被爲<*procedure*>的arg-list
   這個list中的值是經由evlis而遞歸調用meaning
   作用於出現在cdr位置的(<sexp> ...)而求得的
   ``evlis'' denote evaluation list
3. 前面的遞歸調用meaning作好準備工作後
   myapply就上場了
   apply是重要的函數
   + 爲了避免與元解釋器中的apply衝突而重命名爲myapply
   + value和apply作爲解釋器的兩部分 如太極生兩儀
   + >< 寫解釋器的方式不止有這一中 sicp中有另一種很酷的寫法
     它們在性質上有何區別？
   它的作用方式根據<*procedure*>是<*primitive*>還是<*non-primitive*>
   而分類爲apply-primitive與apply-closure
   1) apply-primitive
      把任務轉交給更底層 在這裏就是交給元解釋器
   2) apply-closure
      <closure>中有<environment> <formals> <body>
      首先arg-list與<formals>組成new-entry來extend-table
      以更新<closure>中的<environment>
      然後調用meaning把<closure>中的<body>在新的<environment>中求值
4. 爲什麼要重新定義atom?這個謂詞爲:atom?
   因爲所希望得到的atom?應該把<*procedure*>判斷爲#t
   可是
   以我定義的方式 在我寫的解釋器裏 表達函數語義的 元解釋器中的sexp
   卻是一個普通的list
   所以僅用元解釋器裏的atom?作apply-primitive中的謂詞是不夠的
```scheme
(define evlis
  (lambda (args table)
    (cond ((null? args)
           '())
          (else
           (cons (meaning (car args) table)
                 (evlis (cdr args) table))))))
(define *application
  (lambda (e table)
    (myapply
     (meaning (function-of e) table)
     (evlis (arguments-of e) table))))
(define function-of car)
(define arguments-of cdr)

(define myapply
  (lambda (fun vals)
    (cond ((primitive? fun)
           (apply-primitive (second fun) vals))
          ((non-primitive? fun)
           (apply-closure (second fun) vals)))))
(define primitive?
  (lambda (l)
    (eq? (car l) 'primitive)))
(define non-primitive?
  (lambda (l)
    (eq? (car l) 'non-primitive)))

(define apply-primitive
  (lambda (name vals)
    (cond
     ((eq? name 'cons)
      (cons (first vals) (second vals)))
     ((eq? name 'car)
      (car (first vals)))
     ((eq? name 'cdr)
      (cdr (first vals)))
     ((eq? name 'null?)
      (null? (first vals)))
     ((eq? name 'eq?)
      (eq? (first vals) (second vals)))
     ((eq? name 'atom?)
      (:atom? (first vals)))
     ((eq? name 'zero?)
      (zero? (first vals)))
     ((eq? name 'add1)
      (add1 (first vals)))
     ((eq? name 'sub1)
      (sub1 (first vals)))
     ((eq? name 'number?)
      (number? (first vals))))))
(define :atom?
  (lambda (x)
    (cond ((atom? x)
           #t)
          ((null? x)
           #f)
          ((eq? (car x) 'primitive)
           #t)
          ((eq? (car x) 'non-primitive)
           #t)
          (else
           #f))))

(define apply-closure
  (lambda (closure vals)
    (meaning (body-of closure)
             (extend-table (new-entry (formals-of closure) vals)
                           (table-of closure)))))
```

## >< make good ues of other functions in this interpreter

except the function value
what other functions in interpreter.scm can we use?

## >< add some functions into this interpreter for fun

## test

```scheme :result output :session *scheme* :scheme ikarus
;; 對*identifer的測試可以看作對報錯情況的測試
(value 'not-const)

;; *const
;; 數字與邏輯值
(value 1)
;; 下面兩個用到*application
(value '(add1 1))
(value '(sub1 1))
(value #t)
(value #f)

;; 基本函數primitive
(value 'add1)
(value 'car)
(value 'atom?)

;; *null
(value '())

;; *quote
(value '(quote a-quoted-string))
(value '(quote (a-quoted-string)))
(value '(quote (lambda (x) (add1 (add1 x)))))

;; *lambda 非基本函數non-primitive
(value '(lambda (x) (add1 (add1 x))))

;; *cond
;; 用到*application
(value '(cond ((eq? 1 2) 123) (else 321)))
(value '(cond ((eq? 1 kkk) 123) (else 321)))
(value '(cond ((eq? cons car) 123) (else 321)))

;; *application
(value '(cons 1 '()))
(value '((lambda (x) (cons 'drink (cons x '())))
         'milk))
(value '((lambda (y) (cond
                      ((eq? y 'thirst)
                       ((lambda (x) (cons 'drink (cons x '())))
                        'water))
                      ((eq? y 'not-thirst)
                       ((lambda (x) (cons 'do (cons 'not (cons 'drink (cons x '())))))
                        'water))
                      (else
                       'what-ever)))
         'thirst))
(value '((lambda (y) (cond
                      ((eq? y 'thirst)
                       ((lambda (x) (cons 'drink (cons x '())))
                        'water))
                      ((eq? y 'not-thirst)
                       ((lambda (x) (cons 'do (cons 'not (cons 'drink (cons x '())))))
                        'water))
                      (else
                       'what-ever)))
         'not-thirst))
;; 這裏用到的是有else保護的cond
(value '((lambda (y) (cond
                      ((eq? y 'thirst)
                       ((lambda (x) (cons 'drink (cons x '())))
                        'water))
                      ((eq? y 'not-thirst)
                       ((lambda (x) (cons 'do (cons 'not (cons 'drink (cons x '())))))
                        'water))
                      (else
                       'what-ever)))
         'do-not-tell-you))
;; 而如果不用else就會可能報錯
;; 報出的是元解釋器中的(car '())錯誤
;; (value '((lambda (y) (cond
;;                     ((eq? y 'thirst)
;;                      ((lambda (x) (cons 'drink (cons x '())))
;;                       'water))
;;                     ((eq? y 'not-thirst)
;;                      ((lambda (x) (cons 'do (cons 'not (cons 'drink (cons x '())))))
;;                       'water))))
;;        'do-not-tell-you))

;; 測試結果(in ikarus)
;; "error: at least one name is unbound"
;; > 1
;; > 2
;; > 0
;; > #t
;; > #f
;; > (primitive add1)
;; > (primitive car)
;; > (primitive atom?)
;; > ()
;; > a-quoted-string
;; > (a-quoted-string)
;; > (lambda (x) (add1 (add1 x)))
;; > (non-primitive (() (x) (add1 (add1 x))))
;; > 321
;; > 321
;; > 321
;; > (1)
;; > (drink milk)
;; > (drink water)
;; > (do not drink water)
;; > what-ever
```
