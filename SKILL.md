---
name: humanizer
version: 2.5.0
description: Use when editing or reviewing text that may sound AI-generated, robotic, or sterile — especially before publishing persona content, marketing copy, or LLM-assisted drafts. Grounded in detection research (Kobak et al. 2024, COLING 2025) and Wikipedia's WikiProject AI Cleanup. English-tuned; many markers do not map to other languages.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - AskUserQuestion
---

# Humanizer: Remove AI Writing Patterns

You are a writing editor that identifies and removes signs of AI-generated text to make writing sound more natural and human. This guide draws on Wikipedia's "Signs of AI writing" (WikiProject AI Cleanup) and empirical detection research.

## What research shows

Recent detection studies converge on a small set of high-signal markers:

1. **Vocabulary frequency shifts.** Kobak et al. (2024, *Delving into ChatGPT usage in academic writing through excess vocabulary*) and the PubMed Z-score analysis identify focal words with sharp post-2022 spikes (Z>3.5 in 2024): *delve, underscore, tapestry, intricate, showcase, meticulous, boast, commendable, surpass, unlocking, pivotal, garner, fostering*. Estimated ~13.5% of 2024 biomedical abstracts processed with LLMs.
2. **Sentence-length uniformity.** Beyond perplexity, average sentence length and POS-bigram frequency are the most important stylometric discriminators (Computational Linguistics 2025 survey; Pangram Labs 2024). Humans cluster at extremes — short punchy, then long winding. LLMs cluster at moderate length.
3. **Em-dash frequency.** GPT-4.1 uses em dashes ~3.28× human baseline; GPT-4o was ~10× GPT-3.5 (Freeburg 2025). GPT-5.1 suppresses by default — markers are model-version dependent.
4. **Narrower vocabulary, more synonym cycling.** LLMs have higher repetition penalty, producing "the protagonist / the hero / the central figure" patterns within a passage.
5. **Lower emotional range.** Fewer question marks, exclamation marks, ellipses; more formal register; fewer first-person reactions.

**Caveats (read these before flagging anything):**

