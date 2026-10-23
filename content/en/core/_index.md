---
title: "Core: expressions, polynomials, integration"
weight: 10
description: "Planned for wave 4: the expression DAG, polynomials, GCD and factorisation, Gröbner bases, differentiation, integration, series, limits and exact linear algebra."
---

<!-- From go-scikit/roadmap PLAN.md and its bib/ files: the text is the plan's, summarised, with links to the references. Change it there first. -->

**Status: planned, wave 4; repositories not created yet, names provisional (`expr`, `poly`, `groebner`, `integrate`, `series`, `linalg`).**

The core is Mathematica's symbolic core in Go: an expression DAG with canonical forms, polynomial arithmetic over Z, Q and F_p, GCD and factorisation, differentiation, LaTeX output, then Gröbner bases, integration, series and limits (PLAN.md items 18 and 21, wave 4).

No code exists yet: nothing on this page is released, measured or tested. It
says what the library is planned to provide and how it will be checked. The wave
and the order are in [PLAN.md](https://github.com/go-scikit/roadmap/blob/main/PLAN.md); the state of each item in
[STATUS.md](https://github.com/go-scikit/roadmap/blob/main/STATUS.md).

## What it will provide

| | |
|---|---|
| Expression DAG | hash-consed, canonical forms, `diff`, `expand`, LaTeX output through go-tex |
| Polynomials | over Z, Q and F_p, rational functions; word-sized `uint64` fast paths for F_p |
| GCD | modular GCD (Brown) with CRT, heuristic GCD for small cases, sparse Zippel for multivariate |
| Factorisation | over F_p: square-free, distinct-degree, equal-degree (Cantor-Zassenhaus); over Z: factor mod p, Hensel lifting, recombination by LLL (van Hoeij) |
| Gröbner bases | Buchberger with criteria, then F4 modulo p and lifting; polynomial-system solving through lex order (FGLM), triangular sets and resultants |
| Integration | rational (Hermite reduction, Lazard-Rioboo-Trager), the Risch algorithm for elementary functions, Risch-Norman, and a rule-based engine importing Rubi's rules |
| Series and limits | truncated power series with Newton iteration; Gruntz's algorithm for limits |
| Exact linear algebra | Bareiss fraction-free elimination, multi-modular with CRT, Dixon p-adic lifting |

## References

The references the work starts from (bibliography first):

- [von zur Gathen and Gerhard, *Modern Computer Algebra*, 3rd ed., CUP 2013](https://doi.org/10.1017/CBO9781139856065)
- [Geddes, Czapor and Labahn, *Algorithms for Computer Algebra*, Kluwer 1992](https://doi.org/10.1007/b102438)
- [Bronstein, *Symbolic Integration I: Transcendental Functions*, 2nd ed., Springer 2005](https://doi.org/10.1007/b138171)
- Cantor and Zassenhaus, Math. Comp. 36 (1981) 587-592, doi:10.1090/S0025-5718-1981-0606517-5
- [van Hoeij, J. Number Theory 95 (2002) 167-189](https://doi.org/10.1006/jnth.2001.2763)
- [Buchberger, Aequationes Math. 4 (1970) 374-383](https://doi.org/10.1007/BF01844169)
- Faugère, F4, J. Pure Appl. Algebra 139 (1999) 61-88, doi:10.1016/S0022-4049(99)00005-5
- Risch, Trans. AMS 139 (1969) 167-189, doi:10.1090/S0002-9947-1969-0237477-8
- [Rich, Scheibe and Abbasi, rule-based integration (Rubi), JOSS 3(32) 2018](https://doi.org/10.21105/joss.01073)
- [Rubi rules and test suite](https://rulebasedintegration.org/)

## Reference implementations

| Implementation | Licence and use |
|---|---|
| SymPy (Python) | BSD-3 text; port permitted file by file after reading the appended notices; reference for Gruntz, Risch, `solve`, `simplify` |
| FriCAS (Common Lisp) | BSD-3-Clause; port permitted; the most complete Risch implementation |
| Rubi (Mathematica rules and tests) | MIT; rules and the 72,000-problem suite are reusable data |
| FLINT, PARI/GP, Maxima, Giac, Sage | LGPL or GPL: oracles only, never copied |

## How it will be validated

Reference tools are run as oracles, driven from Go by exec with JSON on a pipe;
the unit suite must pass with no oracle installed.

| Oracle | How |
|---|---|
| SymPy | `uv run --with sympy`; compare by `simplify(a-b)==0` or numeric spot checks, not strings |
| python-flint | `uv run --with python-flint`; `fmpz_poly.factor()`, `fmpz_mpoly` |
| PARI/GP, Maxima, FriCAS | exec with JSON or text on a pipe; FriCAS to verify Risch results |
| Rubi suite | converted to JSON of (integrand, variable, optimal antiderivative); checked by differentiation |

What "correct" means:

| Area | Criterion |
|---|---|
| Polynomials, exact linear algebra | exact equality after canonical form |
| Factorisation | product equals the input and every factor is irreducible per the oracle |
| Gröbner | the reduced basis is unique for a given order: exact equality with Singular, Giac or Sage output |
| Integration | derivative of the result minus the integrand simplifies to 0; Rubi grade histogram (A, B, C, F) |
| Limits, series, solve | exact equality of normal forms, or numeric spot checks at random points with N-digit arithmetic |
| Output | LaTeX compiles and typesets through go-tex; round trip through a parser gives the same tree |

## Risks and open questions

- A CAS comparable to Mathematica is a decades-long target; Rubi, Risch, Gröbner and polynomial factorisation are each a project.
- Two correct antiderivatives can differ by a constant or a form, so the oracle comparison is by differentiation.
- GC pressure on many small `big.Int` is a risk for polynomial arithmetic; word-sized coefficient fast paths for F_p come first.
- GPL and LGPL systems are oracles only; SymPy, SymEngine and Reduce need their LICENSE files read before any port.
