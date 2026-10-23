---
title: "go-symbolic documentation"
linkTitle: "Home"
type: docs
cascade:
  type: docs
description: "Computer algebra in pure Go. Planned, wave 4 of the go-scikit roadmap."
---

**go-symbolic is the planned home of computer algebra for go-scikit**: expressions, polynomials, differentiation, integration, series, limits and solving, as Mathematica's core offers them, and what Maple adds on top (code generation, algebraic numbers, ODE and PDE solving, triangular decomposition, differential algebra, symbolic random variables, groups and tensors). It is in **wave 4**, the last of the roadmap, because nearly everything in it waits for the core: the expression DAG, polynomials, GCD and factorisation.

**Status: planned (wave 4).** The organisation has no library and no
release tag. These pages state the scope, the plan and the references each
library will be validated against. The plan itself is
[PLAN.md](https://github.com/go-scikit/roadmap/blob/main/PLAN.md) of [go-scikit/roadmap](https://github.com/go-scikit/roadmap),
and the state of each item is in [STATUS.md](https://github.com/go-scikit/roadmap/blob/main/STATUS.md).

## Plan

In PLAN.md, wave 4:

- item 18: expression DAG, polynomials over Z, Q and F_p, GCD and factorisation, differentiation, LaTeX through go-tex; then Gröbner bases, integration, series and limits;
- item 19: `codegen`, right after the core (expression DAG and `diff`): expressions to Go source with common-subexpression elimination and pluggable targets, C and Fortran emitters as test oracles only;
- item 20: the Maple delta after the core: `algnum`, modular linear algebra, `ode` (verifier first) then `pde`, `triangular` (regular chains, CAD), `diffalg` and `stats`;
- item 21: `group` (Schreier-Sims) then `tensor` (index canonicalisation, curvature tensors, Lie algebras).

It builds on the number theory of go-bignum (wave 4, item 17), go-tex for rendering, and go-ndarray kernels for word-size prime-field linear algebra.

## The libraries

| Library | Status | What it will provide |
| --- | --- | --- |
| [Core: expressions, polynomials, integration]({{< relref "core" >}}) | planned (wave 4) | Planned for wave 4: the expression DAG, polynomials, GCD and factorisation, Gröbner bases, differentiation, integration, series, limits and exact linear algebra. |
| [Beyond the core: the Maple delta]({{< relref "maple" >}}) | planned (wave 4) | Planned for wave 4: code generation to Go, algebraic numbers, ODE and PDE solving, triangular decomposition, differential algebra, symbolic random variables, permutation groups and tensors. |

Every library of the go-scikit family is pure Go (`CGO_ENABLED=0`), validated
against the tool that is the reference of its domain, run as an oracle from Go,
and measured against it on one core. None of that is claimed here yet: there is
nothing to measure.

## What each library's pages hold

| Library | Pages |
| --- | --- |
| Core: expressions, polynomials, integration | [overview]({{< relref "core" >}}) |
| Beyond the core: the Maple delta | [overview]({{< relref "maple" >}}) |

