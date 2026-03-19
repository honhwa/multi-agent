# OpenClaw：根据 `agents/` 创建协同智能体（INSTRUCTION）

本文档面向 **OpenClaw 编排层 / 自动化脚本**：请 **严格依据** 本仓库 `./agents/` 下四个 Markdown 文件，为每个角色生成磁盘上的 **SOUL.md、TOOLS.md、USER.md**，注册 Agent，预装技能。
目标：专业分工、协同清晰、产出路径明确。

---
## 1. 总原则

1. **单一真相源**：每个角色的完整参数以 `./agents/<role>.md` 为准（`coordinator.md`、`researcher.md`、`writer.md`、`reviewer.md`）。
2. **目录隔离**：Agent 私有文件（SOUL/TOOLS/USER、私有 `memory/`、私有 `skills/`）**不得**写入仓库克隆目录；
3. **提交前缀**：各 Agent 在 `multi-agent/` git commit信息 必须以 `[coordinator]` / `[researcher]` / `[writer]` / `[reviewer]` 开头（与对应文件一致）。
---

## 2. OpenClaw 注册agent步骤

### 确定注册名和配置元

| OpenClaw 注册名 | 配置源文件 |
|-----------------|----------------------|
| coordinator     | `agents/coordinator.md` |
| researcher      | `agents/researcher.md` |
| writer          | `agents/writer.md` |
| reviewer        | `agents/reviewer.md` |


### 逐步创建上述四个智能体，（以创建coordinator为例），其他智能体参照此流程：
```bash
openclaw agents add coordinator 
```

- **生成角色配置**: 生成coordinator专属的SOUL.md、TOOLS.md、USER.md, 参考`agents/coordinator.md`相关指示和要求
- **为角色预装技能**： 打开 `agents/coordinator.md` 中的「预装 Skills」列表，在 OpenClaw 中为该 Agent **启用同名或等价能力**（若市场技能名不同，在 TOOLS.md 中已说明映射关系则由实现方适配）
---

- **创建团队**： `openclaw teams create ... --agents coordinator,researcher,writer,reviewer` 指令，创建协同团队。

## 3. Git 在 仓库`multi-agent/` 内的标准流程（）

```bash
git pull --rebase
git status
git add <允许的文件>
git commit -m "[RoleName] 简要说明"
git pull --rebase
git push
```

**各角色允许改动的路径**见对应 `agents/<role>.md` 的「仓库内可写路径」表；**禁止**修改未授权路径、禁止在 `multi-agent/` 根目录创建 Agent 私有配置文件。

---

## 4. 智能体内部通信 (OpenClaw 须落实的行为)

在 openclaw.json 中添加配置：
```json
{
  tools: {
    agentToAgent: {
      enabled: true,
      allow: ["coordinator", "researcher", "writer", "reviewer"],
    },
  },
}

```

在一个智能体的会话中，可以直接调用 sessions_send 向另一个智能体发消息。例如： coordinator通知researcher开始执行任务
```bash
让 researcher 帮我从网上搜集资料，输出research_data.md并提交
```

## 5. 智能体内部协作 (agent 须落实的行为)
**

1. **协同清晰** 
    任务进度同步：`coordinator`定期更新progress_log.md（如“`researcher`已完成数据收集，`writer`需提交初稿”），团队成员通过git pull获取最新状态。
    修改意见传递：`reviewer` 根据（如drafts/chapter1_v1.md） 撰写修改意见到 comments/review_comments.md中, 并通过git commit 提交修改意见
    草稿更新:    `writer`根据意见修改后生成（drafts/chapter1_v2.md），并通过git commit说明修改内容（如“根据审校意见补充2025年市场规模数据”）。
    版本回溯与审计：Git的Commit历史可追溯每个Agent的修改轨迹（如“2026-03-15 研究员添加了竞品A的用户增长数据”），便于复盘协作效率与责任定位。

2. **产出明确** （以各 `agents/<role>.md` 文件内容要求为准）：
   coordinator → `tasks/task_breakdown.json`；
   researcher → `research_data/*`；
   writer → `drafts/chapter1_v1.md` /  + 最终 PDF；
   reviewer → `comments/review_comments.md`；
   coordinator → `tasks/*`、`memory/*` 等。

---

## 6. 禁止行为（全局）

- 在 `multi-agent/` 内放置 **SOUL.md、TOOLS.md、USER.md** 或各 Agent 私有 **memory/skills**（与仓库平级的那些）。
- 越权修改其他角色独占路径（见各 `agents/*.md`）。
- 提交信息不带 **正确 `[角色名]`** 前缀。

---

## 7. 执行顺序建议

1. 阅读仓库 `agents/*.md`。  
2. 按第 2 节创建智能体团队、并配置智能体的独立工作空间，按照第4节配置通信方式
3. 为每个agent生成专属的 `SOUL.md / TOOLS.md / USER.md`

若 OpenClaw API 与文中 CLI 不一致，**以相同语义**绑定：配置根目录 = `~/openclaw-workspaces/<agent_name>/`，代码/文件操作根 = `./multi-agent`。

## 8. 创建完成验收清单

- [ ] 四个目录 协同智能体的工作目录 均存在，且各有 **独立** `multi-agent/` 克隆。
- [ ] 每个目录下 **SOUL.md / TOOLS.md / USER.md** 与 `agents/<role>.md` 定稿一致。
- [ ] 四个 Agent 均已 `openclaw agents add`
- [ ] 各角色预装 Skills 与 `agents/<role>.md` 列表一致或已等价映射。

---


