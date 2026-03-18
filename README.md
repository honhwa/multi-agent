# multi-agent

基于 **OpenClaw** 的 **Git 驱动异步多人协作写作** 仓库模板。

## 目录结构

| 目录 | 说明 |
|------|------|
| `agents/` | 各角色 Agent 配置：`coordinator.md`、`researcher.md`、`writer.md`、`reviewer.md`（SOUL 风格职责与边界） |
| `skills/` | 自定义技能（脚本、报告生成等），供 Agent 调用 |
| `memory/` | `MEMORY.md` 项目状态；`memory/shared/` 多 Agent 临时同步 |
| `drafts/` | 文稿草稿（按章节/版本命名，如 `report_v1.md`） |
| `research_data/` | 研究员产出（如 `market_size.md`、`players.md`） |
| `tasks/` | `task_breakdown.json`、`progress_log.md`、`review_comments.md` |
| `comments/` | 可选：按稿归档的审校意见 |
| `.githooks/` | `pre-commit` / `post-merge`（见 `docs/GIT_HOOKS.md`） |

## 角色与 Git 工作流（摘要）

1. **Coordinator**：维护任务拆解与进度日志 → 提交 `tasks/*`、`memory/MEMORY.md`  
2. **Researcher**：按任务写入 `research_data/`  
3. **Writer**：基于研究稿写 `drafts/`，按审校意见迭代版本  
4. **Reviewer**：写入 `tasks/review_comments.md` 或 `comments/`  

全员通过 **Pull / Push** 同步；版本与责任以 **Git 历史** 为准。

## 案例参考

《2026 AI 行业趋势研究报告》：`tasks/task_breakdown.json` 已预置与子任务一致的示例条目；研究产出路径与方案中的 `market_size.md`、`players.md` 等对齐。

## 启用 Git Hooks

```bash
git config core.hooksPath .githooks
chmod +x .githooks/pre-commit .githooks/post-merge
```

详见 [docs/GIT_HOOKS.md](docs/GIT_HOOKS.md)。

## 前提条件

- Git 仓库已初始化（本目录可直接 `git add` / `commit`）。  
- OpenClaw 侧：按 `agents/*.md` 配置各角色 Agent 的工具权限与工作区。  
- 可选：Python 用于 pre-commit 的 JSON 校验。

## 注册 Agent 与工作空间规范

本仓库假定你使用 **OpenClaw** 的 `openclaw add` 命令，为四个角色分别注册长期驻场的 Agent，并为每个 Agent 准备独立的工作空间（workspace）。

### 1. 总体约定

- 每个 Agent 对应一个独立的 **workspace 目录**，示例（自行调整路径）：  
  - Coordinator：`~/openclaw-workspaces/coordinator/`  
  - Researcher：`~/openclaw-workspaces/researcher/`  
  - Writer：`~/openclaw-workspaces/writer/`  
  - Reviewer：`~/openclaw-workspaces/reviewer/`
- **每个 workspace 内，都需要先将本 Git 仓库完整克隆一份**，并在该副本中进行 `git add` / `git commit` / `git pull` / `git push`。
- 每个 Agent workspace 的推荐结构：

```text
<agent-workspace>/
  SOUL.md        # 该 Agent 的人格 / 角色配置（与仓库中 agents/*.md 对齐并更详细）
  TOOLS.md       # 可用工具与限制（Web 搜索、本地脚本、Git 操作范围等）
  USER.md        # 该 Agent 所面对的「用户/负责人」上下文说明
  memory/        # Agent 私有记忆（与仓库 global memory/MEMORY.md 区分）
  skills/        # 该 Agent 专属或本地包装的技能
  repo/          # 在此目录下 git clone 本仓库
```

**克隆示例（以 Coordinator 为例）：**

```bash
mkdir -p ~/openclaw-workspaces/coordinator
cd ~/openclaw-workspaces/coordinator
git clone git@github.com:zhangyoujian/multi-agent.git
```

其他 Agent 只需替换上级目录名即可。

### 2. Coordinator Agent（协调）

**推荐 workspace 结构：**

