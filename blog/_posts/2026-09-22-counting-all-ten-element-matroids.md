---
layout: post
title: "Counting All Ten-Element Matroids Without Listing Them"
date: 2026-09-22
author: ainta
tags: [matroid, algorithms, enumeration]
---

## Problem

Count all matroids on a ten-element ground set **up to isomorphism**. Loops, parallel elements, coloops, and disconnected matroids are included. Isomorphism relabels the ground set; it does not identify a matroid with its dual unless the two are isomorphic.

The answer is

$$
\boxed{3,232,000,741,644}.
$$

Rank five alone contributes **3,222,227,959,444** classes. Listing ten-element representatives would make the output itself enormous. The useful smaller object is a matroid on nine elements: there are **190,214** rank-five parent classes. We generate those parents, count all their single-element extensions at once, and recover the isomorphism classes by symmetry.

All rank-preserving extensions use the **same counter for every parent**. The two counting subroutines have distinct jobs:

~~~text
permutation fixes an element  -> delete it; count extensions of a nine-element parent
permutation fixes no element -> count invariant basis families by exact #SAT
~~~

There are 30 cycle types of the first kind and 12 of the second kind. That split is what makes the identity permutation, the largest case, manageable.

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

## Getting the 42 fixed counts for Burnside's lemma

Counting extensions parent by parent does **not** yet count ten-element matroids up to isomorphism. It counts matroids with a distinguished element $e$. A matroid can have several orbits of elements, so neither summing the extension counts up to parent automorphisms nor dividing such a sum by ten gives the answer.

Let $F_{10,r}(\lambda)$ be the number of labeled rank-$r$ matroids fixed by a permutation of cycle type $\lambda\vdash10$. Set

$$
z_\lambda=\prod_j j^{a_j(\lambda)}a_j(\lambda)!,
\qquad
u_{10,r}=\sum_{\lambda\vdash10}\frac{F_{10,r}(\lambda)}{z_\lambda},
$$

where $a_j(\lambda)$ is the number of cycles of length $j$. This is Burnside's lemma grouped by cycle type.

Thirty cycle types have a fixed point. For $\lambda=\mu\cup(1)$, delete such a point $e$ from each fixed matroid. The result is a nine-element parent $N$, and the remaining permutation is an automorphism $g$ of $N$. Let $c(N,g)$ count the $g$-invariant nonempty modular cuts. Each parent class has $9!/\lvert\operatorname{Aut}(N)\rvert$ labeled versions, so we weight its automorphisms of type $\mu$ by that number and by $c(N,g)$.

We must also count extensions in which $e$ is a coloop. Their parents have rank $r-1$, and each such parent contributes one extension for every automorphism of type $\mu$. Let $T_{r,\mu}$ be the sum of these weighted cut and coloop contributions. At rank five, duality pairs the rank-four coloop parents with the rank-five cut parents, so the worker adds a $+1$ to each $c(N,g)$.

There are $9!/z_\mu$ permutations of type $\mu$, all with the same fixed count. Therefore the labeled fixed count for one such permutation is

$$
\boxed{F_{10,r}\bigl(\mu\cup(1)\bigr)
=\frac{z_\mu T_{r,\mu}}{9!}.}
$$

Only one representative per conjugacy class **inside** $\operatorname{Aut}(N)$ needs its cuts counted; multiply by that class's size. Two automorphisms with the same ground-set cycle type need not act alike on the flats of a particular parent.

The other twelve types have no fixed point to delete. For one permutation $\sigma$ of each type, make one Boolean variable per orbit of $r$-subsets. A true variable selects every subset in that orbit as a basis. Require at least one basis and impose basis exchange: for selected $B,C$ and $a\in B\setminus C$, some $b\in C\setminus B$ must make $(B\setminus\lbrace a\rbrace)\cup\lbrace b\rbrace$ selected. These assignments are exactly the rank-$r$ matroids fixed by $\sigma$, so an exact #SAT solver gives the fixed count.

The largest of these instances, cycle type $(2,2,2,2,2)$ at rank five, is split into 256 disjoint assignments and solved in parallel. A single formula for the basis-exchange axiom could also describe permutations with fixed points, but the identity permutation would leave all $\binom{10}{5}=252$ basis variables free. The parent calculation handles that case efficiently.

Together, the two procedures supply every fixed-permutation count in Burnside's sum. Its division by $z_\lambda$ removes the labels, without assuming every matroid has the same number of element orbits.

## Runtime and result

The expensive stage is the rank-five parent calculation over **190,214** nine-element classes. Parent jobs run independently across cores. Within a job, propagating the modular-cut rules prunes the search and independent-set counting aggregates all remaining hyperplane choices. If a job has $\ell$ variables for flats of rank at most $r-2$ and $h$ hyperplane variables, $O(2^{\ell+h}\operatorname{poly}(\lvert\mathcal F(N)\rvert))$ is a loose upper bound. The structural reductions and measured workload explain the running time better than that bound.

| Rank | Unlabeled ten-element matroids |
|:--|--:|
| 3 | 10,037 |
| 4 | 4,886,380,924 |
| 5 | 3,222,227,959,444 |

Ranks above five follow by duality. Ranks zero through two are elementary, and the other-rank contribution is

$$
2(1+10+128+10,037+4,886,380,924)=9,772,782,200.
$$

Adding rank five gives **3,232,000,741,644** matroids on ten elements up to isomorphism.

The [compact implementation](https://github.com/ainta/matroid-count/tree/main/simple) has a 481-line C++ extension worker and a separate counting driver; it shares the nine-element parent generator and exact #SAT solver with the main repository. On a machine with 64 physical cores across two AMD EPYC 9354 processors, the standalone run used 64 workers and took **474 seconds** to process all rank-five parents. Counting ranks three through five from the generated nine-element parents took **602 seconds** in total. Generating those parents took a further **3.8 seconds**.

The same counting path reproduces $u_{6,3}=38$, $u_{7,3}=108$, $u_{8,4}=940$, and $u_{9,4}=190,214$. At ten elements it reproduces the previously known rank-three and [rank-four count](https://doi.org/10.1007/s00454-011-9388-y). All 42 rank-five fixed-permutation counts match the earlier calculation. The nine-element parents come from a generator run from the empty matroid, rather than from a downloaded catalogue; the full nine-element count is [383,172](https://arxiv.org/pdf/math/0702316).

To generate the parents and rerun the compact calculation:

~~~bash
python3 scripts/setup.py
make -j4 build/compact_worker build/colex_to_rank vendor/matroid-generator/build/IC
python3 -S scripts/generate_parents.py --through 9 --jobs 64 \
    --out runs/compact/parents
python3 -S simple/count.py \
    --parents runs/compact/parents \
    --workdir runs/compact/count --jobs 64
~~~
