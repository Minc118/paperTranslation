# AGENTS.md

Strict execution instructions for Codex or other agents working in this repository.

## Repository Purpose

This repository creates question-guided bilingual academic reading packages. The goal is not only translation. The goal is to help the user read, verify, and think through a paper using confirmed academic reading questions.

Do not process or translate any paper unless the user explicitly asks through a supported trigger or direct instruction.

## Trigger Commands

Activate the workflow when the user uses one of these command patterns with a PDF file name:

```text
翻译 <paper-name.pdf>
翻译一下 <paper-name.pdf>
处理 <paper-name.pdf>
阅读 <paper-name.pdf>
translate <paper-name.pdf>
translate and review <paper-name.pdf>
review <paper-name.pdf>
process <paper-name.pdf>
read <paper-name.pdf>
```

Both of these must activate the workflow:

```text
translate <paper-name.pdf>
翻译 <paper-name.pdf>
```

## Stage Decision Logic

If the command contains a paper name but no confirmed questions, run Stage 1 only.

If the command contains a paper name and explicitly confirmed questions, run Stage 2 directly.

Questions become final confirmed questions only after the user explicitly confirms them or provides them as confirmed questions.

Confirmed questions may be introduced by labels such as:

```text
Questions:
问题：
Q1.
Q2.
```

When uncertain whether questions are confirmed, treat them as draft questions and run Stage 1.

## Stage 1: Confirm Reading Questions Only

Stage 1 is only for confirming reading questions.

Hard restrictions:

- Do not read the full paper.
- Do not extract the full paper.
- Do not summarize the paper.
- Do not translate the paper.
- Do not create the final PDF.
- Do not create the paper structure map.
- Do not create `outputs/<paper-name>/` final deliverables.

Allowed Stage 1 work:

- Identify the supplied paper name.
- Ask the user for reading questions if missing.
- Rewrite vague questions into clearer academic questions.
- Merge duplicate questions.
- Split overloaded questions.
- Assign an expected answer type to each question.
- Prepare a candidate confirmed question list.
- Stop and ask the user to explicitly confirm the questions before Stage 2.

Expected answer types:

```text
concept explanation
method summary
comparison
tradeoff analysis
experiment result
limitation
relevance to my own report
not related
```

## Stage 2: Process Paper With Confirmed Questions

Stage 2 starts only after questions are confirmed.

Required Stage 2 actions:

- Read the paper with the confirmed questions in mind.
- Extract paper structure.
- Extract logical blocks instead of raw PDF lines.
- Assign stable block IDs.
- Record original PDF page numbers.
- Translate each main-text block into Chinese.
- Preserve original English text.
- Tag blocks by relevant question IDs.
- Maintain a paper-specific glossary.
- Generate required output files.
- Generate a citation-based question-answer review.
- Mark each question as `Relevant`, `Partially relevant`, or `Not relevant`.
- Run quality checks.

## Logical Block Extraction

Preserve these content types when present:

- title
- authors
- affiliations
- abstract
- keywords / index terms
- section headings
- subsection headings
- paragraphs
- bullet lists
- equations
- formulas
- figures
- figure captions
- tables
- table captions
- footnotes
- URLs
- references

Do not translate the bibliography unless the user explicitly asks.

Use stable block IDs such as:

```text
TIT-001
AUTH-001
ABS-001
KW-001
SEC-001
P-001
P-002
FIGCAP-001
TABLECAP-001
REF-001
```

Each block should track:

```json
{
  "block_id": "P-001",
  "source_pdf_page": 1,
  "section": "I. Introduction",
  "block_type": "paragraph",
  "original_text": "...",
  "chinese_translation": "...",
  "relevant_questions": ["Q1"],
  "notes": ""
}
```

`source_pdf_page` must refer to the original PDF page number.

## Required Output Files

For each processed paper, create:

```text
outputs/<paper-name>/
├── bilingual_facing_pages.pdf
├── bilingual_translation.md
└── question_answer_review.md
```

Optional intermediate files:

```text
outputs/<paper-name>/
├── bilingual_facing_pages.html
├── glossary.md
├── paper_structure_map.md
├── extraction_blocks.json
└── quality_check_report.md
```

## Required Output Order

Both `bilingual_facing_pages.pdf` and `bilingual_translation.md` must begin with:

```text
Cover
Confirmed reading questions
Question-answer review
Glossary
Bilingual paper body
```

Add a quality-check note or appendix after the body if needed.

The full `question_answer_review.md` must also be created as a separate file. If `glossary.md` is created, its content must also be included before the paper body in `bilingual_facing_pages.pdf` and `bilingual_translation.md`.

## PDF Layout Rules

The final PDF must be a facing-page bilingual PDF.

Structure:

```text
Page 1: Cover page
Pages 2-N: Front matter
After front matter: bilingual paper body
```

The front matter may use a normal academic layout.

The paper body must alternate logical block groups:

```text
English original page / block group
Chinese translation page / matching block group
English original page / block group
Chinese translation page / matching block group
```

When viewed in two-page mode with the cover page shown separately:

```text
left page  = English original
right page = Chinese translation
```

