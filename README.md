# .github

Organization-wide GitHub configuration and **reusable workflows** — the single
point of control for CI that is shared across the org's repositories.

## Reusable workflows

### `scan-pull-request.yaml`

Runs two pull-request checks, implemented in plain shell (no third-party actions):

- **PR title** must follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) spec.
- **PR labels** — the PR must carry at least one of a configurable set of labels.

#### Using it from another repository

Add a thin caller workflow to the consuming repo at
`.github/workflows/scan-pull-request.yaml`:

```yaml
---
name: Scan Pull Request

on:
  pull_request:
    branches: [ main ]
    types: [opened, edited, synchronize, reopened, labeled, unlabeled]

jobs:
  scan:
    # Replace <org> with this GitHub organization.
    uses: <org>/.github/.github/workflows/scan-pull-request.yaml@main
    # Optionally override the accepted labels:
    # with:
    #   allowed_labels: 'api,bug,build,dependencies,documentation,enhancement,no-changelog,refactoring'
```

The caller owns the `pull_request` trigger (including the `types`, so that
label add/remove events re-run the check); the logic lives here, once.

#### Inputs

| Input            | Default                                                                  | Description                                                     |
|------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------|
| `allowed_labels` | `api,bug,build,dependencies,documentation,enhancement,no-changelog,refactoring` | Comma-separated labels; at least one must be set on the PR. |
