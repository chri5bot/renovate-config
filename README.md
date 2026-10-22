# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for the repositories under
`chri5bot`, `cybergarden-team` and `TechDevStudio`. It holds no secrets, so it can be
read from any repository that extends it.

## Use it in a repository

Add a `renovate.json` at the repository root:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>chri5bot/renovate-config"]
}
```

Renovate reads `default.json` from this repository's default branch on every run, so
changes here apply everywhere without touching the consuming repositories.

The Mend Renovate GitHub App must be installed on the account or organization that
owns the consuming repository.

## What the preset does

| Concern | Setting |
| --- | --- |
| Baseline | `config:recommended` (all managers enabled: bun, pep621/uv, nuget, dockerfile, docker-compose, github-actions, ...). bun is the standard JavaScript package manager across these repositories |
| Cadence | One run per week, Monday before 07:00 America/Guayaquil |
| Non-major updates | One grouped PR per repository (`group:allNonMajor`) |
| GitHub Actions | Their own grouped PR, never mixed with runtime dependencies |
| Major updates | Listed on the Dependency Dashboard issue; a PR opens only after you tick the item there |
| Security | OSV vulnerability alerts open a PR at any time, labelled `security` |
| Release age | Versions younger than 3 days are skipped (`minimumReleaseAge`), and no PR opens while a version is still inside that window (`internalChecksFilter: strict`) |
| Rebases | Only when a PR conflicts with its base (`rebaseWhen: conflicted`), never after every merge |
| Majors that move together | `vite`, `@vitejs/*`, `vitest` and `@vitest/*` majors form one group, so vite and its React plugin land in the same PR |
| Formatters and linters | `@biomejs/biome`, `prettier`, `eslint` and `oxlint` are pinned to exact versions, so a new release never changes formatting inside a lockfile PR |
| Bun | Every Bun pin moves in one PR, including a `bun-version` input passed to a reusable workflow (a custom regex manager; it assumes 2-space YAML) |
| Lockfiles | Weekly lockfile maintenance PR on Thursday, apart from Monday's grouped PR so the two never conflict in `bun.lock` |
| Commits | Conventional Commits: `chore(deps): ...` |
| Limits | At most 3 open PRs and 2 new PRs per hour per repository |
| Automerge | Off. CI runs locally for now: every PR merges after `merge-gate` (platform) is green |

## Override per repository

Anything in a repository's `renovate.json` wins over this preset. Typical overrides:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>chri5bot/renovate-config"],
  "ignorePaths": ["deploy/**", "context/**"]
}
```

## Validate a change

```sh
npx --yes --package renovate renovate-config-validator default.json
```

The validator must print no errors before a change to this preset is pushed.
