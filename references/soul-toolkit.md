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
