---
layout: post
title: "Counting All Matroids on Ten Elements"
date: 2026-09-22
author: ainta
tags: [matroid, algorithms, enumeration]
---

## Problem

Count all matroids on a ten-element ground set up to isomorphism. Loops, parallel elements, coloops, and disconnected matroids are included. Isomorphism relabels the ground set; it does not identify a matroid with its dual unless the two are isomorphic.

A direct enumeration would have to list trillions of representatives: rank five alone has 3,222,227,959,444 classes. On nine elements, rank five has only 190,214 classes. We generate the nine-element matroids, count their single-element extensions in aggregate, and use symmetry to recover the ten-element isomorphism classes.

## Extensions as modular cuts

Fix a rank-$r$ matroid $N$ on nine elements and add an element $e$ without increasing the rank. A *flat* is a set equal to its closure. For every flat $F$ of $N$, introduce a Boolean variable

$$
x_F=1\quad\Longleftrightarrow\quad e\in\operatorname{cl}_M(F),
$$

where $M$ is the extension. Thus $x_F$ records whether $F$ spans $e$.

The true flats must be upward closed: if $F\subseteq G$ and $x_F=1$, then $x_G=1$. For each modular pair they also satisfy

$$
x_F=x_G=1\quad\Longrightarrow\quad x_{F\cap G}=1
\qquad\text{when}\qquad
r_N(F)+r_N(G)=r_N(F\cup G)+r_N(F\cap G).
$$

