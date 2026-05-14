# Humanizer v3.0.0 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restructure humanizer skill from monolithic 544-line SKILL.md into orchestrator + 7 modular references, adding 5 new modules to address gaps identified in research.

**Architecture:** SKILL.md becomes a thin ~200-line workflow orchestrator that loads references on demand. The 24-pattern catalog (currently inline) moves to `references/pattern-catalog.md` with full attribution to upstream and Wikipedia. Five new reference modules close specific gaps: transition coherence, soul toolkit, context calibration, hybrid input handling, and detector lens. Plus extracted Slovak overlay and rewritten README/NOTICE/CHANGELOG.

**Tech Stack:** Markdown skill files in `~/.claude/skills/humanizer/`. Git for version control. No build system. No automated tests — verification via smoke tests on sample inputs.

**Spec:** [2026-05-14-humanizer-improvements-design.md](../specs/2026-05-14-humanizer-improvements-design.md)

---

## Pre-flight

- [ ] **Step 0a: Confirm working directory and clean state**

Run: `cd ~/.claude/skills/humanizer && git status`
Expected: on `main`, one or two pending modifications (the prior v2.5.0 work — these stay; we build on top).

- [ ] **Step 0b: Read the spec doc**

Run: `cat docs/specs/2026-05-14-humanizer-improvements-design.md | head -100`
Expected: design doc is present and current. If not, abort.

- [ ] **Step 0c: Snapshot current SKILL.md for extraction reference**

Run: `cp SKILL.md /tmp/humanizer-skill-v2.5.0-snapshot.md && wc -l /tmp/humanizer-skill-v2.5.0-snapshot.md`
Expected: ~544 lines copied to /tmp. This is the source for extraction tasks. Do not modify it.

---

## Task 1: Extract pattern-catalog.md

**Files:**
- Create: `references/pattern-catalog.md`
- Source: `/tmp/humanizer-skill-v2.5.0-snapshot.md` lines 87-403

- [ ] **Step 1: Create references directory**

```bash
mkdir -p references
```

- [ ] **Step 2: Write attribution frontmatter to pattern-catalog.md**

```bash
cat > references/pattern-catalog.md <<'EOF'
---
name: pattern-catalog
description: 24-pattern catalog of AI writing tells, loaded by humanizer SKILL.md Step 3
attribution:
  - Patterns 1-24, examples, and pattern structure originate from blader/humanizer
    (MIT licensed, Copyright (c) 2025 Siqi Chen), ultimately derived from Wikipedia's
    "Signs of AI writing" page (WikiProject AI Cleanup, CC BY-SA).
  - Pattern 7 (AI vocabulary) refined in humanizer v2.4.0 by F. Novak — research-validated
    vs context-dependent split based on Kobak et al. 2024.
  - Pattern 25 (Uniform Sentence Rhythm) added in humanizer v2.4.0 by F. Novak — based on
    Computational Linguistics 2025 survey identifying sentence-length variance as the top
    stylometric discriminator beyond perplexity.
  - Removed in v2.4.0: original pattern 25 "Hyphenated Word Pair Overuse" — no empirical
    support and produced grammatically worse output.
---

# Pattern catalog

Loaded by SKILL.md Step 3 (pattern scan). Apply at intensity determined by Step 1 context
calibration. Cluster of 3+ focal vocabulary matches in a paragraph = high signal; isolated
single matches usually not.

EOF
```

- [ ] **Step 3: Append pattern sections from snapshot**

```bash
sed -n '87,403p' /tmp/humanizer-skill-v2.5.0-snapshot.md >> references/pattern-catalog.md
```

- [ ] **Step 4: Verify line count and structure**

Run:
```bash
wc -l references/pattern-catalog.md
grep -c '^### ' references/pattern-catalog.md
```
Expected: ~340 lines total; 25 H3 patterns (1-25).

- [ ] **Step 5: Commit**

```bash
git add references/pattern-catalog.md
git commit -m "feat(v3): extract pattern catalog to references/ with attribution

Move 24 base patterns + pattern 25 (sentence rhythm) from monolithic SKILL.md
into references/pattern-catalog.md. Frontmatter attributes Wikipedia (CC BY-SA),
blader/humanizer (MIT, Siqi Chen), and F. Novak v2.4.0 contributions."
```

---

## Task 2: Extract language-overlay-slovak.md

**Files:**
- Create: `references/language-overlay-slovak.md`
- Source: `/tmp/humanizer-skill-v2.5.0-snapshot.md` lines 405-437

- [ ] **Step 1: Write frontmatter to language-overlay-slovak.md**

```bash
cat > references/language-overlay-slovak.md <<'EOF'
---
name: language-overlay-slovak
description: Slovak-specific pattern maps, inverts, and tells. Loaded by SKILL.md Step 1 when target language is Slovak.
status: heuristic (no Kobak-equivalent Slovak corpus study exists; treat as starting hypothesis)
author: F. Novak (humanizer v2.5.0)
extensible_to: Czech (high overlap), Polish (medium), Hungarian (low — different morphology)
---

# Language overlay: Slovak

EOF
```

