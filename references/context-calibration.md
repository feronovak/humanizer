---
name: context-calibration
description: Content type × length intensity matrix for pattern application. Loaded by humanizer SKILL.md end of Step 1.
author: F. Novak (humanizer v3.0.0)
license: MIT
---

# Context calibration

Different content types and lengths tolerate different humanizer aggressiveness. A regulatory disclosure should not be edited like a LinkedIn post. This module specifies how aggressively to apply each pattern category based on content type × length.

## When to run

End of Step 1 (Diagnose), after determining language / content type / length / input purity.

## Content-type sniff guide

Read the first 200 words. Ask:

- Is the goal to **inform** specific facts that must remain accurate (dates, numbers, names, citations)? → **technical / regulatory / educational**
- Is the goal to **persuade or sell**? → **marketing**
- Is the goal **personal expression or commentary**? → **personal**
- Is the input **a mix** (e.g., a technical post-mortem with personal reflection)? → **mixed** (apply most conservative cell)

## Intensity matrix

Columns: content type. Rows: length. Cells: intensity (light / standard / aggressive).

| | Short <300w | Medium 300-1500w | Long >1500w |
|---|---|---|---|
| Technical | light | light | standard |
| Regulatory | light | light | light |
| Educational | light | standard | standard |
| Marketing | standard | aggressive | aggressive |
| Personal | standard | aggressive | aggressive |
| Mixed | light | standard | standard |

## What "intensity" means

**Light:**
- Apply pattern #7 (vocabulary tells) at focal-list intensity only — skip context-dependent words
- Apply pattern #13 (em dash overuse) — usually safe everywhere
- Apply pattern #19 (chatbot artifacts) — always safe to remove
- **Skip:** #1 (significance inflation often appropriate in technical/regulatory), #5 (vague attributions may be intentional in policy text), #10 (rule of three may be a structural convention)
- **Skip soul toolkit entirely.**

**Standard:**
- Apply most patterns
- Skip only those that conflict with content type:
  - In technical: keep #11 (synonym cycling can be valid — different technical terms aren't synonyms)
  - In educational: keep some #14 (boldface can be pedagogically useful)
- Soul toolkit: light touch — at most one technique per ~400 words

**Aggressive:**
- Apply all patterns from `pattern-catalog.md`
- Soul toolkit: full toolkit, multiple techniques per paragraph if voice permits
- Restructure freely

## Cell-specific notes

**Regulatory short (e.g., privacy policy section):**
Only fix pattern #7 vocabulary clusters and #13 em dashes. Leave structure, repetition, and formal register alone. Compliance prose is supposed to sound bureaucratic.

**Technical medium (e.g., engineering blog post):**
Standard intensity. **Skip pattern #1** (significance inflation) — explaining why something matters technically is not AI-inflation, it's pedagogy. Watch pattern #11 (synonym cycling) carefully — in technical writing "the protagonist / the hero / the central figure" pattern doesn't apply; different terms often mean different things.

**Marketing long (e.g., feature launch post):**
Aggressive intensity. The whole point is voice. Full toolkit applies. Em dashes especially — marketing text overuses them.

**Personal short (e.g., LinkedIn post 150 words):**
Standard intensity, not aggressive. Short personal posts already have voice; over-editing flattens them. Two-pass cap in Step 7 is the key safeguard.

**Personal long (e.g., Substack essay 2000 words):**
Aggressive. Full toolkit. Soul techniques throughout. This is where humanizer earns its keep.

**Mixed (e.g., technical post with personal voice):**
Apply the most conservative cell. If post is 50% technical (light cell) and 50% personal (aggressive cell), use light. Treating personal-tone sections as license to edit aggressively risks breaking the technical content.

## How to pass the intensity decision downstream

When Step 3 of SKILL.md loads `pattern-catalog.md`, the orchestrator knows the intensity. Pattern application then proceeds per the rules above. Step 6 (soul toolkit) also reads intensity to decide how many techniques to apply.

## What this module does NOT do

- Decide whether to edit at all (that's Step 0 over-edit gate)
- Identify hybrid input (that's `hybrid-input.md`)
- Define the patterns themselves (that's `pattern-catalog.md`)
