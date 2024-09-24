---
title: tagged union本质探究
date: 2024-09-13
categories: [Abstract Algebra, Type Theory]
tags: [sum type, tagged union]     # TAG names should always be lowercase
description: 浅析sum type和tagged union的本质及其关系
---

## tagged union本质探究

### sum type

最基本的sum type是either类型

```F#
val obj : either a b

let obj = Inl v     // v:a
let obj = Inr u     // u:b
```

可写作 $a + b$ ，记 $u = a + b$ ，construct方式有两种

$$
\frac{\Gamma \vdash e : a}{\Gamma \vdash \text{Inl} \cdot e : u} \quad
\frac{\Gamma \vdash e : b}{\Gamma \vdash \text{Inr} \cdot e : u}
$$

利用case语句destruct

$$
\frac{\Gamma \vdash e : a+b, \Gamma,x:a \vdash e_1:\tau, \Gamma,y:b \vdash e_2:\tau}{\Gamma \vdash case\ e\ \{\text{Inl} \cdot x \Rightarrow e_1 | \text{Inr} \cdot y \Rightarrow e_2 \}:\tau}
$$

F*中的match语句类似case语句

### 从sum type到其他类型

首先定义一个仅有一个元素 $()$ 的sum type，称为unit type，它的construct和destruct也得到类似简化

$$
\frac{}{\Gamma \vdash () : unit} \quad
\frac{\Gamma \vdash e_0 : unit, \Gamma \vdash e_1:\tau}{\Gamma \vdash case\ e_0\ \{() \Rightarrow e_1 \}:\tau}
$$

利用unit type可以构造bool，使其不再是primitive的，将unit type简写为1

$$
\begin{align}
    &bool &=& 1+1 \\
    &true  &=& Inl\ () \\
    &false &=& Inr\ () \\
    &if e_0\ e_1\ then\ e_2 &=& case\ e_0\ \{Inl\cdot x_1 \Rightarrow e_1 | Inr\cdot x_2 \Rightarrow e_2\}
\end{align}
$$

同样可以构造option type

$$
option\ \tau = \tau + 1
$$

可以构造自然数序列

$$
\begin{align}
    &nat &=& 1+(1+(1+...)) \\
    &0 &=& Inl\cdot () \\
    &succ &=& \lambda n. Inr\cdot n
\end{align}
$$

这里使用省略号来构造nat并不是well-typed，更形式化的，应该使用basic recursion。

### 从sum type到tagged union

通过sum type（either），以二叉树的方式构造有多个选项的tagged union，此时匹配语法需要一定扩展，假设待匹配类型A在左子树中，那么左子树的类型为 ...+A+... 而并非A，因此在上面的case定义中对左子树不会匹配，需要扩展case语法，或认为A是 ...+A+... 的子类。二叉树的构造方式是多样的，结构取决于+是否符合结合律。

上面的方式相当于在标签中进行二分搜索，而线性搜索相当于将树结构特定为左子树均为叶子的形状。例如A+(B+(C+D))，首先匹配x:either A (B+(C+D)) {Inl? x}，若否则匹配x:either B C+D {Inl? x}，以此类推。这种形式似乎更符合sum type的定义，不需要考虑结合律问题。

如何引入default：在定义tagged union是并不需要default tag，而是在匹配的时候需要，根本的原理是在所有类型的匹配之后补充一个对any类型的匹配，在F*中是"_"。

参考
[Tagged Union - wikipedia](https://en.wikipedia.org/wiki/Tagged_union)
[CMU Course Note](https://www.cs.cmu.edu/~fp/courses/15814-f18/lectures/06-sums.pdf#page=6.65)