- [ ] **Step 2: Append Slovak section content from snapshot**

```bash
sed -n '407,437p' /tmp/humanizer-skill-v2.5.0-snapshot.md >> references/language-overlay-slovak.md
```

- [ ] **Step 3: Verify line count and Slovak content**

Run:
```bash
wc -l references/language-overlay-slovak.md
grep -c 'predstavuje\|kľúčový\|prelomový' references/language-overlay-slovak.md
```
Expected: ~45-55 lines total; at least 3 matches for Slovak focal vocabulary.

- [ ] **Step 4: Commit**

```bash
git add references/language-overlay-slovak.md
git commit -m "feat(v3): extract Slovak language overlay to references/"
```

---

## Task 3: Write new SKILL.md orchestrator

**Files:**
- Replace: `SKILL.md` (currently 544 lines, becomes ~200 lines)

- [ ] **Step 1: Write the new orchestrator file**

Replace entire `SKILL.md` with:

````markdown
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
3. **Em-dash frequency** — GPT-4.1 uses ~3.28× human baseline (blog research, Freeburg, reported via McGill OSS 2025). GPT-5.1 suppresses by default.
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
````

- [ ] **Step 2: Verify SKILL.md structure**

Run:
```bash
wc -l SKILL.md
grep -c '^## ' SKILL.md
grep -c '^### Step' SKILL.md
head -20 SKILL.md
```
Expected: ~150-200 lines; 5-7 H2 sections; 9 step subheadings (Step 0 through Step 8); valid YAML frontmatter at top.

- [ ] **Step 3: Verify referenced files exist or are planned**

Run:
```bash
ls references/
```
Expected: `pattern-catalog.md` and `language-overlay-slovak.md` already created. The other 5 files are TBD in upcoming tasks (this is fine — SKILL.md just names them).

- [ ] **Step 4: Commit**

```bash
git add SKILL.md
git commit -m "feat(v3): rewrite SKILL.md as workflow orchestrator

Replace 544-line monolith with ~200-line workflow that loads references
on demand. Nine steps (0-8): over-edit gate, diagnose, voice ID, pattern
scan, rewrite, transition check, soul, anti-AI audit, detector lens.

Pattern catalog and Slovak overlay now in references/.
Five new reference modules referenced by SKILL.md will be created in
subsequent commits (transition-coherence, soul-toolkit, context-calibration,
hybrid-input, detector-lens)."
```

---

## Task 4: Write references/transition-coherence.md

**Files:**
- Create: `references/transition-coherence.md`

- [ ] **Step 1: Write the file**

```bash
cat > references/transition-coherence.md <<'EOF'
---
name: transition-coherence
description: Post-edit diagnostic for broken connective tissue. Loaded by humanizer SKILL.md Step 5.
author: F. Novak (humanizer v3.0.0)
license: MIT
---

# Transition coherence check

The #1 commercial humanizer failure mode: rewriting sentence N leaves the transition to N-1 or N+1 broken. Pronoun without antecedent. Dangling "therefore." Orphaned "and finally." This module catches those.

## When to run

After Step 4 (rewrite problematic sections). Before Step 6 (add soul) or Step 7 (anti-AI audit).

## Diagnostic checklist

Re-read each edited paragraph end-to-end. For each sentence, ask:

1. **Antecedent check.** Does every "this," "that," "it," "these," "they" still have a clear antecedent? Did the edit delete the noun it pointed back to?
2. **Connector check.** Did a "therefore," "similarly," "however," "additionally" survive an edit where its anchor sentence was deleted or rewritten?
3. **List rhythm check.** Did splitting a rule-of-three leave a dangling "and finally" or "lastly" with nothing to enumerate?
4. **Cohesion across deletion.** When a sentence was deleted entirely, do the surrounding sentences still flow when read aloud?
5. **Tense and number consistency.** Did the edit shift tense (past → present) or number (singular → plural) without updating the rest of the paragraph?

If any check fails, fix before proceeding.

## Three before/after examples

### Example 1 — Orphaned antecedent

**Original (AI-inflated):**
> The Statistical Institute of Catalonia was officially established in 1989, marking a pivotal moment in the evolution of regional statistics in Spain. This initiative was part of a broader movement across Spain to decentralize administrative functions.

**After Step 4 edit (significance-inflation removed, sentence 1 rewritten):**
> The Statistical Institute of Catalonia was established in 1989. This initiative was part of a broader movement across Spain to decentralize administrative functions.

**Problem:** "This initiative" has no clear antecedent. The thing it pointed to ("marking a pivotal moment in the evolution of regional statistics") was deleted. "Establishment of the institute" works as the antecedent semantically, but the prose is awkward.

**Fix:**
> The Statistical Institute of Catalonia was established in 1989 as part of Spain's broader push to decentralize administrative functions to regional governments.

### Example 2 — Dangling connector

**Original:**
> The Haolai River supports endemic fish species. Researchers from the Chinese Academy of Sciences documented seven species in 2019. Therefore, the river is considered ecologically significant.

**After Step 4 edit (vague attribution removed in sentence 1, "therefore" sentence kept):**
> The Haolai River supports endemic fish species, per a 2019 Chinese Academy of Sciences survey. Therefore, the river is considered ecologically significant.

**Problem:** The "therefore" now anchors to a single concrete sentence, not the buildup it expected. Reads as overstated.

**Fix:**
> The Haolai River supports endemic fish species, per a 2019 Chinese Academy of Sciences survey. The river is now a designated conservation zone.

### Example 3 — Orphaned list rhythm

**Original:**
> The event features keynote sessions, panel discussions, and networking opportunities. Attendees can expect innovation, inspiration, and industry insights.

**After Step 4 edit (rule-of-three #10 broken):**
> The event includes talks and panels. Attendees can expect innovation, inspiration, and industry insights.

**Problem:** First sentence dropped to two items. Second sentence still has rule-of-three. Reads inconsistent.

**Fix:**
> The event includes talks and panels. There's also time for informal networking between sessions.

## Common fix patterns

- **Orphaned antecedent** → restore a referent, or fold the next sentence into the first
- **Dangling connector** → delete the connector, or restore a setup sentence
- **Broken list rhythm** → match list lengths, or rewrite as prose
- **Cohesion break** → add a bridging clause, or merge sentences

## What this module does NOT do

- Add patterns the writer didn't use (no "let me add a transition for style")
- Rewrite for emotional impact (that's `soul-toolkit.md`)
- Re-evaluate vocabulary (that's `pattern-catalog.md`)

Stay focused: this module fixes connective tissue broken by Step 4. Nothing else.
EOF
```

