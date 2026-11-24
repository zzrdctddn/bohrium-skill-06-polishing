# 学术语言润色 Skill（minimal-edit-semantic-polisher）

一个把润色做成"可核验最小编辑"的 Agent Skill。核心：**真错误修正 + 语义守恒与最小编辑 + 术语与文体一致性 + 逐句修改对照 + 改写审校双通道**（MESP）。

## 目录结构
- `SKILL.md` —— Skill 本体（YAML frontmatter、工作流、输出契约）
- `references/` —— MESP 方法细则、自检清单、字段 schema、修改类型与术语规则
- `assets/` —— 润色稿与对照表可编辑模板
- `examples/` —— 最小调用示例（已标注为示例，非真实案例）
- `GITHUB_URL.txt` —— 提交时填 GitHub 公开链接（当前为 REPLACE_ME 占位）

## 使用方式
1. 将本目录作为 Skill 安装到 Agent（平台约定路径 `/skills/weekly-skill`）。
2. 输入 `/input/case.json` 与 `task.md`，下指令如"在保持原意下润色这段摘要并给出逐句对照"。
3. 按 SOP 执行：语义指纹 → 术语表 → 真错误修正与最小编辑 → 全篇回扫 → 审校守恒回扫 → 对照表与输出。
4. 输出到 `/app/output/result.json` 与 `polished_manuscript.md`。

## 依赖
仅需文本处理能力与 `node`（JSON 校验）；无外部运行时依赖。修改类型、术语治理与语义不变量规则内置于 `references/`。

## 适用与限制
- 适用：论文/摘要/基金申请/审稿回复的语法修正、术语统一、语域调整、中译英去直译腔、逐句对照。
- 限制：纯问答不擅自改写；不改事实、数据、结论；术语冲突/语义模糊进人工复核；不编造事实、数据与 benchmark 分数。

## Runtime contract
Skill 加载到 `/skills/weekly-skill`，输入 `/input/case.json` 与 `task.md`，输出到 `/app/output/result.json`（含全部输出契约字段与 `case_id`）。资源 ≤50 个 UTF-8 文本文件、单文件 ≤500KB、总计 ≤2MB、无软链。执行预算 64 次模型请求 / 900 秒 / 每次 16384 token。实际输入文件是事实依据，不依据错误领域标签补造数据。

## 评分对应
实际效果(40)、使用增益(25)、创新增益(15)、可靠体验(10)、生态贡献(10) 的支撑材料分别见 SKILL.md 第 3/2/2/6/8 节与 `references/`；对照由平台用固定案例与白板完成，作品不自带答案与分数。
