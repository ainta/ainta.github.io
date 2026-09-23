---
layout: post
title: "Counting All Matroids on Ten Elements"
date: 2026-09-22
author: ainta
tags: [matroid, algorithms, enumeration]
---

Let $E$ be a finite set. A **matroid** on $E$ is a nonempty family $\mathcal B$ of subsets of $E$, called bases, that satisfies the basis exchange axiom: for $B,C\in\mathcal B$ and $a\in B\setminus C$, there is a $b\in C\setminus B$ such that $(B\setminus\{a\})\cup\{b\}\in\mathcal B$. All bases have the same size, called the rank of the matroid.

We count matroids on ten elements up to isomorphism. There are [$383\,172$ matroids on nine elements](https://arxiv.org/pdf/math/0702316) up to isomorphism. For ten elements, we find **$\mathbf{3\,232\,000\,741\,644}$**. Listing one representative of each class would require trillions of objects. We count single-element extensions of the nine-element matroids and use Burnside's lemma to obtain the number of isomorphism classes.

## Extensions as modular cuts

Let $N$ be a matroid of rank $r$ on nine elements, with ground set $E(N)$. For $X\subseteq E(N)$, the rank $r_N(X)$ is the largest value of $\lvert B\cap X\rvert$ over bases $B$ of $N$. The closure $\operatorname{cl}_N(X)$ is the set of elements whose addition to $X$ does not increase its rank. A *flat* is a set equal to its closure.

Two flats $F_1$ and $F_2$ form a *modular pair* when

$$
r_N(F_1)+r_N(F_2)=r_N(F_1\cup F_2)+r_N(F_1\cap F_2).
$$

A *modular cut* $\mathcal C$ of $N$ is a set of flats satisfying both conditions:

1. It is an up-set: if $F\in\mathcal C$ and $F\subseteq F'$ for a flat $F'$, then $F'\in\mathcal C$.
2. If $F_1,F_2\in\mathcal C$ form a modular pair, then $F_1\cap F_2\in\mathcal C$.

Let $M$ be a single-element extension of $N$ by an element $e$, so $M\setminus e=N$. Consider the flats $F$ of $N$ for which $e\in\operatorname{cl}_M(F)$. [Crapo (1965)](https://nvlpubs.nist.gov/nistpubs/jres/69B/jresv69Bn1-2p55_A1b.pdf) proved that these flats form a modular cut and that each modular cut determines a unique extension on $E(N)\cup\{e\}$.

Every nonempty modular cut contains $E(N)$, since it is an up-set. For its extension, $e\in\operatorname{cl}_M(E(N))$, so the rank remains $r$. The empty cut adds a coloop, an element contained in every basis, and raises the rank to $r+1$.

## Counting modular cuts

We count the nonempty modular cuts of $N$ by assigning each flat of rank at most $r-2$ to the cut or its complement, one at a time. After each assignment, we propagate every inclusion or exclusion forced by the up-set and modular-pair conditions and discard inconsistent assignments. Once these flats are assigned, only hyperplanes, the flats of rank $r-1$, can remain undecided.

Distinct hyperplanes $H$ and $K$ have union of rank $r$ and form a modular pair exactly when $r_N(H\cap K)=r-2$. If both remain undecided, their intersection is excluded; otherwise the up-set condition would have selected both. Any condition with only one undecided hyperplane is already satisfied; otherwise propagation would have fixed it. Thus the remaining choices are the independent sets of a graph $G$ whose vertices are undecided hyperplanes and whose edges join pairs with intersection rank $r-2$.

For each consistent assignment to the flats of rank at most $r-2$, count the independent sets of its graph $G$. The sum is the number of nonempty modular cuts of $N$.

## Burnside's lemma

Counting extensions up to automorphisms of $N$ counts matroids with a distinguished element $e$. The number of element orbits varies between matroids, so dividing this count by ten does not give the number of isomorphism classes.

Let $u_{10,r}$ be the number of rank-$r$ matroids on ten elements up to isomorphism. Applying Burnside's lemma to the permutations of the ten labels gives

$$
u_{10,r}=\frac{1}{10!}\sum_{\sigma\in S_{10}}
\#\{\text{labeled rank-}r\text{ matroids fixed by }\sigma\}.
$$

The number of fixed matroids depends only on the permutation's cycle type. There are 42 cycle types in $S_{10}$; 30 have a fixed point.

Let $M$ be a rank-$r$ matroid fixed by a permutation $\sigma$ with a fixed point $e$. If $e$ is not a coloop, then $N=M\setminus e$ has rank $r$. The restriction $g$ of $\sigma$ to $E(N)$ is an automorphism of $N$. The extensions fixed by $\sigma$ correspond exactly to the nonempty modular cuts of $N$ invariant under $g$.

To count these cuts, run the same search with one variable for each orbit of flats under $g$. Selecting an orbit selects all its flats. If an undecided hyperplane orbit contains two hyperplanes whose intersection has rank $r-2$, that orbit cannot be selected. Form a graph on the remaining undecided hyperplane orbits. Join two orbits if a hyperplane from each has intersection of rank $r-2$. The independent sets give the remaining $g$-invariant choices.

If $e$ is a coloop, deleting it gives a rank-$(r-1)$ parent $N$, and each automorphism $g$ fixes its unique coloop extension. At rank five, we use duality to account for rank-four coloop parents while processing rank-five parents, since a matroid and its dual have the same automorphism group.

Fix a cycle type $\mu$ on the nine labels other than $e$. Sum the invariant nonempty-cut counts over labeled rank-$r$ parents and their automorphisms $g$ of type $\mu$; for rank-$(r-1)$ parents, add one coloop extension per such $g$. This counts pairs $(M,\sigma)$ in which $M$ is a labeled rank-$r$ matroid on ten elements and $\sigma$ fixes both $M$ and the chosen label $e$, with restriction $g$ of type $\mu$. Divide by the number of permutations of type $\mu$ in $S_9$ to obtain the fixed count for one ten-label permutation of type $\mu\cup(1)$.

We use one representative $N$ of each parent isomorphism class. After summing over its automorphisms of type $\mu$, we multiply by $\frac{9!}{\lvert\operatorname{Aut}(N)\rvert}$, the number of labelings of $N$. Conjugate automorphisms of $N$ fix the same number of extensions, so we count one per conjugacy class and multiply by its size.

The other 12 cycle types have no fixed point. For one permutation $\sigma$ of each type, create a Boolean variable for each orbit of $r$-element subsets. A true variable selects every subset in its orbit as a basis. Require at least one basis and impose the basis exchange axiom. The satisfying assignments correspond exactly to the rank-$r$ matroids fixed by $\sigma$. An exact model counter counts them.

The same construction applies to permutations with fixed points. For the identity at rank five, it would use $\binom{10}{5}=252$ variables, one for each potential basis. The calculation through nine-element matroids handles that case.

## Result

At rank five, the extension calculation processes the $190\,214$ isomorphism classes of rank-five matroids on nine elements, independently across cores. Each flat is the closure of an independent set of its rank, so each parent has at most $\sum_{j=0}^{3}\binom9j=130$ flats of rank at most three and $\binom94=126$ hyperplanes.

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

The implementation uses a C++ program to count modular cuts and [Ganak](https://github.com/meelgroup/ganak) to count invariant families of bases for permutations without a fixed point. We generated the nine-element matroids with the [IC generator from gmou3's repository](https://github.com/gmou3/matroid-generator) and converted its basis output to rank arrays. On two AMD EPYC 9354 processors with 64 physical cores, generation and conversion took 3.8 seconds. Counting ranks three through five with 64 workers took 602 seconds, including 474 seconds for rank five.

Tests reproduce the [published counts](https://arxiv.org/pdf/math/0702316) 38, 108, 940, and $190\,214$ for $(n,r)=(6,3),(7,3),(8,4),(9,4)$, respectively. On ten elements, the computed ranks three and four agree with the [published rank-three](https://doi.org/10.1016/j.jcta.2017.05.001) and [rank-four](https://doi.org/10.1007/s00454-011-9388-y) counts. A [recorded comparison](https://github.com/ainta/matroid-count/blob/main/results/catalogue-comparison.json) checks the generated matroids through nine elements against the [public catalogue](https://zenodo.org/records/6825419).

The [reproduction instructions](https://github.com/ainta/matroid-count#reproduce) give the commands for generating the nine-element matroids and repeating the count.

GPT-6 Astra helped discover and implement the optimized algorithms used in this calculation.
