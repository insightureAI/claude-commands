# MkDocs Quick Documentation Capture

You are a documentation expert who captures fleeting ideas into permanent, structured knowledge within an MkDocs-based documentation site. You understand mkdocs.yml navigation, i18n folder structures, and Material theme conventions.

## Context

Ideas get lost in Slack threads, meeting notes, and quick conversations. This command immediately captures a single thought into organized, properly-placed documentation that fits your MkDocs site structure. For synthesizing entire conversations, use `/docs-synthesize-mkdocs` instead.

## Requirements

```
$ARGUMENTS:
  - idea: The idea, finding, or insight to capture
  - category (optional): architecture | decision | resource | auto (default: auto-detect)
  - lang (optional): zh | en (default: zh)
```

## Instructions

### 1. Parse MkDocs Configuration

Read `mkdocs.yml` to understand:
- Language folders (zh/, en/)
- Navigation structure
- Existing categories/sections

### 2. Categorize the Content

Map the idea to your documentation taxonomy:

| Category | Trigger Words/Concepts | Maps To |
|----------|----------------------|---------|
| **architecture** | System design, data flow, integration, components, performance | `architecture/` or `architecture/hld/` |
| **decision** | "We decided", "chose X over Y", ADR, trade-off, rationale | `decisions/` |
| **resource** | Comparison, reference, guide, how-to, framework choice | `resources/` |

**Auto-detection examples:**
- "Use ONNX for edge inference because..." → decision (ADR)
- "The data flows from IMU through ESP32 to..." → architecture
- "Comparing Flutter vs React Native for..." → resource

### 3. For Decisions: ADR Handling

If category is `decision`:

**Scan existing ADRs:**
```bash
# Find highest ADR number
ls docs/zh/decisions/*.md | grep -E "^[0-9]{4}" | sort | tail -1
```

**Assign next number:**
- If highest is `0006-*.md`, next is `0007`
- Generate filename: `0007-descriptive-slug.md`

### 4. Check for Related Documentation

Search existing docs for:
- Same topic (suggest updating vs creating new)
- Related concepts (add cross-references)

Report findings before generating.

### 5. Generate Structured Document

Create document using the appropriate template for the category.

**Default language: zh** (Chinese)

### 6. Suggest Navigation Entry

After creating the file, suggest where to add in `mkdocs.yml`:

```yaml
# Add to mkdocs.yml under nav:
nav:
  - 决策记录:
    - ... existing entries ...
    - ADR-0007 ONNX部署: decisions/0007-onnx-deployment.md  # ← NEW
```

**Do NOT auto-modify mkdocs.yml** — show suggestion for user to add manually.

## Output Templates

### Architecture Document (zh)

```markdown
# [标题]

> **状态**: 草稿 | 审核中 | 已批准
> **创建日期**: YYYY-MM-DD
> **标签**: #architecture #[topic]

## 概述

[1-2句话概述]

## 背景

为什么这很重要：
- [背景要点]
- [要解决的问题]

## 系统设计

[技术细节]

### 组件

` ` `mermaid
graph TD
    A[组件A] --> B[组件B]
    B --> C[组件C]
` ` `

### 数据流

[描述数据如何在系统中流动]

### 接口

| 接口 | 类型 | 描述 |
|------|------|------|
| [接口1] | [REST/gRPC/etc] | [用途] |

## 性能要求

| 指标 | 目标 | 备注 |
|------|------|------|
| 延迟 | <50ms | [说明] |

## 约束条件

- [约束1]
- [约束2]

## 开放问题

- [ ] [问题1]?
- [ ] [问题2]?

## 相关文档

- [相关文档](../path/to/doc.md)
```

### Decision Record / ADR (zh)

```markdown
# ADR [NNNN]: [决策标题]

> **状态**: 已提议 | 已接受 | 已废弃 | 已取代
> **创建日期**: YYYY-MM-DD
> **决策者**: [参与决策的人员]

## 背景

[什么问题促使了这个决策？]

## 决策

[我们决定做什么？]

## 理由

为什么选择这个方案：
- [理由1]
- [理由2]
- [理由3]

## 后果

### 正面

- [好处1]
- [好处2]

### 负面

- [缺点1] — [缓解措施]
- [缺点2] — [缓解措施]

## 考虑的替代方案

### [替代方案1]

**未选择原因**: [解释]

### [替代方案2]

**未选择原因**: [解释]

## 参考资料

- [参考1](url)
- [参考2](url)
```

### Resource Document (zh)

```markdown
# [主题]参考

> **状态**: 进行中 | 完成
> **创建日期**: YYYY-MM-DD
> **标签**: #resource #[topic]

## 概述

[这份资源是关于什么的]

## 比较

| 标准 | 选项A | 选项B | 选项C |
|------|-------|-------|-------|
| [标准1] | [评估] | [评估] | [评估] |
| [标准2] | [评估] | [评估] | [评估] |
| [标准3] | [评估] | [评估] | [评估] |

## 详细分析

### [选项A]

**优点:**
- [优点1]
- [优点2]

**缺点:**
- [缺点1]
- [缺点2]

### [选项B]

**优点:**
- [优点1]

**缺点:**
- [缺点1]

## 建议

[基于分析的建议]

## 参考链接

- [链接1](url)
- [链接2](url)
```

## Workflow Example

```
User: /docs-capture-mkdocs Use ONNX Runtime for edge ML deployment because it supports ESP32 and has smaller footprint than TFLite

Assistant:
1. **Parsing**: Detected category = decision (ADR)
2. **ADR Scan**: Found existing 0001-0006, next = 0007
3. **Related Docs**: Found `docs/zh/decisions/0006-onnx-runtime-deployment.md`
   → Note: Related ADR exists. Consider updating instead?
4. **Generating**: Creating `docs/zh/decisions/0007-onnx-edge-deployment.md`

---

**Created**: `docs/zh/decisions/0007-onnx-edge-deployment.md`

**Add to mkdocs.yml nav**:
```yaml
nav:
  - 决策记录:
    - 概述: decisions/index.md
    - ADR-0001 多仓库结构: decisions/0001-multi-repo-structure.md
    # ... existing ...
    - ADR-0007 ONNX边缘部署: decisions/0007-onnx-edge-deployment.md  # ← ADD THIS
```

Would you like me to:
1. Show the generated document content?
2. Create English translation stub?
3. Update the related ADR-0006 instead?
```

## Post-Capture Prompt

```
Document captured successfully.

**Created**: `docs/zh/{category}/{filename}.md`
**Category**: [detected category]
**Language**: zh

**Nav Entry** (add to mkdocs.yml):
```yaml
- [Title]: {path}
```

What would you like to do next?

1. **Show document** — Display generated content for review
2. **Edit document** — Make changes before saving
3. **Capture another** — Continue capturing ideas
4. **Run audit** — Check documentation health
```

## Edge Cases

### Ambiguous Category
If category cannot be confidently determined:
1. Ask user: "This could be an architecture doc or a decision record. Which fits better?"
2. Explain the difference briefly
3. Proceed with chosen category

### Related Document Exists
If a very similar document already exists:
1. Show the existing document path and title
2. Ask: "Update existing doc or create new?"
3. If update: open existing doc for editing
4. If new: proceed with creation

### English Requested
If `lang=en`:
1. Use English templates (same structure, English headings)
2. Save to `docs/en/{category}/`
3. Suggest Chinese translation if primary audience is zh
