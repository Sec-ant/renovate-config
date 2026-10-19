# renovate-config

Sec-ant 的共享 Renovate preset。仓库里的 `renovate.json` 只需 `extends`，降噪策略集中在这里改。

## Presets

| 引用 | 作用 |
| --- | --- |
| `github>Sec-ant/renovate-config` | `default.json`：统一 `timezone` + 每周一 9 点前跑；关掉 `prIgnoreNotification` 评论 |
| `github>Sec-ant/renovate-config:silent-automerge` | `silent-automerge.json`：CI 绿的 non-major 升级静默合入默认分支，不建 PR；安全修复类排除在外 |

## 已启用仓库

- 两个 preset 都用（有分支 CI 作为闸门）：`zxing-wasm`、`readable-stream`、`barcode-detector`、`lezer-mdast-markdown`、`ecma-re`、`lezer-python-regex`、`lezer-template-plaintext`
- 只用 `default.json`（没有测试 CI，不能开自动合并）：`gm-fetch`、`zxing-wasm-demo`

## silent-automerge 的生效前提

Renovate 只有在**分支上能看到通过的 check run** 时才会自动合并。所以这些仓库的 `test.yml` 必须同时对
`renovate/**` 的 push 跑：

```yaml
on:
  pull_request:
  push:
    branches: ['renovate/**']
```

缺这段时静默合并只是不生效（不会瞎合）。反过来，如果给没有测试的仓库打开
`silent-automerge`，要么不生效，要么需要 `ignoreTests: true` 才能生效 —— 后者等于放弃质量闸门，不要这么干。

## 有意留在 PR 模式的升级

- major
- 0.x 的生产依赖
- GitHub Actions（`depType` 不是 `dependencies`/`devDependencies`，且 action digest/版本变更属于要人看的一类）
- 安全修复类 PR（`vulnerabilityAlerts` 显式 `automerge: false`）
- CI 跑失败或长期 pending 的分支（25 小时后开 PR 找你）

## 校验

```sh
npx -p renovate@latest renovate-config-validator --no-global --strict default.json silent-automerge.json
```
