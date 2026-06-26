# Question-Guided Bilingual Paper Review

## Purpose

Use this skill to create question-guided bilingual academic reading packages. The output should help the reader understand a paper through confirmed questions, Chinese translation, preserved English originals, original-PDF citations, and a focused review.

The goal is not just translation. The goal is:

1. Confirm the reading questions first.
2. Read the paper with those questions in mind.
3. Translate the paper into Chinese while preserving the original English.
4. Generate a bilingual facing-page PDF.
5. Generate a citation-based question-answer review.
6. Help the user review, verify, and think through the paper again.

## Trigger Behavior

Use this skill when the user asks to process a paper with any of these patterns:

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

If the command contains a paper name but no confirmed questions, run Stage 1 only.

If the command contains a paper name and explicitly confirmed questions, run Stage 2 directly.

Questions become final confirmed questions only after the user explicitly confirms them or provides them as confirmed questions.

## Inputs

Expected inputs:

- PDF file in `inputs/papers/` or a user-provided PDF path.
- Optional seed glossary in `inputs/glossary/`.
- Confirmed reading questions for Stage 2.

## Outputs

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
├── translation_notes.md
└── quality_check_report.md
```

## Two-Stage Workflow

### Stage 1: Confirm Reading Questions Only

Stage 1 is only for confirming reading questions.

Do not:

- read the full paper
- extract the paper
- summarize the paper
- translate the paper
- create the final PDF
- create the paper structure map
- create final output deliverables

Do:

- identify the target paper name
- ask for questions if missing
- refine vague questions
- split overloaded questions
- merge duplicates
- assign an expected answer type
- stop after preparing the candidate confirmed question list
- ask the user to explicitly confirm the questions before Stage 2

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

### Stage 2: Process Paper With Confirmed Questions

Stage 2 starts only after questions are confirmed.

Required actions:

- read the paper with the confirmed questions in mind
- before translation or question answering, build a full-document source map / `extraction_blocks.json`
- extract paper structure
- extract logical blocks
- assign stable block IDs
- record original PDF page numbers
- translate each main-text block into Chinese
- preserve original English text
- tag blocks by relevant question IDs
- maintain a paper-specific glossary
- generate a bilingual facing-page PDF
- generate a citation-based question-answer review
- mark each question as `Relevant`, `Partially relevant`, or `Not relevant`
- run quality checks

The source map is the basis for translation, source citations, question-answer evidence, the terminology ledger / glossary, and follow-up review.

## Logical Block Extraction

Extract logical blocks, not raw PDF lines.

Preserve:

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

## Block IDs

Assign every logical block a stable ID.

Examples:

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
  "order": 1,
  "source_pdf_page": 1,
  "section": "I. Introduction",
  "block_type": "paragraph",
  "source_anchor": "[p. 1, Section I. Introduction, Block P-001]",
  "original_text": "...",
  "chinese_translation": "...",
  "relevant_questions": ["Q1"],
  "confidence": "high | medium | low",
  "related_figures": [],
  "related_tables": [],
  "bbox": null,
  "extraction_notes": "",
  "notes": ""
}
```

`source_pdf_page` must refer to the original PDF page number, not the generated bilingual PDF page number.

The required citation format remains unchanged:

```text
[p. X, Section Y, Block P-0XX]
```

## Figure and Table Handling

- Preserve figures, tables, captions, and table notes whenever possible.
- Place figures and tables near the first substantive mention, not necessarily by exact PDF visual position.
- Keep the original caption and Chinese caption together.
- If a figure/table is used as evidence for an answer, cite the figure/table block or caption block.
- If crop, placement, or extraction is uncertain, mark it in `quality_check_report.md` or `translation_notes.md`.

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

## Paper-Specific Glossary Rules

The glossary must be paper-specific. Do not force a default glossary onto unrelated papers.

Build a paper-specific terminology ledger as the single source of truth for translation terms. The final `glossary.md` should be generated from this ledger.

Workflow:

1. Detect paper domain.
2. Extract important technical terms.
3. Check whether an existing default glossary is relevant.
4. Use matching default terms only when appropriate.
5. Create a paper-specific glossary.
6. Use the paper-specific glossary consistently in translation.
7. Report uncertain terminology choices in the quality-check note.

For important terms, track:

- canonical English term
- Chinese translation
- variants seen in the paper
- first-use source block
- definition or explanation if available
- decision note
- uncertainty if any

### Serverless / FaaS Reference Glossary

Use this glossary only when the paper is about serverless computing, FaaS, function fusion, cloud orchestration, or related topics.

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

## Output Order

Both `bilingual_facing_pages.pdf` and `bilingual_translation.md` must use this order:

```text
Cover
Confirmed reading questions
Question-answer review
Glossary
Bilingual paper body
Quality-check note or appendix if needed
```

