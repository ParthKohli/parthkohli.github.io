---
layout: post
title: Generating Functions 
tags: [math, counting]
comments: false
usemathjax: true
---
Let's look at this math problem from the recent [AIsing Programming Contest 2020 on AtCoder.](https://atcoder.jp/contests/aising2020/tasks/aising2020_f) 

The problem is to compute the sum of \\[\tag{1}(a_2 - a_1)(b_2 - b_1)(c_2 - c_1)(d_2 - d_1)(e_2 - e_1)\\] for all tuples such that \\[\tag{2} a_1 + a_2 + b_1 + b_2 + \cdots + e_1 + e_2 \le N\\] where $$\tag{3} 0 \le a_1 < a_2, ~ 0 \le b_1 < b_2, \cdots,~ 0 \le e_1 < e_2$$The constraint $N \le 10^9$ makes it worse. 

To begin, it's a good idea to cast the differences in the target expression as separate variables. Let's rename $a_1$ as $a$ and $a_2$ as $a + a'$, so that $a' = a_2 - a_1$. It's also a good idea to add a dummy $f \ge 0$ to $(2)$ to the left side so that it becomes an equation. Under this renaming, things become slightly simpler. We're now asked to evaluate the sum of \\[\tag{1*} a' b' c' d' e'\\]for all tuples such that\\[\tag{2\*} 2(a + b + \cdots + e) + (a' + b' + \cdots + e') + f = N \\]where\\[\tag{3\*} \begin{cases}a, b, \cdots, e, f \ge 0 \\\\ a', b', c', d', e' > 0\end{cases}\\]While the target expression $(1\*)$ is simpler, we need to find out what to do about $(2\*)$. Another greatly used tool in combinatorics is casting these problems in terms of [generating functions](https://en.wikipedia.org/wiki/Generating_function) and obtaining the solution by manipulating the power series.

### A few words on generating functions

Consider multiplying two polynomials $f$ and $g$. Question: what exactly does the coefficient of $x^i$ denote? Described purely in words, it is the sum of product of coefficients in any two terms, one from $f$, and one from $g$, so that the sum of degrees of the terms is $i$. We can look at this for more than two polynomials: we pick one term each from every participating polynomial in the product, we find the sum of the degrees, and add the product of these coefficients to that term. We may think of the coefficients, then, as a form of _weight_ for a term.

The technique we're about to employ takes advantage of precisely this. We multiply many series, where each individual series corresponds to a variable. The exponents denote possible values for the variable, and a coefficient intuitively describes the _weight_ in that term. Then the coefficients of the product are the sum of products of these weights.

### Solution 
We need to derive a generating function from $(2*)$. We're adding up to $N$ using $11$ non-negative integers, where the first five of them are even (this is because of the coefficient $2$), and the next five of them are strictly positive. We also need to weigh the series that correspond to $a', b', \cdots, e'$ so that we get the sum of $a'b'c'd'e'$ in the resulting product. This can be done by making the **coefficient equal to the exponent** (so that if $x^i$ is picked, it contributes $i$ to the product). So we obtain the following representation:\\[F(x) = (1 + x^2 + x^4 + \cdots)^5 (1x + 2x^2 + 3x^3 + \cdots )^5 (1 + x + x^2 + \cdots)\\]and we need to find the coefficient of $x^N$ in $F(x)$ to obtain the answer. The first boss move in moving to the solution is noticing\\[x + 2x^2 + 3x^3 + \cdots = x(1 + x + x^2 + \cdots)^2\\]so that\\[F(x) = x^5 (1 + x^2 + x^4 + \cdots)^5(1 + x + x^2 + \cdots)^{11}\\]We should also remove the stupid $x^5$ because it's of no use to us, and we can calculate the coefficient of $x^{N-5}$ in\\[F_2(x) = (1 + x^2 + x^4 + \cdots)^5(1+x+x^2 + \cdots)^{11}\\]We've hit the crux of the problem: what do we do with this? Life would have been much simpler if we had only a single type of exponentiated. Why? Because we have a closed form\\[\tag{\*} (1 + x + x^2 + \cdots)^n = \sum_{k = 0}^{\infty} \binom{n + k - 1}{k - 1} x^k\\]This is [stars-and-bars](https://en.wikipedia.org/wiki/Stars_and_bars_(combinatorics)) in terms of generating functions.

Without further ado, here's the **big trick**:\\[1 + x + x^2 + \cdots = (1 + x)(1 + x^2 + x^4 + \cdots)\\]And so\\[F_2(x) = (1+x)^{11} (1+x^2+x^4+\cdots)^{16}\\]The first part $(1 + x)^{11}$ can be expanded using the [binomial theorem](https://en.wikipedia.org/wiki/Binomial_theorem), and the second part can be expanded using $(\*)$. To evaluate the coefficient of $x^{N-5}$, we loop on the term from the binomial expansion, (say, the $x^i$ term) and multiply its coefficient by the coefficient of $x^{N-5-i}$ in the in the expansion of $(1 + x^2 + x^4 + \cdots)^{16}$. And we're done!

A tiny implementation detail: To compute a binomial coefficient $\binom{p}{q}$ with large $p$ and small $q$, it's better to use the following:\\[\binom{p}{q} = \frac{p(p-1)\cdots(p-q+1)}{q!}\\]Most problems involve precomputing factorials and their inverses, but this is not one of them.
