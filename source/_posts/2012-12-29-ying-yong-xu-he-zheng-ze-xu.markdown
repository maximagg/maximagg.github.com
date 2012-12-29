---
layout: post
title: "应用序和正则序"
date: 2012-12-29 17:27
comments: true
categories: [lisp, scheme, 计算机程序的构造和解释]
---

应用序和正则序主要是解释顺序上面的区别在于:  
应用序首先对参数求值后再代换,
而正则序在代换完全后再归约求值.
```scheme
;; Scheme code
(define (p) (p))
(define (test x y)
  (if (= x 0)
      0
      y))

(test 0 (p))
```
为什么if是特殊表达式
--------------------
if语法是这个样子的
```scheme
(if predicate then-clause else-clause)
```
当predicate为真的时候只对then-clause求值,而忽略else-clause.
一般的应用序表达式首先对参数求值后再求值整个语句,下面的例子说明了这个顺序差异的重要性.
```scheme 牛顿法求平方根
;; Scheme code
(define (good-enough? guess x)
  (if (< (abs (- (* guess guess) x)) 0.000000001)
      true
      false))
(define (improve guess x)
  (/ (+ guess (/ x guess)) 2))
(define (sqrt-iter1 guess x)
  (if (good-enough? guess x)
      guess
      (sqrt-iter1 (improve guess x) x)))
(define (my-if predicate then-clause else-clause)
  (if predicate then-clause else-clause))
(define (sqrt-iter2 guess x)
  (my-if (good-enough? guess x)
	 guess
	 (sqrt-iter2 (improve guess x) x)))
(define (my-sqrt1 x)
  (sqrt-iter1 2.0 x))
(define (my-sqrt2 x)
  (sqrt-iter2 2.0 x))
```
my-sqrt1可以求出正确的平方根,
而my-sqrt2因为首先对参数进行求值,而陷入无限的递归循环中去.
if表达式会首先对predicate求值,根据结果确定要求值的表达式,与一般的表达式参数求值顺序不一致,比较特殊.
