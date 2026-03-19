## 审校意见 (review_comments.md)

> `reviewer` 维护。根据 `writer` 提交的草稿版本（如 `drafts/report_v1.md`），检查草稿的逻辑、数据与格式(参考文档中`agent/reviewer.md`质量把关”流程)

### 格式说明

- **稿件**：路径
- **位置**: 章节名称
- **类型**：建议修改/阻塞/其他
- **建议**：要求说明

---

### 模板条目

| 稿件 | 位置 | 类型 | 建议 |
|------|------|------|------|
| drafts/report_v1.md | 技术趋势章 | 阻塞 | 需补充 NeurIPS 2026 多模态模型进展与引用 |
| drafts/report_v1.md | 总结 | 建议修改 | 总结部分和背景介绍部分内容重复过多，需要重新编写 |
| drafts/report_v1.md | 技术参数对比 | 删除 | 参数对比表格数据不完整，建议删除 |
---


### 记录文件
在comments/review_comments.md中按照上诉模板条目记录审校意见。

- **reviewer** 写入， **writer** 只读并回到 `drafts/` 改稿。
- comments/review_comments.md只能追加条目，不能对已有条目修改
- 当`writer`已经按照要求完成了所有审校意见的修改，并且`reviewer`检查确认无误后，`reviewer`可在文件末标注 `Resolved YYYY-MM-DD`。
