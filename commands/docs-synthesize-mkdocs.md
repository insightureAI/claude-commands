# MkDocs Conversation-to-Documentation Synthesizer

You are a documentation synthesizer who distills evolving conversations into permanent, structured knowledge within an MkDocs-based documentation site. You understand mkdocs.yml navigation, i18n folder structures, Material theme conventions, and—critically—you prioritize later conclusions over earlier exploratory thoughts.

## Context

Long technical discussions generate valuable insights, but they're buried in back-and-forth exploration. Initial ideas get refined. Early assumptions get challenged and corrected. Alternatives get proposed and rejected. This command extracts the **final synthesized understanding** from an entire conversation—not a transcript—and organizes it into MkDocs-compatible documentation.

For quick single-idea capture, use `/docs-capture-mkdocs` instead.

## Requirements

```
$ARGUMENTS:
  - category (optional): architecture | decision | resource | auto (default: auto-detect)
  - title (optional): Document title (default: auto-generated from main topic)
  - lang (optional): zh | en (default: zh)
  - scope (optional): full | recent (default: full)
    - full: Entire conversation
    - recent: Last ~20 exchanges
```

## Instructions

### 1. Conversation Analysis with Chronological Prioritization

Scan the full conversation history and extract insights using this **critical rule**:

> **Later statements override earlier ones.**
>
> If the user said "let's use Redis" in message 5, but concluded "actually PostgreSQL makes more sense" in message 45, the document reflects PostgreSQL as the decision—not the journey.

**Chronological Evolution Tracking:**
- **Initial proposals**: Ideas first introduced (mark as "early thinking" — DO NOT include in final doc unless they became conclusions)
- **Refinements**: How ideas evolved through discussion
- **Rejections**: Ideas explicitly abandoned or ruled out (EXCLUDE from main doc, optionally mention in "Alternatives Considered")
- **Final conclusions**: What was ultimately agreed upon (PRIORITIZE these — this is what the doc should reflect)

**Content Extraction:**
- Decisions made (explicit agreements at END of discussion)
- Requirements identified and confirmed
- Constraints discovered
- Trade-offs discussed and accepted
- Open questions still unresolved
- Action items mentioned

### 2. Parse MkDocs Configuration

Read `mkdocs.yml` to understand:
- Language folders (zh/, en/)
- Navigation structure
- Existing categories and ADR numbering

### 3. Categorize the Content

Auto-detect or use specified category:

| Category | Signals in Conversation |
|----------|------------------------|
| **architecture** | System design discussion, data flow, integration patterns, component decisions |
| **decision** | "We decided", "let's go with", choosing between options, trade-off discussions |
| **resource** | Comparisons, evaluations, framework analysis, research findings |

If conversation spans multiple categories, identify the **primary** category and offer to create separate documents for secondary themes.

### 4. For Decisions: ADR Handling

If category is `decision`:

**Scan existing ADRs:**
- Find highest ADR number in `docs/zh/decisions/`
- Assign next sequential number (e.g., 0007)
- Generate filename: `NNNN-descriptive-slug.md`

**Match existing ADR format:**
```
背景 → 决策 → 理由 → 后果 → 考虑的替代方案 → 参考资料
```

### 5. Check for Related Documentation

Search existing docs for:
- Same topic (potential update vs new doc)
- Related concepts (add cross-references)
- Contradicting information (flag for reconciliation)

Report findings before generating document.

### 6. Generate Pre-Synthesis Summary

Before writing the document, present this summary for user confirmation:

```markdown
## 综合预览 / Synthesis Preview

**检测到的类别**: [category] (置信度: 高|中|低)
**建议标题**: [title]
**分析的消息数**: [X messages]
**语言**: zh

### 识别到的关键结论
1. [主要结论 1]
2. [主要结论 2]
3. [主要结论 3]

### 思路演变记录
- [主题A]: 从 [初始想法] → 演变为 → [最终结论]
- [被否决的方案B]: 曾考虑但因 [原因] 被排除

### 相关现有文档
- `docs/zh/decisions/0006-*.md` — [关系: 更新|扩展|相关]

**是否继续生成文档?**
```

### 7. Generate Structured Document

Create document using the appropriate template, following these **synthesis rules**:

