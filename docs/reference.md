# Reference

The public API lives at the module root (`github.com/go-ruby-kramdown/kramdown`). It is
**Ruby-shaped but Go-idiomatic**: names mirror Ruby's `kramdown`, while the surface follows
Go conventions — value types, explicit errors, no global state.

## Install

```sh
go get github.com/go-ruby-kramdown/kramdown
```

## Import

```go
import "github.com/go-ruby-kramdown/kramdown"
```

## API reference

The authoritative, always-current API reference is generated from the source by
pkg.go.dev:

- **[pkg.go.dev/github.com/go-ruby-kramdown/kramdown](https://pkg.go.dev/github.com/go-ruby-kramdown/kramdown)**

The module's [README](https://github.com/go-ruby-kramdown/kramdown#readme) carries worked
examples and the full, up-to-date surface. This page intentionally links to those
canonical sources rather than duplicating signatures that could drift out of date.

## Conformance

Behaviour is pinned by a **differential oracle** against reference Ruby: a corpus
is run through both the `ruby` binary and this library and the results are compared,
gated on the reference where relevant and skipping itself where `ruby` is absent so
the cross-arch lanes still validate the library.

**190 of 198 shared-corpus cases (95.96%) render byte-for-byte identically to the
Ruby `kramdown` 2.5.2 gem.** A shrink-only ratchet locks this in: a case outside the
known-failing ledger that stops matching fails CI, and a ledgered case that starts
matching must be graduated out.

### Not yet supported

`go-ruby-kramdown` is **not** spec-complete. The eight remaining corpus cases are:

- **LaTeX math** (4 cases) — block and span math beyond single-line MathJax
  `\[…\]` / `\(…\)` wrapping needs a real math engine (itex2mml / KaTeX) or the
  `:math_engine ~` fallback, which this pure-Go, no-Ruby port does not embed.
- **Rouge syntax-highlighted code blocks** (2 cases) — the common Rouge paths are
  wired through the pure-Go highlighter; `rouge/simple` still needs a PHP lexer and
  `rouge/multiple` selects a bespoke formatter defined inside kramdown's own Ruby
  test harness, which no pure-Go wiring can supply.
- **2 advanced IAL / attribute-list cases** — a trailing block IAL that kramdown
  attaches to a preceding table, and deferred nested ALD-reference resolution with
  kramdown's `update_attr_with_ial` accumulation ordering.

### Already ported

A large body of kramdown behaviour is covered, including a full port of kramdown's
`Parser::Html` raw-HTML front-end and `html_to_native` conversion, table-of-contents
generation, the HTML named-entity table, footnotes with back-links and ordering,
smart quotes and typographic substitutions, header options with auto-ids and
transliterated IDs, and CJK line-break handling.