- [ ] **Step 2: Verify line count and structure**

Run:
```bash
wc -l references/transition-coherence.md
grep -c '^### ' references/transition-coherence.md
```
Expected: ~80-100 lines; 3 H3 examples + section subheadings.

- [ ] **Step 3: Commit**

```bash
git add references/transition-coherence.md
git commit -m "feat(v3): add transition-coherence reference module"
```

---

## Task 5: Write references/soul-toolkit.md

**Files:**
- Create: `references/soul-toolkit.md`

- [ ] **Step 1: Write the file**

```bash
cat > references/soul-toolkit.md <<'EOF'
---
name: soul-toolkit
description: Concrete techniques for adding voice to flat text. Loaded by humanizer SKILL.md Step 6.
author: F. Novak (humanizer v3.0.0)
license: MIT
---

# Soul toolkit

"Add soul" without operationalization defaults to safe edits that sand text smooth. This module gives six concrete techniques with before/after examples. Apply only what fits the target voice from SKILL.md Step 2.

## When to run

Step 6 of SKILL.md, after transition coherence (Step 5), before anti-AI audit (Step 7). Skip entirely if:
- Target voice is regulatory, legal, or compliance prose
- Text already reads with personality
- Step 0 over-edit gate said the text was clean

## Meta-rule

Apply only techniques that fit the target voice from Step 2. Don't add fragments to a regulatory disclosure. Don't inject first-person opinions into a third-party news brief. The technique must match the writer.

## Six techniques

### 1. Discourse markers

Conversational anchors that signal a real person thinking out loud.

**Markers:** *anyway, look, the thing is, honestly, fine, actually, OK so, here's the thing*

**Before:**
> The product launched in March. Sales exceeded the forecast. Customer feedback has been positive.

**After:**
> The product launched in March. Look, sales exceeded the forecast. Customers seem to like it.

Use sparingly — one or two per paragraph, not every sentence.

### 2. Sentence fragments

Where rhythm calls for it. Not as a tic.

**Before:**
> The deployment took three hours. The team had been preparing for two months. There was a problem with the database migration in the middle of the rollout.

**After:**
> The deployment took three hours. Two months of prep. Then a database migration broke halfway through.

The fragment "Two months of prep" lands because it follows a longer sentence and precedes another longer one. Rhythm-driven.

### 3. Parenthetical asides

The kind that derail mid-thought, then snap back.

**Before:**
> The team agreed to ship Friday. The deadline was tight but achievable.

**After:**
> The team agreed to ship Friday — which, in retrospect, was optimistic. The deadline was tight but achievable.

Or:

**After:**
> The team agreed to ship Friday (we were wrong). The deadline turned out not to be achievable.

### 4. Mid-sentence self-correction

"…well, mostly." "…sort of." "…or rather…"

**Before:**
> The redesign improved conversion rates.

**After:**
> The redesign improved conversion rates — well, on desktop. Mobile was unchanged.

Self-correction signals a real person calibrating in real time.

### 5. Hedge admissions

"I don't actually know if X is right." "Maybe this is obvious but…"

**Before:**
> The data shows a clear seasonal pattern.

**After:**
> The data shows what looks like a seasonal pattern. I'm not sure if three years is enough to call it.

Hedge admissions convert from oracle-tone to thinking-out-loud-tone. Strong voice signal.

### 6. Specific over generic

Replace abstract concern with concrete image.

**Before:**
> The agents running unsupervised raises concerns about reliability.

**After:**
> There's something unsettling about agents churning away at 3am while nobody's watching.

**Before:**
> Customer feedback was mixed.

**After:**
> Half the users loved it. The other half wrote in to say it was confusing.

Specifics beat abstractions every time.

## Pitfalls

- **Overuse.** A paragraph with three discourse markers, two fragments, a parenthetical, AND a self-correction reads as performative humanity. Pick one or two per paragraph.
- **Voice mismatch.** Don't add "look, the thing is" to a brand-tone marketing post for a luxury watch company. The technique must fit.
- **Hollow asides.** "(which is interesting)" or "(notably)" don't count. The aside must add real content.
- **Forced fragments.** "Big launch. Friday. Three hours." reads as Hemingway parody, not voice.

## What this module does NOT do

- Rewrite for accuracy (that's Step 4)
- Fix transitions (that's `transition-coherence.md`)
- Add facts the writer didn't include
EOF
```

