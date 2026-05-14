---
title: Humanizer v3.0.0 — modular restructure + 5 new modules
date: 2026-05-14
author: Fero Novak (designed with Claude Code)
status: approved — ready for implementation planning
---

# Humanizer v3.0.0 — Design Doc

## Motivation

The current humanizer skill (v2.5.0) is a 544-line monolith. It works, but research on AI detection and commercial humanizer tools reveals seven gaps. Three of those gaps map directly to the universal failure modes of commercial humanizer tools (broken transitions, vague "add soul," tone-flattening). Two more reflect real-world editing patterns the skill doesn't handle (hybrid input, content-type sensitivity). The last two address Option C (voice first, detector check) and ESL writer protection.

A bolt-on approach (add sections to SKILL.md) would push the file past 700 lines and keep the attribution problem unresolved. A modular restructure solves both: the orchestrator becomes original work, the upstream pattern catalog stays attributed in a single file, and each gap maps to a focused reference module.

## Improvement priority

Per session decision: **Option C — voice quality first, detector check only if necessary**. All design choices favor natural voice over detector compliance. The detector lens is an explicit but secondary final pass.

## Research findings (summary)

Empirical detection studies (Kobak et al. 2024, COLING 2025, Computational Linguistics 2025 survey, Freeburg 2025) and commercial detector documentation converge on five signals:

1. **Vocabulary frequency shifts.** Z-score >3.5 in 2024 for *delve, underscore, tapestry, intricate, showcase, meticulous, boast, commendable, surpass, unlocking, pivotal*.
2. **Sentence-length uniformity.** Top stylometric discriminator beyond perplexity.
3. **Em-dash frequency.** GPT-4.1 uses ~3.28× human baseline; GPT-5.1 already suppresses by default.
4. **Narrower vocabulary, synonym cycling.** Higher repetition penalty in LLMs.
5. **Lower emotional range.** Fewer question/exclamation marks, more formal register.

Commercial humanizer tools (StealthGPT, WriteHuman, Phrasly, Undetectable.ai, etc.) consistently fail on:

- Awkward phrasing / odd word choices
- Broken transitions between rewritten sentences
- Tone flattening / homogenized voice
- Inconsistent outputs across runs
- Sentence-level focus producing choppy paragraphs

The best-performing tools (StealthGPT, WriteHuman) distinguish themselves by **restructuring** rather than paraphrasing, and by attempting to preserve voice rather than just swapping vocabulary.

## Gap analysis (v2.5.0 → v3.0.0)

### What v2.5.0 already handles well

