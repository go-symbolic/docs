---
title: "Beyond the core: the Maple delta"
weight: 20
description: "Planned for wave 4: code generation to Go, algebraic numbers, ODE and PDE solving, triangular decomposition, differential algebra, symbolic random variables, permutation groups and tensors."
---

<!-- From go-scikit/roadmap PLAN.md and its bib/ files: the text is the plan's, summarised, with links to the references. Change it there first. -->

**Status: planned, wave 4, after the core; repositories not created yet, names provisional (`codegen`, `algnum`, `ode`, `pde`, `triangular`, `diffalg`, `stats`, `group`, `tensor`).**

What Maple offers beyond Mathematica's core, as a delta over it (PLAN.md items 19 to 21, wave 4). Maple is proprietary and is never run as an oracle: free tools are, and the expected values are frozen into test data by a Go generator that records the tool and its version.

No code exists yet: nothing on this page is released, measured or tested. It
says what the library is planned to provide and how it will be checked. The wave
and the order are in [PLAN.md](https://github.com/go-scikit/roadmap/blob/main/PLAN.md); the state of each item in
[STATUS.md](https://github.com/go-scikit/roadmap/blob/main/STATUS.md).

## What it will provide

| | |
|---|---|
| `codegen` | expressions to Go source with common-subexpression elimination on the hash-consed DAG, an optional Horner scheme, a fusion policy for floating point, a pluggable target interface; C and Fortran emitters as test oracles only |
| `algnum` | algebraic numbers (`RootOf`, `evala`-style): minimal polynomials, dynamic evaluation when the polynomial is reducible, norm, trace, GCD and factorisation over number fields |
| `ode` | classification, integrating factors, Lie symmetry heuristics, Kovacic's algorithm, hypergeometric reductions; a `checkodesol`-style verifier first, so a wrong solution is impossible |
| `pde` | heuristic separation, characteristic strips, travelling waves, splitting into cases |
| `triangular` | regular chains and triangular decomposition, then cylindrical algebraic decomposition by triangular decomposition |
| `diffalg` | rankings, Ritt reduction, Rosenfeld-Groebner differential elimination |
| `stats` | symbolic random variables: PDF, CDF, MGF and moments from distribution definitions |
| `group` | permutation groups by Schreier-Sims, conjugacy classes, Cayley tables, finitely presented groups |
| `tensor` | abstract-index tensors with Butler-Portugal canonicalisation, curvature tensors, noncommutative algebra, Lie algebras |

## References

The references the work starts from (bibliography first):

- [Kovacic, J. Symbolic Comput. 2(1) 1986](https://doi.org/10.1016/S0747-7171(86)80010-4)
- [Singer, Amer. J. Math. 103(4) 1981](https://doi.org/10.2307/2374045)
- [Olver, *Applications of Lie Groups to Differential Equations*, 2nd ed., GTM 107](https://doi.org/10.1007/978-1-4612-4350-2)
- [Della Dora, Dicrescenzo and Duval, EUROCAL '85, LNCS 204](https://doi.org/10.1007/3-540-15984-3_279)
- [Trager, Algebraic factoring and rational function integration, SYMSAC '76](https://doi.org/10.1145/800205.806338)
- [Chen and Moreno Maza, triangular decomposition, J. Symbolic Comput. 47(6) 2012](https://doi.org/10.1016/j.jsc.2011.12.023)
- [Collins, quantifier elimination by CAD, LNCS 33, 1975](https://doi.org/10.1007/3-540-07407-4_17)
- [Boulier, Lazard, Ollivier and Petitot, radicals of differential ideals, ISSAC 1995](https://doi.org/10.1145/220346.220367)
- [Downey, Sethi and Tarjan, common subexpressions, J. ACM 27(4) 1980](https://doi.org/10.1145/322217.322228)
- [Seress, *Permutation Group Algorithms*, CUP 2003](https://doi.org/10.1017/CBO9780511546549)
- [Portugal, tensor simplification, J. Phys. A 32(44) 1999](https://doi.org/10.1088/0305-4470/32/44/313)

## Reference implementations

| Implementation | Licence and use |
|---|---|
| SymPy `solvers/ode`, `combinatorics`, `codegen`, `cse` | BSD-3 text; port permitted file by file after reading the appended notices |
| FriCAS (`kovacic.spad` and others) | BSD-3-Clause; port permitted; the only free implementation of Kovacic's algorithm found |
| Reduce packages | BSD (top-level licence read); port permitted once each package header is read |
| GAP, msolve, LinBox, FFLAS-FFPACK, Cadabra2, Maxima, Singular | GPL or LGPL: oracles only, never copied |

## How it will be validated

Reference tools are run as oracles, driven from Go by exec with JSON on a pipe;
the unit suite must pass with no oracle installed.

| Oracle | How |
|---|---|
| ODE and PDE | SymPy `dsolve`, `classify_ode`, `checkodesol`; FriCAS for Kovacic; correctness by substituting the solution into the equation |
| Algebraic numbers | PARI/GP (`nffactor`, `polredabs`), python-flint |
| Triangular sets | msolve, Singular, Sage (oracle only); checked by radical membership and numerical solving |
| Groups | GAP, SymPy `combinatorics`; exact group order and conjugacy-class sizes |
| Code generation | SymPy `cse`, `ccode`, `fortran_code`; `go build` the output and compare with the interpreter |

What "correct" means:

| Area | Criterion |
|---|---|
| ODE and PDE | residual after substitution is zero, or below 1e-12 relative at 100 random points; the solution is general; wrong answers must be 0 |
| Algebraic numbers | exact equality after canonical form; norm and minimal polynomial equal PARI's |
| Triangular decomposition | union of the zero sets equals the input variety |
| Code generation | generated Go compiles, vet is clean, equals the interpreter within a stated ULP bound |
| Groups | exact: order, orbit sizes, class counts |

## Risks and open questions

- ODE solving is heuristic and open-ended; Kovacic is the only complete piece. A verifier-first design and a time limit per ODE control the cost.
- CAD and triangular decomposition are doubly exponential in the worst case; word-size polynomial arithmetic over Z/p comes first.
- The Maple help pages describe interfaces, rarely algorithms: attributions to Maple are inferences and flagged as such in the bibliography.
- Not planned: Mathematica's curated data (`Entity`, `CountryData`), the Maple `.mw` document round trip, and MapleSim's proprietary component libraries.
