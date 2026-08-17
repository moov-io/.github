[![Moov Banner Logo](https://user-images.githubusercontent.com/20115216/104214617-885b3c80-53ec-11eb-8ce0-9fc745fb5bfc.png)](https://github.com/moov-io)

Meta repository for Moov open-source projects. Join us in the [Moov slack](https://slack.moov.io/) or open a Github issue in the project you're using.

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
    uses: moov-io/.github/.github/workflows/zizmor.yml@master
```

A [workflow template](.github/workflow-templates) is also available from the Actions tab when creating a new workflow.

Findings are uploaded to the repository's code scanning results and do not fail the job. Update `zizmor-action` in this repository; callers pick up the change automatically.
