# Foundations of Parallel Memory-Access Geometry

## 1. Purpose

Parallel Memory-Access Geometry (PMAG) studies the structure induced when concurrently executing program entities access finite hardware resources.

The fundamental composition is

$$
\boxed{
\text{execution}
\longrightarrow
\text{logical state}
\longrightarrow
\text{physical address}
\longrightarrow
\text{hardware resource}
}
$$

This document defines the common objects used across PMAG reference models before choosing a particular algebra, architecture, or optimization objective.

It is intentionally more general than RM-001 / Qingming Affine Shear.

The first reference model specializes these definitions to affine maps over $GF(2)$, linear bank projection, and affine-shear transformations.

---

## 2. Separation of Concerns

The central separation in PMAG is

$$
\boxed{
\text{program semantics}
\neq
\text{hardware resource model}
\neq
\text{optimization objective}.
}
$$

A useful formulation should allow the same access semantics to be analyzed against different hardware models, and the same hardware model to be optimized under different admissible transformations or objectives.

Without a transformation, the basic composition is

$$
E \xrightarrow{A} \mathcal A \xrightarrow{H} R.
$$

With a semantics-preserving transformation,

$$
E \xrightarrow{A} \mathcal A
\xrightarrow{T} \mathcal A'
\xrightarrow{H} R.
$$

The effective resource map is therefore

$$
F=H\circ A
$$

or

$$
F_T=H\circ T\circ A.
$$

The mathematical form of $A$, $T$, and $H$ is model-dependent.

---

# 3. Definition 1 — Execution Domain

Let

$$
E
$$

denote an **execution domain**.

An element

$$
e\in E
$$

represents one participant in a declared simultaneous or phase-related memory access.

Depending on the model, an execution entity may represent a lane, thread, wavefront participant, register element, vector subelement, transaction participant, or another program-defined unit.

The execution domain must encode the simultaneity semantics relevant to the hardware model.

Two accesses should not be grouped merely because they occur in the same source-level statement. Conversely, one machine instruction may need a phase-augmented execution domain if the hardware decomposes it into multiple access phases.

### RM-001 specialization

In RM-001,

$$
E=GF(2)^\ell.
$$

Future models may instead use integer lattices, finite bit-vector domains, products with phase labels, or arbitrary finite sets.

---

# 4. Definition 2 — Logical Coordinate Space

Let

$$
L
$$

denote a **logical coordinate space**.

A logical access map is

$$
A_L:E\rightarrow L.
$$

The purpose of $L$ is to describe program-visible or layout-visible coordinates before they are lowered to a physical address.

Examples include

$$
(row,column),
$$

tensor coordinates,

$$
(tile,row,column),
$$

register-layout coordinates, or other structured indices.

The logical layer is conceptually useful but not mandatory. A model may map execution entities directly to addresses when a separate logical representation adds no information.

---

# 5. Definition 3 — Physical Address Space

Let

$$
\mathcal A
$$

denote a **physical or target address space**.

An address realization is a map

$$
A_P:L\rightarrow\mathcal A.
$$

The composed execution-to-address map is

$$
\boxed{
A=A_P\circ A_L:E\rightarrow\mathcal A.
}
$$

Depending on the model, $\mathcal A$ may represent byte addresses, word addresses, shared-memory offsets, scratchpad offsets, cache-line addresses, symbolic bit-vector addresses, or another address-level representation.

### Model boundary

The address map need not be globally linear.

For example,

$$
base+r\cdot stride+c
$$

uses integer addition and may introduce carries.

A $GF(2)$ model is exact only when the selected address region and bit extraction make the relevant map affine over $GF(2)$, or when an equivalent exact representation has been proved.

---

# 6. Definition 4 — Hardware Resource Space

Let

$$
R
$$

denote a finite **hardware resource space**.

An element

$$
r\in R
$$

identifies a resource state relevant to the declared contention model.

Examples include:

- an LDS/shared-memory bank;
- a bank-port pair;
- a bank-phase pair;
- a transaction identifier;
- a cache set or sector;
- a memory partition;
- a memory channel.