- Voice preservation (Step 1 voice-ID) — addresses tone-flattening directly
- Sentence-rhythm pattern (#25) — targets top stylometric discriminator
- Two-pass cap (Step 6) — solves cross-run inconsistency
- Over-edit gate (Step 0) — prevents partial-rewrite fingerprint leak
- Research-grounded focal vocabulary — Kobak-validated

### Gaps addressed in v3.0.0

| # | Gap | Solution | Module |
|---|---|---|---|
| 1 | No transition-coherence check | Post-edit diagnostic + fix instructions | `transition-coherence.md` |
| 2 | "Add soul" is underspecified | Concrete toolkit of six techniques with examples | `soul-toolkit.md` |
| 3 | No content-type sensitivity | Matrix: content type × length → intensity | `context-calibration.md` |
| 4 | No hybrid-input handling | Heuristics to mark human vs AI-touched sections | `hybrid-input.md` |
| 5 | No length-aware strategy | Folded into `context-calibration.md` matrix | (above) |
| 6 | ESL writer protection asymmetric | Bidirectional note in SKILL.md Step 2 | SKILL.md |
| 7 | No detector lens (Option C fallback) | Final optional stylometric self-check | `detector-lens.md` |

### Explicitly out of scope

- Model-specific tuning (GPT vs Claude vs Gemini tells) — over-engineering for voice-first
- Persona-voice library (dataskater, fero-MD, brand X) — adjacent project, separate `references/persona-voices/` directory used by callers, not bolted into agnostic skill

## Architecture

```
~/.claude/skills/humanizer/
├── SKILL.md                              # Workflow orchestrator (~200 lines, original)
├── references/
│   ├── pattern-catalog.md                # 24 base patterns + #25 (attributed)
│   ├── transition-coherence.md           # Original, ~80 lines
│   ├── soul-toolkit.md                   # Original, ~120 lines
│   ├── context-calibration.md            # Original, ~100 lines
│   ├── hybrid-input.md                   # Original, ~80 lines
│   ├── detector-lens.md                  # Original, ~80 lines
│   └── language-overlay-slovak.md        # Original, ~50 lines
├── LICENSE                               # Unchanged — Original MIT (Siqi Chen)
├── NOTICE                                # New — full attribution
├── README.md                             # Rewritten for new structure
├── CHANGELOG.md                          # New — full version history
└── docs/specs/                           # Design docs (this file lives here)
```

**Total content:** ~840 lines across 7 reference files + ~200-line SKILL.md = ~1040 lines vs current 544. ~90% more content, all in service of identified gaps.

## SKILL.md orchestrator

Replaces current 544-line monolith with ~200-line workflow. Eight steps:

1. **Step 0 — Over-edit gate.** If text reads naturally, declare clean and stop. (Preserved from v2.5.0.)

2. **Step 1 — Diagnose.** Establish four things before editing:
   - Language → if non-English, load `references/language-overlay-<lang>.md`
   - Content type (technical / marketing / personal / regulatory / mixed)
   - Length (short <300w / medium 300-1500w / long >1500w)
   - Input purity → if suspected hybrid, load `references/hybrid-input.md`
   
   Then load `references/context-calibration.md` for pattern intensity.

3. **Step 2 — Identify target voice.** Persona / brand / generic author. Voice signatures override pattern catalog. Bidirectional ESL note: if writer is non-native English, treat low burstiness and limited vocab as ESL features, not AI tells — be more conservative.

4. **Step 3 — Scan for patterns.** Load `references/pattern-catalog.md`. Apply at intensity from Step 1. Cluster of 3+ focal vocab in a paragraph = high signal; isolated single matches usually not.

5. **Step 4 — Rewrite problematic sections.** Replace AI-isms with alternatives fitting Step 2 voice. Preserve meaning and facts. Do not invent details.

6. **Step 5 — Transition coherence check.** Load `references/transition-coherence.md`. Re-read paragraphs end-to-end. Fix broken connective tissue.

7. **Step 6 — Add soul where flat.** Load `references/soul-toolkit.md`. Apply only where text reads flat AND target voice permits. Not every text needs soul injection (regulatory disclosures don't).

8. **Step 7 — Anti-AI audit.** Two-pass cap from v2.5.0 preserved. "What makes the below so obviously AI generated?" → answer briefly → "Now make it not obviously AI generated." → revise. **Stop after one pass.**

9. **Step 8 — Detector lens (optional, Option C fallback).** Load `references/detector-lens.md`. Run stylometric self-check. **Only fix if failure points to genuine quality issue, not detector quirk.** Voice quality beats detector compliance.

## References — content scope

### `pattern-catalog.md` (extracted with attribution)

Verbatim extract of current SKILL.md sections "CONTENT PATTERNS" / "LANGUAGE AND GRAMMAR PATTERNS" / "STYLE PATTERNS" / "COMMUNICATION PATTERNS" / "FILLER AND HEDGING" (~330 lines). Frontmatter attributes Wikipedia (CC BY-SA) and blader/humanizer (MIT). Pattern #7 (vocabulary) and #25 (sentence rhythm) reflect Fero's v2.4.0 contributions.

### `transition-coherence.md` (original)

**Purpose:** Catch broken connective tissue after surgical edits. The #1 commercial humanizer failure mode.

**Contents:**
- Diagnostic checklist (does pronoun X still have antecedent? Did "therefore" survive a deleted sentence? Does the paragraph still flow when read aloud?)
- Three before/after examples:
  - Deleted significance-inflation orphans a "This" with no antecedent
  - Split rule-of-three leaves dangling "and finally"
  - Rewritten copula avoidance breaks list rhythm
- Fix patterns: bridging sentences, antecedent restoration, transition word adjustment

**Trigger:** Step 5 of SKILL.md.
**Length target:** ~80 lines.

### `soul-toolkit.md` (original)

**Purpose:** Operationalize "add soul" with repeatable techniques.

**Contents:**
Six techniques, each with before/after:
1. Discourse markers ("anyway," "look," "the thing is")
2. Sentence fragments where rhythm calls for them
3. Parenthetical asides (the kind that derail mid-thought)
4. Mid-sentence self-correction ("…well, mostly")
5. Hedge admissions ("I don't actually know if X is right")
6. Specific over generic ("agents churning at 3am" beats "this is concerning")

Plus meta-rule: apply only techniques that fit target voice from Step 2. Don't add fragments to regulatory disclosure.

**Trigger:** Step 6 of SKILL.md.
**Length target:** ~120 lines.

### `context-calibration.md` (original)

**Purpose:** Adjust pattern application by content type and length.

**Contents:**
- Content-type sniff guide (how to tell technical from marketing from personal in one read)
- Matrix: content type (technical / marketing / personal / regulatory / educational) × length (short / medium / long) → intensity (light / standard / aggressive)
- Cell-specific notes:
  - Regulatory short: only fix vocabulary #7 and em dashes #13; leave structure alone
  - Marketing long: full pattern catalog, aggressive intensity
  - Technical medium: skip #1 (significance inflation often appropriate), apply #7-#13 standard
  - etc.

**Trigger:** End of Step 1.
**Length target:** ~100 lines.

### `hybrid-input.md` (original)

**Purpose:** Identify already-human sections; only edit AI-touched.

**Contents:**
- Heuristics for marking sections:
  - Voice consistency (sudden register shift = boundary)
  - Sentence-length distribution shift across paragraphs
  - Vocabulary cluster jump (focal-word density spike)
  - Personal/specific details (real names, dates, idiosyncratic anecdotes) → human-likely
  - Generic significance statements → AI-likely
- Output: tag each paragraph as HUMAN / MIXED / AI before editing
- Rule: only edit AI and MIXED. Leave HUMAN alone even if patterns match.

**Trigger:** End of Step 1 if input suspected hybrid.
**Length target:** ~80 lines.

### `detector-lens.md` (original)

**Purpose:** Final optional pass — verify output would survive stylometric detectors WITHOUT optimizing against them.

**Contents:**
- Four mental checks:
  1. Burstiness: do consecutive sentences span meaningful length range?
  2. Vocabulary clustering: focal words below 3-per-paragraph?
  3. Em-dash density: ≤1 per 200 words?
  4. Coherence: text still makes sense after edits?
- Decision rule: **only fix if failure points to real quality issue, not detector quirk.** If burstiness is low because writer is non-native English with naturally uniform rhythm, ship it.
- Voice quality beats detector compliance.

**Trigger:** Step 8 of SKILL.md (optional).
**Length target:** ~80 lines.

### `language-overlay-slovak.md` (extracted)

Verbatim extract of current "Language overlay: Slovak" section (v2.5.0). Frontmatter explains load trigger. Future Czech/Polish/Hungarian overlays slot in as parallel files.

**Trigger:** Step 1 if target language is Slovak.
**Length target:** ~50 lines.

## License, attribution, README, CHANGELOG

- **LICENSE:** Unchanged. Original MIT (Siqi Chen © 2025).
- **NOTICE:** New file documenting upstream fork lineage + Fero's contributions per file + Wikipedia source citation.
- **README.md:** Full rewrite reflecting new modular structure. Prominent fork attribution. List of differences from upstream. Installation and usage.
- **CHANGELOG.md:** New file documenting v2.3.0 (upstream baseline) → v2.4.0 → v2.5.0 → v3.0.0 with concrete changes per version.

Templates for each are in the brainstorming session transcript and will be drafted during implementation.

## Verification approach

This is a technique skill, not a discipline-enforcing one. No subagent pressure testing.

**Smoke test (~15 min after implementation):**
1. Take 3 sample inputs: dataskater-style English post, Fero-style Slovak LinkedIn post, technical FAQ.
2. Run each through v2.5.0 and v3.0.0.
3. Pass criterion: v3.0.0 output is at least as natural as v2.5.0, AND preserves voice signatures.

**Gap-specific tests (~30 min):**
1. **Transition coherence:** paragraph with known broken transition. v3.0.0 catches it; v2.5.0 won't.
2. **Hybrid input:** paragraph 70% human / 30% AI. v3.0.0 leaves human parts alone.
3. **Context calibration:** regulatory disclosure → light edits; casual LinkedIn → more aggressive.
4. **Detector lens:** already-clean text → "ship as-is," no rewrite.

**Real-world validation (downstream, ongoing):**
- Run v3.0.0 on dataskater posts for a week, compare to v2.5.0.
- Spot-check Slovak content for feronovak.com.

## Implementation order

Suggested sequence (writing-plans skill will produce detailed plan):

1. Create `docs/specs/` and commit this design doc.
2. Create `references/` directory.
3. Extract `references/pattern-catalog.md` from current SKILL.md (verbatim + attribution frontmatter).
4. Extract `references/language-overlay-slovak.md` from current SKILL.md.
5. Write new `SKILL.md` orchestrator (the centerpiece — this is where ordering matters).
6. Write new reference modules in priority order:
   a. `transition-coherence.md` (highest voice impact)
   b. `soul-toolkit.md` (highest voice impact)
   c. `context-calibration.md` (high)
   d. `hybrid-input.md` (medium)
   e. `detector-lens.md` (medium-low)
7. Verify LICENSE unchanged.
8. Write `NOTICE`.
9. Rewrite `README.md`.
10. Write `CHANGELOG.md`.
11. Smoke test + gap-specific tests.
12. Commit v3.0.0 release.

## Open decisions (deferred)

- **Publishing scope:** Honest fork at `feronovak/humanizer` (Option A) vs. fresh rewrite as separate skill (Option B) vs. keep private (Option C). Approach 3 design is compatible with all three; decision can be made after v3.0.0 implementation is done and validated.
- **Pattern catalog rewrite:** Currently verbatim extract with attribution. If Option B publishing is chosen later, rewrite pattern-catalog.md from scratch citing Wikipedia directly. Adds ~2-3 hours.
- **Persona-voice library:** Adjacent project. Separate `references/persona-voices/` directory used by callers (e.g., dataskater's voice prompt loads `dataskater.md` and passes it to humanizer's Step 2). Not in v3.0.0 scope.
- **Czech / Polish / Hungarian overlays:** Slovak overlay is a starting point. Additional overlays added on demand when use case appears.
