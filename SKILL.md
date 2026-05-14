---
name: humanizer
version: 3.0.0
description: Use when editing or reviewing text that may sound AI-generated, robotic, or sterile — especially before publishing persona content, marketing copy, or LLM-assisted drafts. Grounded in detection research (Kobak et al. 2024, COLING 2025) and Wikipedia's WikiProject AI Cleanup. Modular: SKILL.md is the orchestrator; specific patterns and language overlays live in references/. English-tuned by default; load language-overlay-<lang>.md for other languages.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - AskUserQuestion
---

# Humanizer: Remove AI Writing Patterns

This skill is an orchestrator. The 24-pattern catalog and language-specific overlays live in `references/`. Each step below loads the references it needs.

## What research shows

Detection studies (Kobak et al. 2024, COLING 2025, Computational Linguistics 2025 survey) identify five high-signal markers:

1. **Vocabulary frequency shifts** — *delve, underscore, tapestry, intricate, showcase, meticulous, boast, commendable, surpass, unlocking, pivotal* spiked sharply post-2022 (Z>3.5 in 2024).
2. **Sentence-length uniformity** — top stylometric discriminator beyond perplexity. Humans cluster at extremes; LLMs cluster at moderate length.
3. **Em-dash frequency** — GPT-4.1 uses ~3.28× human baseline (Freeburg 2025). GPT-5.1 suppresses by default.
4. **Narrower vocabulary, synonym cycling** — LLMs have higher repetition penalty.
5. **Lower emotional range** — fewer question/exclamation marks, more formal register.

**Caveats (read before flagging anything):**

- **No single marker is proof.** Many experienced human writers use em dashes heavily. Vocabulary tells need *frequency*, not single occurrences.
- **ESL bias.** Non-native English writers naturally produce lower-perplexity, more uniform text. Apply judgment.
- **Models adapt.** Detection markers move with model versions. Combine markers, do not rely on any one.
- **Language scope.** Default catalog is English-tuned. Load `references/language-overlay-<lang>.md` for other languages.

## Workflow

The workflow has nine steps (0 through 8). Steps load references on demand.

### Step 0 — Over-edit gate

If the text already reads naturally — sentence variance present, no vocabulary clusters, no significance inflation — declare clean and stop. Reflexive editing produces homogenized output, which is its own AI tell. Single-pattern issues get a single-pattern fix; do not rewrite the rest "for consistency."

### Step 1 — Diagnose

Before editing, establish four things:

1. **Language.** If non-English, load `references/language-overlay-<lang>.md`. Currently available: `slovak`. Other languages: catalog applies as starting hypothesis, with caveats.
2. **Content type.** Technical / marketing / personal / regulatory / educational / mixed.
3. **Length.** Short (<300 words) / medium (300-1500) / long (>1500).
4. **Input purity.** If input might be hybrid (human draft + AI polish), load `references/hybrid-input.md` and tag each paragraph HUMAN / MIXED / AI before editing.

Then load `references/context-calibration.md` to determine which patterns to apply and at what intensity for this content type × length combination.

### Step 2 — Identify target voice

Ask if not told: which persona, brand, or author? Voice signatures take precedence over the pattern catalog. If the writer's natural voice uses a flagged pattern (em dashes for an essayist, "key" as a verb for an engineer, long sentences with semicolons), leave it. Goal: make the text sound like its writer with AI-isms gone — not standardize voice across personas.

**ESL note (bidirectional):** if the writer is a non-native English speaker, treat low burstiness and limited vocabulary as ESL features, not AI tells. Be more conservative.

### Step 3 — Scan for patterns

Load `references/pattern-catalog.md`. Apply patterns at the intensity from Step 1's context calibration. Cluster of 3+ focal vocabulary matches in a paragraph = high signal; isolated single matches usually are not.

### Step 4 — Rewrite problematic sections

Replace AI-isms with natural alternatives that fit Step 2's target voice. Preserve meaning and facts. Do not invent details (names, statistics, citations) to sound "more human" — that creates a different problem.

### Step 5 — Transition coherence check

Load `references/transition-coherence.md`. After surgical edits, re-read paragraphs end-to-end. Fix broken connective tissue: orphaned pronouns, dangling transition words, broken antecedents, abandoned list rhythms.

### Step 6 — Add soul where flat

Load `references/soul-toolkit.md`. Apply only where the text reads flat AND the target voice from Step 2 permits. Not every text needs soul injection — regulatory disclosures don't.

### Step 7 — Anti-AI audit — cap at two passes

Prompt: "What makes the below so obviously AI generated?" Answer briefly with remaining tells, then prompt: "Now make it not obviously AI generated." and revise. **Stop after this pass.** Further iteration produces homogenized "humanized" slop.

### Step 8 — Detector lens (optional, Option C fallback)

Load `references/detector-lens.md`. Run stylometric self-check. **Only fix if failure points to a real quality issue, not a detector quirk.** If burstiness is low because the writer is non-native English with naturally uniform rhythm, ship it. Voice quality beats detector compliance.

## Output Format

Provide:

1. Draft humanized version
2. "What makes the below so obviously AI generated?" — brief bullets
3. Final version (revised after the audit)
4. Brief summary of changes made (optional)

## Reference dependencies

| Reference | Loaded at | Source / Author |
|---|---|---|
| `references/pattern-catalog.md` | Step 3 | Wikipedia/blader (MIT, attributed) + F. Novak v2.4.0 contributions |
| `references/transition-coherence.md` | Step 5 | F. Novak (MIT) |
| `references/soul-toolkit.md` | Step 6 | F. Novak (MIT) |
| `references/context-calibration.md` | End of Step 1 | F. Novak (MIT) |
| `references/hybrid-input.md` | End of Step 1 (if hybrid suspected) | F. Novak (MIT) |
| `references/detector-lens.md` | Step 8 (optional) | F. Novak (MIT) |
| `references/language-overlay-slovak.md` | Step 1 (if Slovak) | F. Novak (MIT) |

See `NOTICE` for full attribution. See `LICENSE` (MIT, Siqi Chen © 2025).
