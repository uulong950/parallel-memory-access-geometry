# Capacity and Obstruction

## 1. Purpose

This document develops the second foundational layer of Parallel Memory-Access Geometry (PMAG):

$$
\boxed{ \text{capacity} \qquad\text{and}\qquad \text{obstruction}. }
$$
The central distinction is:

> **Capacity asks how good one access can become under a declared transformation family. Feasibility asks whether one shared transformation can attain the required capacities for all accesses simultaneously.**

These are different problems.

A poor observed mapping may be poor because:

1. the access itself has already lost information;
2. the hardware/resource model or transformation family imposes an unavoidable ceiling;
3. the chosen transformation fails to attain that ceiling;
4. several individually solvable accesses are incompatible under one shared transformation.

The first three are single-access questions.

The fourth is a multi-access obstruction.

RM-001 — Affine $GF(2)$ Bank Geometry — is the first PMAG reference model in which these distinctions can be made exact.

---

# 2. Capacity Is Relative to a Model Boundary

A PMAG optimization instance is declared by

$$
(E,\;A,\;H,\;\mathcal T,\;\Phi),
$$
where

- $E$ is the execution domain;
- $A$ is the access map;
- $H$ is the hardware-resource model;
- $\mathcal T$ is the admissible transformation family;
- $\Phi$ is the objective.

For a maximization objective, define

$$
\boxed{ \mathrm{Cap}(A,H,\mathcal T,\Phi) = \max_{T\in\mathcal T} \Phi(H\circ T\circ A). }
$$
For a minimization objective,

$$
\boxed{ \mathrm{Cap}(A,H,\mathcal T,\Phi) = \min_{T\in\mathcal T} \Phi(H\circ T\circ A). }
$$
Therefore a statement such as

> "this layout is optimal"

is incomplete unless the access set, target hardware model, admissible transformations, and objective are specified.

Capacity is always model-relative.

---

# 3. Ideal Value, Capacity, and Candidate Value

Suppose a maximization objective has an external ideal value

$$
\Phi_{\mathrm{ideal}}.
$$
For a candidate transformation $T$, write

$$
\Phi_T = \Phi(H\circ T\circ A)
$$
and

$$
\Phi^\* = \mathrm{Cap}(A,H,\mathcal T,\Phi).
$$
Then

$$
\Phi_T \le \Phi^\* \le \Phi_{\mathrm{ideal}}.
$$
This separates two gaps:

$$
\boxed{ \Phi_{\mathrm{ideal}}-\Phi^\* }
$$
is unavoidable under the declared model and transformation family, while

$$
\boxed{ \Phi^\*-\Phi_T }
$$
is avoidable candidate loss.

For minimization objectives the inequalities reverse, but the conceptual separation is the same.

---

# 4. A General Linear Capacity Problem

A large class of PMAG reference models leads to an effective linear operator of the form

$$
\boxed{ M(X)=A+LXR, }
$$
where all maps are linear over a field $\mathbb F$.

Let

$$
A:\mathbb F^n\rightarrow\mathbb F^m,
$$
$$
R:\mathbb F^n\rightarrow\mathbb F^q,
$$
$$
X:\mathbb F^q\rightarrow\mathbb F^p,
$$
$$
L:\mathbb F^p\rightarrow\mathbb F^m.
$$
The variable $X$ is the transformation parameter.

The PMAG rank-capacity problem is

$$
\boxed{ \max_X \mathrm{rank}(A+LXR). }
$$
RM-001 is a specialization of this problem.

---

# 5. Lemma 1 — Free-Corner Matrix Completion

Let

$$
\mathcal M(X) = \begin{bmatrix} X & A\\ B & C \end{bmatrix},
$$
where $X\in\mathbb F^{p\times q}$ is arbitrary and all other blocks are fixed.

Then

