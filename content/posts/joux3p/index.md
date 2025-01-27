---
author: ["Jason"]
title: "Cheating Discrete Log Exactly Once"
date: "2024-11-22"
description: "Joux's 3-party Diffie-Hellman"
math: true
tags: ["cryptography", "diffie-hellman", "bilinear maps"]
summary: "summary info"
categories: ["cryptography", "math"]
series: ["Crypto"]
ShowToc: true
TocOpen: true
---

## Bilinear Maps

Let $G_1, G_2$, and $G_t$ be cyclic groups of the same order.
A bilinear map from $G_1 \times G_2$ to $G_t$ is a function $e$:
$$
e: G_1 \times G_2 \rightarrow G_t \text{ such that } \forall u \in G_1, v \in
G_2, a, b \in \mathbb{Z} \newline
e(u^a, v^b) = e(u,v)^{ab}
$$
Bilinear maps are also called pairings because they pair elements from $G_1,
G_2$ and map them to an element in $G_t$.
The above definition assumes that degenerate maps which map everything to the
identity $G_t$.

An Admissible Bilinear Map is as follows:

Let $e : G_1 \times G_2 \rightarrow G_t$ be a bilinear map.

Let $g_1$ and $g_2$ be generators of $G_1$ and $G_2$ respectively.

The map $e$ is *admissible* bilinear map if $e(g_1, g_2)$ generates $G_t$ and
$e$ is efficiently computable.

The relationship between $G_1, G_2$ and $G_t$:

- $G_1, G_2$ and $G_t$ are all isopmorphic to one another since they have the
same order and are cyclic
- They are different groups in the sense that we represent the elements and
compute the operations differently
- Normally, however, $G_1=G_2$ (in addition to being isopmorphic), we will
now denote both by $G = G_1 = G_2$
- $G$ and $G_t$ may have either composite or prime order which makes a
difference how they work and are used
- If $G=G_t$ it's called a self-bilinear map and is very powerful, no known
examples, still an open problem.

### Groups

Typically $G$ is an elliptic curve (or subgroup), e.g. the elliptic curve
defined by $y^2 = x^3 + 1$ over the finite field $F_p$. More generally, $G$ is
typically abelian variety over some field. $G_t$ is normally a finite field.

## Decisional Diffie-Hellman (DDH)

Let $G$ be a group of order $q$ with generator $g$. The advantage of an
probabilistic algorithm $\mathcal{A}$ in solving the Decisional Diffie-Hellman
problem in $G$ is:

$$
\text{Adv}^{\text{DDH}}_{\mathcal{A},G} = | \Pr[\mathcal{A}(g, g^a, g^b, g^{ab}) = 1] -
P[\mathcal{A}(g, g^a, g^b, g^z)=1] |
$$

where $a,b,z$ are drawn uniformly at random from $\Z_q$ and the probability is
taken over choices $a,b,z$ and $\mathcal{A}$'s coin flips.

A property of the bilinear map is that it makes DDH easy in $G$ such that with a
bilinear map $e: G \times G \rightarrow G_t$, a polynomial time $\mathcal{A}$
may gain advantage. Given $g, g^a, g^b, g^c$, determine whether $c \equiv ab
\mod q$ by checking $e(g^a, g^b) = e(g, g^c)$. However if the map is from
distinct groups $G_1, G_2$ then DDH may still be hard in $G_1$ (or $G_2$) which
is only possible if there is no efficiently computable isomorphic between $G_1$
and $G_2$

## Discrete Log

If there exists a bilinear map $e: G \times G \rightarrow G_t$, then the
discrete log problem in $G$ is no harder than the discrete log problem in $G_t$.
That is, given $g \in G$ and $g^a \in G$, we can compute $e(g,g) \in G_t$ and
$e(g,g^a) = e(g, g)^a \in G_t$. Then we can use a discrete log solver for $G_t$
to obtain $a$. This is called the Menezes, Okamoto, Vanstone (MOV)
reduction[^1].

## Joux's 3-Party Diffie-Hellman

Let $G$ be a group with prime order $1, e : G \times G \rightarrow G_t$ be a
bilinear map, and $g$ be a generator of $G$. Let $\hat{g} = e(g, g) \in G_t$.

### Protocol

1. Alice picks $a \leftarrow \mathbb{Z}$
2. Alice, Bob, Carol broadcast $g^a, g^b, g^c$ respectively
3. Alice computes $e(g^b, g^c)^a = \hat{g}^{abc}$, Bob computes $e(g^c, g^a)^b =
   \hat{g}^{abc}$, and Carol computes $e(g^a, g^b)^c = \hat{g}^{abc}$.

### The "Cheat"

From Alice's perspective, the bilinear map lets you "cheat" to get $\hat{g}^bc$
from $g^b$ and $g^c$. Then, regular exponentiation gets you the rest of the way
to $\hat{g}^{abc}$. You **can not** use $e$ to get $\hat{g}^{abc}$ from the
broadcasted $g^a, g^b, g^c$. Formally,
$$
e(g^a, e(g^b, g^c)) = e(g^a, \hat{g}^{bc}) \neq \hat{g}^{abc}
$$
i.e. $\hat{g}^{bc}$ not in $G$, thus, only one cheat is allowed.

---

[^1]: [Reducing elliptic curve logarithms to logarithms in a finite field](https://ieeexplore.ieee.org/document/259647)
