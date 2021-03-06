## 2.2 Hierarchical Data and the Closure Property


#### Exercise 2.17

```
(define (last-pair items) 
   (let ((rest (cdr items))) 
     (if (null? rest) 
         items 
         (last-pair rest)))) 
```

#### Exercise 2.18

```

(define (reverse list)
  (if (null? (cdr list))
      list
      (append (reverse (cdr list))
              (cons (car list) nil))))
```
#### Exercise 2.19

```

(define (cc amount coin-values)
         (cond ((= amount 0) 1)
               ((or (< amount 0) (no-more? coin-values)) 0)
               (else
                (+ (cc amount 
                       (except-first-denomination coin-values))
                   (cc (- amount
                          (first-denomination coin-values))
                       coin-values)))))

(define (no-more? coin-values)
  (null? coin-values))

(define (first-denomination coin-values)
  (car coin-values))

(define (except-first-denomination coin-values)
  (cdr coin-values))


```


#### Exercise 2.20

```
(define (same-parity head . list)
  (define (helper list number)
    (cond ((null? list) nil)
          ((= number (remainder (car list) 2)) 
           (cons (car list) (helper (cdr list) number)))
       (else (helper (cdr list) number))))
  (cons head (helper list (remainder head 2))))


```



#### Exercise 2.22
```
(define (square-list items)
  (if (null? items)
      nil
      (cons ((lambda (x) (* x x)) (car items))
            (square-list (cdr items)))))

(define (square-list items)
  (map (lambda (x) (* x x)) items))
```

#### Exercise 2.23

```

(define (for-each proc items)
  (cond ((not (null? items))
      (proc (car items))
      (for-each proc (cdr items)))))
```

#### Exercise 2.24
The result is 
```
(1 (2 (3 4)))
```

#### Exercise 2.25

1.

```
(define x (list 1 3 (list 5 7) 9))

(car (cdr (car (cdr (cdr x)))))
```

2.

```
(define y (list (list 7)))

(car (car y))
```

3.

```
(define z (list 1 (list 2 (list 3 (list 4 (list 5 (list 6 7)))))))

(car (cdr (car (cdr (car (cdr (car (cdr (car (cdr (car (cdr z))))))))))))
```

#### Exercise 2.26

```
(define x (list 1 2 3))

(define y (list 4 5 6))

(append x y) => (1 2 3 4 5 6)

(cons x y) => ((1 2 3) 4 5 6)

(list x y) => ((1 2 3) (4 5 6))
```

#### Exercise 2.27
```
(define (insert element lst)
  (if (null? lst)
      (cons element nil)
      (cond ((< element (car lst)) (cons element lst))
            (else (cons (car lst) (insert element (cdr lst)))))))


(define (sort lst)
  (if (null? lst)
      nil
      (insert (car lst) (sort (cdr lst)))))

(define (append list-1 list-2)
  (if (null? list-1)
      list-2
      (cons (car list-1) (append (cdr list-1) list-2))))

(define (reverse list)
  (if (null? list)
      '()
      (append (reverse (cdr list)) (cons (car list) '()) )))

(define (deep-reverse lst)
  (cond ((null? lst) '())
        ((pair? (car lst))
         (append (deep-reverse (cdr lst)) (list (deep-reverse (car lst)))))
        (else (append (deep-reverse (cdr lst)) (list (car lst))))))

(append (list 1 2 3 4) (list 5 6 7 8))

=> 	{1 2 3 4 5 6 7 8}

(reverse (list 1 2 3 4))

=> {4 3 2 1}

(define z (list 1 2 (list 3 4) 5 (list 6 7)))

(deep-reverse z)

=> {{7 6} 5 {4 3} 2 1}

```

#### Exercise 2.28
```
(define (fringe lst)
  (cond ((null? lst) '())
        ((pair? (car lst)) (append (fringe (car lst)) (fringe (cdr lst))))
        (else (append (list (car lst)) (fringe (cdr lst))))))

```
#### Exercise 2.29

a.

```
(define (make-mobile left right)
  (list left right))


(define (left-branch mobile)
  (car mobile))


(define (right-branch mobile)
  (cadr mobile))

(define (make-branch length structure)
  (list length structure))

(define (branch-length branch)
  (car branch))

(define (branch-structure branch)
  (cadr branch))

```

b.