The hardware-resource map is

$$
H:\mathcal A\rightarrow R.
$$

The effective execution-to-resource map is therefore

$$
\boxed{
F=H\circ A:E\rightarrow R.
}
$$

With an intervening transformation $T$,

$$
\boxed{
F_T=H\circ T\circ A.
}
$$

This effective map is the primary object of PMAG analysis.

---

# 7. Definition 5 — Resource Fiber

For a resource state

$$
r\in R,
$$

the **resource fiber** is

$$
\boxed{
F^{-1}(r)
=
\{e\in E:F(e)=r\}.
}
$$

Its cardinality

$$
|F^{-1}(r)|
$$

is the number of declared concurrent execution entities mapped to that resource state.

The family

$$
\{F^{-1}(r):r\in R\}
$$

is the **fiber structure** of the access.

The fibers induce an equivalence relation on $E$:

$$
e_1\sim_F e_2
\iff
F(e_1)=F(e_2).
$$

Thus the resource map partitions the execution domain according to which execution entities are indistinguishable to the selected hardware-resource model.

---

# 8. Definition 6 — Reachable Resource Set

The **reachable resource set** is

$$
\boxed{
\operatorname{Reach}(F)=F(E)\subseteq R.
}
$$

Its cardinality

$$
|\operatorname{Reach}(F)|
$$

measures how many distinct resource states are exercised by the declared access.

For a linear map

$$
F:GF(2)^\ell\rightarrow GF(2)^b,
$$

$$
\boxed{
|\operatorname{Reach}(F)|
=
2^{\operatorname{rank}F}.
}
$$

In nonlinear models, reachable-resource cardinality remains meaningful even when rank does not.

---

# 9. Definition 7 — Information Loss

Two execution entities are **resource-indistinguishable** when

$$
F(e_1)=F(e_2).
$$

PMAG treats this identification as a loss of execution distinction relative to the selected hardware-resource model.

In a linear model,

$$
\ker F
$$

contains the execution directions erased by the map.

More generally, the fiber equivalence relation describes the information discarded by $F$.

This gives the first fundamental PMAG question:

$$
\boxed{
\text{Which execution distinctions survive the mapping to hardware resources?}
}
$$

The answer depends jointly on access geometry and hardware projection.

---

# 10. Definition 8 — Structural Contention

A resource state $r$ is **structurally contended** for the declared access when

$$
|F^{-1}(r)|>1.
$$

A basic contention profile is the multiset

$$
\boxed{
\mathcal C(F)
=
\{
|F^{-1}(r)|:
r\in\operatorname{Reach}(F)
\}.
}
$$

Useful derived quantities may include

$$
\max_{r\in R}|F^{-1}(r)|,
$$

the maximum structural multiplicity, and

$$
|\operatorname{Reach}(F)|,
$$

the reachable resource diversity.

### Structural contention is not latency

A coarse hardware model may identify two accesses as contending while a richer model distinguishes them by address tag, port, phase, broadcast eligibility, transaction grouping, or instruction path.

Accordingly, refinement of the hardware-resource model may split a coarse fiber into several finer fibers.

---

# 11. Definition 9 — Hardware-Model Refinement

Let

$$
H_0:\mathcal A\rightarrow R_0
$$

and

$$
H_1:\mathcal A\rightarrow R_1
$$

be two hardware-resource models.

We say that $H_1$ **refines** $H_0$ if there exists a map

$$
\pi:R_1\rightarrow R_0
$$

such that

$$
\boxed{
H_0=\pi\circ H_1.
}
$$

Then $H_1$ preserves at least as much resource distinction as $H_0$.

For example,

$$
H_{\text{bank+phase}}
$$

may refine

$$
H_{\text{bank}},
$$

and

$$
H_{\text{bank+phase+tag}}
$$

may refine both.

This provides a formal language for moving from coarse structural models toward richer microarchitectural models.

---

# 12. Definition 10 — Semantics-Preserving Transformation

Let

$$
\mathcal T
$$

be a family of transformations acting on logical coordinates, storage coordinates, physical addresses, or layout states.

A transformation

