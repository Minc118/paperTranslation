# One-Shot Prompt: Direct Stage 2

Use the `question-guided-bilingual-paper-review` skill.

The paper and confirmed questions are already known, so run Stage 2 directly.

Paper:

```text
<paper-name.pdf>
```

Confirmed questions:

```text
Q1. ...
Q2. ...
Q3. ...
```

Requirements:

1. Treat the listed questions as confirmed.
2. Read the paper with those questions in mind.
3. Extract structure and logical blocks.
4. Assign stable block IDs and original PDF page numbers.
5. Build the Bilingual Paper Body as a full, source-faithful bilingual translation, not a summary.
6. Preserve the original English text for every main-text block, except for minimal cleanup of obvious PDF extraction artifacts.
7. Translate each preserved original English block into Chinese.
8. Do not compress, paraphrase, simplify, summarize, or replace original paragraphs with explanations in the Bilingual Paper Body.
9. If extraction is uncertain, keep the extracted original text and mark the uncertainty in `translation_notes.md` or `quality_check_report.md`.
10. Ensure the source map / `extraction_blocks.json` represents original paper text, not summarized reading notes.
11. Do not translate the bibliography unless explicitly requested.
12. Create a paper-specific glossary.
13. Generate:

```text
outputs/<paper-name>/
├── bilingual_facing_pages.pdf
├── bilingual_translation.md
└── question_answer_review.md
```

14. Include the question-answer review and glossary before the paper body in both `bilingual_facing_pages.pdf` and `bilingual_translation.md`.
15. Use original PDF page citations in all factual answers:

```text
[p. X, Section Y, Block P-0XX]
```

16. Mark each question as `Relevant`, `Partially relevant`, or `Not relevant`.
17. Do not invent answers for not-relevant questions.
18. Run the full quality check before reporting completion.

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
