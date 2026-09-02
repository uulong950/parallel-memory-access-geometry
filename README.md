# Parallel Memory-Access Geometry

> A Qingming research program on the structure, contention, transformation, and synthesis of parallel memory accesses over finite hardware resources.

**Parallel Memory-Access Geometry (PMAG)** studies how concurrently executing program entities are mapped through logical coordinates and physical addresses onto finite hardware resources.

The basic object is

$$
\boxed{
\text{execution}
\longrightarrow
\text{logical coordinates}
\longrightarrow
\text{physical addresses}
\longrightarrow
\text{hardware resources}
}
$$

Examples of execution entities include lanes, threads, wavefront participants, register elements, and transaction participants. Examples of hardware resources include shared-memory banks, ports, instruction phases, transactions, cache sets, memory partitions, and memory channels.

The central research question is:

> **Given a parallel access geometry and a finite hardware-resource model, what contention is unavoidable, what can be removed by semantics-preserving transformations, and how can an optimal implementable mapping be synthesized?**

This repository develops that question as a research program. It is intentionally broader than any particular swizzle, GPU architecture, layout representation, compiler, or algebraic model.

---

## 1. Four Fundamental Questions

PMAG separates four basic questions:

$$
\boxed{
\text{information}
\qquad
\text{contention}
\qquad
\text{transformation}
\qquad
\text{synthesis}
}
$$

### Information

Which distinctions between concurrently executing entities survive the mapping to finite hardware resources?

For an effective resource map

$$
F:E\rightarrow H,
$$

we want to characterize what information about the execution domain $E$ remains observable after the mapping.

In linear models this leads naturally to

$$
\operatorname{rank}F,\qquad \ker F,\qquad \operatorname{Im}F.
$$

In more general models the corresponding objects may be fibers, equivalence classes, partitions, reachable resource states, or other structural invariants.

### Contention

Which distinct execution entities map to the same hardware resource?

For a resource state $h$,

$$
F^{-1}(h)
$$

is its execution fiber. The geometry of these fibers determines structural contention.

Depending on the hardware model, this may describe bank collisions, port contention, transaction amplification, cache-set concentration, partition imbalance, or other finite-resource conflicts.

Structural contention is not automatically identical to latency. Broadcast behavior, ports, phases, scheduling, instruction decomposition, vector width, occupancy, and pipeline overlap may change its physical cost.

### Transformation

Which semantics-preserving transformations can alter the resource geometry?

A transformation family may contain

$$
\text{shear},\quad
\text{permutation},\quad
\text{swizzle},\quad
\text{padding},\quad
\text{tiling},\quad
\text{interleaving},\quad
\text{layout conversion}.
$$

Abstractly, PMAG studies

$$
H\circ T\circ A,
$$

where $A$ describes the program access, $T$ is an admissible transformation, and $H$ maps the transformed access onto hardware resources.

The goal is not to discover one universal transformation. In general,

$$
T=T(\text{access geometry},\text{hardware model},\text{objective}).
$$

### Synthesis

Once the access, hardware model, transformation family, and objective are declared, can we construct the best transformation automatically?

The desired pipeline is

```text
program access semantics
        ↓
access geometry
        ↓
hardware-resource model
        ↓
structural capacity
        ↓
feasibility
        ↓
synthesis
        ↓
implementation cost
        ↓
hardware validation
```

A mature solver should distinguish

$$
\boxed{\text{optimal solution}}
$$

from

$$
\boxed{\text{provably unavoidable loss}}
$$

and from

$$
\boxed{\text{infeasibility}}.
$$

When practical, infeasibility should come with an independently checkable explanation or certificate.

---

## 2. Research Coordinates

PMAG is not tied to one mathematical language, one hardware resource, or one performance objective.

### Mapping model

```text
GF(2) linear
    ↓
GF(2) affine
    ↓
affine bit-vector
    ↓
integer affine
    ↓
piecewise affine
    ↓
modular / carry-aware
    ↓
general nonlinear / bit-vector
```

### Hardware-resource model

```text
bank
  ↓
bank + port
  ↓
bank + instruction phase
  ↓
bank + phase + broadcast
  ↓
transaction
  ↓
cache set
  ↓
memory partition
  ↓
memory channel
```

### Objective model

```text
information preservation
        ↓
resource diversity
        ↓
fiber / collision multiplicity
        ↓
transaction count
        ↓
arbitration cost
        ↓
latency / throughput
```

### Transformation model

```text
affine shear
      ↓
general linear transform
      ↓
permutation
      ↓
padding
      ↓
tiling / interleave
      ↓
compound transformation
```