$$
T\in\mathcal T
$$

is **admissible** only if it preserves the declared program semantics.

The exact preservation condition is frontend- and model-dependent.

Typical examples include transformations that alter storage layout while preserving the logical value associated with every program coordinate.

For a given access $A$,

$$
\boxed{
F_T=H\circ T\circ A
}
$$

is the transformed effective resource map.

PMAG studies how resource geometry varies as $T$ ranges over the admissible family.

---

# 13. Definition 11 — Objective Functional

An **objective functional**

$$
\Phi
$$

assigns a quality or cost to an effective resource map:

$$
\Phi(F).
$$

Examples include

$$
\Phi(F)=|\operatorname{Reach}(F)|,
$$

$$
\Phi(F)=-\max_r|F^{-1}(r)|,
$$

transaction count, arbitration cost, predicted latency, or measured throughput.

The optimization direction must be declared explicitly.

For a maximization objective,

$$
\boxed{
\Phi^\*
=
\max_{T\in\mathcal T}
\Phi(H\circ T\circ A).
}
$$

For a minimization objective,

$$
\boxed{
\Phi^\*
=
\min_{T\in\mathcal T}
\Phi(H\circ T\circ A).
}
$$

Different objective levels need not induce the same ordering over transformations.

A transformation that maximizes resource diversity need not minimize exact hardware latency.

---

# 14. Definition 12 — Structural Capacity

Given an access map $A$, hardware model $H$, transformation family $\mathcal T$, and objective $\Phi$, the **capacity** of the instance is the best achievable objective value over the declared transformation family.

For a maximization problem,

$$
\boxed{
\operatorname{Cap}(A,H,\mathcal T,\Phi)
=
\max_{T\in\mathcal T}
\Phi(H\circ T\circ A).
}
$$

For a minimization problem,

$$
\boxed{
\operatorname{Cap}(A,H,\mathcal T,\Phi)
=
\min_{T\in\mathcal T}
\Phi(H\circ T\circ A).
}
$$

Capacity is always relative to the model boundary.

Therefore the phrase **optimal layout** is incomplete unless the access set, hardware model, admissible transformations, and objective are also specified.

---

# 15. Definition 13 — Intrinsic and Transformable Loss

Suppose an objective has an ideal value

$$
\Phi_{\mathrm{ideal}}
$$

and the declared transformation family has capacity

$$
\Phi^\*.
$$

The gap between the ideal and $\Phi^\*$ is **unavoidable within the declared model and transformation family**.

For a candidate $T$, the gap between $\Phi^\*$ and $\Phi(F_T)$ is **avoidable transformation loss**.

Conceptually,

$$
\boxed{
\text{observed structural loss}
=
\text{unavoidable loss}
+
\text{avoidable transformation loss},
}
$$

when the chosen objective admits such a decomposition.

RM-001 realizes this idea algebraically through access-intrinsic nullity, hardware/shear-family unavoidable loss, and candidate-specific avoidable rank loss.

---

# 16. Definition 14 — Multi-Access Instance

A **multi-access instance** is a family

$$
\mathcal A=\{A_i\}_{i\in I}
$$

of access maps that must share one transformation

$$
T\in\mathcal T.
$$

Each access may have its own effective resource map

$$
F_{i,T}=H_i\circ T\circ A_i
$$

and its own objective

$$
\Phi_i.
$$

A common requirement is for every access to reach its individual capacity:

$$
\Phi_i(F_{i,T})=\Phi_i^\*
\qquad
\forall i\in I.
$$

The transformation is shared; the constraints are per access.

This distinction is central because individually optimal transformations need not be jointly compatible.

---

# 17. Definition 15 — Exact Feasibility

For a multi-access instance, define the individually optimal transformation set

$$
\mathcal O_i
=
\left\{
T\in\mathcal T:
\Phi_i(F_{i,T})=\Phi_i^\*
\right\}.
$$

The instance is **jointly feasible** when

$$
\boxed{
\bigcap_{i\in I}\mathcal O_i
\neq\varnothing.
}
$$

It is **infeasible** when

