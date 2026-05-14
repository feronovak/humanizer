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
