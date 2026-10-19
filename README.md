# renovate-config

Shared Renovate presets. Extend them from a repository's `renovate.json` instead of repeating the same config everywhere.

```json
{
  "extends": [
    "config:best-practices",
    "github><account>/renovate-config",
    "github><account>/renovate-config:silent-automerge"
  ]
}
```

Substitute the account that hosts this repo. Files are fetched from the default branch on every run, so a change here applies to every extending repository at once.

## `default.json`

Referenced as `github><account>/renovate-config`.

- Dependency updates are evaluated once a week, before 09:00 `Asia/Shanghai`.
- The "closed pull request ignored" comment is suppressed.

## `silent-automerge.json`

Referenced as `github><account>/renovate-config:silent-automerge`.

Passing dependency updates are committed straight to the default branch, with no pull request, for:

- non-major `devDependencies`
- non-major, non-`0.x` `dependencies`

Automerge waits for passing check runs on the update branch, so a workflow that tests those dependencies also triggers on `push` to `renovate/**`:

```yaml
on:
  pull_request:
  push:
    branches: ['renovate/**']
```

Everything outside that scope — majors, `0.x` production dependencies, GitHub Actions, security fixes — keeps the usual pull-request flow.

## Validating changes

```sh
npx -p renovate@latest renovate-config-validator --no-global --strict default.json silent-automerge.json
```
