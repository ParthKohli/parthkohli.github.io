---
layout: post
title: More Generating Functions 
tags: [math, combinatorics, inclusion-exclusion]
date: "2022-02-28"
---

Today we take a look at the problem [ABC241 H](https://atcoder.jp/contests/abc241/tasks/abc241_h).

We are given $N \le 16$ distinct numbers $A_1, \cdots, A_N$ and a sequence describing the amount of supply of each number: a sequence $B_1, \cdots, B_N$ where $B_i$ refers to the supply of the number $A_i$. The score of a combination of $M$ chosen numbers is the product of the numbers. That is, suppose we pick $C_i$ occurences of the $A_i$ where $0 \le C_i \le B_i$ and $\sum C_i = M$, then $\text{score}(C) = \prod_{i = 1}^{N} A_i^{C_i}$. The objective is to find the sum of scores over all possible valid combinations.

This statement might sound convoluted, but its intent is very clearly and naturally expressed in the language of generating functions. Observe that it translates to finding the coefficient of $x^M$ in the following:

$$F(x) = \prod_{i=1}^{N} \sum_{j = 0}^{B_i} (A_j x)^j$$

Here, the index $j$ represents the number of picked $A_i$'s. The sum of powers of $x$ should be $M$, and so our generating function would conveniently pick every valid combination of summing up to $M$ and collect the score for each such combination. Let's now use the following identity:

$$\sum_{i=0}^{n} y^i = \frac{1 - y^{n + 1}}{1 - y}$$

And we get the following product:

$$F(x) = \prod_{i = 1}^{N} \frac {1 - (A_i x)^{B_i+1}}{1 - A_i x}$$

Remember that we are still only looking for the coefficient of $x^M$. The $B_i$'s are sort of annoying, so let's turn our attention to a simpler problem: what if we have infinite supplies of each? In that case, we need the coefficient of $x^M$ in 

$$G(x) = \prod_{i=1}^{N} \frac{1}{1 - A_i x}$$

Which looks awfully familiar - oh wait, this can be decomposed using partial fractions! So let's do it: 

$$G(x) = \prod_{i = 1}^{N} \frac{1}{1 - A_i x} = \sum_{i = 1}^{N}\frac{c_i}{1 - A_i x}$$

A gentle reminder that it's very easy to compute the decomposition when the denominator is conveniently split into a simple product of linear factors - it's a matter of multiplying the product $\prod_{i=1}^{N} (1 - A_i x)$ and for each $i$, plugging in $x = \frac{1}{A_i}$, turning all except the term involving $c_i$ equal to $0$. This is naively done in $O(N^2)$ which is good enough given the unusually low constraint for $N$. To find the coefficient of $x^M$, we just expand $\frac{1}{1 - A_i x}$ and see that it is doable in $O(N\log_2{M})$ with binary exponentiation.

$$G(x) = \sum_{i=1}^{N} \sum_{j=0}^{\infty} c_i A_i^j x^j \implies [x^M]G(x) = \sum_{i=1}^{N} c_i A_i^{M} $$

There's actually not much to be done at this point. Notice that 

$$F(x) = G(x) \prod_{i=1}^{N} (1 - (A_i x)^{B_i + 1})$$

The product is interesting. For the $i$-th factor, we are making a choice: pick one of the two terms $1$ and $-(A_i x)^{B_i + 1}$. All $N$ choices are independent over every $i$ and so we have $2^N$ ways. We can look at every such way independently and add up the resulting coefficients of $x^M$ to obtain the answer. For example, one of these ways is choosing $1$ in every factor. In fact, this series of choices yields $G(x)$, so finding the coefficient of $x^M$ is exactly what we did above. 

The other cases are not very different. Suppose the sum of the picked powers of $x$ in our $N$ choices is $s$ and the collected coefficient is $p$ (for example, $s = 0$ and $p = 1$ when we pick all $1$s). Then we need to find the coefficient of $x^{M - s}$ in $G(x)$ and multiply it by $p$ to give us one of the terms in the sum that involves $2^N$ terms. Of course, the coefficient of $x^{M - s}$ can be found in the same way as that of $x^M$. This gives us an $O(N^2 + 2^N \cdot N \cdot \log_2 M)$ solution.

It is interesting to note that what we have done here is simply the [inclusion-exclusion principle](https://en.wikipedia.org/wiki/Inclusion–exclusion_principle). We noticed that the answer is easily computable in the unrestricted case (call it $g(s)$ where $s$ is the sum of chosen numbers) and we want to compute it for the restricted case, call it $f(s)$. Then we look at the answer when some set of indices $S \subseteq \\{ 1, \cdots, n \\}$ violates its constraint, i.e., for every $i \in S$, $C_i \ge B_i + 1$ which is the same as an unrestricted $C_i' = C_i - (B_i + 1) \ge 0$ (except the score is scaled up by $A_i^{B_i + 1}$ to account for the offset) and so the expression looks like $$ f(M) = \sum_{S} (-1)^{ \mid S \mid } \left(\prod_{i \in S} A_i^{B_i + 1}\right)g\left(M - \sum_{i\in S} (B_i + 1)\right)$$. This is identical to what we obtained using generating functions.