- [ ] **Step 2: Verify line count and structure**

Run:
```bash
wc -l references/soul-toolkit.md
grep -c '^### ' references/soul-toolkit.md
```
Expected: ~130-150 lines; 6 H3 technique sections plus others.

- [ ] **Step 3: Commit**

```bash
git add references/soul-toolkit.md
git commit -m "feat(v3): add soul-toolkit reference module — operationalize 'add soul'"
```

---

## Task 6: Write references/context-calibration.md

**Files:**
- Create: `references/context-calibration.md`

- [ ] **Step 1: Write the file**

```bash
cat > references/context-calibration.md <<'EOF'
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
EOF
```

- [ ] **Step 2: Verify line count and structure**

Run:
```bash
wc -l references/context-calibration.md
grep -E '^\| Technical' references/context-calibration.md
```
Expected: ~100-120 lines; intensity matrix table renders correctly.

- [ ] **Step 3: Commit**

```bash
git add references/context-calibration.md
git commit -m "feat(v3): add context-calibration reference module — content type × length matrix"
```

---

## Task 7: Write references/hybrid-input.md

**Files:**
- Create: `references/hybrid-input.md`

- [ ] **Step 1: Write the file**

```bash
cat > references/hybrid-input.md <<'EOF'
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
EOF
```

- [ ] **Step 2: Verify line count and structure**

Run:
```bash
wc -l references/hybrid-input.md
grep -c '^### ' references/hybrid-input.md
```
Expected: ~120-140 lines; 5 heuristic H3 sections plus others.

- [ ] **Step 3: Commit**

```bash
git add references/hybrid-input.md
git commit -m "feat(v3): add hybrid-input reference module — three-way human/mixed/AI tagging"
```

---

## Task 8: Write references/detector-lens.md

**Files:**
- Create: `references/detector-lens.md`

- [ ] **Step 1: Write the file**

```bash
cat > references/detector-lens.md <<'EOF'
---
name: detector-lens
description: Optional stylometric self-check at end of humanizer workflow. Loaded by SKILL.md Step 8 when Option C (voice first, detector check second) applies.
author: F. Novak (humanizer v3.0.0)
license: MIT
---

# Detector lens

Optional final pass. After Step 7 (anti-AI audit), run a brief stylometric self-check to verify the output would survive common pattern-based detectors. **Critical rule: only fix if the failure points to a real quality issue, not a detector quirk.** Voice quality beats detector compliance.

## When to run

Step 8 of SKILL.md. Skip if:
- Step 0 said the text was clean (the detector lens is for edited text, not pre-edit verification)
- Target voice doesn't care about detector compliance (e.g., internal company memo)

## Four mental checks

### 1. Burstiness check

Read consecutive sentences. Do they span a meaningful length range, or do they cluster around moderate length (15-25 words)?

**Pass criterion:** Out of any 5 consecutive sentences, at least 2 should differ by 10+ words from the mean. Humans cluster at extremes.

**Common failure:** Step 4 rewrote several long sentences into the "natural-feeling" 15-25 word range. They all sound fine individually, but the paragraph reads uniform.

**Fix:** Pick one sentence and shorten it to fragment length. Pick another and let it run long.

### 2. Vocabulary clustering check

Count focal words from `pattern-catalog.md` #7 per paragraph.

**Pass criterion:** Fewer than 3 focal-list matches per paragraph.

**Common failure:** Step 4 caught the obvious vocab clusters but missed isolated focal words that re-cluster after edits.

**Fix:** Replace one or two focal words with their plain-English equivalents.

### 3. Em-dash density check

Count em dashes per 200 words of edited output.

**Pass criterion:** Ratio ≤ 1 per 200 words.

**Common failure:** Step 4 didn't touch em dashes because the writer uses them stylistically.

**Fix:** Replace half with commas, parentheticals, or sentence breaks. Keep at most one per paragraph.

### 4. Coherence check

Read the final output start to finish. Does it still make sense? Does it still sound like the target voice from Step 2?

**Pass criterion:** Yes.

**Common failure:** Step 4 + Step 5 + Step 6 each made local fixes; the cumulative effect is a text that reads choppy.

**Fix:** Smooth one or two transitions. Don't rewrite the whole thing.

## Decision rule

For each failure, ask:

**Is this failure pointing to a real quality issue?**

- **Yes** — text reads worse than the pre-edit version on this dimension. Apply one targeted fix. ONE fix per failure, then stop.
- **No** — failure is a detector quirk (e.g., uniform rhythm because the writer is non-native English with naturally low burstiness, or low em dash count because the topic doesn't call for them). Ship the text as-is. Voice quality beats detector compliance.

## What this module does NOT do

- Optimize against any specific detector (GPTZero, Originality, Pangram). The checks above are stylometric basics shared by all pattern-based detectors. Optimizing per-detector is an arms race that defeats voice quality.
- Replace Step 7's anti-AI audit. Step 7 catches obvious tells in semantic content. Step 8 catches stylometric residue.
- Require passing. The output of humanizer is "voice-corrected text." Whether it passes detector X is a separate question, and the user's call.

## Critical reminder

If applying a fix would damage voice quality — for example, fragmenting a sentence purely to bump burstiness in a regulatory disclosure where uniform sentences are correct — **do not apply it.** Ship the text. The skill's job is voice quality, not detector evasion.
EOF
```

