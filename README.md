# gh-actions-workflows

Reusable GitHub Actions workflows for gitignore-in repositories.

## Workflows

- `actionlint.yml`: runs `rhysd/actionlint` against the caller repository.
- `markdownlint.yml`: runs `markdownlint-cli2` against the caller repository.
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

The workflow follows actionlint's official Docker action pattern. It runs the
official image with both the readable `1.7.12` tag and its immutable
multi-platform digest pinned. With no arguments, actionlint uses its default
repository discovery and enables its bundled ShellCheck integration.

The workflow no longer exposes `files`, `actionlint-version`, `shellcheck`, or
`shellcheck-enabled` inputs. Callers upgrading from an older revision must
remove those values from their `with:` block. A known caller currently passes
`files` while referencing an older immutable workflow commit, so it is not
affected until that reference is updated.

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

### Markdownlint

```yaml
name: Markdownlint
on:
  pull_request:
permissions:
  contents: read
jobs:
  markdownlint:
    uses: gitignore-in/gh-actions-workflows/.github/workflows/markdownlint.yml@main
    with:
      globs: "README.md docs/*.md"
```

A `.markdownlint.json` in the caller repository is picked up automatically by
markdownlint-cli2.
