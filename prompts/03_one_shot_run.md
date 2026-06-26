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
5. Translate the main text into Chinese while preserving the original English.
6. Do not translate the bibliography unless explicitly requested.
7. Create a paper-specific glossary.
8. Generate:

```text
outputs/<paper-name>/
├── bilingual_facing_pages.pdf
├── bilingual_translation.md
└── question_answer_review.md
```

9. Include the question-answer review and glossary before the paper body in both `bilingual_facing_pages.pdf` and `bilingual_translation.md`.
10. Use original PDF page citations in all factual answers:

```text
[p. X, Section Y, Block P-0XX]
```

11. Mark each question as `Relevant`, `Partially relevant`, or `Not relevant`.
12. Do not invent answers for not-relevant questions.
13. Run the full quality check before reporting completion.
