# Stage 1 Prompt: Confirm Reading Questions Only

Use the `question-guided-bilingual-paper-review` skill.

Run Stage 1 only for:

```text
<paper-name.pdf>
```

Do not read, extract, summarize, or translate the full paper.

Do not create the final PDF, paper structure map, translations, or final output files.

Your task:

1. Confirm the paper name.
2. Ask for reading questions if none were supplied.
3. Refine vague questions into clear academic reading questions.
4. Split overloaded questions and merge duplicates when appropriate.
5. Assign one expected answer type to each question:

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

6. Output the confirmed question list.
7. Stop and ask the user to confirm before Stage 2.

Required Stage 1 closing statement:

```text
Stage 1 complete. No full-paper processing was performed. Please confirm these questions before Stage 2.
```