$$
\boxed{
\bigcap_{i\in I}\mathcal O_i
=
\varnothing.
}
$$

This separates two questions:

1. what is the optimum for each access individually?
2. does one shared transformation attain all of those optima simultaneously?

RM-001 demonstrates that the second answer can be negative even when every individual problem is feasible.

---

# 18. Definition 16 — Synthesis

A **synthesis procedure** constructs a transformation

$$
T^\*\in\mathcal T
$$

satisfying a declared optimality or feasibility criterion.

For exact simultaneous synthesis, the target is

$$
\boxed{
T^\*
\in
\bigcap_{i\in I}\mathcal O_i.
}
$$

A synthesizer may return:

- one witness;
- all witnesses;
- an optimum under a secondary implementation-cost objective;
- or an infeasibility result.

An exact synthesizer must distinguish proof of non-existence from failure of a heuristic search.

---

# 19. Definition 17 — Infeasibility Certificate

Let $I$ index a jointly infeasible multi-access instance.

A subset

$$
J\subseteq I
$$

is an **infeasible core** if the restricted constraints remain infeasible.

It is **inclusion-minimal** if $J$ is infeasible but

$$
J\setminus\{j\}
$$

is feasible for every $j\in J$.

Such a core explains which accesses form a minimal obstruction under set inclusion.

An inclusion-minimal core is not necessarily a minimum-cardinality core.

A mature PMAG solver should, when practical, accompany infeasibility with independently checkable evidence.

---

# 20. Definition 18 — Implementation Cost

Let

$$
C_{\mathrm{impl}}(T)
$$

be a declared implementation-cost model for a transformation.

Examples include abstract gate count, instruction count, dependency depth, register pressure, code size, or a target-specific latency estimate.

After structural feasibility has been established, a secondary optimization problem is

$$
\boxed{
\min_{T\in\mathcal F}
C_{\mathrm{impl}}(T),
}
$$

where $\mathcal F$ is the structurally feasible or structurally optimal transformation set.

This separates

$$
\boxed{\text{structural optimality}}
$$

from

$$
\boxed{\text{implementation optimality}}.
$$

RM-001 gives an exact implementation-cost result only for its explicitly declared 5-bit linear-circuit grammar.

---

# 21. Definition 19 — Hardware Validation

A mathematically optimal transformation is not automatically a physically optimal transformation.

**Hardware validation** evaluates a synthesized transformation on a real target system.

Its purpose is to test:

1. whether the declared resource model captures the relevant physical behavior;
2. whether omitted effects materially change candidate ordering;
3. whether compiler lowering realizes the intended transformation;
4. whether the structural objective correlates with the final performance objective.

Hardware validation is evidence about the model-to-machine relationship.

It does not turn a hardware-specific observation into a universal theorem.

---

# 22. Definition 20 — Model Boundary

Every PMAG result is relative to a declared model boundary.

A model boundary should state at least

$$
\boxed{
(E,\;A,\;H,\;\mathcal T,\;\Phi)
}
$$

together with any simultaneity, phase, address-width, vector-width, broadcast, transaction, or architecture assumptions required by the result.

Claims outside that boundary must be separately established.

This is especially important when moving from

$$
\text{structural contention}
$$

to

$$
\text{latency / throughput}.
$$

---

# 23. The Four Fundamental PMAG Problems

The definitions above support four recurring research problems.

## Problem I — Information

Given

$$
F=H\circ A,
$$

determine which execution distinctions survive the mapping.

Possible outputs include reachable-resource count, fiber partition, kernel, image, quotient structure, and information-loss invariants.

In RM-001, this becomes rank, nullity, kernel, and image over $GF(2)$.

## Problem II — Contention

Given

$$
F:E\rightarrow R,
$$

characterize the collision structure of the resource fibers.

Possible outputs include maximum fiber size, fiber-size distribution, number of distinct resource states, collision classes, transaction grouping, and arbitration classes.

In RM-001, equal-size linear fibers give exact multiplicity

$$
2^{\operatorname{nullity}F}.
$$

## Problem III — Transformation

Given an admissible family

$$
\mathcal T,
$$