```text
~/openclaw-workspaces/coordinator/
  SOUL.md
  TOOLS.md
  USER.md
  memory/
  skills/
  repo/      # 克隆的 multi-agent 仓库
```

- **SOUL.md（示例要点）**
  - 角色：负责任务拆解、进度同步、版本节奏控制，对应仓库 `agents/coordinator.md` 中的职责。
  - 目标：确保「研究 → 写作 → 审校」流水线稳定高效，所有任务在 `tasks/task_breakdown.json` 与 `tasks/progress_log.md` 中可追踪。
  - 主要文件：
    - 读写：`tasks/task_breakdown.json`、`tasks/progress_log.md`、`memory/MEMORY.md`
    - 只读：`agents/*.md`、`drafts/`、`research_data/`、`tasks/review_comments.md`
  - 决策风格：偏保守、重可追溯性；避免直接修改他人核心产出（如正文和研究数据），更多通过任务与日志驱动。

- **TOOLS.md（示例要点）**
  - 允许的工具：
    - 文件读写：限定在 `repo/tasks/`、`repo/memory/`、`repo/agents/`。
    - Git：仅在 `repo/` 目录下执行 `git status` / `git add` / `git commit` / `git pull` / `git push`。
    - 可选：调用 OpenClaw 内置的 task scheduling / notification 技能。
  - 禁止：
    - 修改 `repo/drafts/` 实际正文内容。
    - 修改 `repo/research_data/` 中数据（仅协调，不干预数据本身）。

- **USER.md（示例要点）**
  - 描述项目负责人（人类）角色：如何给你任务、你要产出的文件、期望反馈周期（例如：每次重要变更后更新 `progress_log.md`）。

