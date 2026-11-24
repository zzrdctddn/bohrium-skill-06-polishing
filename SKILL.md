---
name: minimal-edit-semantic-polisher
description: 在不改变原意、限定强度、数据单位、引用编号与图表指向的前提下，对学术文本做真错误修正、术语全篇统一与文体语域贴合，并产出逐句原文-改文-修改类型-理由对照表。当用户要求"润色论文/摘要/基金申请""校对语法""统一术语""中译英去直译腔""保持原意改写""给出修改对照表"时使用。输出含润色后完整文本、术语表、逐句修改对照表、语义守恒校验与结构化 result.json。
version: 1.0.0
license: MIT
---

# 学术语言润色：最小编辑的语义守恒润色（Minimal-Edit Semantic-Preserving Polishing, MESP）

> 面向真实学术稿件，把"润色"从整段重写变成**可核验的最小编辑工程**。核心不是把句子写得更漂亮，而是让每处改动都回答：真的改错了什么？原意守住了吗？术语统一了吗？改动讲清楚了吗？

## 1. 用途与适用边界

**解决什么**：基础 Agent 润色的常见失败——整段重写导致限定词（可能/部分/显著）丢失、把相关性偷换成因果、数据与单位被顺手改掉、引用编号与图表指向错乱、术语在引言与方法间漂移、中译英满是直译腔、改写后结论被悄悄加强或削弱、改动无对照无法复核。本 Skill 用"语义守恒 + 最小编辑 + 术语表驱动 + 改写审校双通道"消除这些失败。

**触发场景**：论文/摘要/基金申请/审稿回复的润色与校对；语法句法搭配标点修正；术语全篇统一与缩写首次出现规范；中译英去直译腔；按目标期刊/学位/基金文体调整语气；产出逐句修改对照表。

**不该用**：纯内容问答且未要求改写时（不应擅自改写）；要求重做实验/补数据/改结论时（转用相应写作 Skill，本 Skill 不改事实）；材料缺失或目标文体不明且无法确认时（先标注，不臆断）。

**输入**：`/input/case.json`（含 `case_id`、待润色文本、事实底稿、术语表、目标文体/受众/格式约束）与 `task.md`。**输出**：`/app/output/result.json`（字段见第 4 节）+ 可编辑的 `polished_manuscript.md`。

## 2. 核心方法（MESP = Minimal-Edit Semantic-Preserving Polishing）

本 Skill 的方法名为 **MESP = Minimal-Edit Semantic-Preserving Polishing**，四个不可跳过的机制逐一对应四个关键问题：

1. **机制 M｜真错误修正（Genuine Error Correction）→ 改得对**。只改真实的语法、句法、搭配、标点、用词错误（主谓一致、时态语态、冠词介词、单复数、悬垂修饰、长句切分等），目标是消除硬错误而非换一种说法掩盖；不引入新错。
2. **机制 S｜语义守恒与最小编辑（Semantic Preservation & Minimal Edit）→ 没改坏**。守住原意、限定强度、因果与相关性的区别、否定范围、条件句与量词；数据、单位、引用编号、图表指向、术语译法一律不变；能不改就不改，优先最小编辑，禁止过度同义替换、禁止偷偷加强或削弱结论。
3. **机制 P｜术语与文体一致性（Terminology & Register Consistency）→ 说得像**。建立并维护术语表，统一同一概念的中英文写法、缩写首次出现形式、大小写与连字符，处理同义混用与跨章节漂移；按目标文体（期刊/学位/基金/审稿回复/英文摘要）调整语气，消除口语化、宣传化、绝对化与人称误用，读起来仍像同行写的学术文本。
4. **机制 L｜逐句修改对照（Sentence-level Change Ledger）→ 讲得清**。每处修改给出原文、改文、修改类型（语法/术语/语气/语义保持/标点）与理由，他人可据此判断"这处该不该改"。