characterize how resource geometry varies under

$$
F_T=H\circ T\circ A.
$$

Possible questions include invariants under all $T$, maximum achievable resource diversity, unavoidable collision subspaces, equivalence classes of transformations, quotient search spaces, and structural obstructions.

In RM-001, the family is an affine shear parameterized by a binary matrix $P$.

## Problem IV — Synthesis

Given one or more accesses, construct a transformation satisfying the desired objective.

Possible outputs include an exact optimum, all exact optima, a Pareto set, a minimum-cost implementation, or an infeasibility certificate.

Synthesis is the constructive counterpart of capacity analysis.

---

# 24. Canonical PMAG Problem Statement

A general PMAG instance can be written as follows.

### Given

- an execution domain $E$;
- access semantics $A$;
- a hardware-resource model $H$;
- an admissible transformation family $\mathcal T$;
- one or more objectives $\Phi$;
- an implementation-cost model $C_{\mathrm{impl}}$, if needed.

### Determine

1. the structural invariants of $H\circ A$;
2. the best achievable objective over $\mathcal T$;
3. whether a shared optimum exists across all required accesses;
4. one or all optimal transformations;
5. an infeasibility certificate if no shared optimum exists;
6. the minimum implementation cost among acceptable transformations;
7. the residual discrepancy between the structural model and hardware measurement.

This is the standard PMAG analysis-synthesis pipeline.

---

# 25. Refinement Axes

PMAG models may be refined along several independent axes.

## Access-language refinement

$$
GF(2)
\rightarrow
\text{affine bit-vector}
\rightarrow
\mathbb Z\text{-affine}
\rightarrow
\text{piecewise affine}
\rightarrow
\text{nonlinear / modular}.
$$

A refinement should preserve exactness where possible and clearly identify where a previously exact theorem becomes only a bound, approximation, or local statement.

## Hardware refinement

$$
\text{bank}
\rightarrow
\text{bank+phase}
\rightarrow
\text{bank+port}
\rightarrow
\text{transaction}
\rightarrow
\text{cache}
\rightarrow
\text{channel}.
$$

A refined resource model may split collisions that appear identical under a coarser model.

## Objective refinement

$$
\text{information}
\rightarrow
\text{collision multiplicity}
\rightarrow
\text{transaction count}
\rightarrow
\text{arbitration}
\rightarrow
\text{latency / throughput}.
$$

A richer objective generally requires a richer hardware model.

## Transformation refinement

$$
\text{shear}
\rightarrow
\text{general linear}
\rightarrow
\text{permutation}
\rightarrow
\text{padding}
\rightarrow
\text{tiling}
\rightarrow
\text{compound layout transformation}.
$$

Expanding the transformation family can increase capacity, but may also enlarge synthesis and implementation complexity.

---

# 26. Relation to Qingming Access IR

PMAG requires a representation capable of preserving the access semantics needed by multiple downstream models.

The working name for this layer is **Qingming Access IR**.

The Access IR should represent program-side facts such as:

- execution domain;
- lane/thread/register participation;
- instruction or access phase;
- logical coordinates;
- symbolic address expression;
- element and vector width;
- load/store semantics;
- word identity;
- simultaneity constraints.

It should not hard-code one hardware bank model into the access representation.

Hardware-specific resource projections belong in a separate target model.

The intended separation is

```text
frontend layout / program IR
            ↓
     Qingming Access IR
            ↓
      PMAG access model
            ↓
    target hardware model
            ↓
 analysis / synthesis / certificate
```

---

# 27. Relation to Existing Layout Systems

PMAG does not require replacing existing layout systems.

A layout representation such as Triton Linear Layouts, CuTe layouts, or CK tensor descriptors can serve as:

- a source from which access geometry is extracted;
- a language in which transformations are expressed;
- a target into which a synthesized mapping is emitted.

PMAG focuses on the induced mapping from concurrent execution entities to finite hardware resources.

A layout system and a PMAG model are therefore related but not identical abstractions.

---

# 28. RM-001 as the First Exact Instance

RM-001 specializes the general PMAG objects as follows.

