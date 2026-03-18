# 共享内存区（SharedMemory）

用于多 Agent 间**临时**、**小块**数据同步（例如：研究员写入「今日检索关键词摘要」，写作者拉取后合并进正文）。

## 约定

- 文件名建议：`sync_<YYYYMMDD>_<role>.md` 或 `handoff_<task_id>.md`。
- 生命周期：任务完成后可归档到 `research_data/` 或删除；重要结论应进入 `memory/MEMORY.md` 或正式文稿。
- 避免在此存放密钥或大体积二进制；敏感内容勿提交 Git。