$$
\boxed{ \max_X \mathrm{rank} \begin{bmatrix} X&A\\ B&C \end{bmatrix} = \min \left\{ p+\mathrm{rank}[B\ C], \; q+\mathrm{rank} \begin{bmatrix} A\\ C \end{bmatrix} \right\}. }
$$
## Proof

The two upper bounds are immediate.

The top $p$ rows can increase the rank of the fixed bottom block by at most $p$, so

$$
\mathrm{rank}\mathcal M(X) \le p+\mathrm{rank}[B\ C].
$$
Likewise, the left $q$ columns can increase the rank of the fixed right block by at most $q$, so

$$
\mathrm{rank}\mathcal M(X) \le q+\mathrm{rank} \begin{bmatrix} A\\ C \end{bmatrix}.
$$
It remains to show that the smaller bound can always be attained.

Let

$$
c=\mathrm{rank}C.
$$
Using invertible row operations on the bottom block and invertible column operations on the right block, reduce $C$ to

$$
\begin{bmatrix} I_c&0\\ 0&0 \end{bmatrix}.
$$
Using those pivot rows and columns, eliminate the corresponding components of $A$ and $B$.

Because $X$ is completely free, additions to the free block caused by these operations do not restrict the set of values attainable by $X$.

The matrix is therefore equivalent, for purposes of maximum rank, to

$$
\begin{bmatrix} X'&0&A_0\\ 0&I_c&0\\ B_0&0&0 \end{bmatrix},
$$
with $X'$ still arbitrary.

Let

$$
a=\mathrm{rank}A_0, \qquad b=\mathrm{rank}B_0.
$$
By further invertible operations confined to the corresponding row and column groups, reduce $A_0$ and $B_0$ to identity blocks of ranks $a$ and $b$.

Those fixed pivots contribute

$$
c+a+b
$$
to the rank.

After removing their pivot rows and columns, the only remaining useful part of the free block has size

$$
(p-a)\times(q-b).
$$
Choose that free subblock to contain an identity matrix of size

$$
\min(p-a,q-b).
$$
Hence

$$
\max_X\mathrm{rank}\mathcal M(X) = c+a+b+\min(p-a,q-b).
$$
Now

$$
\mathrm{rank}[B\ C] = c+b
$$
and

$$
\mathrm{rank} \begin{bmatrix} A\\ C \end{bmatrix} = c+a.
$$
Therefore

$$
c+a+b+\min(p-a,q-b) = \min\{p+c+b,\;q+c+a\},
$$
which is exactly

$$
\min \left\{ p+\mathrm{rank}[B\ C], \; q+\mathrm{rank} \begin{bmatrix} A\\ C \end{bmatrix} \right\}.
$$
Thus the bound is attainable. ∎

---

# 6. Theorem 1 — Exact Rank Capacity of $A+LXR$

For linear maps over any field $\mathbb F$,

$$
\boxed{ \max_X \mathrm{rank}(A+LXR) = \min \left\{ \mathrm{rank}[A\ L], \; \mathrm{rank} \begin{bmatrix} A\\ R \end{bmatrix} \right\}. }
$$
This is the basic single-access capacity theorem for the linear PMAG family $A+LXR$.

## Proof

Let

$$
\ell=\mathrm{rank}L, \qquad r=\mathrm{rank}R.
$$
Choose invertible basis changes so that

$$
L \sim \begin{bmatrix} I_\ell&0\\ 0&0 \end{bmatrix}
$$
and

$$
R \sim \begin{bmatrix} I_r&0\\ 0&0 \end{bmatrix}.
$$
More explicitly, there exist invertible matrices $U,V,W,Z$ such that

$$
ULV = \begin{bmatrix} I_\ell&0\\ 0&0 \end{bmatrix},
$$
$$
WRZ = \begin{bmatrix} I_r&0\\ 0&0 \end{bmatrix}.
$$
Multiplication by invertible matrices does not change rank.

As $X$ ranges over all matrices of its declared size,

$$
V^{-1}XW^{-1}
$$
also ranges over all such matrices.

Therefore the transformed family