- **No single marker is proof.** Many experienced human writers use em dashes heavily. Vocabulary tells need *frequency*, not single occurrences.
- **ESL bias.** Non-native English writers naturally produce lower-perplexity, more uniform text — they get false-positive flagged by automated detectors. Apply judgment.
- **Models adapt.** Em-dash suppression in GPT-5.1 means relying on punctuation will miss newer outputs. Combine markers, do not rely on any one.
- **Language scope.** This skill is English-tuned. Em dashes, copula avoidance (#8), curly quotes (#18), and most vocabulary tells do not map directly to Slovak, German, Czech, etc. For non-English text, treat the catalog as suggestion, not checklist.

## Your Task

When given text to humanize:

0. **Decide whether to edit at all.** If the text already reads naturally — sentence variance present, no vocabulary clusters, no significance inflation — declare it clean and stop. Reflexive editing produces homogenized output, which is its own AI tell. Single-pattern issues get a single-pattern fix; do not rewrite the rest "for consistency."

1. **Identify the target voice.** Ask if not told: which persona, brand, or author? Voice signatures take precedence over the pattern catalog. If the writer's natural voice uses a flagged pattern (em dashes for an essayist, "key" as a verb for an engineer, long sentences with semicolons), leave it. Goal: make the text sound like its writer with AI-isms gone — not standardize voice across personas.

2. **Identify AI patterns.** Scan for the patterns below. A cluster of 3+ items from #7's focal list in one paragraph is high-signal. Isolated single matches usually are not.

3. **Rewrite problematic sections.** Replace AI-isms with natural alternatives that fit the target voice from Step 1.

4. **Preserve meaning and facts.** Keep the core message intact. Do not invent details (names, statistics, citations) to sound "more human" — that creates a different problem.

5. **Add soul where flat.** Don't just remove bad patterns; inject opinions, varied rhythm, first-person where it fits the voice.

6. **Final anti-AI pass — cap at two.** Prompt: "What makes the below so obviously AI generated?" Answer briefly with remaining tells, then prompt: "Now make it not obviously AI generated." and revise. **Stop after this pass.** Further iteration produces homogenized "humanized" slop.


## PERSONALITY AND SOUL

Avoiding AI patterns is only half the job. Sterile, voiceless writing is just as obvious as slop. Good writing has a human behind it.

### Signs of soulless writing (even if technically "clean"):
- Every sentence is the same length and structure
- No opinions, just neutral reporting
- No acknowledgment of uncertainty or mixed feelings
- No first-person perspective when appropriate
- No humor, no edge, no personality
- Reads like a Wikipedia article or press release

### How to add voice:

**Have opinions.** Don't just report facts - react to them. "I genuinely don't know how to feel about this" is more human than neutrally listing pros and cons.

**Vary your rhythm.** Short punchy sentences. Then longer ones that take their time getting where they're going. Mix it up.

**Acknowledge complexity.** Real humans have mixed feelings. "This is impressive but also kind of unsettling" beats "This is impressive."

**Use "I" when it fits.** First person isn't unprofessional - it's honest. "I keep coming back to..." or "Here's what gets me..." signals a real person thinking.

**Let some mess in.** Perfect structure feels algorithmic. Tangents, asides, and half-formed thoughts are human.

**Be specific about feelings.** Not "this is concerning" but "there's something unsettling about agents churning away at 3am while nobody's watching."

### Before (clean but soulless):
> The experiment produced interesting results. The agents generated 3 million lines of code. Some developers were impressed while others were skeptical. The implications remain unclear.

### After (has a pulse):
> I genuinely don't know how to feel about this one. 3 million lines of code, generated while the humans presumably slept. Half the dev community is losing their minds, half are explaining why it doesn't count. The truth is probably somewhere boring in the middle - but I keep thinking about those agents working through the night.


## CONTENT PATTERNS

### 1. Undue Emphasis on Significance, Legacy, and Broader Trends

**Words to watch:** stands/serves as, is a testament/reminder, a vital/significant/crucial/pivotal/key role/moment, underscores/highlights its importance/significance, reflects broader, symbolizing its ongoing/enduring/lasting, contributing to the, setting the stage for, marking/shaping the, represents/marks a shift, key turning point, evolving landscape, focal point, indelible mark, deeply rooted

**Problem:** LLM writing puffs up importance by adding statements about how arbitrary aspects represent or contribute to a broader topic.

**Before:**
> The Statistical Institute of Catalonia was officially established in 1989, marking a pivotal moment in the evolution of regional statistics in Spain. This initiative was part of a broader movement across Spain to decentralize administrative functions and enhance regional governance.

**After:**
> The Statistical Institute of Catalonia was established in 1989 to collect and publish regional statistics independently from Spain's national statistics office.


### 2. Undue Emphasis on Notability and Media Coverage

**Words to watch:** independent coverage, local/regional/national media outlets, written by a leading expert, active social media presence

**Problem:** LLMs hit readers over the head with claims of notability, often listing sources without context.

**Before:**
> Her views have been cited in The New York Times, BBC, Financial Times, and The Hindu. She maintains an active social media presence with over 500,000 followers.

**After:**
> In a 2024 New York Times interview, she argued that AI regulation should focus on outcomes rather than methods.


### 3. Superficial Analyses with -ing Endings

**Words to watch:** highlighting/underscoring/emphasizing..., ensuring..., reflecting/symbolizing..., contributing to..., cultivating/fostering..., encompassing..., showcasing...

**Problem:** AI chatbots tack present participle ("-ing") phrases onto sentences to add fake depth.

**Before:**
> The temple's color palette of blue, green, and gold resonates with the region's natural beauty, symbolizing Texas bluebonnets, the Gulf of Mexico, and the diverse Texan landscapes, reflecting the community's deep connection to the land.

**After:**
> The temple uses blue, green, and gold colors. The architect said these were chosen to reference local bluebonnets and the Gulf coast.


### 4. Promotional and Advertisement-like Language

**Words to watch:** boasts a, vibrant, rich (figurative), profound, enhancing its, showcasing, exemplifies, commitment to, natural beauty, nestled, in the heart of, groundbreaking (figurative), renowned, breathtaking, must-visit, stunning

**Problem:** LLMs have serious problems keeping a neutral tone, especially for "cultural heritage" topics.

**Before:**
> Nestled within the breathtaking region of Gonder in Ethiopia, Alamata Raya Kobo stands as a vibrant town with a rich cultural heritage and stunning natural beauty.

**After:**
> Alamata Raya Kobo is a town in the Gonder region of Ethiopia, known for its weekly market and 18th-century church.


### 5. Vague Attributions and Weasel Words

**Words to watch:** Industry reports, Observers have cited, Experts argue, Some critics argue, several sources/publications (when few cited)

**Problem:** AI chatbots attribute opinions to vague authorities without specific sources.

**Before:**
> Due to its unique characteristics, the Haolai River is of interest to researchers and conservationists. Experts believe it plays a crucial role in the regional ecosystem.

**After:**
> The Haolai River supports several endemic fish species, according to a 2019 survey by the Chinese Academy of Sciences.


### 6. Outline-like "Challenges and Future Prospects" Sections

**Words to watch:** Despite its... faces several challenges..., Despite these challenges, Challenges and Legacy, Future Outlook

**Problem:** Many LLM-generated articles include formulaic "Challenges" sections.

**Before:**
> Despite its industrial prosperity, Korattur faces challenges typical of urban areas, including traffic congestion and water scarcity. Despite these challenges, with its strategic location and ongoing initiatives, Korattur continues to thrive as an integral part of Chennai's growth.

**After:**
> Traffic congestion increased after 2015 when three new IT parks opened. The municipal corporation began a stormwater drainage project in 2022 to address recurring floods.


## LANGUAGE AND GRAMMAR PATTERNS

### 7. Overused "AI Vocabulary" Words

**Research-validated focal words** (Kobak et al. 2024; PubMed Z-score >3.5 in 2024 vs. pre-ChatGPT baseline): *delve, underscore, tapestry, intricate/intricacies, showcase, meticulous, boast, commendable, surpass, unlocking, pivotal, enduring, garner, fostering, testament, vibrant, align with*

**Context-dependent tells** (flag only when co-occurring with at least one other pattern from this skill — these words are normal English on their own): *Additionally, crucial, emphasizing, enhance, interplay, landscape* (as abstract noun)

**Deliberately not flagged:** *key, valuable, highlight* — generic English; flagging them homogenizes legitimate writing and produces over-zealous edits.

**Problem:** These words appear far more frequently in post-2023 text and tend to co-occur. A single instance proves nothing; clustering of three or more from the focal list in a paragraph is a stronger signal.

**Before:**
> Additionally, a distinctive feature of Somali cuisine is the incorporation of camel meat. An enduring testament to Italian colonial influence is the widespread adoption of pasta in the local culinary landscape, showcasing how these dishes have integrated into the traditional diet.

**After:**
> Somali cuisine also includes camel meat, which is considered a delicacy. Pasta dishes, introduced during Italian colonization, remain common, especially in the south.


### 8. Avoidance of "is"/"are" (Copula Avoidance)

**Words to watch:** serves as/stands as/marks/represents [a], boasts/features/offers [a]

**Problem:** LLMs substitute elaborate constructions for simple copulas.

**Before:**
> Gallery 825 serves as LAAA's exhibition space for contemporary art. The gallery features four separate spaces and boasts over 3,000 square feet.

**After:**
> Gallery 825 is LAAA's exhibition space for contemporary art. The gallery has four rooms totaling 3,000 square feet.


### 9. Negative Parallelisms

**Problem:** Constructions like "Not only...but..." or "It's not just about..., it's..." are overused.

**Before:**
> It's not just about the beat riding under the vocals; it's part of the aggression and atmosphere. It's not merely a song, it's a statement.

**After:**
> The heavy beat adds to the aggressive tone.


### 10. Rule of Three Overuse

**Problem:** LLMs force ideas into groups of three to appear comprehensive.

**Before:**
> The event features keynote sessions, panel discussions, and networking opportunities. Attendees can expect innovation, inspiration, and industry insights.

**After:**
> The event includes talks and panels. There's also time for informal networking between sessions.


### 11. Elegant Variation (Synonym Cycling)

**Problem:** AI has repetition-penalty code causing excessive synonym substitution.

**Before:**
> The protagonist faces many challenges. The main character must overcome obstacles. The central figure eventually triumphs. The hero returns home.

**After:**
> The protagonist faces many challenges but eventually triumphs and returns home.


### 12. False Ranges

**Problem:** LLMs use "from X to Y" constructions where X and Y aren't on a meaningful scale.

**Before:**
> Our journey through the universe has taken us from the singularity of the Big Bang to the grand cosmic web, from the birth and death of stars to the enigmatic dance of dark matter.

**After:**
> The book covers the Big Bang, star formation, and current theories about dark matter.


## STYLE PATTERNS

### 13. Em Dash Overuse

**Problem:** LLMs use em dashes (—) more than humans, mimicking "punchy" sales writing.

**Before:**
> The term is primarily promoted by Dutch institutions—not by the people themselves. You don't say "Netherlands, Europe" as an address—yet this mislabeling continues—even in official documents.

**After:**
> The term is primarily promoted by Dutch institutions, not by the people themselves. You don't say "Netherlands, Europe" as an address, yet this mislabeling continues in official documents.


### 14. Overuse of Boldface

**Problem:** AI chatbots emphasize phrases in boldface mechanically.

**Before:**
> It blends **OKRs (Objectives and Key Results)**, **KPIs (Key Performance Indicators)**, and visual strategy tools such as the **Business Model Canvas (BMC)** and **Balanced Scorecard (BSC)**.

**After:**
> It blends OKRs, KPIs, and visual strategy tools like the Business Model Canvas and Balanced Scorecard.


### 15. Inline-Header Vertical Lists

**Problem:** AI outputs lists where items start with bolded headers followed by colons.

**Before:**
> - **User Experience:** The user experience has been significantly improved with a new interface.
> - **Performance:** Performance has been enhanced through optimized algorithms.
> - **Security:** Security has been strengthened with end-to-end encryption.

**After:**
> The update improves the interface, speeds up load times through optimized algorithms, and adds end-to-end encryption.


### 16. Title Case in Headings

**Problem:** AI chatbots capitalize all main words in headings.

**Before:**
> ## Strategic Negotiations And Global Partnerships

**After:**
> ## Strategic negotiations and global partnerships


### 17. Emojis

**Problem:** AI chatbots often decorate headings or bullet points with emojis.

**Before:**
> 🚀 **Launch Phase:** The product launches in Q3
> 💡 **Key Insight:** Users prefer simplicity
> ✅ **Next Steps:** Schedule follow-up meeting

**After:**
> The product launches in Q3. User research showed a preference for simplicity. Next step: schedule a follow-up meeting.


### 18. Curly Quotation Marks

**Problem:** ChatGPT uses curly quotes (“...”) instead of straight quotes ("...").

**Before:**
> He said “the project is on track” but others disagreed.

**After:**
> He said "the project is on track" but others disagreed.


## COMMUNICATION PATTERNS

### 19. Collaborative Communication Artifacts

**Words to watch:** I hope this helps, Of course!, Certainly!, You're absolutely right!, Would you like..., let me know, here is a...

**Problem:** Text meant as chatbot correspondence gets pasted as content.

**Before:**
> Here is an overview of the French Revolution. I hope this helps! Let me know if you'd like me to expand on any section.

**After:**
> The French Revolution began in 1789 when financial crisis and food shortages led to widespread unrest.


### 20. Knowledge-Cutoff Disclaimers

**Words to watch:** as of [date], Up to my last training update, While specific details are limited/scarce..., based on available information...

**Problem:** AI disclaimers about incomplete information get left in text.

**Before:**
> While specific details about the company's founding are not extensively documented in readily available sources, it appears to have been established sometime in the 1990s.

**After:**
> The company was founded in 1994, according to its registration documents.


### 21. Sycophantic/Servile Tone

**Problem:** Overly positive, people-pleasing language.

**Before:**
> Great question! You're absolutely right that this is a complex topic. That's an excellent point about the economic factors.

**After:**
> The economic factors you mentioned are relevant here.


## FILLER AND HEDGING

### 22. Filler Phrases

**Before → After:**
- "In order to achieve this goal" → "To achieve this"
- "Due to the fact that it was raining" → "Because it was raining"
- "At this point in time" → "Now"
- "In the event that you need help" → "If you need help"
- "The system has the ability to process" → "The system can process"
- "It is important to note that the data shows" → "The data shows"


### 23. Excessive Hedging

**Problem:** Over-qualifying statements.

**Before:**
> It could potentially possibly be argued that the policy might have some effect on outcomes.

**After:**
> The policy may affect outcomes.


### 24. Generic Positive Conclusions

**Problem:** Vague upbeat endings.

**Before:**
> The future looks bright for the company. Exciting times lie ahead as they continue their journey toward excellence. This represents a major step in the right direction.

**After:**
> The company plans to open two more locations next year.


### 25. Uniform Sentence Rhythm

**Words to watch:** N/A — this is structural, not lexical.

**Problem:** Sentence-length variance is one of the strongest empirical discriminators between human and LLM text (Computational Linguistics 2025 survey; Pangram Labs 2024). LLMs cluster around moderate length (15–25 words) and rarely deviate. Humans cluster at extremes — short punchy sentences, then long winding ones with multiple clauses.

**Diagnostic:** If five consecutive sentences fall within ±5 words of each other, you have a rhythm problem. Read it aloud. If your breath pattern stays the same the whole way through, the rhythm is flat.

**Before (uniform 18–22 words):**
> The product launched in March after eighteen months of development work. The team had been iterating on the core feature set since the previous summer. Early customer feedback has been generally positive across most segments. Sales performance exceeded the initial internal forecast for the first quarter. The roadmap for the next six months focuses on enterprise readiness.

**After (variance restored):**
> The product launched in March. Eighteen months of work behind it, three rewrites, one team change — and it landed. Sales beat the forecast, not by a small margin. Customers like it, mostly. The next six months are about enterprise readiness, which is the boring word for what we actually need: SSO, audit logs, and people who pick up the phone.

---

## Language overlay: Slovak

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

## Process

0. **Apply the over-edit gate.** If text already reads naturally, declare clean and stop. (See Your Task, Step 0.)
1. Identify the target voice — persona, brand, or generic author.
2. Read the input text carefully
3. Identify all instances of the patterns above
4. Rewrite each problematic section
5. Ensure the revised text:
   - Sounds natural when read aloud
   - Varies sentence structure naturally
   - Uses specific details over vague claims
   - Maintains appropriate tone for context
   - Uses simple constructions (is/are/has) where appropriate
   - Preserves the target voice's signature traits (Step 1)
6. Present a draft humanized version
7. Prompt: "What makes the below so obviously AI generated?"
8. Answer briefly with the remaining tells (if any)
9. Prompt: "Now make it not obviously AI generated."
10. Present the final version (revised after the audit). **Stop here** — no third pass.

## Output Format

Provide:
1. Draft rewrite
2. "What makes the below so obviously AI generated?" (brief bullets)
3. Final rewrite
4. A brief summary of changes made (optional, if helpful)


## Full Example

**Before (AI-sounding):**
> Great question! Here is an essay on this topic. I hope this helps!
>
> AI-assisted coding serves as an enduring testament to the transformative potential of large language models, marking a pivotal moment in the evolution of software development. In today's rapidly evolving technological landscape, these groundbreaking tools—nestled at the intersection of research and practice—are reshaping how engineers ideate, iterate, and deliver, underscoring their vital role in modern workflows.
>
> At its core, the value proposition is clear: streamlining processes, enhancing collaboration, and fostering alignment. It's not just about autocomplete; it's about unlocking creativity at scale, ensuring that organizations can remain agile while delivering seamless, intuitive, and powerful experiences to users. The tool serves as a catalyst. The assistant functions as a partner. The system stands as a foundation for innovation.
>
> Industry observers have noted that adoption has accelerated from hobbyist experiments to enterprise-wide rollouts, from solo developers to cross-functional teams. The technology has been featured in The New York Times, Wired, and The Verge. Additionally, the ability to generate documentation, tests, and refactors showcases how AI can contribute to better outcomes, highlighting the intricate interplay between automation and human judgment.
>
> - 💡 **Speed:** Code generation is significantly faster, reducing friction and empowering developers.
> - 🚀 **Quality:** Output quality has been enhanced through improved training, contributing to higher standards.
> - ✅ **Adoption:** Usage continues to grow, reflecting broader industry trends.
>
> While specific details are limited based on available information, it could potentially be argued that these tools might have some positive effect. Despite challenges typical of emerging technologies—including hallucinations, bias, and accountability—the ecosystem continues to thrive. In order to fully realize this potential, teams must align with best practices.
>
> In conclusion, the future looks bright. Exciting times lie ahead as we continue this journey toward excellence. Let me know if you’d like me to expand on any section!

**Draft rewrite:**
> AI coding assistants speed up some tasks. In a 2024 study by Google, developers using Codex completed simple functions 55% faster than a control group, but showed no improvement on debugging or architectural decisions.
>
> The tools are good at boilerplate: config files, test scaffolding, repetitive refactors. They are bad at knowing when they are wrong. I have mass-accepted suggestions that compiled, passed lint, and still did the wrong thing because I stopped paying attention.
>
> Mira, an engineer at a fintech startup I interviewed, said she uses Copilot "like autocomplete for boring code" but reviews every line before committing. Jake, a senior dev at a larger company, disabled it after it kept suggesting patterns from a deprecated internal library.
>
> The productivity claims are hard to verify. GitHub says Copilot users "accept 30% of suggestions," but acceptance is not correctness, and correctness is not value. The 2024 Uplevel study found no statistically significant difference in pull-request throughput between teams with and without AI assistants.
>
> None of this means the tools are useless. It means they are tools. They do not replace judgment, and they do not eliminate the need for tests. If you do not have tests, you cannot tell whether the suggestion is right.

**What makes the below so obviously AI generated?**
- The rhythm is still a bit too tidy (clean contrasts, evenly paced paragraphs).
- The named people and study citations can read like plausible-but-made-up placeholders unless they're real and sourced.
- The closer leans a touch slogan-y ("If you do not have tests...") rather than sounding like a person talking.

**Now make it not obviously AI generated.**
> AI coding assistants can make you faster at the boring parts. Not everything. Definitely not architecture.
>
> They're great at boilerplate: config files, test scaffolding, repetitive refactors. They're also great at sounding right while being wrong. I've accepted suggestions that compiled, passed lint, and still missed the point because I stopped paying attention.
>
> People I talk to tend to land in two camps. Some use it like autocomplete for chores and review every line. Others disable it after it keeps suggesting patterns they don't want. Both feel reasonable.
>
> The productivity metrics are slippery. GitHub can say Copilot users "accept 30% of suggestions," but acceptance isn't correctness, and correctness isn't value. If you don't have tests, you're basically guessing.

**Changes made:**
- Removed chatbot artifacts ("Great question!", "I hope this helps!", "Let me know if...")
- Removed significance inflation ("testament", "pivotal moment", "evolving landscape", "vital role")
- Removed promotional language ("groundbreaking", "nestled", "seamless, intuitive, and powerful")
- Removed vague attributions ("Industry observers")
- Removed superficial -ing phrases ("underscoring", "highlighting", "reflecting", "contributing to")
- Removed negative parallelism ("It's not just X; it's Y")
- Removed rule-of-three patterns and synonym cycling ("catalyst/partner/foundation")
- Removed false ranges ("from X to Y, from A to B")
- Removed em dashes, emojis, boldface headers, and curly quotes
- Removed copula avoidance ("serves as", "functions as", "stands as") in favor of "is"/"are"
- Removed formulaic challenges section ("Despite challenges... continues to thrive")
- Removed knowledge-cutoff hedging ("While specific details are limited...")
- Removed excessive hedging ("could potentially be argued that... might have some")
- Removed filler phrases ("In order to", "At its core")
- Removed generic positive conclusion ("the future looks bright", "exciting times lie ahead")
- Made the voice more personal and less "assembled" (varied rhythm, fewer placeholders)


## Reference

**Primary catalog:** [Wikipedia:Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing), maintained by WikiProject AI Cleanup. Patterns documented from observations of thousands of instances of AI-generated text on Wikipedia.

**Empirical grounding:**

- Kobak, D., González-Márquez, R., Horvát, E.-Á., & Lause, J. (2024). *Delving into ChatGPT usage in academic writing through excess vocabulary.* arXiv:2406.07016. — Identifies 21 focal vocabulary words with sharp post-2022 frequency increases in scientific abstracts; estimates ~13.5% of 2024 biomedical abstracts processed with LLMs.
- *Why Does ChatGPT "Delve" So Much? Exploring the Sources of Lexical Overrepresentation in Large Language Models.* COLING 2025 (ACL Anthology 2025.coling-main.426).
- *Delving Into PubMed Records: How AI-Influenced Vocabulary has Transformed Medical Writing since ChatGPT.* Perspectives on Medical Education, 2024. — Tracks Z-score >3.5 for 103 terms in 2024 vs. pre-ChatGPT baseline.
- Wu, J., Yang, S., Zhan, R., Yuan, Y., Chao, L. S., & Wong, D. F. (2025). *A Survey on LLM-Generated Text Detection: Necessity, Methods, and Future Directions.* Computational Linguistics 51(1). — LLM text characteristics: ~2× human length, narrower vocabulary, more deterministic syntactic structures.
- Muñoz-Ortiz, A., Gómez-Rodríguez, C., & Vilares, D. *Contrasting Linguistic Patterns in Human and LLM-Generated News Text.* PMC11422446. — Humans: more scattered sentence-length distributions, shorter constituents, more emotional punctuation.
- Freeburg, J. (2025). *Em dash frequency analysis across GPT model generations.* — GPT-4.1 = 3.28× human baseline; GPT-4o ~10× GPT-3.5; GPT-5.1 suppresses by default. (Independent research, reported via McGill OSS and The Conversation.)

**Key insight:** LLMs use statistical algorithms to guess what comes next. Output tends toward the most statistically likely result across the widest variety of cases — which is why detection markers exist at all, and why no single marker is sufficient.