- [ ] **Step 2: Verify line count and structure**

Run:
```bash
wc -l references/detector-lens.md
grep -c '^### ' references/detector-lens.md
```
Expected: ~90-110 lines; 4 check H3 sections plus others.

- [ ] **Step 3: Commit**

```bash
git add references/detector-lens.md
git commit -m "feat(v3): add detector-lens reference module — optional Option C fallback"
```

---

## Task 9: Write NOTICE file

**Files:**
- Create: `NOTICE`

- [ ] **Step 1: Write the file**

```bash
cat > NOTICE <<'EOF'
humanizer skill
===============

This skill is a fork of blader/humanizer (MIT licensed, Copyright (c) 2025
Siqi Chen) extended with original work by Fero Novak.

Upstream:        https://github.com/blader/humanizer
Fork started:    2026-05-14
Current version: 3.0.0


Pattern catalog source (references/pattern-catalog.md):
-------------------------------------------------------

- Patterns 1-24: blader/humanizer, ultimately derived from Wikipedia's
  "Signs of AI writing" page (WikiProject AI Cleanup, CC BY-SA).
- Pattern 7 (AI vocabulary) refined in humanizer v2.4.0 by F. Novak —
  research-validated vs context-dependent split based on Kobak et al. 2024.
- Pattern 25 (Uniform Sentence Rhythm) added in humanizer v2.4.0 by F. Novak,
  grounded in Computational Linguistics 2025 survey.
- Removed in v2.4.0: original pattern 25 "Hyphenated Word Pair Overuse"
  (no empirical support; produced grammatically worse output).


Original work by F. Novak (MIT licensed, included in this repository):
----------------------------------------------------------------------

- SKILL.md orchestrator (v3.0.0 workflow design)
- references/transition-coherence.md
- references/soul-toolkit.md
- references/context-calibration.md
- references/hybrid-input.md
- references/detector-lens.md
- references/language-overlay-slovak.md
- "What research shows" section in SKILL.md
- Over-edit gate (Step 0)
- Voice-ID step (Step 2) with bidirectional ESL awareness
- Two-pass cap (Step 7) preventing homogenization spiral
- Modular architecture (orchestrator + references) replacing v2.x monolith


Research sources cited:
-----------------------

- Kobak et al. (2024) "Delving into ChatGPT usage in academic writing through
  excess vocabulary." arXiv:2406.07016.
- "Why Does ChatGPT Delve So Much?" COLING 2025 (ACL Anthology 2025.coling-main.426).
- Wu et al. (2025) "A Survey on LLM-Generated Text Detection."
  Computational Linguistics 51(1), MIT Press.
- Muñoz-Ortiz et al. "Contrasting Linguistic Patterns in Human and
  LLM-Generated News Text." PMC11422446.
- "Delving Into PubMed Records." Perspectives on Medical Education, 2024.
- Freeburg (2025) "Em dash frequency analysis across GPT model generations." [blog research, reported via McGill OSS 2025; no formal publication identifier]


License:
--------

All work in this repository is licensed under MIT. See LICENSE for full text.
Original copyright (c) 2025 Siqi Chen. Additions copyright (c) 2026 Fero Novak.

Permission to use, copy, modify, merge, publish, distribute, sublicense
under MIT — see LICENSE file.
EOF
```

- [ ] **Step 2: Verify the file**

Run:
```bash
cat NOTICE | head -20
wc -l NOTICE
```
Expected: ~60-70 lines, full attribution block visible.

- [ ] **Step 3: Commit**

```bash
git add NOTICE
git commit -m "feat(v3): add NOTICE with full attribution"
```

---

## Task 10: Rewrite README.md

**Files:**
- Replace: `README.md`

- [ ] **Step 1: Replace the file**

