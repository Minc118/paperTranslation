# Stage 2 Prompt: Run With Confirmed Questions

Use the `question-guided-bilingual-paper-review` skill.

Run Stage 2 for:

```text
<paper-name.pdf>
```

Confirmed questions:

```text
Q1. ...
Q2. ...
Q3. ...
```

Tasks:

1. Read the paper with the confirmed questions in mind.
2. Extract the paper structure.
3. Extract logical blocks instead of raw PDF lines.
4. Assign stable block IDs.
5. Record original PDF page numbers for each block.
6. Build the Bilingual Paper Body as a full, source-faithful bilingual translation, not a summary.
7. Preserve the original English text for every main-text block, except for minimal cleanup of obvious PDF extraction artifacts.
8. Translate each preserved original English block into natural Chinese.
9. Do not compress, paraphrase, simplify, summarize, or replace original paragraphs with explanations in the Bilingual Paper Body.
10. If extraction is uncertain, keep the extracted original text and mark the uncertainty in `translation_notes.md` or `quality_check_report.md`.
11. Ensure the source map / `extraction_blocks.json` represents original paper text, not summarized reading notes.
12. Do not translate the bibliography unless explicitly requested.
13. Tag blocks by relevant question IDs.
14. Create a paper-specific glossary based on actual terminology in the paper.
15. Use default glossary terms only when they match the paper domain.
16. Generate the required outputs:

```text
outputs/<paper-name>/
├── bilingual_facing_pages.pdf
├── bilingual_translation.md
└── question_answer_review.md
```

Optional outputs:

```text
outputs/<paper-name>/
├── bilingual_facing_pages.html
├── glossary.md
├── paper_structure_map.md
├── extraction_blocks.json
├── translation_notes.md
└── quality_check_report.md
```

Both `bilingual_facing_pages.pdf` and `bilingual_translation.md` must use this order:

```text
Cover
Confirmed reading questions
Question-answer review
Glossary
Bilingual paper body
Quality-check note or appendix if needed
```

The question-answer review must use original PDF page-number citations:

```text
[p. X, Section Y, Block P-0XX]
```

Mark every question as:

```text
Relevant
Partially relevant
Not relevant
```

For not-relevant questions, answer exactly:

```text
本文没有提供与该问题直接相关的内容，因此该问题与本文不相关。
```

Run a quality check before the final response. Confirm required files, output order, citation rules, not-relevant handling, paper-specific glossary use, and any PDF alignment limitations.

Important distinction:

```text
Question-Answer Review = may summarize, synthesize, and interpret with citations.
Bilingual Paper Body = must preserve original source text and provide a faithful Chinese translation.
```

Hard failure rule:

```text
If any main-text block in the Bilingual Paper Body is a summary, paraphrase, compressed rewrite, or explanatory replacement instead of the original source text plus Chinese translation, the output fails the task.
```

Before finishing Stage 2, explicitly report:

- whether the bilingual paper body is full-text or summarized
- number of original source blocks extracted
- number of translated blocks
- any omitted sections or paragraphs
- any omitted figures/tables/captions
- any extraction limitations
- whether any block was paraphrased or compressed