$$
U(A+LXR)Z
$$
differs from the fixed matrix

$$
UAZ
$$
only by an arbitrary $\ell\times r$ top-left block.

The problem is therefore exactly a free-corner completion problem.

By Lemma 1, its maximum rank equals the minimum of:

1. the rank allowed by appending the column space of $L$;
2. the rank allowed by appending the row information of $R$.

Undoing the invertible basis changes gives

$$
\boxed{ \max_X \mathrm{rank}(A+LXR) = \min \left\{ \mathrm{rank}[A\ L], \; \mathrm{rank} \begin{bmatrix} A\\ R \end{bmatrix} \right\}. }
$$
∎

---

# 7. Interpretation of Theorem 1

The theorem gives two independent ceilings.

Define

$$
\rho_{\mathrm{out}} = \mathrm{rank}[A\ L]
$$
and

$$
\rho_{\mathrm{in}} = \mathrm{rank} \begin{bmatrix} A\\ R \end{bmatrix}.
$$
Then

$$
\boxed{ \rho^\* = \min(\rho_{\mathrm{out}},\rho_{\mathrm{in}}). }
$$
## Output-side ceiling

Because

$$
\mathrm{Im}(A+LXR) \subseteq \mathrm{Im}A+\mathrm{Im}L,
$$
the effective map cannot use resource directions outside

$$
\mathrm{Im}A+\mathrm{Im}L.
$$
Therefore

$$
\rho_{\mathrm{out}} = \dim(\mathrm{Im}A+\mathrm{Im}L)
$$
is an output-resource span ceiling.

## Input-side ceiling

If a domain direction is simultaneously invisible to $A$ and $R$, then no choice of $X$ can make it visible:

$$
v\in\ker A\cap\ker R \quad\Longrightarrow\quad (A+LXR)v=0
$$
for every $X$.

Hence

$$
\ker A\cap\ker R
$$
is an unavoidable kernel.

Since

$$
\mathrm{rank} \begin{bmatrix} A\\ R \end{bmatrix} = n-\dim(\ker A\cap\ker R),
$$
the input-side ceiling is exactly the amount of domain information not destroyed before the transformation can act.

## Completeness

The non-trivial content of Theorem 1 is that these two obvious ceilings are the only single-access obstructions in the family $A+LXR$.

There is no additional hidden rank obstruction.

---

# 8. Corollary 1 — Canonical Affine-Shear Capacity

In the canonical RM-001 model,

$$
M(P)=C+PR.
$$
Set

$$
A=C, \qquad L=I, \qquad X=P.
$$
Then

$$
\mathrm{rank}[C\ I]=n
$$
in the square $n$-dimensional output case, and Theorem 1 gives

$$
\boxed{ \max_P \mathrm{rank}(C+PR) = \mathrm{rank} \begin{bmatrix} C \\ R \end{bmatrix} }
$$
Equivalently,

$$
\boxed{ \max_P \mathrm{rank}(C+PR) = n-\dim(\ker C\cap\ker R) }
$$
Thus the common kernel

$$
\ker C\cap\ker R
$$
is the exact access-intrinsic obstruction in the square canonical model.

---

# 9. Corollary 2 — Hardware-Aware RM-001 Rank Capacity

RM-001 uses

$$
M(P) = B_rR+B_cC+B_cPR.
$$
Set

$$
A=B_rR+B_cC, \qquad L=B_c, \qquad X=P.
$$
Theorem 1 gives

$$
\max_P\mathrm{rank}M(P) = \min \left\{ \mathrm{rank}[B_rR+B_cC\ \ B_c], \; \mathrm{rank} \begin{bmatrix} B_rR+B_cC\\ R \end{bmatrix} \right\}.
$$
The first term simplifies because the columns of $B_cC$ already lie in the column span of $B_c$:

$$
\mathrm{rank}[B_rR+B_cC\ \ B_c] = \mathrm{rank}[B_rR\ \ B_c].
$$
The second term simplifies by subtracting $B_r$ times the lower block $R$ from the upper block:

$$
\mathrm{rank} \begin{bmatrix} B_rR+B_cC\\ R \end{bmatrix} = \mathrm{rank} \begin{bmatrix} B_cC\\ R \end{bmatrix}.
$$
Therefore

$$
\boxed{ \rho^\* = \max_P\mathrm{rank}M(P) = \min \left\{ \mathrm{rank}[B_rR\ \ B_c], \; \mathrm{rank} \begin{bmatrix} R\\ B_cC \end{bmatrix} \right\}. }
$$
This is the exact hardware-aware rank ceiling used by RM-001.

Unlike finite $n=2$ exhaustive verification, the argument above proves the formula for arbitrary compatible dimensions over any field.

---

# 10. Two Exact Single-Access Obstructions in RM-001

Corollary 2 exposes two distinct structural obstructions.

## 10.1 Resource-span obstruction

Define

$$
S_{\mathrm{out}} = \mathrm{Im}(B_rR)+\mathrm{Im}(B_c).
$$
For every $P$,

$$
\mathrm{Im}M(P) \subseteq S_{\mathrm{out}}.
$$
Therefore

$$
\boxed{ \mathrm{rank}M(P) \le \dim S_{\mathrm{out}} = \mathrm{rank}[B_rR\ \ B_c]. }
$$
If the hardware projection exposes only a low-dimensional resource span, no transformation in this family can exceed it.

## 10.2 Domain-information obstruction

Define

$$
K_{\mathrm{in}} = \ker R\cap\ker(B_cC).
$$
If

$$
v\in K_{\mathrm{in}},
$$
then

$$
Rv=0
$$
and

$$
B_cCv=0.
$$
Hence

$$
M(P)v = B_rRv+B_cCv+B_cPRv = 0
$$
for every $P$.

Thus

$$
\boxed{ K_{\mathrm{in}} \subseteq \ker M(P) \qquad \forall P. }
$$
Moreover,

$$
\mathrm{rank} \begin{bmatrix} R\\ B_cC \end{bmatrix} = \ell-\dim K_{\mathrm{in}}.
$$
This is the exact domain-side information ceiling.

## 10.3 Completeness

The rank-capacity theorem states that the best achievable rank is precisely the smaller of these two limits.

Therefore, for this single-access linear family,

$$
\boxed{ \text{resource-span obstruction} + \text{domain-information obstruction} }
$$
completely characterize the rank ceiling.

---

# 11. Three-Layer Loss Decomposition

Let the execution domain have dimension

$$
\ell.
$$
For RM-001 access $i$, define the full logical access map

$$
J_i= \begin{bmatrix} R_i\\ C_i \end{bmatrix}.
$$
Define the access-intrinsic loss

$$
\boxed{ d_i = \ell_i-\mathrm{rank}J_i. }
$$
This counts execution directions already lost by the declared logical access geometry.

Let

$$
\rho_i^\* = \max_P\mathrm{rank}M_i(P)
$$
be the exact hardware/shear-family capacity.

Define the unavoidable hardware/transformation-family loss

$$
\boxed{ \eta_i^\* = \mathrm{rank}J_i-\rho_i^\*. }
$$
For a concrete transformation $P$, define the avoidable candidate loss

$$
\boxed{ e_i(P) = \rho_i^\*-\mathrm{rank}M_i(P). }
$$
Then

$$
\begin{aligned} \mathrm{nullity}M_i(P) &= \ell_i-\mathrm{rank}M_i(P)\\ &= \bigl(\ell_i-\mathrm{rank}J_i\bigr) + \bigl(\mathrm{rank}J_i-\rho_i^\*\bigr) + \bigl(\rho_i^\*-\mathrm{rank}M_i(P)\bigr). \end{aligned}
$$
Therefore

$$
\boxed{ \mathrm{nullity}M_i(P) = d_i+\eta_i^\*+e_i(P). }
$$
This gives an exact three-layer decomposition:

