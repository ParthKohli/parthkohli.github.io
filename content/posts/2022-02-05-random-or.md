---
layout: post
title: Random OR
tags: [math, probability]
date: "2022-02-05"
---
Today I'll discuss a [probability problem from CodeChef](https://www.codechef.com/FEB221A/problems/RANDOMOR).

The problem is as follows: we have an $N$-bit number $S$ which is initially $0$. At each step, we pick an $N$-bit number $X$ uniformly randomly and set $S := S \mid X$ (the symbol $\mid$ denotes bitwise-or). The process terminates when $S$ becomes $2^N - 1$ (that is, it is impossible to increase it any further because every bit is 1). What is the expected number of steps until termination?

If you are familiar with statistical distributions, you should immediately see the goal of this problem. Don't worry if you are not - you should be able to follow.

#### The case $N = 1$
The only two choices for $X$ are $0$ and $1$ and the process terminates as soon as we get a $1$. This is visualised as tossing a coin and waiting until the first heads. The number of steps follows a geometric distribution with $p = \frac{1}{2}$ (again, fret not if you don't know what that means). It is worth mentioning how to calculate the expected value even though it is covered in the samples. Let $e$ denote the expected number of tosses until we meet our first $1$. On our first step, we either obtain a $1$ with probability $\frac{1}{2}$ or we have to "reset" the process with probability $\frac{1}{2}$. Thus, the equation is $e = \frac{1}{2} \cdot 1 + \frac{1}{2} \cdot (1 + e)$ which yields $e = 2$.

#### Understanding the general case
The general case is visualised as follows: do the above experiment with $N$ separate coins. What is the expected value of the maximum number of trials among them? Formally, we have $N$ iid random variables $X_1, \cdots, X_N$ all following the geometric distribution with $p = \frac{1}{2}$. What is the value of $E[Y]$ where $Y = \max X_i$?

We know by the definition of expected value that $E[Y] = \sum_{i \ge 1} i\cdot P(Y = i)$. But this can also be seen as $E[Y] = \sum_{i \ge 1} P(Y \ge i)$. Why? Try writing $P(Y \ge i)$ as $P(Y = i) + P(Y = i + 1) + \cdots$ and check that this becomes the original definition. And so, since the $N$ variables are independent,

$$E[Y] = \sum_{i \ge 1} 1 - P(Y < i) = \sum_{i \ge 1} 1 - P(X_1 < i) \cdots P(X_N < i)$$

The probability $P(X_1 < i)$ is $1 - P(X_1 \ge i)$. Note that $P(X_1 \ge i)$ means that you want the first $i - 1$ tosses to appear as tails the probability of which is $\frac{1}{2^{i - 1}}$. The same goes for $X_2$ through $X_N$ as they are identically distributed. Thus,

$$E[Y] = \sum_{i \ge 1} 1 - \left(1 - \frac{1}{2^{i-1}}\right)^{N}$$

As a sanity check, try substituting $N = 1$ and see that it matches the expected value (pun not intended).

For brevity, we change the indexing to 0-based and denote $p = \frac{1}{2}$. We need to calculate $\sum_{i\ge 0} 1 - (1 - p^i)^N$ where $N$ is fixed.

#### Finishing it off

Using binomial theorem, the series is

$$S = \sum_{i \ge 0}\sum_{j = 1}^{N} \binom{N}{j} (-1)^{N - j} p^{ij}$$

Here is the kicker: switch indexing.

$$S = \sum_{j = 1}^{N} \sum_{i \ge 0} \binom{N}{j} (-1)^{N - j} p^{ij} = \sum_{j = 1}^{N} \binom{N}{j} (-1)^{N - j} \sum_{i \ge 0} p^{ij}$$

Of course, we know that the inner summation is merely the geometric series and so we can finish with the result (where $p$ still denotes $\frac{1}{2}$).

$$S = \sum_{j = 1}^{N} \binom{N}{j} (-1)^{N - j} \frac{1}{1 - p^j}$$

The problem is thus solved in complexity $O(N \log(10^9 + 7))$. 

The computation of binomial coefficients modulo a large prime is a fairly common idea in competitive programming: compute in advance the arrays $\rm factorial[0..N]$ and $\rm inverse\\_factorial[0..N]$ where computations are carried out modulo the prime $10^9 + 7$, that is, $\rm inverse\\_factorial[i] \cdot factorial[i] \equiv 1 \pmod {10^9 + 7}$. Computing inverses/reciprocals is done using Fermat's theorem.