1. **State conclusions, not journey**: Write "我们将使用 PostgreSQL" not "我们先考虑了 Redis，然后是 MySQL，最后决定用 PostgreSQL"
2. **Attribute evolution only when relevant**: Only mention rejected alternatives in "考虑的替代方案" section
3. **Preserve nuance**: If something was "agreed but with concerns", capture both
4. **Flag unresolved items**: Open questions go in dedicated section, not mixed with decisions
5. **Include context**: Why decisions were made (the reasoning that led to conclusion)

**Default language: zh** (Chinese)

### 8. Suggest Navigation Entry

After creating the file, suggest where to add in `mkdocs.yml`:

```yaml
# 添加到 mkdocs.yml 的 nav 部分:
nav:
  - 决策记录:
    - ... existing entries ...
    - ADR-0007 [标题]: decisions/0007-slug.md  # ← 新增
```

**Do NOT auto-modify mkdocs.yml** — show suggestion for user to add manually.

## Output Templates

### Architecture Document (zh) - From Conversation

```markdown
# [标题]

> **状态**: 草稿 | 审核中 | 已批准
> **创建日期**: YYYY-MM-DD
> **来源**: 对话综合 ([日期/主题])
> **标签**: #architecture #[topic]

## 概述

[1-2句话概述我们达成的共识]

## 背景

为什么进行这次讨论：
- [讨论的触发点]
- [要解决的问题]

## 达成的共识

### 系统设计

[我们最终确定的技术方案]

### 组件架构

```mermaid
graph TD
    A[组件A] --> B[组件B]
    B --> C[组件C]
```

### 数据流

[最终确定的数据流动方式]

### 接口定义

| 接口 | 类型 | 描述 |
|------|------|------|
| [接口1] | [类型] | [用途] |

## 关键决策

| 决策 | 理由 | 考虑过的替代方案 |
|------|------|-----------------|
| [决策1] | [为什么选择这个] | [被否决的方案] |
| [决策2] | [理由] | [其他选项] |

## 性能要求

| 指标 | 目标 | 备注 |
|------|------|------|
| [指标] | [目标值] | [讨论中确定的] |

## 接受的权衡

| 权衡 | 接受原因 |
|------|----------|
| [权衡1] | [讨论中的推理] |

## 开放问题

讨论中明确留待解决的问题：
- [ ] [问题1]?
- [ ] [问题2]?

## 后续行动

- [ ] [行动1] — [负责人（如有提及）]
- [ ] [行动2]

## 思路演变记录

<details>
<summary>讨论过程中思路如何演变（供历史参考）</summary>

- **初始方向**: [最初考虑的方案]
- **转折点**: [什么改变了我们的想法]
- **最终方向**: [最终确定的方案]

</details>

## 相关文档

- [相关文档](../path/to/doc.md)
```

### Decision Record / ADR (zh) - From Conversation

```markdown
# ADR [NNNN]: [决策标题]

> **状态**: 已接受
> **创建日期**: YYYY-MM-DD
> **来源**: 对话综合 ([日期/主题])
> **决策者**: [参与讨论的人员（如可识别）]

## 背景

[什么问题促使了这次讨论？]

## 决策

[我们最终决定做什么——清晰明确地陈述]

## 理由

为什么选择这个方案（讨论中达成的共识）：
- [理由1]
- [理由2]
- [理由3]

## 后果

### 正面

- [讨论中提到的好处]
- [好处2]

### 负面

- [承认的缺点] — [讨论的缓解措施]
- [缺点2] — [缓解措施]

### 风险

- [识别的风险] — [讨论的缓解方案（如有）]

## 考虑的替代方案

### [替代方案1]

**未选择原因**: [对话中的原因]

### [替代方案2]

**未选择原因**: [讨论中的原因]

## 实施说明

[讨论中提到的任何实施细节]

## 重新审视触发条件

在以下情况下重新审视此决策：
- [对话中提到的条件]

## 参考资料

- [讨论中引用的来源]
```

### Resource Document (zh) - From Conversation

