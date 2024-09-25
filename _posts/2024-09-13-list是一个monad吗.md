---
title: monad学习：list是一个monad吗
date: 2024-09-12
categories: [Abstract Algebra, Group Theory]
tags: [monad, list type]     # TAG names should always be lowercase
description: list如何成为一个monad？它为什么需要是monad？
---


## monad学习：list是一个monad吗

Monad的基本元素

```F#
type monad a = return : a -> monad a

val bind : monad a -> (a -> monad b) -> monad b
```

Monad的基本性质

```F#
bind (return x) f == f x    // left unit

bind m return == m         // right unit

bind (bind m f) g == bind m (fun x -> bind f x g) // associativity
```

对于list：

定义return
return x = | Nil
           | Cons hd tl

定义bind
Nil `bind` f = Nil
(Cons hd tl) `bind` f = Append (f hd) (tl `bind` f)

验证是否满足monad性质，Nil情况

Nil `bind` f = Nil 没有严格的x，先pass性质1，2

(Nil `bind` f) `bind` g == Nil `bind` g == Nil == Nil `bind` (fun x -> Nil) == Nil `bind` (fun x -> bind Nil g)

Cons情况

((Cons hd tl) `bind` f) `bind` g == (Append (f hd) (tl `bind` f)) `bind` g 
    == Append ((f hd) `bind` g) ((tl `bind` f) `bind`g) 
    == (可递归到Nil得证)
    == Append ((f hd) `bind` g) (tl `bind` (fun x-> (f x) `bind` g))
    == (Cons hd tl) `bind` (fun x-> (f x) `bind` g) 

为什么list需要是个monad？list是把多个同类型值打包到一起，有monad的打包盒子味道。在上面的bind定义下，结合律能够得到验证，但左右单位元没有恰当的定义，或许可以不追求幺半群（Monoid）而将其认为一个半群，这样只要求封闭性和结合律就可以了。

> 一个return定义的idea：return x = Cons x Nil 能验证它是左右幺元，不用再分情况讨论

现有list中的map操作很类似bind，f的类型是a -> b，结合律性质在MapReduce中有优化作用

参考
[五分钟了解Monad](https://www.cnblogs.com/Helium-Air/p/15646488.html)

*<p align="right">Completed on: 9/20</p>