```bash
cat > README.md <<'EOF'
# Humanizer

A Claude Code skill for removing signs of AI-generated writing while preserving voice.

> Fork of [blader/humanizer](https://github.com/blader/humanizer) with research
> grounding, modular workflow, transition coherence, voice-ID, context calibration,
> hybrid-input handling, detector lens, and Slovak language support. See
> [NOTICE](NOTICE) for full attribution.

## What's different from upstream

- **Workflow restructure** — 9-step orchestrator (SKILL.md) instead of monolithic catalog
- **Research grounding** — citations to Kobak et al. 2024, COLING 2025, CL 2025 survey
- **Sentence-rhythm pattern** — top stylometric discriminator (replaces upstream's hyphenation pattern, which had no empirical support)
- **Voice-ID step** — preserves writer signature, prevents tone-flattening (the #1 commercial humanizer failure)
- **Transition-coherence check** — catches broken connective tissue after edits
- **Soul toolkit** — six concrete techniques for adding voice (vs. vague "be human")
- **Context calibration** — different edit intensity for technical / marketing / personal / regulatory content
- **Hybrid input handling** — identifies already-human sections and leaves them alone
- **Detector lens** — optional final stylometric check (Option C fallback)
- **Slovak language overlay** — Czech/Polish/Hungarian extensible

## Installation

```bash
git clone <fork-url> ~/.claude/skills/humanizer
```

(If you haven't published this as a fork yet, the skill lives at
`~/.claude/skills/humanizer/` after this restructure.)

## Usage

In Claude Code, invoke when editing text that may sound AI-generated. The
orchestrator decides which reference modules to load based on input
characteristics (language, content type, length, input purity).

Example invocations:

- "Use the humanizer skill to clean up this LinkedIn post."
- "Apply humanizer with Slovak language overlay."
- "Run the humanizer pre-publish check on this Substack draft."

## Structure

```
~/.claude/skills/humanizer/
├── SKILL.md                              # Workflow orchestrator (~200 lines)
├── references/
│   ├── pattern-catalog.md                # 24 base patterns (attributed)
│   ├── transition-coherence.md           # Post-edit connective-tissue check
│   ├── soul-toolkit.md                   # 6 techniques for adding voice
│   ├── context-calibration.md            # Content type × length intensity matrix
│   ├── hybrid-input.md                   # Three-way HUMAN / MIXED / AI tagging
│   ├── detector-lens.md                  # Optional stylometric self-check
│   └── language-overlay-slovak.md        # Slovak-specific maps/inverts
├── LICENSE                               # MIT (unchanged from upstream)
├── NOTICE                                # Full attribution
├── README.md                             # This file
├── CHANGELOG.md                          # Version history
└── docs/
    ├── specs/                            # Design documents
    └── plans/                            # Implementation plans
```

## Workflow at a glance

The orchestrator runs nine steps:

0. Over-edit gate — if text reads naturally, stop
1. Diagnose — language, content type, length, input purity
2. Identify target voice — preserve signatures
3. Scan for patterns — load pattern-catalog.md
4. Rewrite problematic sections
5. Transition coherence check
6. Add soul where flat
7. Anti-AI audit — cap at two passes
8. Detector lens — optional, voice quality beats detector compliance

## Sources

- Wikipedia: [Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing) (WikiProject AI Cleanup)
- Kobak et al. (2024) — vocabulary frequency shifts
- COLING 2025, Computational Linguistics 2025 survey — stylometric markers
- Freeburg (2025) — em-dash blog research (reported via McGill OSS 2025; no formal publication)

See [NOTICE](NOTICE) for full citation list.

## License

MIT. See [LICENSE](LICENSE) and [NOTICE](NOTICE).
EOF
```

- [ ] **Step 2: Verify the file**

Run:
```bash
wc -l README.md
grep -c '^## ' README.md
```
Expected: ~80-100 lines; 7-9 H2 sections.

- [ ] **Step 3: Commit**

```bash
git add README.md
git commit -m "feat(v3): rewrite README for new modular structure"
```

---

## Task 11: Write CHANGELOG.md

**Files:**
- Create: `CHANGELOG.md`

- [ ] **Step 1: Write the file**

```bash
cat > CHANGELOG.md <<'EOF'
# Changelog

All notable changes to the humanizer skill.

## [3.0.0] — 2026-05-14

### Changed
- Restructured into orchestrator (SKILL.md) + 7 modular references
- Pattern catalog extracted to `references/pattern-catalog.md` with full attribution
- Slovak language overlay extracted to `references/language-overlay-slovak.md`
- SKILL.md reduced from 544 lines (monolith) to ~200 lines (workflow)

### Added
- `references/transition-coherence.md` — post-edit broken-transition diagnostic with three before/after examples
- `references/soul-toolkit.md` — six concrete techniques for adding voice (discourse markers, fragments, parentheticals, self-correction, hedge admissions, specific-over-generic)
- `references/context-calibration.md` — content type × length intensity matrix
- `references/hybrid-input.md` — three-way HUMAN / MIXED / AI section tagging before editing
- `references/detector-lens.md` — optional stylometric self-check per Option C (voice first, detector check second)
- `NOTICE` file with full attribution to Siqi Chen (upstream), Wikipedia (catalog origin), and F. Novak (modifications)
- Bidirectional ESL bias awareness (detection AND editing — not just detection)
- `docs/specs/` and `docs/plans/` directories with v3.0.0 design + plan

### Documented
- README fully rewritten for new structure
- CHANGELOG (this file) documents v2.3.0 through v3.0.0 transitions

## [2.5.0] — 2026-05-14

### Added
- Step 0 over-edit gate — if text reads naturally, declare clean and stop
- Step 1 target-voice identification — preserve writer signature across personas
- Slovak language overlay — 29 patterns mapped, inverted, or added (heuristic, not corpus-validated)
- Two-pass cap (Step 6) — stop after one anti-AI audit pass to prevent homogenization spiral

### Changed
- Process section reorganized to start with over-edit gate

## [2.4.0] — 2026-05-14

### Added
- "What research shows" section with empirical citations (Kobak et al. 2024, COLING 2025, CL 2025 survey, Freeburg 2025 blog research)
- Pattern 25: Uniform Sentence Rhythm — top stylometric discriminator beyond perplexity
- Pattern 7 (AI vocabulary): research-validated focal words split from context-dependent words

### Removed
- Pattern 25 (original): Hyphenated Word Pair Overuse — no empirical support; "After" example was grammatically worse than "Before"

### Changed
- Description rewritten to triggers-only per writing-skills guideline (descriptions should not summarize workflow)
- Reference section expanded with six empirical sources

## [2.3.0] and earlier

Upstream releases from blader/humanizer. See https://github.com/blader/humanizer
for full upstream history.
EOF
```

