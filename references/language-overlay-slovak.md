---
name: language-overlay-slovak
description: Slovak-specific pattern maps, inverts, and tells. Loaded by SKILL.md Step 1 when target language is Slovak.
status: heuristic (no Kobak-equivalent Slovak corpus study exists; treat as starting hypothesis)
author: F. Novak (humanizer v2.5.0)
extensible_to: Czech (high overlap), Polish (medium), Hungarian (low — different morphology)
---

# Language overlay: Slovak

The default catalog is English-tuned. For Slovak text, apply with these adjustments. *(Heuristic, not corpus-validated — no equivalent of Kobak et al. exists for Slovak yet. Treat as starting hypotheses, not verdicts.)*

**Maps directly:**

- **#1 Significance inflation** — Slovak forms: *predstavuje míľnik, znamená zlom, kľúčový moment, prelomový, výrazne formuje, neoddeliteľná súčasť, zohráva dôležitú úlohu*
- **#3 Superficial -ing analyses** — Slovak transgressives: *zdôrazňujúc, ukazujúc, podčiarkujúc, odrážajúc, prispievajúc k*
- **#4 Promotional language** — *pôvabný, prekrásny, jedinečný, mimoriadny, neúnavný, fascinujúci, výnimočný*
- **#7 Vocabulary tells** (Slovak equivalents): *vyzdvihuje, podčiarkuje, predstavuje, tvorí, neúnavný, mimoriadny, kľúčový, prelomový, fascinujúci, neoceniteľný, nenahraditeľný*
- **#8 Copula avoidance** — Slovak version replaces *je/sú* with *predstavuje, slúži ako, tvorí, znamená, zastáva úlohu*
- **#9 Negative parallelisms** — *Nielen že..., ale aj...; Nie je to len o..., je to o...*
- **#10 Rule of three** — maps directly
- **#19 Chatbot artifacts** — *Dúfam, že to pomôže!, Samozrejme!, Určite!, Rád pomôžem!, Ak by ste potrebovali viac...*
- **#22 Filler phrases** — *Je dôležité poznamenať, že...; V dnešnej dobe...; V neposlednom rade...; S cieľom dosiahnuť...*
- **#24 Generic positive conclusions** — *Budúcnosť vyzerá sľubne; Pred nami sú vzrušujúce časy; Pokračujeme v ceste za excelentnosťou*

**Does not map / inverts:**

- **#16 Title case in headings** — Slovak uses sentence case by default. AI-generated title case (*"Strategické Rokovania A Globálne Partnerstvá"*) is even more obvious as a tell than in English.
- **#18 Curly quotes** — Slovak convention is „bottom-top" („...") not "...". Straight quotes are the AI tell here, not curly ones.
- **#13 Em dashes** — used in Slovak (pomlčka) but historically less frequently than English. Same overuse pattern applies; trigger threshold is lower.

**Slovak-specific tells (not in main catalog):**

- **Conjunction overload at paragraph starts** — *Navyše, Zároveň, Okrem toho, Taktiež, Avšak* opening every other paragraph
- **Genitive noun stacking** — *transformácia digitalizácie procesov modernizácie verejnej správy* (chain of four+ genitives = AI signature)
- **Passive-overuse with *byť* + past participle** — *bolo zaznamenané, bolo zistené, bolo poukázané na...* in contexts where active voice fits
- **Anglicism leak in formal text** — *implementácia, optimalizácia, leverage-ovať, deliverable* where native Slovak verbs exist (*zaviesť, vyladiť, využiť*)

**For Czech / Polish / Hungarian:** the Slovak overlay is a reasonable starting hypothesis for Czech (similar morphology, same Central European register tells). Polish and Hungarian need their own work — copula behavior, case systems, and conjunction patterns differ enough that direct mapping will misfire.

---