```text
access-intrinsic loss
        +
hardware / transformation-family unavoidable loss
        +
avoidable candidate-transformation loss
```

For a linear $GF(2)$ resource map, the corresponding structural multiplicity is

$$
\boxed{ 2^{d_i+\eta_i^\*+e_i(P)}. }
$$
This is structural multiplicity, not cycle latency.

---

# 12. Capacity Is Not Feasibility

For a single access $i$, define

$$
\rho_i^\* = \max_{T\in\mathcal T} \Phi_i(T)
$$
for the declared objective.

The set of transformations that attain this capacity is

$$
\boxed{ \mathcal O_i = \{ T\in\mathcal T: \Phi_i(T)=\rho_i^\* \}. }
$$
By definition,

$$
\mathcal O_i\neq\varnothing
$$
for each individually solvable finite instance.

A multi-access problem asks for one shared transformation:

$$
\boxed{ T\in\bigcap_i\mathcal O_i. }
$$
Thus

$$
\boxed{ \text{individual capacity} \neq \text{joint feasibility}. }
$$
Each access may have a non-empty optimal set while

$$
\bigcap_i\mathcal O_i = \varnothing.
$$
This is a different kind of obstruction from a single-access capacity ceiling.

---

# 13. Definition — Local Structural Obstruction

A **local structural obstruction** exists when a desired ideal objective value exceeds the capacity of one access under the declared model:

$$
\boxed{ \Phi_{\mathrm{ideal},i} > \mathrm{Cap}_i }
$$
for a maximization objective.

The obstruction is local because it exists even if the access is optimized alone.

Examples include:

- an unavoidable common kernel;
- insufficient hardware-resource span;
- an access width exceeding the distinguishable resource capacity;
- a transformation family too restricted to expose all available information.

The rank-capacity theorem identifies such obstructions exactly for the family $A+LXR$.

---

# 14. Definition — Shared-Transformation Obstruction

A **shared-transformation obstruction** exists when every access can individually attain its capacity, but no single transformation attains all capacities simultaneously:

$$
\boxed{ \mathcal O_i\neq\varnothing \quad\forall i, \qquad \bigcap_i\mathcal O_i=\varnothing. }
$$
This is not a failure of any individual access.

It is an incompatibility among requirements imposed on the shared transformation.

RM-001 contains explicit finite examples where:

- every individual constraint is feasible;
- lower-order compatibility checks can succeed;
- the full set is infeasible.

Therefore multi-access synthesis is a genuine higher-order constraint problem.

---

# 15. Minimal Obstruction Cores

Let $I$ index an infeasible multi-access set.

A subset

$$
J\subseteq I
$$
is an **infeasible core** if

$$
\bigcap_{j\in J}\mathcal O_j = \varnothing.
$$
It is **inclusion-minimal** if

$$
\bigcap_{j\in J}\mathcal O_j = \varnothing
$$
but for every $k\in J$,

$$
\bigcap_{j\in J\setminus\{k\}}\mathcal O_j \neq \varnothing.
$$
Such a core gives a concise explanation:

> these constraints cannot all be satisfied, but removing any one of them restores feasibility.

An inclusion-minimal core is not necessarily minimum-cardinality.

This distinction should always be explicit.

---

# 16. Witnessed Minimality

A strong finite infeasibility artifact can contain:

1. the infeasible core $J$;
2. an exact or solver-certified proof that $J$ is infeasible;
3. for every $k\in J$, a witness transformation

$$
T_k \in \bigcap_{j\in J\setminus\{k\}}\mathcal O_j.
$$
The deletion witnesses prove inclusion minimality independently of the extraction algorithm.

This separates:

$$
\boxed{\text{UNSAT evidence}}
$$
from

$$
\boxed{\text{minimality evidence}}.
$$
Future SAT-backed PMAG models may additionally emit proof objects such as DRAT/LRAT-style certificates when appropriate.