- **memory/**（示例内容）
  - `memory/checkpoints.md`：记录阶段性里程碑（如「初稿完成」「审校第二轮完成」）。
  - `memory/open_issues.md`：记录当前未解决的协作问题。

- **skills/**（示例内容）
  - `task_template/`：内含任务模板片段，方便自动扩写 `task_breakdown.json`。

> 注册时，使用 `openclaw add` 将该 workspace 目录设置为 Coordinator Agent 的根目录，SOUL/TOOLS/USER 路径按工具配置指向上述文件。

### 3. Researcher Agent（研究员）

**workspace 结构：**

```text
~/openclaw-workspaces/researcher/
  SOUL.md
  TOOLS.md
  USER.md
  memory/
  skills/
  repo/
```

- **SOUL.md 重点：**
  - 角色：根据 `tasks/task_breakdown.json` 中分配给 Researcher 的任务，产出结构化研究资料。
  - 主要目标：
    - 在 `repo/research_data/` 中生成或更新 `market_size.md`、`players.md`、`policy.md`、`tech_trends.md`、`cases.md` 等文件。
    - 对每条关键数据附带「来源 + 日期」说明。
  - 文件权限：
    - 读写：`repo/research_data/**`、`repo/memory/shared/**`
    - 只读：`repo/tasks/task_breakdown.json`、`repo/tasks/progress_log.md`、`repo/agents/researcher.md`

- **TOOLS.md 重点：**
  - 允许：
    - Web 搜索类技能：访问权威报告（IDC、Gartner、论文库等）。
    - 数据清洗技能：`skills/` 下自定义脚本（如表格整理、单位换算）。
    - Git：在 `repo/` 下执行 `git add` / `git commit` / `git pull` / `git push`，提交研究结果。
  - 禁止：
    - 修改 `repo/drafts/` 正文。
    - 修改 `repo/tasks/task_breakdown.json` 的任务结构。

- **USER.md 重点：**
  - 描述「资料组成员」如何向 Researcher 下达需求：比如在 `tasks/task_breakdown.json` 中新增任务，或在 `memory/MEMORY.md` 中标注重点关注领域。

- **memory/** 示例：
  - `memory/sources_log.md`：记录常用数据源、使用说明与可能的偏差。
  - `memory/search_history.md`：记录关键搜索策略，便于复用。

- **skills/** 示例：
  - `web_search/`：封装调用 OpenClaw Web 搜索的统一入口。
  - `data_clean/`：针对某些报告格式的解析脚本。

### 4. Writer Agent（写作）

**workspace 结构：**

```text
~/openclaw-workspaces/writer/
  SOUL.md
  TOOLS.md
  USER.md
  memory/
  skills/
  repo/
```

- **SOUL.md 重点：**
  - 角色：基于 `repo/research_data/` 与任务需求，撰写与迭代 `repo/drafts/` 下的文稿。
  - 目标：
    - 遵守「结论先行，数据支撑」，结构清晰、易读。
    - 根据 `repo/tasks/review_comments.md` 或 `repo/comments/` 中的意见迭代版本（`report_v1.md` → `report_v2.md` 等）。
  - 文件权限：
    - 读写：`repo/drafts/**`
    - 只读：`repo/research_data/**`、`repo/tasks/review_comments.md`、`repo/comments/**`、`repo/agents/writer.md`

- **TOOLS.md 重点：**
  - 允许：
    - 模板/章节生成类技能（如「自动生成摘要骨架」「自动生成结论草稿」等）。
    - Git：在 `repo/` 下提交文稿版本。
  - 禁止：
    - 随意篡改 `repo/research_data/` 中的数字；如需更正，应通过 Researcher 处理。

- **USER.md 重点：**
  - 描述 Writer 接受任务的方式（Coordinator/Reviewer 如何指派章节、截止时间），以及 Writer 提交产出的约定（包括 commit message 风格）。

- **memory/** 示例：**
  - `memory/style_guide.md`：团队写作风格与禁忌（避免套话、避免无数据泛谈）。
  - `memory/structure_snippets.md`：常见章节模板（市场分析、竞品分析等）。

- **skills/** 示例：**
  - `outline_helper/`：自动从任务与研究数据生成章节大纲。
  - `rewrite_helper/`：根据审校意见重写段落时使用。

### 5. Reviewer Agent（审校）

**workspace 结构：**

```text
~/openclaw-workspaces/reviewer/
  SOUL.md
  TOOLS.md
  USER.md
  memory/
  skills/
  repo/
```

- **SOUL.md 重点：**
  - 角色：对 `repo/drafts/` 中指定版本进行「批评-审查者」式审校。
  - 目标：
    - 检查逻辑、数据引用、格式一致性。
    - 将意见写入 `repo/tasks/review_comments.md` 或 `repo/comments/<稿件>.md`，而不是直接覆盖正文（除非团队特别约定）。
  - 文件权限：
    - 只读：`repo/drafts/**`、`repo/research_data/**`
    - 读写：`repo/tasks/review_comments.md`、`repo/comments/**`

- **TOOLS.md 重点：**
  - 允许：
    - 文本审校/质量评估技能（逻辑检查、事实一致性检查等）。
    - Git：在 `repo/` 下提交审校意见的变更。
  - 禁止：
    - 在未被授权的情况下直接修改 `repo/drafts/` 正文内容。

- **USER.md 重点：**
  - 描述审校的输入输出约定：输入是哪个稿件版本、审校粒度（整体结构 vs. 细节语言）、输出在何处记录。

- **memory/** 示例：**
  - `memory/checklist.md`：审校检查清单（数据、结构、风格、引用等）。
  - `memory/edge_cases.md`：历史上出现过的典型问题与处理方式。

### 6. 在各自 workspace 中使用 Git

每个 Agent 在开始工作前，均应：

1. 在自己的 workspace 下完成仓库克隆：

   ```bash
   cd <agent-workspace>
   git clone <远程地址> repo
   ```

2. 所有修改都在 `repo/` 子目录中完成，并遵循：

   ```bash
   cd repo
   git pull              # 先与远程同步
   # ... 进行文件修改 ...
   git status
   git add <相关文件>
   git commit -m "清晰的改动说明"
   git pull --rebase     # 解决可能的冲突
   git push
   ```

3. 各 Agent 仅在自己职责范围内的文件上执行 `git add`，避免误提交无关文件。

通过上述约定，可以让 OpenClaw 中的多 Agent 与本仓库的 Git 工作流、目录结构、角色配置做到**一一对应、可追溯、可审计**。 
