##Section 3.1

#### Exercise 3.1
```
(define (make-accumulator init)
  (lambda (number)
    (set! init (+ init number))
    init))
```

#### Exercise 3.2

```
(define (make-monitored function)
  (let ((start-count 0))
    (lambda (number)
      (if (equal? number 'how-many-calls?)
          start-count
          (begin 
            (set! start-count (+ 1 start-count))
            (function number))))))
```

#### Exercise 3.3

```

(define (make-account balance init-password)
  (let ((user-password init-password))    
    (define (withdraw amount)
      (if (>= balance amount)
          (begin (set! balance (- balance amount))
                 balance)
              "Insufficient funds"))
    
    (define (deposit amount)
      (set! balance (+ balance amount))
      balance)
    
    (define (dispatch input-password action)
      (if (equal? input-password user-password)
          (cond ((eq? action 'withdraw) withdraw)
                ((eq? action 'deposit) deposit)
                (else (error "Unknown request -- MAKE-ACCOUNT" action)))
          (error "wrong password")))
    
    dispatch))
```

#### Exercise 3.4

```
(define (make-account balance init-password)
  (let ((user-password init-password)
        (wrong-psword-count 0))  
    (define (withdraw amount)
      (if (>= balance amount)
          (begin (set! balance (- balance amount))
                 balance)
              "Insufficient funds"))
    
    (define (deposit amount)
      (set! balance (+ balance amount))
      balance)
    
    (define call-the-cop
      (lambda (number)
        (display "we are calling the cops now")))
    
    (define wrong-password
      (lambda (number)
        (display "wrong password")))
    
    (define (dispatch input-password action)
      (cond ((equal? input-password user-password)
            (cond ((eq? action 'withdraw) withdraw)
                ((eq? action 'deposit) deposit)
                (else (error "Unknown request -- MAKE-ACCOUNT" action))))
            ((= 7 wrong-psword-count) call-the-cop)
            (else
               (begin (set! wrong-psword-count (+ 1 wrong-psword-count))
                      wrong-password))))
    
    dispatch))
```


#### Exercise 3.5

```
(define (random-in-range low high)
  (let ((range (- high low)))
    (+ low (random range))))

(define (area-rec x1 y1 x2 y2)
  (* (abs (- x1 x2)) (abs (- y1 y2))))

(define (estimate-integral predicate x1 y1 x2 y2 trials)
  (* (area-rec x1 y1 x2 y2) (monte-carlo trials
                                         (lambda ()
                                           (predicate (random-in-range x1 x2) (random-in-range y1 y2))))))

```


#### Exercise 3.6

```
(define rand
  (let ((init 45))
    (lambda (type)
      (cond ((equal? type 'generate)
             (random init))
            ((equal? type 'reset)
             (lambda (new-init)
               (set! init new-init)))))))

```
 
 
#### Exercise 3.7
 
```
(define (make-account balance init-password)
  (let ((user-password-list (list init-password))
        (wrong-psword-count 0))
    
    (define (withdraw amount)
      (if (>= balance amount)
          (begin (set! balance (- balance amount))
                 balance)
              "Insufficient funds"))

    (define (deposit amount)
      (set! balance (+ balance amount))
      balance)
    
    (define (check-password password)
      (contains? password user-password-list))
    
    (define (contains? element lst)
      (cond ((null? lst) false)
            ((equal? element (car lst)) true)
            (else (contains? element (cdr lst)))))

    (define call-the-cop
      (lambda (number)
        (display "we are calling the cops now")))

    (define wrong-password
      (lambda (number)
        (display "wrong password")))
    
    (define (add-new-password new-password)
      (set! user-password-list (cons new-password user-password-list)))

    (define (dispatch input-password action)
      (cond ((check-password input-password)
            (cond ((eq? action 'withdraw) withdraw)
                  ((eq? action 'deposit) deposit)
                  ((eq? action 'check-password) check-password)
                  ((eq? action 'add-new-password) add-new-password)
                  (else (error "Unknown request -- MAKE-ACCOUNT" action))))
            ((= 7 wrong-psword-count) call-the-cop)
            (else
               (begin (set! wrong-psword-count (+ 1 wrong-psword-count))
                      wrong-password))))

    dispatch))


(define (make-joint account password new-password)
  (if (account password 'check-password)
      (begin ((account password 'add-new-password) new-password)
             account)
      (display "wrong password")))
      
      

(define acc (make-account 100 'pass))

(define new-acc (make-joint acc 'pass 'new))

((acc 'pass 'withdraw) 40)

((new-acc 'new 'withdraw) 20)


```