These axes are related but should not be conflated. A richer objective usually requires a richer hardware model, while a richer transformation family may increase capacity but also enlarge the synthesis and implementation problem.

---

## 3. Reference Models

The research program is developed through explicit **reference models**.

Each reference model should declare:

```text
execution domain
access language
hardware projection
allowed transformations
optimization objective
exact solvability boundary
proof status
implementation status
hardware validation status
```

A reference model is useful when its assumptions are narrow enough to audit and its conclusions are strong enough to expose general structure.

---

# RM-001 — Affine GF(2) Bank Geometry

Implementation:

**[Qingming Affine Shear](https://github.com/uulong950/qingming-affine-shear)**

RM-001 is the first exactly-solvable reference model in PMAG.

It studies

$$
\boxed{
\text{affine access over }GF(2)
+
\text{linear bank projection}
+
\text{affine shear transformation}
}
$$

and asks whether one shared transformation can preserve the maximum achievable bank information across one or more access patterns.

Its core pipeline is

```text
affine access geometry
        ↓
hardware bank projection
        ↓
rank capacity
        ↓
shared transformation
        ↓
exact feasibility
        ↓
MUS / infeasibility diagnosis
        ↓
implementation cost
```

The purpose of RM-001 is not to claim that all GPU memory behavior is linear over $GF(2)$. Its purpose is to establish the first closed-form and executable instance of the broader PMAG framework.

---

## 4. RM-001 Access and Transformation Model

Let

$$
t\in GF(2)^\ell
$$

parameterize concurrent execution entities.

An affine access is

$$
r(t)=Rt+r_0,\qquad c(t)=Ct+c_0.
$$

Its linear access geometry is

$$
J=
\begin{bmatrix}
R\\
C
\end{bmatrix}.
$$

RM-001 uses the affine shear

$$
S_P(r,c)=(r,c+Pr),
$$

with block operator

$$
S_P=
\begin{bmatrix}
I&0\\
P&I
\end{bmatrix}.
$$

Over $GF(2)$,

$$
S_P^2=I.
$$

Therefore the full coordinate transformation is invertible for every $P$, even when $P$ itself is singular.

---

## 5. RM-001 Hardware Projection

The bank model is

$$
h(r,c)=B_rr+B_cc+b_0.
$$

After applying the shear,

$$
\boxed{
M(P)=B_rR+B_cC+B_cPR
}
$$

is the effective execution-to-bank operator.

The canonical bank model

$$
B_r=0,\qquad B_c=I
$$

reduces this to

$$
M(P)=C+PR.
$$

---

## 6. What RM-001 Establishes

### 6.1 Exact linear collision structure

For a linear resource map

$$
M:GF(2)^\ell\rightarrow GF(2)^b,
$$

the number of reachable resource states is

$$
\boxed{
2^{\operatorname{rank}M}
}
$$

and every reachable resource state has exactly

$$
\boxed{
2^{\ell-\operatorname{rank}M}
}
$$

execution-domain preimages.

Thus rank measures reachable resource diversity and nullity measures exact structural fiber multiplicity within the model.

This is a structural statement, not a cycle-latency claim.

### 6.2 Exact canonical single-access capacity

For

$$
M(P)=C+PR,
$$

the transformation cannot recover execution distinctions already lost simultaneously by $R$ and $C$.

In the square $n$-dimensional case,

$$
\boxed{
\max_P\operatorname{rank}(C+PR)
=
\operatorname{rank}
\begin{bmatrix}
R\\
C
\end{bmatrix}
}
$$

or equivalently,

$$
\boxed{
\max_P\operatorname{rank}(C+PR)
=
n-\dim(\ker R\cap\ker C).
}
$$

This identifies access-intrinsic information loss independently of the chosen shear.

### 6.3 Hardware-aware rank capacity

For

$$
M(P)=B_rR+B_cC+B_cPR,
$$

the current theory uses the capacity expression

$$
\boxed{
\rho^\*
=
\min
\left\{
\operatorname{rank}
\begin{bmatrix}
B_rR & B_c
\end{bmatrix},
\;
\operatorname{rank}
\begin{bmatrix}
R\\
B_cC
\end{bmatrix}
\right\}.
}
$$

The finite $n=2$ domain has been exhaustively checked against this expression:

```text
hardware/access configurations: 65,536
P evaluations:                  1,048,576
mismatches:                     0
```

The project deliberately distinguishes a general proof from finite exhaustive verification. The general-dimensional proof should live in the theory layer and be independently reviewable.

### 6.4 Multi-access feasibility

For canonical access $i$, define

$$
L_i=
\operatorname{Im}
\begin{bmatrix}
R_i\\
C_i
\end{bmatrix}
$$

and

$$
\Gamma_P=\{(y,Py):y\in GF(2)^n\}.
$$

Then the additional transformation-induced nullity is determined by

$$
L_i\cap\Gamma_P.
$$

A pattern reaches its individual structural optimum exactly when

$$
\boxed{
L_i\cap\Gamma_P=\{0\}.
}
$$

A shared transformation must satisfy this for every modeled access.

Individual solvability does not imply joint solvability. Pairwise compatibility does not in general imply global compatibility. The simultaneous problem is therefore a higher-order constraint problem.

### 6.5 Exact synthesis

For the current 5-bit model, the complete binary transformation space has

$$
2^{25}=33,554,432
$$

matrices.

For the three declared CK access patterns in the current real-system case study, exhaustive synthesis finds

$$
\boxed{
2,887,680
}
$$

simultaneously rank-optimal solutions.

The current CK transformation is therefore one rank-optimal member of a large solution set. It is not uniquely determined by those three constraints.

### 6.6 Infeasibility diagnosis

When a shared transformation does not exist, the implementation can extract an **inclusion-minimal infeasible subset** of access constraints.

The returned set is infeasible, while deleting any one member restores feasibility.

This is a structural explanation for synthesis failure. The current claim is inclusion minimality, not globally minimum cardinality.

### 6.7 Exact implementation cost under a declared circuit model

RM-001 separates structural optimality from implementation cost.

For a declared 5-bit linear-circuit grammar containing unit-cost

$$
SHL,\quad SHR,\quad AND,\quad XOR,
$$

breadth-first state-space search gives exact minimum gate counts within that grammar.

For the current CK transformation,

$$
\boxed{
\operatorname{cost}=4
}
$$

under this abstract model.

This is an exact circuit result, not an ISA-latency claim.

---

## 7. First Real-System Case Study

RM-001 originated from a shared-memory bank-conflict problem in CK-Tile / ROCm.

The analyzed access contains a coupled coordinate transformation for which an identity-coupled XOR mapping can collapse diagonal bank diversity.

Under the declared effective 32-bank model,

$$
M=0
$$

for the problematic diagonal pattern, giving one reachable bank.

The affine shear used in the proposed transformation restores full rank for the declared diagonal and anti-diagonal patterns while retaining full rank for the modeled stride-2 pattern.

The corresponding engineering work is tracked in ROCm PR #11594.

This case study motivates the theory. It does not define the full scope of PMAG.

---

## 8. Boundaries of RM-001

RM-001 is deliberately narrow.

It does **not** currently establish:

- a universal GPU memory model;
- a cycle-accurate LDS model;
- exact effects of broadcast, ports, instruction phases, or arbitration;
- exact transaction formation;
- cache-set, partition, or channel behavior;
- global $GF(2)$-linearity in the presence of integer carries;
- general non-power-of-two modular address behavior;
- a universal fixed swizzle;
- global optimality of the current CK transform across all CK accesses or objectives;
- equivalence between abstract circuit gate count and target-ISA latency.

On RDNA3, the current CK case study models an effective 32-bank side relevant to the declared access. It does not claim that gfx1100 physically contains only 32 LDS banks.

Structural collision multiplicity is not automatically latency because real hardware may include broadcast, multiple ports, instruction phases, and scheduling effects.

---

## 9. Why Start With GF(2)?

RM-001 was chosen because it is simultaneously:

- relevant to real XOR-based GPU layout transformations;
- rich enough to exhibit non-trivial multi-access incompatibility;
- small enough to synthesize exactly for the 5-bit bank case;
- algebraically structured enough to admit exact rank and kernel reasoning;
- executable enough to verify exhaustively.

The purpose is not to reduce GPU memory systems to $GF(2)$.

The purpose is to begin with a domain in which the entire chain

$$
\boxed{
\text{model}
\rightarrow
\text{capacity}
\rightarrow
\text{feasibility}
\rightarrow
\text{synthesis}
\rightarrow
\text{certificate}
\rightarrow
\text{implementation}
}
$$

can be made exact.

---

## 10. Qingming Access IR

A long-term requirement is a canonical representation of parallel access semantics independent of any one frontend or layout system.

Working name:

**Qingming Access IR**

Its job is not to replace existing layout representations. It should preserve the information required to derive resource geometry:

```text
execution domain
instruction / phase
lane semantics
logical coordinates
address maps
element width
vector width
load / store semantics
word identity
simultaneity constraints
```

Existing systems may act as frontends:

```text
CK TensorDescriptor ──────┐
Triton LinearLayout ──────┤
CuTe Layout ──────────────┤
CUDA / HIP expressions ───┘
                          ↓
                  Qingming Access IR
                          ↓
                  PMAG analysis models
```

Hardware-specific information should remain in a separate hardware model.

---

## 11. Relationship to Layout Systems

Parallel Memory-Access Geometry is not intended to replace layout representation systems.

Systems such as Triton Linear Layouts, CuTe layouts, and CK tensor descriptors describe important parts of how program data and execution coordinates are organized.

PMAG asks a different question:

> Once an access is instantiated, what geometry does it induce over finite hardware resources, what part of the resulting contention is unavoidable, and what admissible transformation can optimize that geometry?

A layout system may therefore be an input representation, a transformation language, an output representation, or all three.

The research object remains the induced parallel access-to-resource mapping.

---

## 12. Proof and Evidence Policy

PMAG distinguishes several kinds of evidence:

```text
DEFINITION
DERIVED
PROVED
EXHAUSTIVELY VERIFIED
SOLVER-CERTIFIED
HARDWARE-VALIDATED
EMPIRICAL
CONJECTURE
OPEN
```

These labels are intentionally different.

In particular:

- exhaustive verification over $n=2$ is not a general-dimensional proof;
- exact structural rank is not measured latency;
- hardware measurements do not by themselves establish a universal theorem;
- solver infeasibility should be accompanied by a checkable certificate when practical.

---

## 13. Current Status

### RM-001 — Affine GF(2) Bank Geometry

```text
affine access model                  implemented
linear bank projection              implemented
exact linear collision law          established
canonical single-access ceiling     established
hardware-aware capacity             theorem + exhaustive finite validation
multi-access formulation            implemented
exact n=5 synthesis                 implemented
infeasibility / MUS diagnosis       implemented
exact abstract circuit cost         implemented
CK-Tile / gfx1100 case study        implemented

phase-aware model                   open
port-aware model                    open
transaction geometry                open
piecewise-affine address model      open
cross-framework Access IR           open
```

This repository should be read as the beginning of the research program, not as a claim that the open layers above have already been solved.

---

## 14. Research Roadmap

The next questions are not limited to finding additional XOR swizzles.

Access geometry can expand from

```text
Affine GF(2)
      ↓
affine bit-vector
      ↓
integer affine
      ↓
piecewise affine
      ↓
modular mappings
      ↓
general bit-vector / nonlinear mappings
```

Hardware-resource geometry can expand from

```text
bank
  ↓
bank + phase
  ↓
bank + port
  ↓
bank + broadcast
  ↓
transaction
  ↓
cache set
  ↓
memory partition / channel
```

Objectives can expand from

```text
rank / information
        ↓
fiber multiplicity
        ↓
transaction count
        ↓
arbitration cost
        ↓
latency / throughput
```

The central research question remains unchanged as these models become richer.

---

## 15. Repository Structure

A tentative structure is:

```text
parallel-memory-access-geometry/
│
├── README.md
├── theory/
│   ├── 01_parallel_access_geometry.md
│   ├── foundations/
│   ├── access-geometry/
│   ├── resource-geometry/
│   ├── transformation/
│   └── synthesis/
│
├── reference-models/
│   └── 001-affine-gf2.md
│
├── hardware/
│   ├── amd/
│   └── nvidia/
│
├── ir/
│   └── access-ir/
│
├── case-studies/
│   └── ck-gfx1100/
│
├── related-work/
│
└── roadmap/
```

The detailed implementation and reproducibility artifact for RM-001 remains in:

**https://github.com/uulong950/qingming-affine-shear**

---

## 16. Research Principle

GPU memory optimization is often approached as

```text
choose a layout
      ↓
benchmark
      ↓
modify it
      ↓
benchmark again
```

Parallel Memory-Access Geometry asks whether part of that process can instead become

```text
extract access semantics
        ↓
construct resource geometry
        ↓
derive structural limits
        ↓
solve feasibility
        ↓
synthesize an optimum
        ↓
minimize implementation cost
        ↓
measure the physical machine
```

The aim is not to eliminate measurement.

The aim is to move as much reasoning as possible from empirical search into explicit models, exact analysis, constructive synthesis, and independently checkable evidence.

---

## 17. Qingming

Parallel Memory-Access Geometry is a research program within **Qingming**.

Qingming is broader than this repository.

This repository is dedicated specifically to the theory and systems problem of parallel accesses over finite hardware resources.