These two rules define a *modular cut*. [Crapo (1965)](https://nvlpubs.nist.gov/nistpubs/jres/69B/jresv69Bn1-2p55_A1b.pdf) showed that nonempty modular cuts are in bijection with rank-preserving extensions. We force $x_{E(N)}=1$ to count the nonempty cuts. The empty cut adds a coloop and raises the rank, so its parent has rank $r-1$.

The cut really determines the whole extension: for $X\subseteq E(N)$,

$$
r_M(X)=r_N(X),\qquad
r_M(X\cup\{e\})=r_N(X)+1-x_{\operatorname{cl}_N(X)}.
$$

We can therefore count extensions without constructing their bases or testing ten-element matroids for isomorphism.

## Counting the remaining hyperplane choices

First assign the flats of rank at most $r-2$, propagating the modular-cut rules after every choice. The only undecided variables left are hyperplanes, the flats of rank $r-1$.

Distinct hyperplanes span $E(N)$. They form a modular pair exactly when their intersection has rank $r-2$. After the other flats have been assigned, two still-undecided hyperplanes with such an intersection cannot both be true. Make a graph whose vertices are the undecided hyperplanes, with an edge for each such pair. The remaining valid choices are exactly its independent sets.

For example, take the three-point line $U_{2,3}$ and add one element. If the empty flat is false, the graph on its three point flats is a triangle. The triangle has four independent sets: one uniform extension and three extensions in which the new element is parallel to an old one. If the empty flat is true, the new element is a loop. The five rank-preserving labeled extensions are counted as $4+1$.

For an automorphism $g$ of $N$, identify flats in the same $g$-orbit before branching. A true orbit selects all its flats. Propagation handles conflicts inside an orbit. The worker is then:

~~~text
CountCuts(N, g):
    make one variable for each g-orbit of flats
    impose upward closure, modular-pair rules, and x_E = true
    propagate

    DFS:
        if an orbit of flats of rank at most r-2 is undecided:
            try true and false, propagate, and add the two answers
        otherwise:
            return the number of independent sets
                   in the graph of undecided hyperplane orbits
~~~

The graph counter branches by $i(G)=i(G-v)+i(G-N[v])$. It also removes isolated vertices, splits components, uses Fibonacci counts for paths and cycles, and caches repeated vertex masks. Thus each assignment to flats of rank at most $r-2$ contributes the exact number of cuts extending it, rather than one branch per completed matroid.

For rank-five parents on nine elements, there are at most $\binom94=126$ hyperplanes and at most $\sum_{j=0}^{3}\binom9j=130$ flats of rank at most three. These bounds describe the local instances, not a polynomial-time guarantee. In particular, the familiar $3^{h/3}$ bound for maximal independent sets is not a bound for counting *all* independent sets here.

## Burnside's lemma

Counting extensions parent by parent does not yet count ten-element matroids up to isomorphism. It counts matroids with a distinguished element $e$. A matroid can have several orbits of elements, so neither summing the extension counts up to parent automorphisms nor dividing such a sum by ten gives the answer.

Write $u_{10,r}$ for the number of rank-$r$ matroids on ten elements up to isomorphism. Burnside's lemma gives

$$
u_{10,r}=\frac{1}{10!}\sum_{\sigma\in S_{10}}
\#\{\text{labeled rank-}r\text{ matroids fixed by }\sigma\}.
$$

The fixed count depends only on the permutation's cycle type, leaving 42 cases.

Thirty of those types have a fixed point. Choose one such point $e$ and delete it. If $e$ is not a coloop, the deletion is a rank-$r$ matroid $N$ on nine elements, and the restricted permutation is an automorphism $g$ of $N$. The fixed extensions are exactly the nonempty modular cuts of $N$ invariant under $g$. If $e$ is a coloop, its deletion has rank $r-1$ and has exactly one coloop extension. We generate one representative of each parent class and account for its different labelings and automorphisms when adding these contributions. At rank five, duality pairs the rank-four coloop parents with the rank-five parents.

For speed, we count one representative per conjugacy class in a parent's automorphism group and multiply by the class size. Automorphisms with the same cycle type on the nine elements can act differently on that parent's flats.

The other twelve types have no fixed point to delete. For one permutation $\sigma$ of each type, make one Boolean variable per orbit of $r$-subsets. A true variable selects every subset in that orbit as a basis. Require at least one basis and impose basis exchange: for selected $B,C$ and $a\in B\setminus C$, some $b\in C\setminus B$ must make $(B\setminus\lbrace a\rbrace)\cup\lbrace b\rbrace$ selected. These assignments are exactly the rank-$r$ matroids fixed by $\sigma$, so an exact #SAT solver gives the fixed count.

The largest of these instances, cycle type $(2,2,2,2,2)$ at rank five, is split into 256 disjoint assignments and solved in parallel. A single formula for the basis-exchange axiom could also describe permutations with fixed points, but the identity permutation would leave all $\binom{10}{5}=252$ basis variables free. The parent calculation handles that case efficiently.

Together, the two procedures give the fixed count for every permutation type. Burnside's average then gives the number of isomorphism classes, without assuming every matroid has the same number of element orbits.

## Result

The expensive stage is the rank-five parent calculation over 190,214 nine-element classes. Parent jobs run independently across cores. Within a job, propagating the modular-cut rules prunes the search and independent-set counting aggregates all remaining hyperplane choices. If a job has $\ell$ variables for flats of rank at most $r-2$ and $h$ hyperplane variables, $O(2^{\ell+h}\operatorname{poly}(\lvert\mathcal F(N)\rvert))$ is a loose upper bound. The structural reductions and measured workload explain the running time better than that bound.

| Rank | Unlabeled ten-element matroids |
|:--|--:|
| 3 | 10,037 |
| 4 | 4,886,380,924 |
| 5 | 3,222,227,959,444 |

Ranks above five follow by duality. Ranks zero through two are elementary, and the other-rank contribution is

$$
2(1+10+128+10,037+4,886,380,924)=9,772,782,200.
$$

Adding rank five gives 3,232,000,741,644 matroids on ten elements up to isomorphism.

The [compact implementation](https://github.com/ainta/matroid-count/tree/main/simple) has a 481-line C++ extension worker and a separate counting driver; it shares the nine-element parent generator and exact #SAT solver with the main repository. On a machine with 64 physical cores across two AMD EPYC 9354 processors, the standalone run used 64 workers and took 474 seconds to process all rank-five parents. Counting ranks three through five from the generated nine-element parents took 602 seconds in total. Generating those parents took a further 3.8 seconds.

The same counting path reproduces 38 rank-three matroids on six elements, 108 on seven, 940 rank-four matroids on eight, and 190,214 on nine. At ten elements it reproduces the previously known rank-three and [rank-four count](https://doi.org/10.1007/s00454-011-9388-y). All 42 rank-five fixed-permutation counts match the earlier calculation. The nine-element parents come from a generator run from the empty matroid, rather than from a downloaded catalogue; the full nine-element count is [383,172](https://arxiv.org/pdf/math/0702316).

The [reproduction instructions](https://github.com/ainta/matroid-count/blob/main/simple/README.md) give the commands for generating the parents and running this calculation.
