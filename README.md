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
- Freeburg (2025) — em-dash empirical analysis

See [NOTICE](NOTICE) for full citation list.

## License

MIT. See [LICENSE](LICENSE) and [NOTICE](NOTICE).