Align by logical blocks. If exact visual alignment is difficult, prioritize logical block alignment and report the limitation.

## Citation Rules

Use this citation format:

```text
[p. X, Section Y, Block P-0XX]
```

Rules:

- `p. X` must refer to the original PDF page number.
- Do not cite generated bilingual PDF page numbers.
- Every important claim must have a citation.
- Every answer must include an evidence table unless the relevance judgment is `Not relevant`.
- If a claim cannot be linked to an original PDF page number and block ID, do not include it as a factual answer.
- Do not cite the Chinese translation as evidence.
- If interpretation is necessary, clearly mark it as interpretation and cite the supporting source blocks.

Hard rule:

```text
If a claim cannot be linked to an original PDF page number and block ID, do not include it as a factual answer.
```

## Not-Relevant Rule

Use one relevance judgment for every question:

```text
Relevant
Partially relevant
Not relevant
```

If the paper does not contain relevant content for a question, answer:

```text
本文没有提供与该问题直接相关的内容，因此该问题与本文不相关。
```

Do not invent an answer. Do not use external knowledge to fill the gap. Do not over-interpret unrelated passages.

## Question-Answer Format

For `Relevant` or `Partially relevant` questions:

```markdown
# Q1. Question text

## Relevance judgment

Relevant / Partially relevant / Not relevant.

## Short answer

Chinese answer with original PDF page-number citations.

## Evidence table

| Claim | Citation | Evidence from paper | How it supports the answer |
|---|---|---|---|
| Chinese claim here | [p. X, Section Y, Block P-0XX] | Short English quote or faithful paraphrase | Chinese explanation |

## Detailed answer

Detailed Chinese explanation with citations.

## Blocks to reread

- [p. X, Section Y, Block P-0XX]
- [p. X, Section Y, Block P-0YY]

## Uncertainty

State whether the paper fully answers, partially answers, or does not answer the question.
```

For not-relevant questions:

```markdown
# QX. Question text

## Relevance judgment

Not relevant.

## Short answer

本文没有提供与该问题直接相关的内容，因此该问题与本文不相关。

## Evidence status

No relevant evidence found in the paper.

## Checked locations

List the main sections/pages checked.

## Detailed note

Explain briefly why the paper does not answer this question.

## Uncertainty

This is a relevance judgment based on the extracted structure and searched blocks.
```

## Translation Rules

Translate English academic text into natural Chinese.

Rules:

1. Preserve technical meaning over literal wording.
2. Keep citations such as `[1]`, `[2]`, `[5]` unchanged.
3. Keep formulas, variables, Greek letters, URLs, code identifiers, function names, and system names unchanged.
4. Preserve section numbers, figure numbers, table numbers, captions, and paragraph order.
5. Translate figure and table captions.
6. Do not translate the bibliography unless explicitly requested.
7. Keep technical terms consistent.
8. Do not invent explanations not present in the paper.
9. Avoid overly mechanical machine translation.
10. Preserve the author's degree of certainty.

## Paper-Specific Glossary Policy

The glossary must be paper-specific. For every processed paper:

1. Detect paper domain.
2. Extract important technical terms.
3. Check whether an existing default glossary is relevant.
4. Use matching default terms only when appropriate.
5. Create a paper-specific glossary.
6. Use the paper-specific glossary consistently in translation.
7. Report uncertain terminology choices in the quality-check note.

The Serverless / FaaS glossary below is only a reference glossary. Use it only when the paper is about serverless computing, FaaS, function fusion, cloud orchestration, or related topics.

| English | Chinese |
|---|---|
| serverless computing | 无服务器计算 |
| Function-as-a-Service / FaaS | 函数即服务 / FaaS |
| function | 函数 |
| task | 任务 |
| function fusion | 函数融合 |
| cold start | 冷启动 |
| invocation | 调用 |
| synchronous invocation | 同步调用 |
| asynchronous invocation | 异步调用 |
| double billing | 双重计费 |
| deployment artifact | 部署工件 |
| fusion group | 融合组 |
| fusion setup | 融合配置 |
| request response latency | 请求响应延迟 |
| billed duration | 计费时长 |
| optimization strategy | 优化策略 |
| heuristic | 启发式算法 |
| monitoring data | 监控数据 |
| call graph | 调用图 |
| runtime | 运行时 |
| orchestration | 编排 |
| choreography | 协同编排 / 事件协同 |
| overhead | 开销 |
| limitation | 局限性 |

Important notes:

- Translate `invocation` as `调用`, not `呼叫`, in the FaaS/serverless context.
- Translate `limitation` as `局限性`, not `优势`.
- Translate `billed duration` as `计费时长`.
- Translate `deployment artifact` as `部署工件`.
- If a term has a different meaning in another paper domain, follow the paper-specific meaning.

## Final Reporting Requirements

At the end of Stage 1, report only:

- candidate confirmed question list
- expected answer type for each question
- request for user confirmation before Stage 2

At the end of Stage 2, report:

- created output files
- required output order confirmation
- citation basis confirmation
- glossary policy confirmation
- not-relevant questions, if any
- quality-check summary
- limitations, especially PDF alignment or uncertain terminology

Do not commit unless the user explicitly asks.