### Execution domain

$$
E=GF(2)^\ell.
$$

### Logical coordinates

$$
(r,c)\in GF(2)^p\times GF(2)^q.
$$

### Access

$$
r(t)=Rt+r_0,
\qquad
c(t)=Ct+c_0.
$$

### Transformation

$$
S_P(r,c)=(r,c+Pr).
$$

### Hardware resource map

$$
h(r,c)=B_rr+B_cc+b_0.
$$

### Effective map

$$
\boxed{
M(P)=B_rR+B_cC+B_cPR.
}
$$

### Structural objective

Maximize

$$
\operatorname{rank}M(P).
$$

### Collision interpretation

For linear $M$,

$$
|\operatorname{Im}M|
=
2^{\operatorname{rank}M},
$$

and each reachable resource has

$$
2^{\ell-\operatorname{rank}M}
$$

preimages.

### Synthesis

Find one shared $P$, or quotient variable $Q=B_cP$, satisfying all required per-access rank optima.

### Infeasibility

Return an inclusion-minimal infeasible access subset when a joint optimum is impossible.

### Implementation objective

Minimize exact gate count under the declared 5-bit

$$
\{SHL,SHR,AND,XOR\}
$$

linear-circuit grammar.

This specialization demonstrates that the general PMAG pipeline can be made exact for a non-trivial GPU-relevant instance.

---

# 29. Evidence Classification

PMAG uses the following evidence labels.

### DEFINITION

A term or mathematical object introduced by the framework.

### DERIVED

A consequence obtained mechanically from declared definitions or previously established results.

### PROVED

A statement with a general proof under its declared assumptions.

### EXHAUSTIVELY VERIFIED

A statement checked over an entire finite declared domain.

This does not substitute for a proof outside that finite domain.

### SOLVER-CERTIFIED

A result accompanied by a solver witness or independently checkable certificate.

### HARDWARE-VALIDATED

A structural or implementation claim tested on declared physical hardware.

### EMPIRICAL

An observed result without a general proof or exhaustive finite certificate.

### CONJECTURE

A mathematically precise claim believed to hold but not yet proved.

### OPEN

A research question or implementation layer not yet established.

---

# 30. Non-Goals of the Foundational Definition

This foundational PMAG definition does not claim:

- that all memory behavior is geometric in one algebraic sense;
- that rank is a universal performance metric;
- that every resource system has equal-size fibers;
- that one layout is optimal for all accesses;
- that structural contention determines exact latency;
- that $GF(2)$ is sufficient for general address arithmetic;
- that a universal fixed swizzle exists;
- that current hardware resource models capture every microarchitectural detail.

The framework instead requires those assumptions to be explicit per reference model.

---

# 31. Research Program

The long-term objective of PMAG is to turn a class of memory-layout problems from

```text
candidate
   ↓
benchmark
   ↓
modify
   ↓
benchmark
```

into

```text
access semantics
      ↓
resource geometry
      ↓
structural capacity
      ↓
feasibility
      ↓
synthesis
      ↓
certificate
      ↓
implementation cost
      ↓
hardware validation
```

The framework does not eliminate empirical measurement.

It attempts to identify which parts of the optimization problem can be solved structurally, exactly, and reproducibly before measurement.

---

# 32. Current Scope and Next Formal Work

At present, the strongest complete executable instance is RM-001:

**Qingming Affine Shear**

$$
\boxed{
\text{Affine }GF(2)
+
\text{linear bank projection}
+
\text{rank objective}
+
\text{exact multi-access synthesis}.
}
$$

The next foundational work should strengthen the common theory while keeping model boundaries explicit.

Candidate next steps include:

1. a complete general proof of the hardware-aware rank-capacity theorem;
2. formal model-refinement results for bank, phase, port, and broadcast;
3. an exact affine-bit-vector or piecewise-affine reference model;
4. formalization of Access IR semantics;
5. transaction-level resource geometry;
6. cross-framework extraction from CK, Triton, and CuTe representations;
7. target-specific implementation-cost models separated from structural optimality.

These are research directions, not claims of completed support.
