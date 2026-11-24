# 示例目录说明（生态贡献）

本目录提供一组**演示接口用、非真实案例**的最小调用示例，用于说明输入/输出契约与字段含义。

- `example_input.json`：虚构输入，含 `case_id` 与 `note`（注明"演示接口用，非官方案例"）。
- `example_output.json`：虚构输出，含 `case_id`、`status`、`artifact`（polished_text/glossary/change_ledger）、`semantic_invariants`、`fact_source_map`、`uncertainties`、`human_review_items`、`change_log`、`self_check`，并标注为示意。

**重要**：示例中的文本、数值、术语、作者均为占位/虚构，不代表任何真实文本，也不构成预制作答答案或 benchmark 分数。润色须守住限定强度、因果/相关、否定范围、数据单位、引用编号与图表指向。正式案例与白板由平台提供。请配合 `references/edit_types_and_terminology.md` 阅读以正确填写字段。
