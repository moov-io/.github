[![Moov Banner Logo](https://user-images.githubusercontent.com/20115216/104214617-885b3c80-53ec-11eb-8ce0-9fc745fb5bfc.png)](https://github.com/moov-io)

Meta repository for Moov open-source projects. Join us in the [Moov slack](https://slack.moov.io/) or open a Github issue in the project you're using.

## Agent instructions

Org-wide coding-agent defaults live in [`AGENTS.md`](AGENTS.md).

GitHub does **not** inject a root `AGENTS.md` from this repository into other `moov-io` projects. Coding agents (Copilot, Claude, Codex, Grok, and others) only read `AGENTS.md` from the repository they are working in.

What this repository *does* share:

| Path | Who sees it |
| --- | --- |
| [`AGENTS.md`](AGENTS.md) | Canonical source. Copy or point to it from each project. |
| [`agents/moov.agent.md`](agents/moov.agent.md) | GitHub Copilot **custom agent**, available to pick in every `moov-io` repository. |

To apply the defaults in another project, add a root `AGENTS.md` that links here and then lists only local commands and layout. A local `AGENTS.md` / `CLAUDE.md` always wins on conflict.

## Shared workflows

Reusable GitHub Actions workflows live in [`.github/workflows/`](.github/workflows). Other `moov-io` repositories call them instead of copying the job definition.

### zizmor

[zizmor](https://docs.zizmor.sh) audits GitHub Actions workflows for security issues. Add this caller to each repo so it runs on every push and pull request:

```yaml
name: zizmor

on:
  push:
  pull_request:

permissions: {}

jobs:
  zizmor:
    permissions:
      security-events: write
      contents: read
      actions: read
    uses: moov-io/.github/.github/workflows/zizmor.yml@768836368a4ebdf0959c6497d41433e7193103cc
```

A [workflow template](.github/workflow-templates) is also available from the Actions tab when creating a new workflow.

Findings are uploaded to the repository's code scanning results and do not fail the job. Update `zizmor-action` in this repository; callers pick up the change automatically.
