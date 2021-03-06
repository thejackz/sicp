# SICP 

## 2.1 Introduction to Data Abstraction

In this section, the book introduces a power strategy called *wishful thinking* by implementing serveral arithmetic operations for rational number.

It assumes that we have already implemented the constructor and the selector for rational number, and use them to do the following operations: add, subtract, multiply, divide and test equality.

Assuming x is a rational number,

`numer x ` returns the numerator of x

`denom x` returns the denominator of x

`make-rat a b` returns the rational number whose numerator is the integer x and who denominator is the integer b


***

Now the book introduces the *pair* data type, which has three primitive operators: cons, car and cdr.

By using pair, we can represent rational number like this:

```
(define (make-rat n d) (cons n d))

(define (numer x) (car x))

(define (denum x) (cdr x))

```

Then we can do something like this

```
(define one-half (make-rat 1 2))

(add-rat one-half one-half)

```

There is a problem about this implementation:

if we do `(add-rat one-third one-third)`, instead of 2/3, the program returns 6/9, which is not really what we want.

To resolve this, we need to modify our constructor

```
(define (make-rat n d)
(let ((g (gcd n d)))
	(cons (/ n g) (/d g))))
```

We need to get the greatest common divisor of two integers, and use it to reduce the numerator and the denominator to lowest term before constructing the pair.

#### Exercise 2.1

```
((define (make-rat n d)
  
  (define (gcd a b)
  (if (= b 0)
      a
      (gcd b (remainder a b))))

  (define (make-rat-reduce n d)
    (let ((g (gcd n d)))
      (cons (/ n g) (/ d g))))
  
  (cond ((and (< n 0) (< d 0)) (make-rat-reduce (- n) (- d)))
        ((and (> n 0) (< d 0)) (make-rat-reduce (- n) (- d)))
        (else (make-rat-reduce n d))))

```
Another concise solution would be:

```
(define (make-rat n d)
  (define (gcd a b)
    (if (= b 0)
      a
      (gcd b (remainder a b))))
  
  (define (make-rat-reduce n d)
    (let ((g (gcd n d)))
      (cons (/ n g) (/ d g))))
  
  (if (< d 0)
      (make-rat-reduce (- n) (- d))
      (make-rat-reduce n d)))

```

This is based on the observation that when d is negative, we can toggle the sign of n and the sign of d, or we leave them untouched.



## 2.1.2 Abstraction Barriers

Data abstraction: 

**the underlying idea of data abstraction is to identify for each type of data object a basic set of operations in terms of which all manipulations of data objects of that type will be expressed, and then to use only those operations in manipulating the data.**

This section take the rational number system from previous section as an example, to describe the idea of abstraction barriers, that is, to isolate different level of the system.

At each level, the program above the barrier use the data abstraction from the program below the barrier that implement the data abstraction.

For example, programs that use rational numbers manipulate them solely in terms of the procedures supplied by the rational number package: add, sub, mul, div. These procedure are interfaces that define the abstraction barriers and connect the different levels.


#### Exercise 2.2

This question is very similar to the rational number system.

```
(define (make-segment start end)
  (cons start end))

(define (start-segment segment)
  (car segment))

(define (end-segment segment)
  (cdr segment))

(define (make-point x y)
  (cons x y))

(define (x-point point)
  (car point))

(define (y-point point)
  (cdr point))


(define (midpoint-segment segment)
  (let ((start (start-segment segment))
        (end (end-segment segment)))
        
(define (midpoint-segment segment)
  (let ((start (start-segment segment))
        (end (end-segment segment)))
        
        (make-point (/ (+ (x-point start)
                          (x-point end)) 
                       2)
                    (/ (+ (y-point start)
                          (y-point end)) 
                       2))))
```

#### Exercise 2.3