另外贯穿一条铁律：**改写与审校双通道**——先完成改写，再以"审校者"身份独立执行第 6 节语义守恒回扫（含限定强度对照与回译校验），两遍不可合并；同一段落重复润色不得结论漂移。

## 3. 工作流（SOP）

严格按顺序执行，每步都有检查点；检查点不过则回退该步。

**Step 0 · 输入解析与约束确认**
- 读 `case.json` 与 `task.md`，提取：待润色文本、事实底稿、术语表、目标文体/受众/格式、指定章节、明确禁止事项。
- 检查点：目标文体是否明确？术语表是否有内部冲突？不明项登记 `uncertainties`，不臆断。

**Step 1 · 语义指纹建立（Semantic Fingerprint）**
- 改写前记录不可变项清单：数据/单位、限定词强度（可能/约/仅/显著）、因果vs相关、否定范围、条件句、量词、引用编号、图表指向、关键术语译法。
- 检查点：这份指纹是否覆盖所有"改坏"风险点？缺项补登记。

**Step 2 · 术语表构建与对齐（机制 P）**
- 建立/合并术语表：概念↔中英文写法↔缩写首次出现形式↔大小写/连字符；标出章节间漂移与同义混用。
- 检查点：术语表内部是否自洽？冲突项进 `human_review_items`，不擅自定译。

**Step 3 · 真错误修正与最小编辑（机制 M + S）**
- 分句处理，只改真实错误与必要的一致性/语气问题；能不改就不改；每处改动先对照 Step 1 指纹确认不触碰不可变项。
- 检查点：改动是否都是"真错误/真必要"？是否偷换了限定强度或因果？触碰不可变项则回退并记 `change_log`。

**Step 4 · 术语与语域全篇回扫（机制 P）**
- 以术语表为准全篇回扫，统一写法与缩写首次出现形式；按目标文体调整语气，去口语化/宣传化/绝对化。
- 检查点：术语全篇一致？语气符合目标文体？仍像同行写的学术文本？

**Step 5 · 审校通道：语义守恒回扫（机制 S，与改写分离）**
- 以"审校者"身份独立执行：逐句比对原文-改文，核对限定强度、因果/相关、否定、数据单位、引用编号、图表指向；必要时做回译校验。**同一段落重复润色须结论不漂移。**
- 检查点：任何"改坏"项必须回退并在 `change_log` 记录；`semantic_invariants_preserved` 方可为 true。

**Step 6 · 对照表与输出（机制 L）**
- 生成逐句修改对照表（原文/改文/修改类型/理由）+ 润色后完整文本；写 `polished_manuscript.md` 与 `result.json`（含 `case_id` 与全部输出契约字段）。

## 4. 输出契约（result.json）

```json
{
  "case_id": "<原样带回>",
  "status": "success | partial | blocked",
  "artifact": {
    "polished_text": "<润色后完整文本>",
    "glossary": [
      {"concept": "<概念>", "zh": "<中文写法>", "en": "<英文写法>", "abbrev_first": "<缩写首次出现形式>", "status": "consistent|conflict"}
    ],
    "change_ledger": [
      {"sentence_id": "S1", "original": "<原文句>", "revised": "<改文句>", "change_type": "grammar|terminology|register|semantic_preservation|punctuation", "reason": "<修改理由>", "invariant_safe": true}
    ]
  },
  "semantic_invariants": [
    {"item": "<限定词/数据/单位/引用/图表指向/因果>", "original_value": "<原值>", "preserved": true, "note": "<核对说明>"}
  ],
  "fact_source_map": [
    {"item": "<数字/术语/引用/图表>", "value": "<值>", "source_or_record": "<来源或修改记录>"}
  ],
  "uncertainties": ["<材料缺失/文体不明/术语冲突等项>"],
  "human_review_items": ["<需人工确认的术语冲突/语义模糊项>"],
  "change_log": [{"step": 5, "action": "...", "reason": "..."}],
  "self_check": {
    "errors_genuinely_fixed": true,
    "semantics_preserved": true,
    "terminology_consistent": true,
    "changes_explained": true
  }
}
```

