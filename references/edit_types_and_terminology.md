# 修改类型、术语治理与语义不变量规则

## 1. 修改类型（change_type，受控）
| 类型 | 适用 | 示例（示意） |
|---|---|---|
| `grammar` | 语法/句法/搭配错误 | 主谓一致、时态、冠词介词、单复数 |
| `terminology` | 术语统一 | 方法A统一译法为 Method A |
| `register` | 文体语域 | 去口语化、宣传化、绝对化，调人称 |
| `semantic_preservation` | 为守住语义而做的必要调整 | 回退被加强的限定词 |
| `punctuation` | 标点/长句切分 | 逗号、分号、破折号规范 |

对照表每条必填：`sentence_id`、`original`、`revised`、`change_type`、`reason`、`invariant_safe`。

## 2. 语义不变量清单（semantic_invariants，改写前后逐项核对）
- 限定强度：may/might/can/suggest/likely/约/仅/显著/主要；
- 关系类型：correlate/associate 不得写成 cause/lead to；
- 否定范围：not/no/未/不 的辖域；
- 条件与量词：if/when/only if、all/some、所有/部分；
- 数据单位：数值、单位、约数、有效数字；
- 引用与图表：引用编号、Fig./Table/Equation 指向；
- 术语译法：与术语表/事实底稿一致。

任何一项 `preserved=false` 必须回退改动并写 `change_log`。

## 3. 术语表治理（glossary）
字段：`concept`、`zh`、`en`、`abbrev_first`（缩写首次出现形式）、`status`（consistent/conflict）。
- 同一概念全篇统一写法；缩写首次出现处给全称；大小写/连字符一致。
- 术语表内部冲突 → `status=conflict`，进 `human_review_items`，不擅自定译。

## 4. 目标文体（register）要点
- 期刊论文：客观、限定严谨、避免绝对化；
- 学位论文：规范完整、时态一致；
- 基金申请：清晰有力但不宣传化；
- 审稿回复：礼貌、逐条对应、语气克制；
- 英文摘要：去直译腔、名词化适度、信息重心合理。

## 5. 最小编辑原则
能不改就不改；一处只解决一个问题；优先局部替换而非整句重写；改动量越小越好，但以"真错误被修正"为前提，不为少改而放过硬错误。
