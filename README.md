# buildd-ai/.github

Org-shared GitHub workflows, actions, and templates for buildd-ai repos.

## Reusable workflows

### Release

Detects a conventional-commit semver bump on `dev`, opens a release PR to `main`, lets a per-repo `release-tag.yml` auto-tag on merge.

**Caller** (`.github/workflows/release.yml` in your repo):

```yaml
name: Release
on:
  schedule: [{ cron: '0 9 * * *' }]
  workflow_dispatch:
    inputs:
      force:
        description: 'Bypass "no shippable commits" check'
        default: 'false'
jobs:
  release:
    uses: buildd-ai/.github/.github/workflows/release.yml@main
    with:
      # Space-separated package.json paths to bump. Defaults to root package.json.
      package_files: 'package.json apps/web/package.json'
      force: ${{ inputs.force == 'true' }}
```

Pair with these in the caller repo:
- `release-tag.yml` — tags `main` and creates the GitHub release when the release PR merges
- `sync-dev.yml` — resets `dev` to `main` on every push to `main`, so successive release PRs stay clean

## Scripts

- `scripts/release.sh` — vendored by the reusable workflow at runtime. Conventional commits → semver bump → bump versions in `$PACKAGE_FILES` (space-separated, default: typical buildd monorepo layout) → open the release PR.
