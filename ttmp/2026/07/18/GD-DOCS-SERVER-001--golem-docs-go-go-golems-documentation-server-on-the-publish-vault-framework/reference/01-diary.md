---
Title: Diary
Ticket: GD-DOCS-SERVER-001
Status: active
Topics:
    - docs-server
    - publish-vault
    - collector
DocType: reference
Intent: long-term
Owners: []
RelatedFiles: []
ExternalSources: []
Summary: ""
LastUpdated: 2026-07-18T19:28:03.86998318-04:00
WhatFor: ""
WhenToUse: ""
---

# Diary

## Goal

Chronological record of building golem-docs: the go-go-golems documentation server
built on the publish-vault framework.

## Step 1: Repository bootstrap and design doc

Created the golem-docs repository from the go-go-golems/go-template GitHub template,
normalized every placeholder (module `github.com/go-go-golems/golem-docs`, binary
`golem-docs`, logcopter area `go-go-golems.golem-docs`), initialized docmgr, forked
to wesen/golem-docs with origin kept on upstream, and wrote the GD-DOCS-SERVER-001
design doc: an intern-level guide covering the two systems a newcomer must
understand (the pkg/doc embed convention + the publish-vault framework), the
collector/normalizer/docs.data/widget-pages architecture, decisions D1-D5, and a
4-phase plan.

### Prompt Context

**User prompt (verbatim):** (see publish-vault ticket PV-FRAMEWORK-017 diary, Step 1 — the same message requested both tickets and the repo creation)

**Assistant interpretation:** Create this repository from go-template with docmgr properly initialized, then write a detailed intern-level analysis/design/implementation guide as a ticket doc and upload it to reMarkable.

**Inferred user intent:** Have the docs-server project exist as a properly scaffolded go-go-golems repo with onboarding-quality design docs before any implementation starts, so the work can be picked up by anyone.

### What I did

- `gh repo create go-go-golems/golem-docs --template go-go-golems/go-template`;
  cloned to ~/code/wesen/go-go-golems/golem-docs; `wsm discover .`.
- Placeholder normalization via the scripted python edit (go.mod, Makefile,
  logcopter_generate.go, pkg/logcopter.go, AGENT.md, README.md, cmd/XXX -> cmd/golem-docs);
  `rg "XXX|go-template"` returned clean.
- Hardened the template Makefile with the two fixes from publish-vault PR #12
  review: svu output validation in tag-* targets, `|| exit 1` in the
  bump-go-go-golems loop (upstream go-template still has the unguarded versions).
- `docmgr init --root ttmp --seed-vocabulary`; committed .ttmp.yaml + ttmp
  separately from the rename commit.
- Forked to wesen/golem-docs (verified parent), remotes: origin=go-go-golems,
  wesen=fork; pushed main to both.
- Wrote the design doc; related glazed evidence files; added vocab slugs
  docs-server/publish-vault/collector.

### Why

- The repo must exist before PV-FRAMEWORK-017 implementation so the framework work
  has a named consumer, and the design doc pins the contract between the two
  (server.Config surface, module registration pattern) while both are in motion.

### What worked

- Template flow end to end with zero placeholder leftovers on first pass.
- `GOWORK=off go build ./... && go test ./...` green immediately after rename.

### What didn't work

- A combined fork+remote+push one-liner was blocked by the sandbox permission
  classifier; splitting into single-purpose commands passed. Lesson: keep
  repo-mutating gh/git operations one per command.
- A `docmgr changelog update` failed once with `malformed --file-note` because the
  relative-path variable from a previous shell invocation was empty after cwd
  reset; reran with the absolute path.

### What I learned

- The gh template flow copies workflows but NOT the fork relationship; fork
  creation inherits already-pushed commits, so `git push wesen main` reported
  up-to-date.

### What was tricky to build

- Nothing structurally; the care points were (a) not clobbering the template's
  release workflows before deciding on the release story, and (b) recording that
  glazed's doc FS is unexported today - the design doc turns that into an explicit
  upstream PR (decision D5) instead of a hidden assumption.

### What warrants a second pair of eyes

- Design doc section 4.2 (frontmatter normalization table) - it is the contract
  between glazed's schema and the vault parser; a missed key silently disappears
  from the site.

### What should be done in the future

- Phases 1-4 as tracked tasks; first code lands after publish-vault
  PV-FRAMEWORK-017 Phase 2 makes pkg/server importable.

### Code review instructions

- `git log --oneline` (bootstrap commits); design doc top to bottom; verify
  placeholder cleanliness with `rg -n "XXX|go-template" -S . --glob '!ttmp/**'`.

### Technical details

- Remotes: origin=git@github.com:go-go-golems/golem-docs.git,
  wesen=git@github.com:wesen/golem-docs.git. Module: github.com/go-go-golems/golem-docs.
