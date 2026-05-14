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
