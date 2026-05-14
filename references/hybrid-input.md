---
name: hybrid-input
description: Identify human-written vs AI-touched sections before editing. Loaded by humanizer SKILL.md end of Step 1 when input is suspected hybrid.
author: F. Novak (humanizer v3.0.0)
license: MIT
---

# Hybrid input handling

Real-world editing is rarely "100% pure AI output." Usually the input is a human draft polished by AI, a human outline filled in by AI, or a brand voice with AI elaboration. This module identifies which sections are already human (leave alone) and which are AI-touched (edit per pattern catalog).

## When to run

End of Step 1, if input suspected hybrid. Suspect hybrid when:
- User mentions a draft they wrote first
- User mentions AI assistance in writing
- The text has visibly different registers in different paragraphs
- The user is editing their own post-AI output

When in doubt, run the heuristics — false positive (treating pure AI as hybrid) costs little; false negative (treating mixed as pure AI) homogenizes the human parts.

## Five heuristics for tagging sections

Read paragraph by paragraph. For each, evaluate:

### 1. Voice consistency

Does this paragraph sound like the rest of the human writer's voice? Or does it sound like a different person took over?

- Sudden shift from "I think X is wrong" to "It is widely acknowledged that X presents challenges" → boundary
- Sudden shift from casual contractions ("don't," "won't") to formal expansions ("do not," "will not") → boundary

### 2. Sentence-length distribution shift

In the writer's known-human paragraphs, what's the average sentence length and variance? Does this paragraph match, or does it suddenly cluster around moderate length?

Pattern: human paragraphs have wider distribution; AI-touched paragraphs cluster at 15-25 words.

### 3. Vocabulary cluster jump

Did focal-word density (from `pattern-catalog.md` #7) suddenly spike in this paragraph?

- 0-1 focal words: probably human-written
- 2+ focal words in one paragraph: probably AI-touched

### 4. Specific details indicate human

Sentences with:
- Real names ("Mária from accounting said…")
- Specific dates ("on March 14 at 2pm")
- Anecdotes ("when we tried this in 2019…")
- Idiosyncratic numbers ("we lost 17 customers, not 20, not 15")

…are almost always human-written. AI doesn't invent specific details without prompting. Tag HUMAN unless context strongly suggests otherwise.

### 5. Generic significance indicates AI

Sentences with:
- "represents a pivotal moment"
- "underscores the importance of"
- "speaks to broader trends in"
- "marks a turning point"

…are almost always AI-touched. Tag AI.

## Output: three-way tagging

Tag each paragraph (or sentence cluster) as:

- **HUMAN** — leave entirely alone. Do NOT edit even if patterns match.
- **MIXED** — apply patterns conservatively. Don't change voice; only fix concrete AI tells (vocabulary clusters, em dash overuse, chatbot artifacts).
- **AI** — apply full pattern catalog at intensity from `context-calibration.md`.

## Rule

**Editing HUMAN paragraphs is the worst failure mode.** It flattens the writer's voice exactly where the voice was strongest. When in doubt between HUMAN and MIXED, choose HUMAN.

## Why this matters

Detection tools flag the AI-touched sections more than the human ones. If you edit indiscriminately, you flatten the human sections (making the whole text sound more uniform) AND leave the AI sections half-edited (because you spread effort thin). Tagging concentrates editing where it matters.

## Example tagging

Input paragraph:

> Q3 was rough. We lost two customers in October to a competitor that undercut us by 30%, and one in November to a product issue we caught too late. Despite these challenges, our resilient team continues to innovate and drive forward, demonstrating an unwavering commitment to excellence.

Sentence 1 ("Q3 was rough."): **HUMAN** — terse, specific to writer's voice.
Sentence 2 ("We lost two customers…"): **HUMAN** — specific details (two, October, 30%, one, November).
Sentence 3 ("Despite these challenges…"): **AI** — "resilient team," "drive forward," "unwavering commitment" — three focal words in 19 words, plus formulaic "Despite these challenges" structure (pattern #6).

Edit: leave first two sentences alone; rewrite the third.

> Q3 was rough. We lost two customers in October to a competitor that undercut us by 30%, and one in November to a product issue we caught too late. The team is regrouping for Q4.

## What this module does NOT do

- Decide overall intensity (that's `context-calibration.md`)
- Apply patterns (that's Step 3 + `pattern-catalog.md`)
- Define what counts as "AI" beyond the heuristics above
