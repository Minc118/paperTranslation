# paperTranslation

Reusable project for question-guided bilingual academic paper translation and review.

This repository is for building bilingual reading packages, not for one-off blind translation. The workflow first confirms what the reader wants to learn, then processes the paper around those questions.

## Purpose

The project creates question-guided bilingual academic reading packages:

1. Confirm the reading questions first.
2. Read the paper with those questions in mind.
3. Translate the paper into Chinese while preserving the original English.
4. Generate a bilingual facing-page PDF.
5. Generate a citation-based question-answer review.
6. Help the user review, verify, and think through the paper again.

## Quick Commands

Supported Chinese triggers:

```text
翻译 <paper-name.pdf>
翻译一下 <paper-name.pdf>
处理 <paper-name.pdf>
阅读 <paper-name.pdf>
```

Supported English triggers:

```text
translate <paper-name.pdf>
translate and review <paper-name.pdf>
review <paper-name.pdf>
process <paper-name.pdf>
read <paper-name.pdf>
```

If the command contains a paper name but no confirmed questions, run Stage 1 only.

Examples:

```text
翻译 fusionize.pdf
translate fusionize.pdf
```

If the command contains a paper name and explicitly confirmed questions, run Stage 2 directly. Questions become final confirmed questions only after the user explicitly confirms them or provides them as confirmed questions.

Example:

```text
translate fusionize.pdf

Questions:
Q1. ...
Q2. ...
Q3. ...
```

## Stage 1: Confirm Reading Questions Only

Stage 1 is only for confirming the reading questions.

In Stage 1:

- Do not process the paper content.
- Do not read, extract, summarize, or translate the full paper.
- Do not create the final PDF.
- Do not create the paper structure map.
- Do not create final output directories or final deliverables such as `outputs/<paper-name>/`, `bilingual_facing_pages.pdf`, `bilingual_translation.md`, or `question_answer_review.md`.
- Help the user formulate clear academic reading questions.
- Rewrite vague questions into clearer academic questions.
- Assign an expected answer type to each question.
- Stop after preparing the candidate confirmed question list.
- Ask the user to explicitly confirm the questions before Stage 2.

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

## Stage 2: Process Paper with Confirmed Questions

Stage 2 starts only after questions are confirmed.

In Stage 2:

- Read the paper with the confirmed questions in mind.
- Extract paper structure and logical blocks.
- Assign stable block IDs.
- Record original PDF page numbers.
- Translate each main-text block into Chinese.
- Preserve original English text.
- Tag blocks by relevant question IDs.
- Maintain a paper-specific glossary.
- Generate a bilingual facing-page PDF.
- Generate a citation-based question-answer review.
- Mark each question as `Relevant`, `Partially relevant`, or `Not relevant`.
- Run quality checks.

## Required Outputs

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

Both `bilingual_facing_pages.pdf` and `bilingual_translation.md` must use this order:

```text
Cover
Confirmed reading questions
Question-answer review
Glossary
Bilingual paper body
Quality-check note or appendix if needed
```

The question-answer review and glossary must appear before the translated paper body so the reader knows what to look for before reading the full bilingual text. The full `question_answer_review.md` must also be created as a separate file.

## Facing-Page PDF Layout

PDF structure:

```text
Page 1: Cover page
Pages 2-N: Front matter
After front matter: bilingual paper body
```

The front matter does not need strict facing-page layout. The paper body must use facing pages:

```text
left page = original English text
right page = Chinese translation
```

When viewed in two-page mode with the cover page shown separately, English original pages should appear on the left and matching Chinese translation pages on the right. Align by logical blocks, not raw PDF line breaks. If exact visual alignment is not possible, prioritize logical block alignment and report the limitation.

## Citation Rules

All answers in `question_answer_review.md` must cite original PDF evidence using:

```text
[p. X, Section Y, Block P-0XX]
```

Rules:

- `p. X` refers to the original PDF page number, not the generated bilingual PDF.
- Every important claim must have a citation.
- Every answer must include an evidence table unless the relevance judgment is `Not relevant`.
- Do not cite the Chinese translation as evidence.
- If interpretation is necessary, mark it as interpretation and cite the supporting source blocks.
- If a claim cannot be linked to an original PDF page number and block ID, do not include it as a factual answer.

## Not-Relevant Rule

If the paper does not contain relevant content for a question, answer:

```text
本文没有提供与该问题直接相关的内容，因此该问题与本文不相关。
```

Do not invent an answer, use external knowledge, or over-interpret unrelated passages.

## Paper-Specific Glossary Policy

Every processed paper must have a paper-specific glossary based on terminology actually used in that paper.

Workflow:

1. Detect the paper domain.
2. Extract important technical terms.
3. Check whether an existing default glossary is relevant.
4. Use matching default terms only when appropriate.
5. Create a paper-specific glossary.
6. Use it consistently in translation.
7. Report uncertain terminology choices in the quality-check note.

The Serverless / FaaS glossary in `AGENTS.md` and the skill file is only a reference glossary. Use it only when the paper is about serverless computing, FaaS, function fusion, cloud orchestration, or related topics. Do not force it onto unrelated papers.

## Quality Checks

Before final reporting, verify:

- Stage 1 did not read or translate the full paper.
- Stage 2 used confirmed questions.
- Required output files exist.
- Output order is correct.
- Original English and Chinese translation are both preserved.
- Citations use original PDF page numbers, sections, and block IDs.
- Not-relevant questions are not answered with invented content.
- Glossary is paper-specific.
- Any PDF alignment limitations are reported.