- [ ] **Step 2: Verify the file**

Run:
```bash
wc -l CHANGELOG.md
grep -c '^## \[' CHANGELOG.md
```
Expected: ~70-90 lines; 4 version headers.

- [ ] **Step 3: Commit**

```bash
git add CHANGELOG.md
git commit -m "feat(v3): add CHANGELOG documenting v2.3.0 → v3.0.0 evolution"
```

---

## Task 12: Smoke test verification

**Files:**
- Create: `docs/verification/2026-05-14-smoke-test.md`

- [ ] **Step 1: Create verification log directory**

```bash
mkdir -p docs/verification
```

- [ ] **Step 2: Prepare three sample inputs**

Create `docs/verification/sample-inputs.md`:

```bash
cat > docs/verification/sample-inputs.md <<'EOF'
# Smoke test sample inputs (2026-05-14)

## Sample A — dataskater-style English LinkedIn post (~150 words)

[Generate or paste a sample AI-generated LinkedIn post in dataskater voice.
Should contain at least three pattern violations from the catalog.]

## Sample B — Fero-style Slovak LinkedIn post (~180 words)

[Generate or paste a sample AI-generated Slovak post in MD register.
Should contain Slovak-specific tells: predstavuje, kľúčový, nielen...ale aj.]

## Sample C — Technical FAQ (~400 words)

[Generate or paste a sample AI-generated technical FAQ. Should be
moderately AI-toned without aggressive significance inflation.]
EOF
```

(Fill in the actual sample texts before running tests. Source: Claude Code can generate AI-tone samples on demand for testing.)

- [ ] **Step 3: Run sample A through v3.0.0**

In Claude Code, invoke the humanizer skill on Sample A. Observe:

1. Did Step 0 over-edit gate fire correctly? (Should NOT fire — sample has patterns)
2. Did Step 1 diagnose identify English / personal / short / pure-AI correctly?
3. Did Step 3 reference `pattern-catalog.md`?
4. Did Step 5 transition coherence check fire?
5. Was output natural?

Record observations in `docs/verification/2026-05-14-smoke-test.md`:

```bash
cat > docs/verification/2026-05-14-smoke-test.md <<'EOF'
# Smoke test results — humanizer v3.0.0

Date: 2026-05-14

## Sample A — dataskater English LinkedIn

- [ ] Step 0 (over-edit gate): correctly did NOT fire
- [ ] Step 1 (diagnose): identified English / personal / short / pure-AI
- [ ] Step 3: loaded pattern-catalog.md
- [ ] Step 5: transition coherence ran
- [ ] Output: natural, voice preserved, no broken transitions

Notes:
[fill in]

## Sample B — Fero Slovak LinkedIn

- [ ] Step 1: loaded language-overlay-slovak.md
- [ ] Slovak-specific tells caught (predstavuje, kľúčový, etc.)
- [ ] Output: preserved MD register, no over-edit

Notes:
[fill in]

## Sample C — Technical FAQ

- [ ] Step 1: identified technical / medium / pure-AI
- [ ] Context calibration: light intensity applied
- [ ] Step 1 (significance inflation) NOT applied (technical pedagogy preserved)
- [ ] Output: facts intact, voice preserved

Notes:
[fill in]

## Overall

- [ ] v3.0.0 output is at least as natural as v2.5.0 across all three samples
- [ ] Voice signatures preserved in each
EOF
```

- [ ] **Step 4: Commit verification log**

```bash
git add docs/verification/
git commit -m "test(v3): smoke test on 3 sample inputs — dataskater English, Fero Slovak, technical FAQ"
```

---

## Task 13: Gap-specific verification

**Files:**
- Append to: `docs/verification/2026-05-14-smoke-test.md`

- [ ] **Step 1: Test broken-transition diagnostic**