字段原则：`status` 诚实反映完成情况；`partial` 必须写清 `uncertainties`；术语冲突/语义模糊进 `human_review_items`；不得编造事实、数据或 benchmark 分数；材料缺失、无法判断、运行失败分别说明。

## 5. 质量自检清单

执行前完整读取 `references/checklist.md`，逐项打钩。关键硬项：

- [ ] 改动都是真实语法/句法/搭配/标点/用词错误或必要的一致性调整
- [ ] 未用换说法掩盖错误、未引入新错
- [ ] 原意、限定强度、因果vs相关、否定范围、条件句、量词保持不变
- [ ] 数据、单位、引用编号、图表指向保持不变
- [ ] 术语全篇统一，缩写首次出现形式规范，无跨章节漂移
- [ ] 语气符合目标文体，仍像同行写的学术文本
- [ ] 每处修改有原文/改文/修改类型/理由
- [ ] 改写与审校分离，重复润色结论不漂移

## 6. 异常处理与边界

| 情况 | 处理 |
|---|---|
| 超长输入/分章节 | 分句分章处理，维护同一术语表与语义指纹，保证跨章一致 |
| 编码混乱/中英日混排 | 先规范编码再处理，异常字符进 `uncertainties` |
| 公式与引用混杂 | 公式/引用编号视为不可变项，不改动 |
| 术语表内部冲突 | 不擅自定译，进 `human_review_items` |
| 目标文体不明 | 按材料指定；无则默认目标期刊论文体并在 `uncertainties` 说明 |
| 语义疑似被改坏 | 回退该处改动，记 `change_log`，必要时标 `partial` |
| 纯问答未要求改写 | 不触发润色，正常作答 |
| 重复润色漂移 | 以首次语义指纹为准回扫，禁止逐轮加强/削弱 |

## 7. 最小调用示例（示意，非真实案例）

**输入**（`case.json` 片段）：
```json
{"case_id":"DEMO-NOT-A-REAL-CASE","task":"在保持原意下润色摘要并给出逐句修改对照","target_register":"期刊论文英文摘要","text":"（示意）The results suggest that A is correlated with B, and may improve C by 10%.","glossary":[{"concept":"方法A","en":"Method A"}]}
```
**调用**：加载本 Skill 后，用户说"用 minimal-edit-semantic-polisher 润色这段摘要并给对照表"。

**示意输出**（节选）：
```json
{"case_id":"DEMO-NOT-A-REAL-CASE","status":"success","artifact":{"polished_text":"（示意）The results suggest that Method A is correlated with B and may improve C by 10%.","change_ledger":[{"sentence_id":"S1","original":"（示意）The results suggest that A is correlated with B, and may improve C by 10%.","revised":"（示意）The results suggest that Method A is correlated with B and may improve C by 10%.","change_type":"terminology","reason":"（示意）统一方法A译法为 Method A","invariant_safe":true}]},"semantic_invariants":[{"item":"限定词 may/correlated","original_value":"correlated; may","preserved":true,"note":"（示意）未改因果与强度"}],"self_check":{"errors_genuinely_fixed":true,"semantics_preserved":true,"terminology_consistent":true,"changes_explained":true}}
```
> 上述为演示接口用的虚构示例，不代表任何真实案例结果。

## 8. 资源清单

- `references/method.md` —— MESP 方法细则与反例库
- `references/checklist.md` —— 逐项自检清单（覆盖改得对/说得像/没改坏/讲得清）
- `references/schema.json` —— 输出字段字典（draft-07）
- `references/edit_types_and_terminology.md` —— 修改类型定义、术语治理与语义不变量规则
- `assets/polish_template.md` —— 润色稿与逐句对照表可编辑模板
- `examples/` —— 最小调用示例（标注为示例，非真实案例）
