---
layout: post
title: "Counting All Matroids on Ten Elements"
date: 2026-09-22
author: ainta
tags: [matroid, algorithms, enumeration]
---

## Problem

Let $E$ be a finite set. A matroid on $E$ is a nonempty family $\mathcal B$ of equally sized subsets of $E$, called bases. It satisfies basis exchange: for $B,C\in\mathcal B$ and $a\in B\setminus C$, some $b\in C\setminus B$ satisfies

$$
(B\setminus\{a\})\cup\{b\}\in\mathcal B.
$$

The common base size is the rank. We count matroids on ten elements up to relabeling.

There are [383,172 matroids on nine elements](https://arxiv.org/pdf/math/0702316) up to isomorphism. For ten elements, we find **3,232,000,741,644**. Listing one representative of each ten-element class would require trillions of objects. We count single-element extensions of nine-element matroids without listing the resulting matroids. Burnside's lemma then gives the number of isomorphism classes.

## Extensions as modular cuts

Let $N$ be a rank-$r$ matroid on nine elements, with ground set $E(N)$. Add an element $e$ without increasing the rank, and call the extension $M$. We call $N$ the parent of $M$. The rank $r_N(X)$ of a subset $X$ is the largest value of $\lvert B\cap X\rvert$ over bases $B$ of $N$. Its closure $\operatorname{cl}_N(X)$ contains exactly the elements whose addition to $X$ does not increase its rank. A *flat* is a set equal to its closure. For each flat $F$ of $N$, define

$$
x_F=1\quad\Longleftrightarrow\quad e\in\operatorname{cl}_M(F),
$$

Thus $x_F=1$ when $F$ spans $e$ in $M$.

The selected flats are upward closed: if $F\subseteq G$ and $x_F=1$, then $x_G=1$. They also contain the intersection of any two selected flats that form a modular pair:

$$
x_F=x_G=1\quad\Longrightarrow\quad x_{F\cap G}=1
\qquad\text{when}\qquad
r_N(F)+r_N(G)=r_N(F\cup G)+r_N(F\cap G).
$$

These rules define a *modular cut*. [Crapo (1965)](https://nvlpubs.nist.gov/nistpubs/jres/69B/jresv69Bn1-2p55_A1b.pdf) proved that nonempty modular cuts correspond bijectively to rank-preserving extensions. We set $x_{E(N)}=1$ to count nonempty cuts. The empty cut adds a coloop and increases the rank by one.

The cut determines the rank function of $M$. For $X\subseteq E(N)$,

$$
r_M(X)=r_N(X),\qquad
r_M(X\cup\{e\})=r_N(X)+1-x_{\operatorname{cl}_N(X)}.
$$

We can count extensions without constructing their bases or testing the resulting matroids for isomorphism.

## Counting the remaining hyperplane choices

First assign the flats of rank at most $r-2$. Propagate the modular-cut rules after each choice. Only hyperplanes, which are flats of rank $r-1$, can remain undecided.

Distinct hyperplanes span $E(N)$. They form a modular pair exactly when their intersection has rank $r-2$. If both hyperplanes remain undecided, their intersection is false: otherwise upward closure would have selected both. Selecting both would therefore violate the modular-cut rule. Make a graph with one vertex for each undecided hyperplane and an edge for each such pair. Its independent sets are exactly the valid choices.

For example, add one element to $U_{2,3}$, the rank-two uniform matroid on three elements. If the empty flat is false, the graph on its three rank-one flats is a triangle. Its four independent sets give one uniform extension and three extensions in which the new element is parallel to an old one. If the empty flat is true, the new element is a loop. Thus there are $4+1=5$ rank-preserving labeled extensions.

To count extensions fixed by an automorphism $g$ of $N$, assign one variable to each $g$-orbit of flats. Selecting an orbit selects every flat in it. If two hyperplanes in one orbit conflict, propagation forces that orbit false. After propagation, make a graph on the undecided hyperplane orbits. Join two orbits if any pair of their hyperplanes conflicts. The procedure is:

~~~text
CountCuts(N, g):
    make one variable for each g-orbit of flats
    impose the modular-cut rules and select the top flat
    propagate

    DFS:
        if an orbit of flats of rank at most r-2 is undecided:
            try true and false, propagate, and add the two answers
        otherwise:
            return the number of independent sets
                   in the graph of undecided hyperplane orbits
~~~

Let $i(G)$ count the independent sets of a graph $G$, including the empty set. Let $N_G[v]$ contain $v$ and its neighbors. The graph counter uses $i(G)=i(G-v)+i(G-N_G[v])$. It removes isolated vertices, splits components, uses Fibonacci formulas for paths and cycles, and caches repeated vertex sets. Each completed assignment to flats of rank at most $r-2$ therefore gives the exact number of cuts extending it.

On nine elements, a rank-five matroid has at most $\binom94=126$ hyperplanes. It also has at most $\sum_{j=0}^{3}\binom9j=130$ flats of rank at most three. These bounds describe the size of each counting problem, not its running time.

## Burnside's lemma

Counting extensions up to each parent's automorphism group counts matroids with a distinguished element $e$. Different matroids can have different numbers of element orbits. We therefore cannot divide the number of distinguished matroids by ten to obtain the number of isomorphism classes.

Let $u_{10,r}$ be the number of rank-$r$ matroids on ten elements up to isomorphism. Let $S_{10}$ be the group of permutations of the ten labels. Burnside's lemma gives

$$
u_{10,r}=\frac{1}{10!}\sum_{\sigma\in S_{10}}
\#\{\text{labeled rank-}r\text{ matroids fixed by }\sigma\}.
$$

The number of matroids fixed by a permutation depends only on its cycle type. There are 42 cycle types.

For 30 cycle types, the permutation fixes an element $e$. Delete $e$ from a matroid fixed by that permutation. If $e$ is not a coloop, the deletion is a rank-$r$ matroid $N$ on nine elements. The restricted permutation is an automorphism $g$ of $N$. The extensions fixed by the permutation correspond exactly to the nonempty modular cuts of $N$ preserved by $g$.

If $e$ is a coloop, its deletion has rank $r-1$. Each such parent has one coloop extension. We generate one representative of each parent class and account for all its labelings and relevant automorphisms. At rank five, duality pairs these rank-four parents with rank-five parents and preserves their automorphism groups.

We count one representative per conjugacy class in a parent's automorphism group and multiply by the class size. Automorphisms with the same cycle type on nine elements can act differently on a parent's flats.

The other 12 cycle types have no fixed point. Choose one permutation $\sigma$ of each type. Create one Boolean variable for each orbit of $r$-element subsets. A true variable selects every subset in its orbit as a basis. Require at least one basis and impose the basis-exchange axiom from the definition. The satisfying assignments are exactly the rank-$r$ matroids fixed by $\sigma$. An exact model counter counts these assignments.

The permutation of cycle type $(2,2,2,2,2)$ consists of five transpositions. At rank five, it gives 126 variables. We split its formula into 256 disjoint partial assignments and solve them in parallel. The basis-exchange formula also applies to permutations with fixed points. For the identity permutation, however, it would leave all $\binom{10}{5}=252$ basis variables free. The parent calculation handles that case efficiently.

## Result

The rank-five parent calculation is the most expensive stage. It covers 190,214 nine-element matroids up to isomorphism. We run parent jobs independently across cores. Propagating the modular-cut rules removes invalid branches. The independent-set counter combines all remaining hyperplane choices in each branch. Let $\ell$ count orbit variables for flats of rank at most $r-2$, and let $h$ count orbit variables for hyperplanes. One call to `CountCuts` takes at most $2^{\ell+h}$ times a polynomial in the number of flats.

| Rank | Isomorphism classes on ten elements |
|:--|--:|
| 0 | 1 |
| 1 | 10 |
| 2 | 128 |
| 3 | 10,037 |
| 4 | 4,886,380,924 |
| 5 | 3,222,227,959,444 |

Duality gives the counts for ranks 6–10 from ranks 4–0. Summing all ranks gives 3,232,000,741,644 matroids on ten elements up to isomorphism.

The [implementation](https://github.com/ainta/matroid-count) has a 481-line C++ worker for extensions and a separate counting driver. The repository also provides a generator for nine-element parents and an exact model counter. We used 64 workers on a machine with 64 physical cores and two AMD EPYC 9354 processors. Processing the rank-five parents took 474 seconds. Counting ranks three through five from the generated parents took 602 seconds. Generating the parents took another 3.8 seconds.

The implementation reproduces the known rank-three counts: 38 on six elements and 108 on seven. It also reproduces the rank-four counts: 940 on eight elements and 190,214 on nine. At ten elements, it agrees with the published rank-three and [rank-four counts](https://doi.org/10.1007/s00454-011-9388-y). The parent generator starts from the empty matroid and reproduces the published nine-element count.

The [reproduction instructions](https://github.com/ainta/matroid-count#reproduce) give the commands for generating the parents and running this calculation.

GPT-6 Astra helped discover and implement the optimized algorithms used in this calculation.