```

(define (total-weight mobile)
  (let (
        (left (left-branch mobile))
        (right (right-branch mobile)))
    (let ((left-stru (branch-structure left))
        (right-stru (branch-structure right)))
      (cond ((null? mobile) 0)
          ((and (not (pair? left-stru)) (not (pair? left-stru)))
           (+ left-stru right-stru))
          ((and (pair? left-stru) (not (pair? right-stru)))
           (+ (total-weight left-stru) right-stru))
          ((and (not (pair? left-stru)) (pair? right-stru))
           (+ left-stru (total-weight right-stru)))
          (else
           (+ (total-weight left-stru) (total-weight right-stru)))))))

```

Another implementation:

```
(define (atom? item)
  (and (not (pair? item)) (not (null? item))))

(define (is-weight? structure)
  (atom? structure))

(define (weight-of-branch branch)
  (let ((structure (branch-structure branch)))
    (if (is-weight? structure)
        structure
        (total-weight structure))))

(define (total-weight mobile)
  (+ (weight-of-branch (left-branch mobile))
     (weight-of-branch (right-branch mobile))))

```


c.

```
(define (torque-of-branch branch)
  (* (branch-length branch)
     (weight-of-branch branch)))

(define (balanced-branch? branch)
  (let ((structure (branch-structure branch)))
    (or (is-weight? structure)
        (balanced-mobile? structure))))
        
(define (balanced-mobile? mobile)
  (let ((left (left-branch mobile))
        (right (right-branch mobile)))
    (and
     (= (torque-of-branch left)
        (torque-of-branch right))
     (balanced-branch? left)
     (balanced-branch? right))))

```

d.

First, we need to know the difference between ```cons``` and  ```list```

```
(define x (cons 1 2))
(car x) => 1
(cdr x) => 2

(define y (list 1 2))
(car y) => 1
(cdr y) => {2}
```

When we apply ```cdr``` on a pair, we get the second element, when we apply ```cdr```on a list, however, we get a list, which contains the second element.

So
In our selectors:

```
(define (right-branch mobile)
  (cadr mobile))

(define (branch-structure branch)
  (cadr branch))

```

We need to change ```cadr``` to ```cdr```

#### Exercise 2.30

```
(define (square num)
  (* num num))

(define (square-tree tree)
  (cond ((null? tree) '())
        ((pair? (car tree))
         (cons (square-tree (car tree)) (square-tree (cdr tree))))
        (else (cons (square (car tree)) (square-tree (cdr tree))))))

```

Use Map function:

```
(define (square-tree-1 tree)
  (map (lambda (sub-tree)
         (if (pair? sub-tree)
             (square-tree-1 sub-tree)
             (square sub-tree)))
       tree))

```

#### Exercise 2.31

```
(define (tree-map proc tree)
  (map (lambda (sub-tree)
         (if (pair? sub-tree)
             (tree-map proc sub-tree)
             (proc sub-tree)))
  tree))

```

#### Exercise 2.32

```
(define (subsets s)
  (if (null? s)
      (list '())
      (let ((rest (subsets (cdr s))))
        (append 
         rest 
         (map (lambda (lst)
                (cons (car s) lst))
              rest)))))

```

#### Exercise 2.33

```
(define (map procedure sequence)
  (accumulate (lambda (x y) (cons (procedure x) y)) '() sequence))

(define (append sequence-1 sequence-2)
  (accumulate cons sequence-2 sequence-1))

(define (length sequence)
  (accumulate (lambda (x y) (+ 1 y)) 0 sequence))

```

#### Exercise 2.34

```
(define (horner-eval x coefficient-sequence)
  (accumulate (lambda (this-coeff higher-terms)
                (+ this-coeff (* x higher-terms)))
              0
              coefficient-sequence))
```

#### Exercise 2.35

```
(define (count-leaves t)
  (accumulate + 0 (map (lambda (sub)
                         (if (atom? sub)
                             1
                             (count-leaves sub))) t)))

```

Here is the python version:

```
def isAtom(item):
    return not (isinstance(item, list) or isinstance(item, tuple) or (item is None))

def accumulate(op, init, seq):
    if not seq:
        return init
    else:
        return op(seq[0], accumulate(op, init, seq[1:]))

def count_leave(tree):
    return accumulate(add, 0, map(lambda sub : 1 if isAtom(sub) else count_leave(sub), tree))

```

#### Exercise 2.36

```
(define (accumulate-n op init seqs)
  (if (null? (car seqs))
      '()
      (cons (accumulate op init (map (lambda (sub) (car sub)) seqs))
            (accumulate-n op init (map (lambda (sub) (cdr sub)) seqs)))))
```
#### Exercise 2.37

---

