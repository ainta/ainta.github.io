---
layout: post
title: "Counting All Matroids on Ten Elements"
date: 2026-09-22
author: ainta
tags: [matroid, algorithms, enumeration]
---

## Problem

Count all matroids on a ten-element ground set up to isomorphism. Loops, parallel elements, coloops, and disconnected matroids are included. Isomorphism relabels the ground set; it does not identify a matroid with its dual unless the two are isomorphic.

There are [383,172 matroids on nine elements](https://arxiv.org/pdf/math/0702316) up to isomorphism. For ten elements, we find 3,232,000,741,644. Listing that many representatives would be impractical. We instead count single-element extensions of nine-element matroids in aggregate, then use symmetry to count isomorphism classes.

## Extensions as modular cuts

Fix a rank-$r$ matroid $N$ on nine elements, with ground set $E(N)$, and add an element $e$ without increasing the rank. A *flat* is a set equal to its closure. For every flat $F$ of $N$, introduce a Boolean variable

$$
x_F=1\quad\Longleftrightarrow\quad e\in\operatorname{cl}_M(F),
$$

where $M$ is the extension. Thus $x_F$ records whether $F$ spans $e$.

The selected flats must be upward closed: if $F\subseteq G$ and $x_F=1$, then $x_G=1$. They are also closed under the intersection of a modular pair:

$$
x_F=x_G=1\quad\Longrightarrow\quad x_{F\cap G}=1
\qquad\text{when}\qquad
r_N(F)+r_N(G)=r_N(F\cup G)+r_N(F\cap G).
$$

These two rules define a *modular cut*. [Crapo (1965)](https://nvlpubs.nist.gov/nistpubs/jres/69B/jresv69Bn1-2p55_A1b.pdf) showed that nonempty modular cuts are in bijection with rank-preserving extensions. We force $x_{E(N)}=1$ to count the nonempty cuts. The empty cut adds a coloop and raises the rank by one.

The cut really determines the whole extension: for $X\subseteq E(N)$,

$$
r_M(X)=r_N(X),\qquad
r_M(X\cup\{e\})=r_N(X)+1-x_{\operatorname{cl}_N(X)}.
$$

We can therefore count extensions without constructing their bases or testing ten-element matroids for isomorphism.

## Counting the remaining hyperplane choices

First assign the flats of rank at most $r-2$, propagating the modular-cut rules after every choice. The only undecided variables left are hyperplanes, the flats of rank $r-1$.

Distinct hyperplanes span $E(N)$. They form a modular pair exactly when their intersection has rank $r-2$. Once the flats of lower rank have been assigned, two still-undecided hyperplanes with such an intersection cannot both be selected. Make a graph whose vertices are the undecided hyperplanes, with an edge for each such pair. The remaining valid choices are exactly its independent sets.

For example, start with $U_{2,3}$, the rank-two uniform matroid on three elements, and add one element. If the empty flat is false, the graph on its three rank-one flats is a triangle. The triangle has four independent sets: one uniform extension and three extensions in which the new element is parallel to an old one. If the empty flat is true, the new element is a loop. The five rank-preserving labeled extensions are counted as $4+1$.

For the symmetry calculation below, let $g$ be an automorphism of $N$ and identify flats in the same $g$-orbit before branching. A true orbit selects all its flats. Propagation handles constraints within an orbit. The worker is then:

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

Let $i(G)$ count the independent sets of a graph $G$, including the empty set, and let $N_G[v]$ contain $v$ and its neighbors. The graph counter uses $i(G)=i(G-v)+i(G-N_G[v])$. It also removes isolated vertices, splits components, uses Fibonacci counts for paths and cycles, and caches repeated vertex sets. Thus each assignment to flats of rank at most $r-2$ contributes the exact number of cuts extending it, rather than one branch per completed matroid.

For rank-five parents on nine elements, there are at most $\binom94=126$ hyperplanes and at most $\sum_{j=0}^{3}\binom9j=130$ flats of rank at most three. These are bounds on the sizes of the local problems, not on their running time.

## Burnside's lemma

Counting extensions parent by parent does not yet count ten-element matroids up to isomorphism. It counts matroids with a distinguished element $e$. A matroid can have several orbits of elements, so neither summing the extension counts up to parent automorphisms nor dividing such a sum by ten gives the answer.

Write $u_{10,r}$ for the number of rank-$r$ matroids on ten elements up to isomorphism, and $S_{10}$ for the permutations of the ten labels. Burnside's lemma gives

$$
u_{10,r}=\frac{1}{10!}\sum_{\sigma\in S_{10}}
\#\{\text{labeled rank-}r\text{ matroids fixed by }\sigma\}.
$$

The fixed count depends only on the permutation's cycle type, leaving 42 cases.

30 of those types have a fixed point. Choose one such point $e$ and delete it. If $e$ is not a coloop, the deletion is a rank-$r$ matroid $N$ on nine elements, and the restricted permutation is an automorphism $g$ of $N$. The fixed extensions are exactly the nonempty modular cuts of $N$ invariant under $g$.

If $e$ is a coloop, its deletion has rank $r-1$ and has exactly one coloop extension. We generate one representative of each parent class and account for its different labelings and automorphisms when adding these contributions. At rank five, the rank-four coloop parents are dual to rank-five parents and have the same automorphism groups.

For speed, we count one representative per conjugacy class in a parent's automorphism group and multiply by the class size. Automorphisms with the same cycle type on the nine elements can act differently on that parent's flats.

The other 12 types have no fixed point to delete. For one permutation $\sigma$ of each type, make one Boolean variable per orbit of $r$-subsets. A true variable selects every subset in that orbit as a basis. Require at least one basis and impose the basis-exchange axiom: for selected $B,C$ and $a\in B\setminus C$, some $b\in C\setminus B$ must make $(B\setminus\lbrace a\rbrace)\cup\lbrace b\rbrace$ selected. These assignments are exactly the rank-$r$ matroids fixed by $\sigma$, so an exact model counter gives the fixed count.

The rank-five case with cycle type $(2,2,2,2,2)$, five transpositions, has 126 variables. We split it into 256 disjoint assignments and solve them in parallel. A single formula for the basis-exchange axiom could also describe permutations with fixed points, but the identity permutation would leave all $\binom{10}{5}=252$ basis variables free. The parent calculation handles that case efficiently.

Together, the two procedures give the fixed count for every permutation type. Burnside's lemma then gives the number of isomorphism classes, without assuming every matroid has the same number of element orbits.

## Result

The expensive stage is the rank-five parent calculation over 190,214 nine-element classes. Parent jobs run independently across cores. Within a job, propagating the modular-cut rules prunes the search and independent-set counting aggregates all remaining hyperplane choices. If $\ell$ variables for flats of rank at most $r-2$ and $h$ hyperplane variables remain, a loose upper bound is $2^{\ell+h}$ times a polynomial in the number of flats. The measured workload is more informative than this bound.

| Rank | Isomorphism classes on ten elements |
|:--|--:|
| 0 | 1 |
| 1 | 10 |
| 2 | 128 |
| 3 | 10,037 |
| 4 | 4,886,380,924 |
| 5 | 3,222,227,959,444 |

Duality gives the counts for ranks 6–10 from ranks 4–0. Summing all ranks gives 3,232,000,741,644 matroids on ten elements up to isomorphism.

The [simple implementation](https://github.com/ainta/matroid-count/tree/main/simple) has a 481-line C++ extension worker and a separate counting driver. The repository also supplies the nine-element parent generator and exact model counter. On a machine with 64 physical cores across two AMD EPYC 9354 processors, the standalone run used 64 workers and took 474 seconds to process all rank-five parents. Counting ranks three through five from the generated nine-element parents took 602 seconds in total. Generating those parents took a further 3.8 seconds.

The implementation reproduces the known rank-three counts 38 on six elements and 108 on seven, and the rank-four counts 940 on eight and 190,214 on nine. At ten elements it agrees with the published rank-three and [rank-four counts](https://doi.org/10.1007/s00454-011-9388-y). The parent catalogue is generated from the empty matroid and agrees with the published nine-element enumeration.

The [reproduction instructions](https://github.com/ainta/matroid-count/blob/main/simple/README.md) give the commands for generating the parents and running this calculation.

GPT-6 Astra helped discover and implement the optimized algorithms used in this calculation.
