# gh-actions-workflows

Reusable GitHub Actions workflows for gitignore-in repositories.

## Workflows

- `actionlint.yml`: runs `rhysd/actionlint` against the caller repository.
- `spellcheck.yml`: runs `crate-ci/typos` against the caller repository.

Caller repositories should keep only small workflow files that define the
trigger, permissions, and `jobs.<job_id>.uses` reference. The reusable workflows
run in the caller repository context after `actions/checkout`.

## Caller Examples

### Actionlint

```yaml
name: Actionlint
on:
  pull_request:
permissions:
  contents: read
jobs:
  actionlint:
    uses: gitignore-in/gh-actions-workflows/.github/workflows/actionlint.yml@main
```

Callers can customize the checked files and ShellCheck behavior:

```yaml
jobs:
  actionlint:
    uses: gitignore-in/gh-actions-workflows/.github/workflows/actionlint.yml@main
    with:
      files: ".github/workflows/*.yml action.yml"
      shellcheck-enabled: false
```

The `files` input is passed unquoted to the shell so that glob patterns expand
normally. Pass one or more space-separated shell glob patterns. Paths containing
spaces are not supported. When overriding `actionlint-version`, pass an
actionlint release tag such as `v1.7.12`. The workflow validates the tag format
before invoking `go run`.

The legacy `shellcheck: disabled` input is accepted for compatibility, but new
callers should prefer `shellcheck-enabled: false`.

### Spellcheck

```yaml
name: Spellcheck
on:
  pull_request:
permissions:
  contents: read
jobs:
  check:
    uses: gitignore-in/gh-actions-workflows/.github/workflows/spellcheck.yml@main
```

If a repository needs to check a non-root path or a custom typos configuration,
pass explicit inputs from the caller workflow:

```yaml
jobs:
  check:
    uses: gitignore-in/gh-actions-workflows/.github/workflows/spellcheck.yml@main
    with:
      files: docs
      config: .github/typos.toml
```

## Development

This repository dogfoods its reusable workflows through `.github/workflows/ci.yml`.

Local checks:

```sh
actionlint
typos
```
