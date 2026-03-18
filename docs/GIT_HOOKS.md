# Git Hooks 启用说明

本仓库在 `.githooks/` 下提供：

| 钩子 | 作用 |
|------|------|
| `pre-commit` | 对暂存的 `tasks/**/*.json` 做 JSON 语法校验（需本机有 `python` 或 `python3`）。 |
| `post-merge` | `git pull` 合并后提示检查 `agents/` 与进度文件。 |

## 一次性启用（推荐）

在仓库根目录执行：

```bash
git config core.hooksPath .githooks
```

Windows（Git Bash）同样适用。启用后钩子需有执行权限；若提交时被拒绝，在 Bash 中执行：

```bash
chmod +x .githooks/pre-commit .githooks/post-merge
```

## PowerShell 仅使用 Git 时

若未用 Bash，可将 `core.hooksPath` 设为 `.githooks` 后，用 Git Bash 执行一次 `chmod +x`，或把同名 `.cmd` 包装复制到 `.git/hooks/`（高级用法，一般团队统一用 Git Bash 即可）。