#### Exercise 3.9

![3.9](https://github.com/thejackz/sicp/blob/master/3-1/sicp-3-9.png)


#### Exercise 3.10

After expand the ```let``` expression, we get the following function:

```
(define (make-withdraw init-amount)
  (lambda (balance)
    (lambda (amount)
      (if (>= balance amount)
          (begin (set! balance (- balance amount))
                 balance)
          "Insufficient amount")) init-amount))

```

When we call ```(define w1 (make-withdraw 100))```, it creates an environment E1, whose enclosing environment is the global environment, and E1 bounds init-amount to 100, and E1 also creates a unnamed procedure object, the lambda function whose parameter is balance, and the body is ```(lambda (amount) <body>)```. The unnamed procedure object will be called right after E1 gets created. Its formal parameter is ```balance```, and its environment E2, who bounds balance to init-amount.

![3.9](https://github.com/thejackz/sicp/blob/master/3-1/sicp-3-10.png)

#### Exercise 3.12

The first ```append``` will create a new list which is referred by z, so ```cdr x``` will return ```(b)```. The second ```append!```, however, attach ```y``` to the end of ```x```, so ```x``` and ```w``` are referring to the same list, which means ```cdr x``` returns ```(b c d)``` 


#### Exercise 3.13

The last pointer of the list will point to the beginning of the list, so we have a list whose end is pointing to its beginning.

If we try ```last-point``` on such list, we will never get an answer, since such list does not have an end.

#### Exercise 3.14

This mystery function is used to reverse a list.

#### Exercise 3.16
```
(define a (cons 1 2))
(define b (cons 1 2))
(define c (cons a b))

(bad-count a b)
=> 3
```

```
(define a (cons 1 2))
(define b (cons 1 2))
(define c (cons a b))
(set-car! b a)

(bad-count c)
=> 4
```


```
(define a (cons 1 2))
(define b (cons a a))
(define c (cons b b))

(bad-count c)
=> 7

```

#### Exercise 3.17
```
(define (count-pairs x)
  (let ((table '()))
    (define (traverse x)
      (cond ((null? x) 0)
            ((not (pair? x)) 0)
            ((memq x table) 0)
            (else
             (begin (set! table (cons x table))
                    (+ (traverse (car x))
                       (traverse (cdr x))
                       1)))))
    (traverse x)))
```

#### Exercise 3.18
```
(define (cycle? lst)
  (let ((table '()))
    (define (traverse lst)
      (cond ((or (null? lst) (not (pair? lst))) false)
            ((memq (car lst) table) true)
            (else (begin
                    (set! table (cons (car lst) table))
                    (traverse (cdr lst))))))
    (traverse lst)))
```

#### Exercise 3.19
```
(define (cycle? lst) 
  (cond ((or (null? lst) (null? (cdr lst))) false)
        (else
         (let ((slow lst)
               (fast (cddr lst)))
           (define (loop)
             (cond ((null? fast) false)
                   ((eq? fast slow) true)
                   (else (begin (set! slow (cdr slow))
                                (set! fast (cddr fast))
                                (loop)))))
           (loop)))))
```


#### Exercise 3.21

```
(define singleton?
  (lambda (queue)
    (null? (cdr (front-ptr queue)))))

(define delete-queue
  (lambda (queue)
    (cond ((empty-queue? queue)
           (error "Empty queue"))
          ((singleton? queue)
           (begin (set-front-ptr! queue nil)
                  (set-rear-ptr! queue nil)
                  queue))
          (else
           (begin
             (set-front-ptr! queue (cdr (front-ptr queue)))
             queue)))))
             
(define print-queue
  (lambda (queue)
    (display (front-ptr queue))))

```

The reason why ben was getting such result, is that the delete-queue function did not update the rear-ptr pointer. When there is only one element left in the queue, which is pointed by both front-ptr and rear-ptr, we still need to make rear-ptr point to nil in order to get the correct result.


#### Exercise 3.22

```
(define (make-queue)
  (let ((front-ptr nil)
        (rear-ptr nil))

    (define (empty?)
        (null? front-ptr))

    (define (singleton?)
      (null? (cdr front-ptr)))

    (define set-front-ptr!
      (lambda (pair)
        (set! front-ptr pair)))

    (define set-rear-ptr!
      (lambda (pair)
        (set! rear-ptr pair)))

    (define (front-queue)
      (if (empty?)
          (error "empty queue")
          (car front-ptr)))

    (define enqueue
      (lambda (item)
        (let ((new-pair (cons item nil)))
          (cond ((empty?)
                 (begin (set! front-ptr new-pair)
                        (set! rear-ptr new-pair)))
                (else
                 (begin
                   (set-cdr! rear-ptr new-pair)
                   (set-rear-ptr! new-pair)))))))
      
      (define print-queue
        (lambda ()
          (begin (display (list front-ptr rear-ptr))
                 (display "\n"))))
        
    (define dequeue
      (lambda ()
        (cond ((empty?) (error "empty queue"))
              ((singleton?)
               (begin (set-front-ptr! nil)
                      (set-rear-ptr! nil)))
              (else
               (set-front-ptr! (cdr front-ptr))))))
      
    (define dispatch
      (lambda (action)
        (cond ((eq? action 'enqueue) enqueue)
              ((eq? action 'print-queue) (print-queue))
              ((eq? action 'front-queue) (front-queue))
              ((eq? action 'dequeue) (dequeue))
              (else "error action" action))))

    dispatch))


(define queue (make-queue))

((queue 'enqueue) 3)

((queue 'enqueue) 4)
((queue 'enqueue) 5)
((queue 'enqueue) 6)

(queue 'print-queue)

(queue 'front-queue)
(queue 'dequeue)
(queue 'dequeue)
(queue 'dequeue)

(queue 'print-queue)


```



#### Exercise 3.23

Interesting question

```
(define (make-deque)
  (cons nil nil))

(define front-ptr
  (lambda (deque)
    (car deque)))

(define rear-ptr
  (lambda (deque)
    (cdr deque)))

(define (value dlink)
  (caar dlink))

(define (previous dlink)
  (cdar dlink))

(define (next dlink)
  (cdr dlink))

(define (set-value! dlink value)
  (set-car! (car dlink) value))

(define (set-previous! dlink pre-dlink)
  (set-cdr! (car dlink) pre-dlink))

(define (set-next! dlink next-dlink)
  (set-cdr! dlink next-dlink))

(define empty-deque?
  (lambda (deque)
    (null? (front-ptr deque))))

(define set-front-ptr!
  (lambda (deque new-dlink)
    (set-car! deque new-dlink)))

(define set-rear-ptr!
  (lambda (deque new-dlink)
    (set-cdr! deque new-dlink)))

(define (front-deque deque)
  (if (empty-deque? deque)
      (error "empty deque")
      (value (front-ptr deque))))

(define (rear-deque deque)
  (if (empty-deque? deque)
      (error "empty deque")
      (value (rear-ptr deque))))

(define front-enqueue
  (lambda (deque item)
    (let ((new-dlink (cons (cons item nil) nil))
          (rear-dlink (rear-ptr deque)))
      (if (null? rear-dlink)
          (begin (set-front-ptr! deque new-dlink)
                 (set-rear-ptr! deque new-dlink))
          (begin (set-previous! new-dlink rear-dlink)
                 (set-next! rear-dlink new-dlink)
                 (set-rear-ptr! deque new-dlink))))))

(define rear-enqueue
  (lambda (deque item)
    (let ((new-dlink (cons (cons item nil) nil))
          (front-dlink (front-ptr deque)))
      (if (null? front-dlink)
          (begin (set-front-ptr! deque new-dlink)
                 (set-rear-ptr! deque new-dlink))
          (begin (set-previous! front-dlink new-dlink)
                 (set-next! new-dlink front-dlink)
                 (set-front-ptr! deque new-dlink))))))

(define front-delete
  (lambda (deque)
    (let ((front-dlink (front-ptr deque)))
      (let ((new-front (next front-dlink)))
        (if (null? front-dlink)
            (error "empty queue")
            (begin
              (set-next! front-dlink nil)
              (set-previous! new-front nil)
              (set-front-ptr! deque new-front)))))))

(define rear-delete
  (lambda (deque)
    (let ((rear-dlink (rear-ptr deque)))
      (let ((new-rear (previous rear-dlink)))
        (if (null? rear-dlink)
            (error "empty queue")
            (begin
              (set-next! new-rear nil)
              (set-previous! rear-dlink nil)
              (set-rear-ptr! deque new-rear)))))))

(define print-deque
  (lambda (deque)
    (let ((front-dlink (front-ptr deque)))
      (define (print-dlink dlink)
        (if (null? dlink)
            (display "\n")
            (begin
              (display (value dlink))
              (display " ")
              (print-dlink (next dlink)))))
      (print-dlink front-dlink))))

(define deque (make-deque))

(print-deque deque)

(front-enqueue deque 3)

(front-enqueue deque 4)

(front-enqueue deque 5)

(print-deque deque)

(rear-enqueue deque 9)

(print-deque deque)

(front-deque deque)

(front-delete deque)

(print-deque deque)

(rear-delete deque)

(print-deque deque)

```


#### Exercise 3.24

```
(define (make-table same-key?)
  (let ((local-table (list 'table)))

    (define (look-up key-1 key-2 table)
      (let ((subtable (assoc key-1 table)))
        (if subtable
            (let ((record (assoc key-2 subtable)))
              (if record
                  (cdr record)
                  false))
            false)))

    (define (insert! key-1 key-2 value table)
      (let ((subtable (assoc key-1 table)))
        (if subtable
            (let ((record (assoc key-2 subtable)))
              (if record
                  (set-cdr! record value)
                  (set-cdr! subtable
                            (cons (cons key-2 value) (cdr subtable)))))
            (set-cdr! table
                      (cons (list key-1
                                  (cons key-2 value))
                            (cdr table))))))

    (define (assoc key table)
      (cond ((null? table) false)
            ((same-key? key (caar table)) (car table))
            (else (assoc key (cdr table)))))

    (define (dispatch action)
      (cond ((eq? action 'look-up) look-up)
            ((eq? action 'insert!) insert!)
            (else (error "wrong action" action))))

    dispatch))

```

#### Exercise 3.25

##### Hardest question I have ever had since I started reading this book.


```
(define (test-key? key-1 key-2)
  (equal? key-1 key-2))

(define (make-table same-key?)
  (let ((local-table (list 'table)))

    (define (fold-left op initial sequence)
      (define (iter result rest)
        (if (null? rest)
            result
            (iter (op result (car rest))
                  (cdr rest))))
      (iter initial sequence))

    ; table should be without the prefix key
    (define (assoc table key)
      (cond ((null? table) false)
            ((equal? (caar table) key) (car table))
            (else (assoc (cdr table) key))))

    (define (lookup keys)
      (define (lookup-record records key)
        (if records
            (let ((record (assoc records key)))
              (if record
                  (cdr record)
                  false))
            false))
      (fold-left lookup-record (cdr local-table) keys))
    
    (define (insert! keys value)
      ; table here contains the prefix key)
      (define (descend table key)
        (let ((record (assoc (cdr table) key)))
          (if record
              record
              (let ((new (cons (list key) (cdr table))))
                (set-cdr! table new)
                (car new)))))

      (set-cdr! (fold-left descend table keys)
                value))
        
    
    (define (print)
      (display local-table)
      (display "\n"))
    

    (define (dispatch action)
      (cond ((equal? action 'lookup) lookup)
            ((equal? action 'table) local-table)
            ((equal? action 'insert!) insert!)
            ((equal? action 'print) (print))))

    dispatch))
                

(define table
  (list 'table
        (cons 'math (list (cons '+ 43)
                          (cons '- 45)
                          (cons '* 42)))
        (cons 'letters (list (cons 'a 97)
                             (cons 'b 98)))
        (cons 'name (list (cons 'jack
                                (list (cons 'zheng 3)))))))


(define test (make-table test-key?))
(test 'print)

(set-cdr! (test 'table) (cdr table))

(test 'print)

((test 'lookup) '(math /))

((test 'insert!) '(math /) 99)

(test 'print)
```


#### Exercise 3.26
```
#lang planet neil/sicp

(define (make-empty-node)
  (list nil nil nil nil))

(define (make-node key value)
  (list key value nil nil))

(define (left-branch node)
  (caddr node))

(define (right-branch node)
  (cadddr node))

(define (get-key node)
  (car node))

(define (get-value node)
  (cadr node))

(define (is-null? node)
  (and (null? (get-key node))
       (null? (get-value node))
       (null? (left-branch node))
       (null? (right-branch node))))

(define (set-left! node left-node)
  (set-car! (cddr node) left-node))

(define (set-right! node right-node)
  (set-car! (cdddr node) right-node))

(define (set-value! node new-value)
  (set-car! (cdr node) new-value))

(define (set-key! node new-key)
  (set-car! node new-key))

(define (add-node root key value)
  (cond ((is-null? root) (begin (set-key! root key) (set-value! root value)))
        ((< key (get-key root))
         (if (null? (left-branch root))
             (set-left! root (make-node key value))
             (add-node (left-branch root) key value)))
        ((> key (get-key root))
         (if (null? (right-branch root))
             (set-right! root (make-node key value))
             (add-node (right-branch root) key value)))
        (else (set-value! root value))))

(define (is-node? node)
  (= (length node) 4))


(define (find root key)
  (cond ((or (null? root) (is-null? root)) false)
        ((< key (get-key root)) (find (left-branch root) key))
        ((> key (get-key root)) (find (right-branch root) key))
        (else (get-value root))))

(define (get-all-but-last lst)
  (cond ((or (null? lst) (null? (cdr lst))) nil)
        (else (cons (car lst) (get-all-but-last (cdr lst))))))

(define (get-last lst)
  (cond ((null? lst) nil)
        ((null? (cdr lst)) (car lst))
        (else (get-last (cdr lst)))))



(define (make-table)
  (let ((local-table (make-empty-node)))

  (define (fold-left op init sequence)
    (define (iter result sequence)
      (if (null? sequence)
          result
          (iter (op result (car sequence))
                     (cdr sequence))))
    (iter init sequence))



  (define (fold-table op nodes keys)
    (fold-left op nodes keys))

  (define (lookup keys)
    (define (lookup-nodes nodes keys)
      (cond ((null? keys) nodes)
            ((is-null? nodes) false)
            (else
             (let ((node (find nodes (car keys))))
               (cond ((number? node) node)
                     ((is-node? node) (lookup-nodes  node (cdr keys)))
                     (else (error "unknow node")))))))
                
    (lookup-nodes local-table keys))

  (define (insert! keys value)
    (define (descend nodes key)
      (let ((node (find nodes key)))
        (if node
            node            
            (begin (add-node nodes key (make-empty-node))
                   (get-value nodes)))))

    (let ((all-but-last-kays (get-all-but-last keys))
          (last-key (get-last keys)))
      (let ((innermost-node (fold-table descend local-table all-but-last-kays)))
        (add-node innermost-node last-key value))))




    (define (print)
      (begin (display local-table)
             (display "\n")))


    (define (dispatch action)
      (cond ((eq? action 'insert!) insert!)
            ((eq? action 'lookup) lookup)
            ((eq? action 'print) (print))))

    dispatch))




(define test (make-table))

((test 'insert!) '(3 4 5) 6)

((test 'lookup) '(3 4))

(test 'print)

```

#### Exercise 3.28

```
(define (or-gate input-1 input-2 output)
  (define (or-action-procedure)
    (let (new-value (logical-or (get-signal input-1)
                                (get-signal input-2)))
      (after-delay or-gate-delay
                   (lambda ()
                     (set-signal! output new-value)))))
  (add-action! input-1 or-action-procedure)
  (add-action! input-2 or-action-procedure)
  'ok)
```

#### Exercise 3.29

```
(define (or-gate input-1 input-2 output)
  (let ((invert-1 (make-wire))
        (invert-2 (make-wire))
        (and-1-2 (make-wire)))
    (inverter input-1 invert-1)
    (inverter input-2 invert-2)
    (and-gate invert-1 invert-2 and-1-2)
    (inverter and-1-2 output)
    'ok))
```

The delay of OR gate is the delay of and-gate plus twice the delay of inverter

#### Exercise 3.30

```
(define (ripple-carry-adder a1 a2 c s)
  (let ((carry (make-wire)))
    (if (null? a1)
        s
        (begin (full-adder (car a1) (car a2) c (car s) carry)
               (ripple-carry-adder (cdr a1) (cdr a2) carry (cdr s))
               'ok))))

```

#### Exercise 3.33

```
(define (averager input-1 input-2 average)
  (define (average-two a1 a2)
    (/ (+ a1 a2) 2))
  
  (define (process-new-value)
    (cond ((and (has-value? input-1) (has-value? input-2))
           (set-value! average (average-two input-1 input-2) me))
          ((and (has-value? input-2) (has-value? average))
           (set-value! input-1 (- (* average 2) input-2)))
          ((and (has-value? input-1) (has-value? average))
           (set-value! input-2 (- (* average 2) input-1)))))

  (define (process-forget-value)
    (forget-value! average me)
    (forget-value! input-1 me)
    (forget-value! input-2 me)
    (process-new-value))
  (define (me request)
    (cond ((eq? request 'I-have-a-value) (process-new-value))
          ((eq? request 'I-lost-my-value) (process-forget-value))
          (else (error "Unknow request --MULTIPLIER" request))))
  (connect input-1 me)
  (connect input-2 me)
  (connect average me)
  me)
```

#### Exercise 3.35

```
(define (squarer a b)
  (define (process-new-value)
    (if (has-value? b)
        (if (< (get-value b) 0)
            (error "Square less than 0 -- squraer" (get-value b))
            (set-value! a (sqrt (get-value b)) me))
        (if (has-value? a)
            (set-value! b (square (get-value a)) me)
            'igored)))
  (define (process-forget-value)
    (forget-value! a me)
    (forget-value! b me)
    (process-new-value))
  (define (me request)
    (cond ((eq? request 'I-have-a-value) (process-new-value))
          ((eq? request 'I-lost-my-value) (process-forget-value))
          (else (error "Unknow request -- squarer" ))))
  (connect a me)
  (connect b me)
  me)
```

#### Exercise 3.38

a. There are four possibilities: 35, 40, 45, 50


#### Exercise 3.39

x could still be 100. Since the assignment of ```(* x x)``` to ```x``` is not protected by serializer. The following sinario could happend:

```(lambda () (* x x))``` access x twice (not interleave with p2, since it is protected) and get value 100, before it assign it to x, p2 is executed and set x to be 11, then the 100 is assigned to x and override the original 11.

#### Exercise 3.40

1. First execute P1, then P2, we get 1,000,000
2. First execute P2, then P1, we get 1,000,000
3. First P1 access x and set the first x to be 10, then P2 access x and compute ```(* x x x)``` and assign x to be 1000, then P1 access x and set the second x to be 1000, then compute x to be (* 10 1000) which is 10,000
4. P2 access x twice and set the first two x to be 10, then P1 access x twice and compute x to be 100, then P2 access x and finally set x to be ```(* 10 10 100)``` which is 10,000
5. P2 access x once and set the first x to be 10, then P1 access x and compute x to be 100, then P2 access x and finally compute x to be ```(* 10 100 100)``` which is 100,000
6. P1 access x twice, then P2 access x and set x to 1,000, then P1 set x to 100
7. P2 access x three times, then P1 access x and set x to 100, then P2 set x to 1,000

In conclusion, there are five possibilities:

```
1,000,000
100,000 
10,000
1,000
100
```

If we use serialized procedures, only the first two senarios remains, so the result is 1,000,000

#### Exercise 3.41

Since deposit and withdraw are protected by serializer, accessing balance is not going to be interleaved with other operations. Since accessing variable itself is an atomic action. 

#### Exercise 3.42

There is no difference, and it is safe to do so. It is not the creation of serializer that preotects the procedure.