# Agent instructions for moov-io

These are the default operating rules for coding agents working in [moov-io](https://github.com/moov-io) repositories. They apply to this meta-repo and are the template for every other `moov-io` project.

A repository-local `AGENTS.md`, `CLAUDE.md`, or `.github/copilot-instructions.md` wins on conflict. Use those files for project-specific architecture, commands, and testdata — do not copy this whole file into them.

## What Moov OSS is

Moov publishes small, production-used libraries and services that implement a single financial-services responsibility: ACH, Fedwire, ISO 20022, image cash letter, Watchman, and related formats. Correctness against the spec matters more than convenience.

Community: [slack.moov.io](https://slack.moov.io). Security reports: `security@moov.io` (not public issues). See [SECURITY.md](.github/SECURITY.md).

## Before changing code

1. Read the repo `README.md`, `CONTRIBUTING.md` if present, and neighboring code.
2. For parsers and writers, treat the spec docs and existing testdata as authoritative. Do not guess field widths, record order, or XML/JSON paths.
3. Prefer a small, complete change. Do not drive-by refactor, rename, or reformat unrelated files.
4. Do not add a dependency without a clear need. Never introduce `github.com/moovfinancial/*` into a `moov-io` module (`lint-project.sh` rejects this).
5. Do not commit secrets, credentials, customer files, or live payment data.

## Go

Most repositories are Go modules under `github.com/moov-io/<repo>`.

- Follow [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments), [Go Proverbs](https://go-proverbs.github.io/), and the style of adjacent files.
- Keep the Apache 2.0 license header on new `.go` files (copy it from a neighbor).
- Reuse `github.com/moov-io/base` instead of reinventing time, http, strftime, or similar helpers.
- Exported APIs should stay boring: `Read`/`Write`/`Parse` from `io.Reader`/`io.Writer` where that is the existing pattern.
- Errors: typed or sentinel errors with useful context (`fmt.Errorf("...: %w", err)`). Do not discard errors. Do not panic in library code.
- Logging in services is typically go-kit. Do not log full payment files, account numbers, or other PII.
- `gofmt` is mandatory. Match existing names (`ID`, `ACH`, `NACHA`, `Fedwire`) rather than inventing new casing.

## Financial file formats

These libraries read and write money-movement files. Silent data loss is a bug.

- Never truncate, pad incorrectly, or coerce a value to “make the test pass.”
- Do not weaken validation, skip checksums, or change testdata golden files unless the spec change is explicit and covered by tests.
- Fixed-width records: field positions and lengths are part of the API. Preserve padding and encoding (ASCII, EBCDIC, X9, etc.) already used in the package.
- XML/ISO 20022: map against real sample messages in the repo, not against Go struct names.
- When adding a record type or message version, follow the existing factory/registration pattern and add tests that round-trip a real sample.

## Tests and checks

- Default check is `make check` when a Makefile exists; otherwise `go test ./...`. Many Makefiles download [moov-io/infra `lint-project.sh`](https://github.com/moov-io/infra/blob/master/go/lint-project.sh) — do not commit that script.
- Do not commit with a failing `make check`. Do not lower coverage thresholds to land a change.
- Tests are table-driven where there are multiple cases. Use `github.com/stretchr/testify/require` when the package already does.
- Test names follow existing patterns (`TestFoo`, `TestFoo_Bar`, `TestFoo__Bar`). Use `t.Helper()` in fixtures.
- Put fixtures under `testdata/` or the package’s existing sample directory. Prefer real-format files over hand-waved strings.
- Fuzz tests and `testdata` corpora exist in several repos; extend them rather than replacing them.

## HTTP APIs and services

- OpenAPI specs (`openapi.yaml`) are the contract for HTTP servers. Change the spec and regenerate clients with the repo’s `openapi-generator` helper; do not hand-edit generated `client/` trees except to make tests compile after regen.
- Docker images are published as `moov/<repo>`. Keep Dockerfiles boring and aligned with siblings.
- GitHub Actions are pinned by commit SHA. Reusable workflows live in this repository (see the README). Do not copy job YAML when a reusable workflow exists.

## Git and PRs

- Default branch is usually `master`.
- Small PRs with a title that describes the change. Prefer one complete commit per PR when practical.
- Do not force-push `master`. Do not use GitHub auto-close keywords (`fixes`, `closes`) unless the issue is actually resolved by this change.
- Generated files (OpenAPI clients, WASM, coverage dumps) follow whatever the Makefile already does. Do not check in `lint-project.sh`, `bin/`, or `coverage.txt` unless the repo already tracks them.

## Repo-specific files

If you are working in a project other than `moov-io/.github`, look for a local `AGENTS.md` or `CLAUDE.md` first. This file is the org default, not a substitute for that repo’s build commands, package map, or spec notes.

To adopt these defaults in another `moov-io` repository, add a root `AGENTS.md` that points here and then lists only local deltas:

```markdown
# AGENTS.md

Follow https://raw.githubusercontent.com/moov-io/.github/master/AGENTS.md

## Local
- Check: make check
- Spec: ./docs
```
