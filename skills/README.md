# 自定义技能（skills）

存放 Agent 可调用的脚本、Skill 定义或包装说明（如数据清洗、可视化、报告模板生成）。

## 约定

- 每个子目录或单个文件对应一项可发现的能力；在文件头用 Markdown 说明：**用途、入参、出参、依赖**。
- 与 OpenClaw 内置 Skill 命名冲突时，本项目内使用前缀（如 `local_`）区分。

## 示例结构（可选）

```text
skills/
  README.md          # 本说明
  data_clean/      # 数据清洗脚本 + 说明
  report_export/   # 导出 PDF/HTML 等
```