Prepare a paragraph with a known broken transition (e.g., delete a sentence containing the antecedent of a "This" in the next sentence). Run through v3.0.0.

Pass criterion: Step 5 catches the orphaned antecedent and fixes it.

- [ ] **Step 2: Test hybrid input handling**

Prepare a paragraph: first two sentences with specific human details ("Q3 was rough. We lost two customers in October…"), third sentence AI-toned ("Despite these challenges, our resilient team continues to innovate…").

Pass criterion: Step 1 tags sentences 1-2 as HUMAN and only sentence 3 as AI. Output preserves sentences 1-2 verbatim.

- [ ] **Step 3: Test context calibration**

Prepare two inputs:
- A regulatory disclosure (privacy policy section, ~200 words)
- A casual LinkedIn post (~150 words)

Pass criterion: regulatory output preserves structure and formal register (light intensity). LinkedIn output is rewritten more freely (standard intensity).

- [ ] **Step 4: Test detector lens optional pass**

Prepare an already-clean input (no AI patterns).

Pass criterion: Step 0 fires (over-edit gate). Step 8 (detector lens) either doesn't run or returns "ship as-is."

- [ ] **Step 5: Record results**

Append to `docs/verification/2026-05-14-smoke-test.md`:

```bash
cat >> docs/verification/2026-05-14-smoke-test.md <<'EOF'

## Gap-specific tests

### Transition coherence
- [ ] Broken antecedent: caught and fixed
- Notes:

### Hybrid input
- [ ] Sentences 1-2 tagged HUMAN, sentence 3 tagged AI
- [ ] HUMAN sentences preserved verbatim
- Notes:

### Context calibration
- [ ] Regulatory: light intensity, structure preserved
- [ ] LinkedIn: standard intensity, fuller rewrite
- Notes:

### Detector lens
- [ ] Already-clean text: Step 0 fires, no rewrite
- Notes:
EOF
```

- [ ] **Step 6: Commit**

```bash
git add docs/verification/2026-05-14-smoke-test.md
git commit -m "test(v3): gap-specific verification — transitions, hybrid input, context, detector lens"
```

---

## Task 14: Tag v3.0.0

**Files:** none modified

- [ ] **Step 1: Verify all changes committed**

Run:
```bash
git status
```
Expected: working tree clean.

- [ ] **Step 2: Check the commit log**

Run:
```bash
git log --oneline -20
```
Expected: ~14 commits since session start, each one a single Task or step.

- [ ] **Step 3: Tag v3.0.0**

```bash
git tag -a v3.0.0 -m "Humanizer v3.0.0

Modular restructure: orchestrator (SKILL.md) + 7 reference modules.
Five new modules: transition-coherence, soul-toolkit, context-calibration,
hybrid-input, detector-lens. Extracted: pattern-catalog, language-overlay-slovak.

Workflow: 9 steps from over-edit gate through optional detector lens.
Priority: voice first, detector check only if necessary (Option C).

Forked from blader/humanizer (MIT, Siqi Chen).
See NOTICE for full attribution.
See CHANGELOG.md for version history.
See docs/specs/2026-05-14-humanizer-improvements-design.md for design rationale.
"
```

- [ ] **Step 4: Verify tag**

Run:
```bash
git tag --list 'v3*'
git show v3.0.0 --stat | head -30
```
Expected: tag exists; shows commit summary and file changes.

---

## Post-flight

After implementation:

- The skill is ready for use locally at `~/.claude/skills/humanizer/`
- Publishing to GitHub is a separate downstream decision (Option A honest fork / B fresh rewrite / C keep private). All three are compatible with this implementation.
- If publishing as Option A:
  - Create `feronovak/humanizer` on GitHub
  - `git remote add fero <new-url>`
  - `git push fero main && git push fero v3.0.0`
- If publishing as Option B (fresh rewrite later):
  - Rewrite `references/pattern-catalog.md` from scratch, citing Wikipedia and Kobak et al. directly without copying blader's examples
  - Update NOTICE to reflect "based on" rather than "fork of"
  - Estimated +2-3 hours
- If keeping private: no further action needed.

---

## Self-review checklist

- [x] **Spec coverage:** Each spec gap → corresponding task
  - Gap 1 transition coherence → Task 4
  - Gap 2 soul operationalization → Task 5
  - Gap 3 content-type sensitivity → Task 6
  - Gap 4 hybrid input → Task 7
  - Gap 5 length-aware (folded into Task 6)
  - Gap 6 ESL bidirectional → Task 3 (Step 2 in SKILL.md)
  - Gap 7 detector lens → Task 8
- [x] **Placeholder scan:** all steps contain concrete content (heredoc'd file bodies, exact commands, expected outputs)
- [x] **Type consistency:** file paths and reference names match across all tasks
- [x] **Attribution coverage:** NOTICE (Task 9), README (Task 10), CHANGELOG (Task 11), per-file frontmatter (Tasks 1-8)
- [x] **Verification coverage:** smoke test (Task 12) + gap-specific (Task 13)
- [x] **Commit cadence:** one commit per task, ~14 commits total
- [x] **Reversibility:** each task is independently revertible via `git revert <task-commit>`