```
(define (matrix-*-vector matrix vector)
  (map (lambda (row) (dot-product row vector)) matrix))

```

I actually think this should be the correct version of matrix-*-vector

```
(define (matrix-*-vector m v)
  (map (lambda (sub-m)
         (map (lambda (sub-v) (* (car sub-m) sub-v)) v)) m))
```

---
AMAZING QUESTION. Transposing a matrix can be easy.

```
(define (transpose matrix)
  (accumulate-n cons '() matrix))

```
---

```
(define (matrix-*-matrix matrix-1 matrix-2)
  (let ((cols (transpose matrix-2)))
    (map (lambda (row) (matrix-*-vector cols row)) matrix-1)))
```

#### Exercise 2.38

``` op ``` should be a communitive and associative operation


#### Exercise 2.39


```

(define (reverse seq)
  (fold-right (lambda (head rest) (append rest (list head))) '() seq))


```


```

(define (reverse seq)
  (fold-left (lambda (front-part tail) (append (list tail) front-part)) '() seq))

```
#### Exercise 2.40

```
(define (unique-pair number)
  (flatmap 
   (lambda (i) (map (lambda (j) (list i j)) (enumerate 1 (- i 1)))) 
   (enumerate 1 number)))

(define (filter predicate? seq)
  (cond ((null? seq) '())
        ((predicate? (car seq))
         (cons (car seq) (filter predicate? (cdr seq))))
        (else (filter predicate? (cdr seq)))))


(define (prime? number)
  (define (helper dividend divisor)
    (cond ((= dividend divisor) #t)
          ((= 0 (remainder dividend divisor)) #f)
          (else (helper dividend (+ 1 divisor)))))
  (helper number 2))

(define (prime-sum? pair)
  (prime? (+ (car pair) (cadr pair))))

(define (make-pair-sum pair)
  (list (car pair) (cadr pair) (+ (car pair) (cadr pair))))

(define (prime-sum-pairs number)
  (map make-pair-sum (filter prime-sum? (unique-pair number))))

```

#### Exercise 2.41

```
(define (unique-triple number)
  (flatmap 
   (lambda (i) (flatmap 
                (lambda (j) (map (lambda (k) (list i j k)) (enumerate 1 (- j 1)))) 
                (enumerate 1 (- i 1)))) 
   (enumerate 1 number)))


(define (sum-equal-to-s triple s)
  (= s (+ (car triple) (cadr triple) (caddr triple))))


(define (triples-sum-s num s)
  (filter (lambda (triple) (= s (+ (car triple) (cadr triple) (caddr triple)))) (unique-triple num)))

```

#### Exercise 2.42

```

(define (accumulate proc init seq)
  (if (null? seq)
      init
      (proc (car seq) (accumulate proc init (cdr seq)))))

(define (flatmap proc seq)
  (accumulate append nil (map proc seq)))

(define empty-board nil)

(define (reverse lst)
  (if (null? lst)
      nil
      (append (reverse (cdr lst)) (list (car lst)))))

(define (horizontally-safe? element seq)
  (if (null? seq)
      #t
      (and (not (= (car seq) element)) (horizontally-safe? element (cdr seq)))))

(define (diagonally-safe? element seq)
  (define (helper element k seq)
    (if (null? seq)
        #t
        (and (not (= (car seq) (+ element k)))
             (not (= (car seq) (- element k)))
             (helper element (+ k 1) (cdr seq)))))
  (helper element 1 seq))
                  
(define (safe? k-th-col positions) 
  (let ((reversed (reverse positions))
        (last (car (reverse positions))))
    (and (horizontally-safe? last (cdr reversed))
         (diagonally-safe? last (cdr reversed)))))
    
(define (filter predicate seq)
  (cond ((null? seq) nil)
        ((predicate (car seq))
         (cons (car seq) (filter predicate (cdr seq))))
        (else (filter predicate (cdr seq)))))

(define (adjoin-position new-row k rest-of-queens)
  (append rest-of-queens (list new-row)))

(define (enumerate-interval start end)
  (if (> start end)
      nil
      (cons start (enumerate-interval (+ 1 start) end))))

(define (queens board-size)
  (define (queen-cols k)
    (if (= k 0)
        (list empty-board)
        (filter
         (lambda (positions) (safe? k positions))
         (flatmap
          (lambda (rest-of-queens)
            (map (lambda (new-row)
                   (adjoin-position new-row k rest-of-queens))
                 (enumerate-interval 1 board-size)))
          (queen-cols (- k 1))))))
  (queen-cols board-size))

```
