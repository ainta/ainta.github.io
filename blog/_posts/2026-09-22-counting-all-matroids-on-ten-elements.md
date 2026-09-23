---
layout: post
title: "Counting All Matroids on Ten Elements"
date: 2026-09-22
author: ainta
tags: [matroid, algorithms, enumeration]
---

Let $E$ be a finite set. A **matroid** on $E$ is a nonempty family $\mathcal B$ of subsets of $E$, all of the same size, called bases. The bases satisfy the basis exchange axiom: for $B,C\in\mathcal B$ and $a\in B\setminus C$, there is a $b\in C\setminus B$ such that

$$
(B\setminus\{a\})\cup\{b\}\in\mathcal B.
$$

The common size of the bases is the rank. We count matroids on ten elements up to isomorphism.

There are [$383\,172$ matroids on nine elements](https://arxiv.org/pdf/math/0702316) up to isomorphism. For ten elements, we find **$\mathbf{3\,232\,000\,741\,644}$**. Listing one representative of each class would require trillions of objects. We count single-element extensions of the nine-element matroids and use Burnside's lemma to obtain the number of isomorphism classes.

## Extensions as modular cuts

Let $N$ be a matroid of rank $r$ on nine elements, with ground set $E(N)$. Let $M$ be a single-element extension of $N$: $M\setminus e=N$. For $X\subseteq E(N)$, the rank $r_N(X)$ is the largest value of $\lvert B\cap X\rvert$ over bases $B$ of $N$. The closure $\operatorname{cl}_N(X)$ is the set of elements whose addition to $X$ does not increase its rank. A *flat* is a set equal to its closure.

The extension $M$ determines a set of flats of $N$: those $F$ for which $e\in\operatorname{cl}_M(F)$. This set always has two properties. First, it is an up-set: if it contains $F$, it contains every flat $G$ with $F\subseteq G$.

Two flats $F$ and $G$ form a *modular pair* when

$$
r_N(F)+r_N(G)=r_N(F\cup G)+r_N(F\cap G).
$$

Second, if the set contains both flats of a modular pair, it also contains their intersection. A *modular cut* is, by definition, a set of flats with these two properties. Thus the set associated with $M$ is a modular cut. [Crapo (1965)](https://nvlpubs.nist.gov/nistpubs/jres/69B/jresv69Bn1-2p55_A1b.pdf) proved the converse: every modular cut determines a unique extension.

For counting, assign a variable $x_F$ to each flat $F$ of $N$. Set $x_F=1$ when $F$ belongs to the modular cut, and $x_F=0$ otherwise. For $X\subseteq E(N)$, the corresponding extension $M$ has rank function

$$
r_M(X)=r_N(X),\qquad
r_M(X\cup\{e\})=r_N(X)+1-x_{\operatorname{cl}_N(X)}.
$$

Taking $X=E(N)$ shows that $M$ has rank $r$ exactly when $x_{E(N)}=1$. Since the cut is an up-set, this is equivalent to the cut being nonempty. The empty cut adds a coloop, an element contained in every basis, and gives rank $r+1$. We require $x_{E(N)}=1$ in the count below.

## Counting modular cuts

Assign the flats of rank at most $r-2$ one at a time, propagating the conditions for a modular cut after each choice. Discard an assignment if propagation finds a contradiction. After these assignments, only hyperplanes, the flats of rank $r-1$, can remain undecided. We count their choices using independent sets of a graph.

Distinct hyperplanes $H$ and $K$ have $r_N(H\cup K)=r$. They form a modular pair exactly when $r_N(H\cap K)=r-2$. If both remain undecided, then $x_{H\cap K}=0$. Otherwise, the up-set condition would have selected both hyperplanes. The modular cut therefore cannot contain both $H$ and $K$.

Let $G$ have one vertex for each undecided hyperplane. Join $H$ and $K$ when $r_N(H\cap K)=r-2$. After propagation, every remaining condition on the undecided hyperplanes has this form. Each independent set of $G$ determines exactly one way to select the remaining hyperplanes.

For each consistent assignment to the flats of rank at most $r-2$, count the independent sets of its graph $G$. The sum is the number of nonempty modular cuts of $N$.

## Burnside's lemma

Counting extensions up to automorphisms of $N$ counts matroids with a distinguished element $e$. The number of element orbits varies between matroids, so dividing this count by ten does not give the number of isomorphism classes.

Let $u_{10,r}$ be the number of rank-$r$ matroids on ten elements up to isomorphism. Applying Burnside's lemma to the permutations of the ten labels gives

$$
u_{10,r}=\frac{1}{10!}\sum_{\sigma\in S_{10}}
\#\{\text{labeled rank-}r\text{ matroids fixed by }\sigma\}.
$$

The number of fixed matroids depends only on the permutation's cycle type. There are 42 cycle types in $S_{10}$; 30 have a fixed point.

For a permutation $\sigma$ with a fixed point, choose a fixed label $e$. If $e$ is not a coloop, then $N=M\setminus e$ has rank $r$. The restriction $g$ of $\sigma$ to $E(N)$ is an automorphism of $N$. The extensions fixed by $\sigma$ correspond exactly to the nonempty modular cuts of $N$ invariant under $g$.

To count these cuts, use one variable for each orbit of flats under $g$. The corresponding graph has one vertex for each undecided orbit of hyperplanes. Two vertices are adjacent if some pair of hyperplanes in their orbits has intersection of rank $r-2$. Propagation also handles pairs within one orbit.

If $e$ is a coloop, $N$ has rank $r-1$ and gives one extension. At rank five, each such rank-four matroid is dual to a rank-five matroid on nine elements. Duality preserves its automorphism group.

For a fixed label $e$, each isomorphism class of $N$ has $\frac{9!}{\lvert\operatorname{Aut}(N)\rvert}$ labelings of the other nine elements. For each cycle type on those nine elements, we sum the extension counts over these labelings and the relevant automorphisms. Dividing by the number of permutations of that type gives the number of matroids fixed by one permutation. Within $\operatorname{Aut}(N)$, we evaluate one representative per conjugacy class and multiply by its size. Automorphisms with the same cycle type on nine elements can act differently on the flats of $N$.

The other 12 cycle types have no fixed point. For one permutation $\sigma$ of each type, create a Boolean variable for each orbit of $r$-element subsets. A true variable selects every subset in its orbit as a basis. Require at least one basis and impose the basis exchange axiom. The satisfying assignments correspond exactly to the rank-$r$ matroids fixed by $\sigma$. An exact model counter counts them.

The same construction applies to permutations with fixed points. For the identity, it would leave all $\binom{10}{5}=252$ basis variables free. The calculation through nine-element matroids handles that case.

## Result

The rank-five extension calculation processes one representative of each of the $190\,214$ isomorphism classes on nine elements. These computations run independently across cores. Each matroid has at most $\sum_{j=0}^{3}\binom9j=130$ flats of rank at most three and $\binom94=126$ hyperplanes.

Among permutations without a fixed point, cycle type $(2,2,2,2,2)$ gives the largest formula at rank five, with 126 variables. We fix eight variables in all possible ways and solve the resulting 256 formulas separately.

Ranks zero through two follow from counting loops and parallel classes. The extension calculation handles ranks three through five.

| Rank | Isomorphism classes on ten elements |
|:--|--:|
| 0 | 1 |
| 1 | 10 |
| 2 | 128 |
| 3 | $10\,037$ |
| 4 | $4\,886\,380\,924$ |
| 5 | $3\,222\,227\,959\,444$ |

Duality gives the counts for ranks 6–10 from ranks 4–0. The sum over all ranks is $3\,232\,000\,741\,644$ matroids on ten elements up to isomorphism.

The implementation uses a C++ program to count modular cuts and Ganak to count invariant families of bases for permutations without a fixed point. On two AMD EPYC 9354 processors with 64 physical cores, generating the nine-element matroids took 3.8 seconds. Counting ranks three through five with 64 workers took 602 seconds, including 474 seconds for rank five.

Tests reproduce the [published counts](https://arxiv.org/pdf/math/0702316) 38, 108, 940, and $190\,214$ for $(n,r)=(6,3),(7,3),(8,4),(9,4)$, respectively. On ten elements, the computed ranks three and four agree with the [published rank-three](https://doi.org/10.1016/j.jcta.2017.05.001) and [rank-four](https://doi.org/10.1007/s00454-011-9388-y) counts. A [recorded comparison](https://github.com/ainta/matroid-count/blob/main/results/catalogue-comparison.json) checks the generated matroids through nine elements against the [public catalogue](https://zenodo.org/records/6825419).

The [reproduction instructions](https://github.com/ainta/matroid-count#reproduce) give the commands for generating the nine-element matroids and repeating the count.

GPT-6 Astra helped discover and implement the optimized algorithms used in this calculation.
