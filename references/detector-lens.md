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
