---
title: 数学概念研究：introduction and elimination
date: 2024-12-04
categories: [Abstract Algebra]
tags: [FStar, Connectives]
description: 介绍基本概念
math: true
---

尽管每个连接词的特定 Introduction 和 Elimination 规则很容易理解，但整体特征并不容易理解，每个连接词与另一个连接词的一般联系也不容易理解。Gentzen 认为 （1） 引入规则提供了连接词的“定义”，（2） 它们还提供了这些连接词的含义，（3） 应该能够将 E-推理显示为其相应 I-推理的独特功能，以及 （4） “归根结底”，E-规则是相应I-规则的结果。

以 $\forall$ 和 $\exists$ 为例介绍二者究竟是什么

### $\exists$ - Introduction

一个存在语句的形式化描述为 $\exists x \in X, P(x)$，证明该语句为真的最直接方法为指出X中的某一个元素，满足谓词P。这就是$\exists$ - Introduction规则。

```
let x = SomeThing
(x <: X /\ P(x) == True) ==> exists (x:X).P(x)
```

### $\exists$ - Elimination

假设已经知道$\exists x \in X, P(x)$为真，那么就可以从X中找到一个元素x，确定它是满足谓词P的。

### $\forall$ - Introduction

要证明一个全称语句$\forall x \in X, P(x)$为真，就需要从X中的任意一个元素x开始，证明其满足P。需要注意除了假定x是X的元素之外不能再给其设定额外的性质。

Example:

```
let aux x : Lemma (p x) =
    // proof of `p x`
in
forall_intro aux // and now we have `forall x. p x`
```

### $\forall$ - Elimination

假设已经知道$\forall x \in X, P(x)$为真，那么对任意$x \in X$，都有$P(x)$为真。