---

# 17. Transformation Visibility

A transformation parameter may contain degrees of freedom that no declared access or hardware projection can observe.

Those degrees of freedom should be quotiented out before synthesis.

For the linear family

$$
M_i(X)=A_i+LXR_i,
$$
two transformation parameters $X$ and $X'$ are observationally equivalent for the declared access family if

$$
\boxed{ L(X-X')R_i=0 \qquad \forall i. }
$$
Only the induced action visible through the compositions $LXR_i$ matters.

This is the **transformation visibility quotient**.

---

# 18. Theorem 2 — Active-Subspace / Resource-Image Quotient

Let

$$
R_i:V_i\rightarrow Y
$$
be a family of right-side access maps and

$$
L:Z\rightarrow W
$$
a fixed left-side hardware projection.

Let

$$
\boxed{ U = \sum_i\mathrm{Im}R_i \subseteq Y }
$$
be the **active transformation-input subspace**, and let

$$
\boxed{ S=\mathrm{Im}L\subseteq W }
$$
be the visible transformation-output subspace.

Then the declared family of effective maps depends on $X:Y\rightarrow Z$ only through the restricted visible map

$$
\boxed{ Q = L\circ X|_U : U\rightarrow S. }
$$
Moreover, every linear map

$$
Q:U\rightarrow S
$$
is realizable by some $X:Y\rightarrow Z$.

Therefore the exact quotient transformation space is

$$
\boxed{ \mathrm{Hom}(U,S). }
$$
## Proof

For every access $i$,

$$
XR_i
$$
depends only on the restriction of $X$ to

$$
\mathrm{Im}R_i.
$$
Across all accesses, only

$$
X|_U
$$
can affect any effective map.

Applying $L$ removes every component in

$$
\ker L,
$$
so the observable action is exactly

$$
Q=L\circ X|_U.
$$
It remains to show that every

$$
Q:U\rightarrow S
$$
can be lifted.

Because

$$
S=\mathrm{Im}L,
$$
choose a linear section

$$
\sigma:S\rightarrow Z
$$
such that

$$
L\circ\sigma=\mathrm{id}_S.
$$
Define on $U$

$$
X_U=\sigma\circ Q.
$$
Then

$$
L\circ X_U=Q.
$$
Extend $X_U$ arbitrarily from $U$ to all of $Y$.

The resulting $X:Y\rightarrow Z$ realizes the requested $Q$.

Therefore all and only maps in

$$
\mathrm{Hom}(U,S)
$$
are observationally distinct. ∎

---

# 19. Corollary 3 — Exact Finite Quotient Size

If the field is

$$
GF(q),
$$
and

$$
u=\dim U, \qquad s=\dim S,
$$
then

$$
\mathrm{Hom}(U,S)
$$
has dimension

$$
us
$$
over $GF(q)$.

Therefore the exact number of observationally distinct transformation classes is

$$
\boxed{ q^{us}. }
$$
For the 5-bit binary RM-001 CK case,

$$
q=2, \qquad u=5, \qquad s=5,
$$
so the quotient contains

$$
\boxed{ 2^{25} = 33,554,432 }
$$
candidates.

This explains why exhaustive synthesis is practical in the frozen $n=5$ model.

---

# 20. RM-001 Projection Quotient

For RM-001,

$$
M_i(P) = B_rR_i+B_cC_i+B_cPR_i.
$$
The transformation is visible only through

$$
\boxed{ Q=B_cP. }
$$
Across a family of accesses, only

$$
Q|_U
$$
matters, where

$$
U=\sum_i\mathrm{Im}R_i.
$$
Let

$$
S=\mathrm{Im}B_c.
$$
Then the exact synthesis object is

$$
\boxed{ Q:U\rightarrow S. }
$$
Any degrees of freedom in $P$ outside this quotient are gauge freedom with respect to the declared bank model and access family.

This is why PMAG should distinguish:

$$
\boxed{\text{transformation representation}}
$$
from

$$
\boxed{\text{transformation behavior visible to the model}}.
$$
---

# 21. Capacity Before Search

The capacity theorem changes the role of synthesis.

A naive workflow asks:

```text
enumerate transformations
        ↓
measure objective
        ↓
keep the best observed candidate
```

A capacity-first workflow asks:

```text
derive exact capacity
        ↓
define the optimality constraints
        ↓
quotient invisible degrees of freedom
        ↓
solve only for transformations that attain capacity
```

The difference is epistemic as well as computational.

Without a capacity theorem, failure to find a better candidate is only evidence about the search procedure.

With an exact capacity theorem, a candidate satisfying the ceiling is certified structurally optimal under the declared model.

---

# 22. Capacity Certificates

A useful capacity result should make both directions auditable:

## Upper-bound certificate

Show that no admissible transformation can exceed a declared value.

For the rank family $A+LXR$, the two universal upper bounds are

$$
\mathrm{rank}(A+LXR) \le \mathrm{rank}[A\ L]
$$
and

$$
\mathrm{rank}(A+LXR) \le \mathrm{rank} \begin{bmatrix} A\\ R \end{bmatrix}.
$$
## Attainment certificate

Construct or synthesize an $X$ satisfying

$$
\mathrm{rank}(A+LXR) = \rho^\*.
$$
Theorem 1 proves that such an $X$ always exists for a single access in this family.

For a concrete finite instance, an explicit matrix $X$ is a direct attainment witness.

Thus exact capacity consists of

$$
\boxed{ \text{upper bound} + \text{attainment}. }
$$
---

# 23. Joint Feasibility Certificates

For a feasible multi-access instance, a single transformation

$$
T^\*
$$
with independently recomputable objective values

$$
\Phi_i(T^\*)=\Phi_i^\* \qquad \forall i
$$
is a feasibility certificate.

For a finite exact model, this witness should be inexpensive to verify relative to synthesis.

For an infeasible instance, the desired evidence is different:

$$
\boxed{ \text{UNSAT proof or exhaustive exclusion} }
$$
optionally accompanied by

$$
\boxed{ \text{inclusion-minimal core + deletion witnesses}. }
$$
PMAG should keep witness verification simpler than witness discovery whenever practical.

---

# 24. Structural Optimality and Secondary Cost

Suppose the structurally optimal set is

$$
\mathcal F = \bigcap_i\mathcal O_i.
$$
If

$$
|\mathcal F|>1,
$$
the structural objective alone does not identify one implementation.

A secondary cost model

$$
C_{\mathrm{impl}}:\mathcal F\rightarrow\mathbb R
$$
can then be optimized:

$$
\boxed{ T^\* = \mathrm{arg\,min}_{T\in\mathcal F} C_{\mathrm{impl}}(T). }
$$
This is lexicographic optimization:

1. first satisfy exact structural optimality;
2. then optimize implementation cost inside the optimal set.

RM-001 demonstrates why this matters: the declared CK access constraints admit many structurally rank-optimal transformations.

Structural non-uniqueness is therefore not a defect.

It creates a second optimization layer.

---

# 25. When the Ideal Objective Is Itself Model-Dependent

The phrase

$$
\Phi_{\mathrm{ideal}}
$$
requires care.

For example, a bank-only model might treat one distinct bank per execution entity as ideal.

A bank-plus-broadcast model may assign no penalty when several entities access the same bank and same word.

A transaction model may care about coalescing rather than one-resource-per-lane separation.

Therefore an "ideal" value is always relative to the chosen resource semantics.

Refining the hardware model may change:

- the fibers;
- the objective;
- the ideal value;
- the capacity;
- the set of optimal transformations.

This is expected.

PMAG should not treat a coarse-model optimum as universally optimal after model refinement.

---

# 26. Model Refinement and Obstruction Refinement

Suppose

$$
H_1
$$
refines

$$
H_0
$$
through

$$
H_0=\pi\circ H_1.
$$
Then every coarse resource fiber is a union of refined resource fibers.

Thus a collision under $H_0$ may disappear under $H_1$, but two executions separated by $H_0$ cannot become equal under $H_1$ if $\pi$ is a deterministic coarsening of the refined state.

This gives a partial order of structural descriptions.

However, optimization objectives on $H_0$ and $H_1$ need not be directly comparable unless their relationship is also specified.

For that reason, hardware refinement and objective refinement should be tracked separately.

---

# 27. Open General Problem — Capacity Beyond Linear Rank

Theorem 1 solves a specific but important family:

$$
\boxed{ \text{linear maps} + \text{bilinear transformation parameter} + \text{rank objective}. }
$$
The broader PMAG capacity problem remains open for richer settings, including:

$$
\text{affine bit-vector},
$$
$$
\text{integer affine with carries},
$$
$$
\text{piecewise affine},
$$
$$
\text{modular maps},
$$
$$
\text{transaction formation},
$$
$$
\text{cache-set geometry},
$$
and objectives such as

$$
\text{transaction count}, \quad \text{arbitration cost}, \quad \text{latency}.
$$
The research question is not whether every such model admits a closed form.

The question is:

> **For each model, what is the strongest exact capacity statement available, what obstruction explains any gap, and what synthesis method attains the bound when it is attainable?**

---

# 28. Evidence Status for This Document

The intended evidence labels are:

```text
Definition of model-relative capacity          DEFINITION
Ideal / unavoidable / avoidable gap            DEFINITION
Free-corner completion lemma                   PROVED
max rank(A + L X R) theorem                    PROVED
canonical affine-shear capacity                PROVED
hardware-aware RM-001 rank capacity            PROVED
resource-span obstruction                      PROVED
domain-information obstruction                 PROVED
three-layer RM-001 loss decomposition          DERIVED
capacity vs joint feasibility distinction      DEFINITION / DERIVED
active-subspace/resource-image quotient        PROVED
finite quotient size q^(us)                    DERIVED
RM-001 n=5 quotient size 2^25                   DERIVED
general nonlinear capacity theory              OPEN
```

The general hardware-aware rank-capacity result therefore no longer needs to rely on $n=2$ exhaustive verification as its primary justification.

Finite exhaustive verification remains valuable as an independent implementation check.

---

# 29. Consequence for RM-001 Documentation

RM-001 can now separate four statements cleanly.

### Theorem

For one access,

$$
\boxed{ \rho^\* = \min \left\{ \mathrm{rank}[B_rR\ \ B_c], \; \mathrm{rank} \begin{bmatrix} R\\ B_cC \end{bmatrix} \right\}. }
$$
This is a general algebraic theorem.

### Verification

The finite $n=2$ exhaustive checker independently verifies the implementation of that theorem across its complete declared domain.

### Synthesis

For the frozen $n=5$ case, exact enumeration or SAT-style solving finds transformations attaining all declared per-access capacities when jointly feasible.

### Diagnosis

When joint feasibility fails, an inclusion-minimal infeasible core identifies a higher-order shared-transformation obstruction.

These four evidence layers should not be merged into one claim.

---

# 30. Research Principle

The capacity-and-obstruction viewpoint changes the optimization question from

> Which candidate happens to benchmark best among the candidates we tried?

to

> What is the exact structural ceiling under the declared model, why can it not be exceeded, can one shared transformation attain it, and if not, what is the smallest explainable obstruction?

The PMAG pipeline therefore becomes

```text
declare model boundary
        ↓
derive capacity
        ↓
identify unavoidable obstruction
        ↓
quotient invisible transformation freedom
        ↓
solve joint feasibility
        ↓
produce witness or infeasibility core
        ↓
optimize implementation cost
        ↓
validate against hardware
```

RM-001 is the first reference model in which this pipeline is exact from the algebraic capacity theorem through finite synthesis and implementation-cost analysis.
