# docs

[![docs](https://github.com/go-symbolic/docs/actions/workflows/docs.yml/badge.svg)](https://github.com/go-symbolic/docs/actions/workflows/docs.yml)
[![License](https://img.shields.io/badge/license-BSD--3--Clause-9810A4?style=flat-square)](LICENSE)

**The go-symbolic documentation site**, published at
<https://go-symbolic.github.io/docs/>.

A [Hugo](https://gohugo.io/) site with the [tannevaled/hextra](https://github.com/tannevaled/hextra)
fork of the [Hextra](https://github.com/imfing/hextra) theme (pinned to v0.16.0),
built the same way as the [go-numerics documentation](https://github.com/go-numerics/docs),
with go-symbolic's branding. go-symbolic is **planned, wave 4**
([PLAN.md](https://github.com/go-scikit/roadmap/blob/main/PLAN.md)): no library is released, so the pages state the scope,
the plan and the references of each planned library.

## Versions

`params.libraries` in `hugo.yaml` names the release of each library the pages
describe, or `main` while it has no tag. A version of the documentation is
**the newest minor version among the tagged ones**, or `main` while none is
tagged:

| Source | Published under |
| --- | --- |
| branch `main`, with `params.libraries` in `hugo.yaml` | `https://go-symbolic.github.io/docs/<MAJOR.MINOR>/`, or `/docs/main/` |
| the newest version (else `main`) | also `https://go-symbolic.github.io/docs/latest/` |

`https://go-symbolic.github.io/docs/` redirects to `latest/`. Each push to `main`
replaces the directory of the version it describes, and nothing else. No library
is tagged yet, so the pages are published under `main/` (also `latest/`).

## Pages

| Page | What |
| --- | --- |
| home | the scope, the plan and the status |
| core | Core: expressions, polynomials, integration: what is planned, the references, the oracles and the criteria |
| maple | Beyond the core: the Maple delta: what is planned, the references, the oracles and the criteria |

A library's page will become its own documentation, summarised, once the
library exists. A comment at the top of each page names the source: here, the
roadmap. Change the text in the roadmap first, then here.

## Layout

| Path | What |
| --- | --- |
| `content/en/` | the pages; `_index.md` is the home, one directory per library, `weight` orders the sidebar |
| `i18n/en.yaml` | the site's strings, beside the theme's |
| `hugo.yaml` | `baseURL`, the library versions described, the navbar (version › theme › search › GitHub), the brand mounts |
| `layouts/_partials/custom/content-begin.html` | the outdated-version banner (reads `versions.json`) |
| `layouts/_partials/favicons.html` | the brand's favicons |
| `assets/css/custom.css` | the brand cyan as Hextra's primary colour, the table headers |
| `themes/hextra` | submodule: [tannevaled/hextra](https://github.com/tannevaled/hextra), pinned to v0.16.0 |
| `branding` | submodule: [go-symbolic/brand](https://github.com/go-symbolic/brand) (the mark, its PNGs and ICO) |
| `scripts/publish-version.sh` | puts one build into a `gh-pages` checkout and rewrites `versions.json`, `latest` and the root redirect |

## Working locally

```sh
git clone --recurse-submodules https://github.com/go-symbolic/docs.git
cd docs
mkdir -p data && go run github.com/tannevaled/hextra/cmd/pagehistory@v0.21.0 > data/pagehistory.json
hugo server --baseURL http://localhost:1313/docs/main/
```

then open <http://localhost:1313/docs/main/>. Hugo 0.146 or later; no Python,
no Node.

## Publication

`.github/workflows/docs.yml`:

| Job | When | What |
| --- | --- | --- |
| `build` | pull requests, `main` | builds with `--baseURL …/docs/<version>/`, checks internal links and anchors (lychee, offline), uploads the site |
| `links` | pull requests, `main` | fetches every external URL the pages name |
| `deploy` | `main` | `scripts/publish-version.sh` into `gh-pages` (started empty on the first deploy), then a commit and a push |

GitHub Pages serves the `gh-pages` branch.

## Licence

BSD-3-Clause.