The question-answer review and glossary must appear before the translated paper body. The full review must also be created as `question_answer_review.md`.

## Source-Faithful Full-Paper Translation Rule

Distinguish the review layer from the paper body:

```text
Question-Answer Review = may summarize, synthesize, and interpret with citations.
Bilingual Paper Body = must preserve original source text and provide a faithful Chinese translation.
```

The `Bilingual Paper Body` must be a full, source-faithful bilingual translation of the paper.

For every main-text block:

1. Preserve the original English text as extracted from the source paper, except for minimal cleanup of obvious PDF extraction artifacts.
2. Do not summarize the original paper body.
3. Do not compress multiple original paragraphs into one short paraphrase.
4. Do not rewrite the original English into simplified English.
5. Do not replace original paragraphs with explanations.
6. Do not omit original paragraphs unless they are bibliography entries, front matter explicitly excluded by the project rules, or content explicitly excluded by the user.
7. Translate the preserved original English block into Chinese directly below it in Markdown, or on the paired facing page in PDF.
8. If extraction is uncertain, keep the extracted original text and mark the uncertainty in `translation_notes.md` or `quality_check_report.md`.
9. The bilingual paper body must allow the user to review the original wording and the Chinese translation side by side.
10. The source map / `extraction_blocks.json` must represent the original paper text, not a summarized version of it.

Hard failure rule:

```text
If any main-text block in the Bilingual Paper Body is a summary, paraphrase, compressed rewrite, or explanatory replacement instead of the original source text plus Chinese translation, the output fails the task.
```

## PDF Layout

The final PDF must be a facing-page bilingual PDF.

PDF structure:

```text
Page 1: Cover page
Pages 2-N: Front matter
After front matter: bilingual paper body
```

The front matter does not need strict left-English/right-Chinese facing-page layout.

The bilingual paper body must use:

```text
Left page: original English text
Right page: Chinese translation
```

Paper body page order:

```text
English original page / block group
Chinese translation page / matching block group
English original page / block group
Chinese translation page / matching block group
```

When viewed in two-page mode with the cover page shown separately, the left page should be English and the right page should be Chinese. Align by logical blocks, not raw PDF line breaks. If exact visual alignment is difficult, prioritize logical block alignment and report the limitation.

## Citation Requirements

All answers in `question_answer_review.md` must cite the original PDF page number, section, and block ID.

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
- Do not treat a passage as evidence merely because it is topically related.
- Use the smallest defensible relevance judgment: `Relevant`, `Partially relevant`, or `Not relevant`.
- If the paper only weakly supports an answer, mark it as `Partially relevant`.
- If a claim relies on interpretation, clearly label it as interpretation and cite the supporting blocks.
- Metadata-only, title-only, abstract-only, or unchecked blocks cannot support detailed body-level claims unless the answer explicitly says the support is limited.

Hard rule:

```text
If a claim cannot be linked to an original PDF page number and block ID, do not include it as a factual answer.
```

## Not-Relevant Handling

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

## Quality Check

Before final response, check:

- required files exist
- `extraction_blocks.json` / source map exists
- output order is correct
- confirmed questions appear before the paper body
- question-answer review appears before the paper body and as a separate file
- glossary appears before the paper body
- every block has a stable block ID and original PDF page number
- every factual answer claim has original PDF page, section, and block citation
- every factual QA claim has at least one source block citation
- every figure/table asset or placeholder has a source pointer
- question-answer review cites figure/table blocks when visual evidence is used
- not-relevant questions use the required not-relevant answer
- bibliography was not translated unless requested
- technical terms are consistent with the terminology ledger / paper-specific glossary
- uncertain or missing content is recorded in `quality_check_report.md` or `translation_notes.md`
- any uncertain terminology is reported
- PDF layout uses facing pages for the body or reports alignment limitations
- no unsupported or over-interpreted answers are included
- every main-text paragraph from the original paper is represented in the Bilingual Paper Body as original English + Chinese translation
- no main-text paragraph is replaced by a summary, paraphrase, or explanatory rewrite
- the source map is based on original extracted text, not compressed reading notes
- the number of original source blocks and translated blocks is reported
- any omitted sections, paragraphs, figures, tables, captions, or references are explicitly listed with reasons

## Final Response Format

For Stage 1, report:

```text
Prepared candidate confirmed reading questions only.
No full-paper processing was performed.
Please confirm before Stage 2.
```

Include a table:

| ID | Confirmed question | Expected answer type |
|---|---|---|
| Q1 | ... | ... |

For Stage 2, report:

- created files
- relevance status summary
- original-PDF citation confirmation
- glossary policy confirmation
- quality-check result
- limitations
- whether the bilingual paper body is full-text or summarized
- number of original source blocks extracted
- number of translated blocks
- any omitted sections or paragraphs
- any omitted figures/tables/captions
- any extraction limitations
- whether any block was paraphrased or compressed
