# Theory

This directory contains the foundational theory for **Parallel Memory-Access Geometry (PMAG)**.

## Current documents

1. [`01_parallel_access_geometry.md`](01_parallel_access_geometry.md)  
   Defines the common PMAG objects: execution domains, access maps, resource maps, fibers, structural contention, transformation families, objectives, capacity, feasibility, synthesis, certificates, implementation cost, and model boundaries.

2. [`02_capacity_and_obstruction.md`](02_capacity_and_obstruction.md)  
   Develops model-relative capacity and obstruction, proves the general rank-capacity theorem for `A + L X R`, derives the RM-001 hardware-aware rank ceiling, and formalizes the active-subspace/resource-image quotient and multi-access obstruction viewpoint.

## Evidence policy

The project distinguishes:

`DEFINITION`, `DERIVED`, `PROVED`, `EXHAUSTIVELY VERIFIED`, `SOLVER-CERTIFIED`, `HARDWARE-VALIDATED`, `EMPIRICAL`, `CONJECTURE`, and `OPEN`.

Finite exhaustive verification is not used as a substitute for a general proof outside the verified finite domain.
