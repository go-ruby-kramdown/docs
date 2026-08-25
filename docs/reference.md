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

**197 of 198 shared-corpus cases (99.49%) render byte-for-byte identically to the
Ruby `kramdown` 2.5.2 gem.** A shrink-only ratchet locks this in: a case outside the
known-failing ledger that stops matching fails CI, and a ledgered case that starts
matching must be graduated out.

### Not yet supported

`go-ruby-kramdown` is **not** spec-complete, but the single remaining corpus case
is a test-harness artifact rather than a library feature:

- **`rouge/multiple`** — this case sets `formatter: RougeHTMLFormatters` in its
  options, naming a custom Rouge formatter subclass defined *only inside kramdown's
  own Ruby test harness* (kramdown 2.5.2 `test/test_files.rb`), whose `#stream`
  wraps every block in an extra `<div class="custom-class">`. It is not a kramdown
  library feature — the options file references an arbitrary Ruby class the gem's
  test process happens to have loaded — so no pure-Go wiring can resolve or run it.
  The three code blocks it contains (two Ruby, one PHP) already tokenise
  byte-for-byte; only the harness-injected wrapper div is unreachable.

### Already ported

Essentially all of kramdown's rendered behaviour is covered, including a full port
of kramdown's `Parser::Html` raw-HTML front-end and `html_to_native` conversion,
table-of-contents generation, the complete 905-entry HTML named-entity table,
footnotes with back-links and ordering, smart quotes and typographic substitutions,
header options with auto-ids and transliterated IDs, CJK line-break handling, LaTeX
**math** (`$$…$$` block/span, MathJax-wrapped by default), Inline Attribute Lists /
Attribute List Definitions (IAL/ALD), and **Rouge** syntax highlighting via the
pure-Go go-ruby-rouge lexers — including the PHP lexer that closed `rouge/simple`.
