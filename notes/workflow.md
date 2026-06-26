# Workflow Notes

This project uses a two-stage workflow.

## Stage 1: Confirm Questions

When the user runs a trigger such as:

```text
translate <paper-name.pdf>
翻译 <paper-name.pdf>
```

without confirmed questions, the agent must run Stage 1 only.

Stage 1 produces a confirmed reading-question list with expected answer types. It must not read, extract, summarize, or translate the full paper.

## Stage 2: Process Paper

Stage 2 starts only after the reading questions are confirmed, or when the initial request already includes confirmed questions.

Stage 2 reads the paper with the questions in mind, extracts logical blocks with original PDF page numbers, translates the main text into Chinese, builds a paper-specific glossary, and produces the required outputs.

## Required Reading Package Order

Both the PDF and Markdown translation must appear in this order:

```text
Cover
Confirmed reading questions
Question-answer review
Glossary
Bilingual paper body
```

The review and glossary are front matter so the reader can understand what to look for before reading the full translated body.
