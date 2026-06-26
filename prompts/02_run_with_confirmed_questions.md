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
6. Translate the full main text into natural Chinese.
7. Preserve the original English text.
8. Do not translate the bibliography unless explicitly requested.
9. Tag blocks by relevant question IDs.
10. Create a paper-specific glossary based on actual terminology in the paper.
11. Use default glossary terms only when they match the paper domain.
12. Generate the required outputs:

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