```
(define (make-rectangle p1 p2)
  (make-segment p1 p2))

(define (height rectangle)
  (abs (- (y-point (end-segment rectangle))
          (y-point (start-segment rectangle)))))

(define (width rectangle)
  (abs (- (x-point (end-segment rectangle))
          (x-point (start-segment rectangle)))))

(define (abs x)
    (if (> x 0)
        x
        (- x)))

(define (perimeter rectangle)
  (* 2 (+ (height rectangle)
          (width rectangle))))

(define (area rectangle)
  (* (height rectangle)
     (width rectangle)))

```

Another representation we can used is to represent  rectangle as height segment and width segment. I'll skip the representation here since it is very obvious

## 2.1.3 What is meant by data?
This chapter contains a strikingly interesting example: we can re-implement the primitive pair with basic procedures. This example demonstrates that the ability to manipulate procedures as objects automatically provide the ability to represend compound data.

#### Exercise 2.4

Re-implement cons, car and cdr using lambda



```
(define (cons x y)
	(lambda (m) (m x y)))

(define (car z)
	(z (lambda (p q) p)))
```

``` cons x y``` returns a procedure, which also takes a procedure as argument, and apply to x and y. So when we execute ``` car z ```, the lambda function in its body will replace the m in ```cons```, then it looks like this:


``` 
lambda (lambda (p q) p)
	(lambda (p q) p) x y)
```

So we can implement ```cdr``` like

```
(define (cdr z)
	(z (lambda (p q) q)))
```

#### Exercise 2.5
```
(define (cons x y)
  (* (expt 2 x) (expt 3 y)))

(define (car z)
  (define (helper a n)
    (if (not (= 0 (remainder a 2)))
        n
        (helper (/ a 2) (+ n 1))))
  (helper z 0))

(define (cdr z)
  (define (helper a n)
    (if (not (= 0 (remainder a 3)))
        n
        (helper (/ a 3) (+ n 1))))
  (helper z 0))
```

#### Exercise 2.6

[Wiki reference](https://en.wikipedia.org/wiki/Church_encoding)

```
(define one (lambda (f) (lambda (x) (f x))))

(define two (lambda (f) (lambda (x) (f (f x)))))

(define (add m n)
	(lambda (f) (lambda (x) ((m f) ((n f) x)))))

```

#### Exercise 2.7

```
(define (lowe-bound interval)
	(car interval))

(define (upper-bound interval)
	(cdr interval))

```

#### Exercise 2.8

```
(define (sub-interval interval-1 interval-2)
	(let ((lower-bound-1 (lower-bound interval-1))
		(upper-bound-1 (upper-bound interval-1))
		(lower-bound-2 (lower-bound interval-2))
		(upper-bound-2 (upper-bound interval-2)))
          (make-interval (- lower-bound-1 upper-bound-2)
                         (- upper-bound-2 lower-bound-1))))
```
#### Exercise 2.9
```
(define (width interval)
	(/ (- (upper-bound interval) (lower-bound interval)) 2))
```
width(interval1 + interval2) = width(interval1) + width(interval2)

width(interval1 -  interval2) = width(interval1) - width(interval2)

#### Exercise 2.10

```
(define (div-interval x y)
  (if (and (>= (upper-bound y) 0)
           (<= (lower-bound y) 0))
      (error "denominator spans 0")
      (mul-interval x
                    (make-interval (/ 1.0 (upper-bound y))
                                   (/ 1.0 (lower-bound y))))))
```

#### Exercise 2.11

First we need to consider: why do we need 9 cases? The reason is in the original ```mul-interval```, we assume that those two intervals are all positive, but if one interval has a negative bound (or two), then we can't use that method anymore.


For each interval, there are three cases:

1. Both bounds are positive.
2. Both bounds are negative.
3. One bound is positive and one bound is negative

Then there are 3 * 3 = 9 cases to consider if we want to multiply two intervals.

#### Exercise 2.12

```
(define (make-center-percent center percentage)
	(let ((tolerance (* center percentage)))
          (make-center-width center tolerance)))
```