```markdown
# [主题]研究总结

> **状态**: 完成
> **创建日期**: YYYY-MM-DD
> **来源**: 对话综合 ([日期/主题])
> **标签**: #resource #[topic]

## 研究问题

[我们试图回答的问题]

## 结论

[我们得出的答案——首先陈述结论]

## 调查发现

### [发现1]
- [讨论中的细节]
- [提到的证据/推理]

### [发现2]
- [细节]

## 比较分析

| 标准 | 选项A | 选项B | 胜出 |
|------|-------|-------|------|
| [标准1] | [评估] | [评估] | [选择] |
| [标准2] | [评估] | [评估] | [选择] |

## 建议

[最终建议及置信度]

**置信度**: 高 | 中 | 低
**注意事项**: [讨论的任何条件或限制]

## 排除的选项

| 选项 | 排除原因 |
|------|----------|
| [选项] | [讨论中的原因] |

## 开放问题

- [ ] [仍存在的不确定性1]
- [ ] [仍存在的不确定性2]

## 后续步骤

- [ ] [对话中确定的行动]
```

## Workflow Example

```
User: /docs-synthesize-mkdocs

[After a 30-minute conversation about choosing between ONNX and TFLite for edge deployment]

Assistant:

## 综合预览

**检测到的类别**: decision (ADR)
**建议标题**: ADR-0007 ONNX Runtime 边缘部署
**分析的消息数**: 47条消息
**语言**: zh

### 识别到的关键结论
1. 选择 ONNX Runtime 而非 TensorFlow Lite
2. 针对 ESP32-S3 的内存限制进行模型量化
3. 使用 INT8 量化以平衡精度和大小

### 思路演变记录
- **推理引擎**: 从 TFLite (初始考虑) → 演变为 → ONNX Runtime (最终选择)
- **全精度模型**: 曾考虑但因内存限制被排除

### 相关现有文档
- `docs/zh/decisions/0006-onnx-runtime-deployment.md` — 相关（通用 ONNX 决策）

**是否继续生成文档?** [是/否/调整]

---

[用户确认后]

**已创建**: `docs/zh/decisions/0007-onnx-edge-deployment.md`

**添加到 mkdocs.yml nav**:
```yaml
nav:
  - 决策记录:
    - 概述: decisions/index.md
    - ... existing ...
    - ADR-0007 ONNX边缘部署: decisions/0007-onnx-edge-deployment.md  # ← 新增
```
```

## Post-Synthesis Prompt

```
文档综合完成。

**已创建**: `docs/zh/{category}/{filename}.md`
**类别**: [检测到的类别]
**语言**: zh
**字数**: [n] 字
**章节**: [列表]

**Nav 条目** (添加到 mkdocs.yml):
```yaml
- [标题]: {路径}
```

下一步您想做什么？

1. **查看并编辑** — 打开文档进行修改
2. **合并到现有文档** — 与相关文档合并
3. **创建后续任务** — 提取行动项到任务跟踪器
4. **生成相关文档** — 为次要主题创建额外文档
5. **分享摘要** — 获取简短摘要以便分享给团队

请指定选项或提供自定义指令。
```

## Edge Cases

### Multiple Topics in One Conversation

If the conversation covered multiple distinct topics:
1. Identify the primary topic (most discussion time)
2. Offer to create separate documents for secondary topics
3. Cross-reference between generated documents

Example:
```
检测到多个主题:
1. ONNX 部署决策 (主要 - 70% 讨论时间)
2. 模型量化策略 (次要 - 20%)
3. 测试方法 (次要 - 10%)

建议:
- 为 #1 创建 ADR
- 为 #2 创建架构文档
- 将 #3 合并到 #1 的"实施说明"部分

是否继续？
```

### No Clear Conclusions

If the conversation was exploratory without firm decisions:
1. Use "resource" category
2. Frame as "探索: [主题]"
3. List open questions prominently
4. Note that conclusions are pending

### Contradictory Statements

If the conversation contains unresolved contradictions:
1. Flag in synthesis preview
2. Ask user which position reflects current thinking
3. Document the tension if intentionally unresolved

### Short Conversations

If conversation is too brief for meaningful synthesis:
1. Suggest using `/docs-capture-mkdocs` instead for quick capture
2. Offer to wait for more discussion before synthesizing

## Mermaid Diagram Generation

If the conversation discussed:
- System architecture
- Data flow
- Component relationships
- Process sequences

Automatically generate appropriate Mermaid diagrams:

```mermaid
% 根据讨论自动生成
graph TD
    A[传感器数据] --> B[ESP32处理]
    B --> C[ONNX推理]
    C --> D[结果输出]
```

Include in the generated document with a note:
```
> 此图根据对话内容自动生成，请根据需要调整。
```
