# Design Brief — Constructiveness XAI Trust Report

## What this is

A trust-building report for news editors who use our AI constructiveness scoring tool. It lives on the constructive-news-mirror website (Next.js/Vercel) and is shared via link — not as a file attachment. It is the answer to: "Can I trust this tool's scores?"

The audience is skeptical by nature. They are trained to read critically, spot gaps, and distrust anything that oversells. The report earns credibility by being honest about limitations, not by burying them.

---

## Audience

**Primary reader:** News editor evaluating whether to use the constructiveness scoring tool in their workflow. Likely skeptical, time-limited, not a technical reader. Reads fast and pattern-matches on credibility signals.

**What they need to leave with:**
1. A concrete sense of what the model actually does (not abstract stats)
2. Confidence it gets conventional journalism right (low false-alarm rate)
3. Honest awareness of where it fails and why
4. A way to explore the evidence themselves (case studies)

**What will lose them:**
- AI-generated hedging language ("it is important to note that")
- Buried caveats in appendices
- Statistics without context
- Passive voice and corporate tone

---

## Voice and Tone

Write like a thoughtful journalist explaining their tools to a senior editor. Not a researcher defending methodology.

**Rules:**
- Active voice. Short sentences. No hedge-stacking.
- Numbers always get context: "6 out of 10" not "recall of 0.626"
- First person plural where appropriate: "we found", "our model", "we built"
- Acknowledge limitations plainly and early — don't save them for the end
- "The model misses 4 in 10 constructive articles" not "recall is 0.60 at this threshold"
- Specific is trustworthy. Vague is suspicious.

**Phrases to avoid:**
- "robust", "leverage", "it is worth noting", "state-of-the-art", "novel approach"
- "The model was trained on...", "In order to..."
- Any sentence starting with "This"

**Reference tone:** The Economist's explanatory journalism. Precise but readable. Opinionated but fair.

---

## Visual Style

Carry this from the existing draft (`report/Constructiveness XAI Report - Standalone.html`):

### Colors
| Token | Hex | Use |
|-------|-----|-----|
| `--bg` | `#f4ede0` | Page background — warm parchment |
| `--bg-raised` | `#ebe2cf` | Cards, panels, masthead |
| `--border` | `#d2c8b1` | Dividers, table borders |
| `--ink` | `#1a1612` | Body text, headings |
| `--ink-muted` | `#968b78` | Captions, meta text |
| `--sage` | `#3a6b5b` | Constructive signal — green |
| `--brick` | `#a13e26` | Conventional signal / caution — brick red |
| `--gold` | `#b88a2c` | Neutral highlight, numbers, key callouts |

### Typography
- Body: serif (Georgia or a web-safe serif equivalent)
- Line height: 1.85 for body text
- Headings: same serif family, weight contrast only — no display font
- Captions and meta: small, `--ink-muted`, same serif

### Layout
- Max content width: 960px, centered
- Generous vertical whitespace between sections
- Section dividers: thin horizontal rule in `--border`, not heavy boxes or cards
- Avoid heavy drop shadows. Prefer border + subtle background tint.

### Attribution colors for highlights
- Green = sentence is more constructive-signaling than the article's own average
- Red = sentence is more conventional-signaling than the article's own average
- Bar length = how far above or below the article average (not a cross-article score)
- Grey = beyond the model's 4096-token reading window — no attribution available
- Alpha encodes strength (stronger = more opaque) for word-level highlights

---

## Page Structure

Build the report as a single scrollable page with anchor navigation. Sections in this order:

---

### Section 1 — Hero / Conclusions

**Purpose:** Give the editor the punchline immediately. Three key findings, scannable in 30 seconds.

**Layout:** Full-width masthead bar in `--bg-raised`, then three side-by-side conclusion panels below.

**Panel 1 — What the model does well (sage green accent)**
> Headline: "Gets conventional journalism right 96% of the time"
> Body: Of 468 conventional articles in our test, 451 were correctly identified. Editors using the filter see very little noise from content that clearly isn't constructive.

**Panel 2 — What it misses (brick red accent)**
> Headline: "Struggles at the boundary"
> Body: The model's main challenge is c_score 4 — borderline constructive articles with the fewest training examples. These often score 2.5–3.5 rather than 4+. Use the score as a priority queue, not a yes/no filter: the top of the list will be disproportionately constructive even when individual scores fall below a clear threshold.

**Panel 3 — What drives the score (gold accent)**
> Headline: "Sentence structure, not just topic"
> Body: The model reads which sentences push an article toward solutions, context, and forward momentum — not just what it's about. An article on a serious topic can still score high if it frames a problem and explores a path forward.

**Subhead below panels:**
> "Below: how we checked the model's reasoning, where it gets it right, and where it doesn't."

---

### Section 2 — Showcase

**Purpose:** Two concrete examples that make the model's reasoning tangible. One constructive, one conventional. This is the heart of the report for most readers.

**Layout:** Two full-width article panels, displayed one after the other. Each panel:
1. Article headline / summary (2–3 sentences)
2. Model verdict badge: "Constructive ✓" or "Conventional ✓" in appropriate color
3. Model score on the 1–5 scale — label `MODEL` paired with the `predicted_score` value from `data/metrics.json` (e.g. `MODEL  4.0`), mirroring the `C_SCORE` label layout. Do not show a percentage.
4. Sentence highlight view (embed `sentence_chart.png` from assets/showcase/)
5. Brief interpretive caption (1–2 sentences explaining what the chart shows)

**Showcase Article 1 — Constructive (article 1942)**
Use: `assets/showcase/1942_sentence_chart.png`

> **Article:** A 2022 news report on carbon capture technology under construction on Norway's North Sea coast. The facility will store CO₂ from European industry underground, indefinitely.
>
> **Model verdict:** Constructive ✓ — MODEL 4.0
>
> **Chart caption:** Each bar shows how much a sentence deviates from the article's own average — green means more constructive-signaling than typical for this piece, red means more conventional-signaling. The opening sentence — "a 'graveyard' under construction is raising the hopes of climate experts" — stands far above the average: it reframes industrial infrastructure as a hopeful solution. Sentences about the cross-border commercial agreement and new pipeline announcements follow. Red bars mark short, generic sentences or moments of skepticism about the technology ("the technology is not unanimously supported").

**Showcase Article 2 — Conventional (article 2421)**
Use: `assets/showcase/2421_sentence_chart.png`

> **Article:** A 2022 entertainment news piece about a Lithuanian TV talk show that uses AI to recreate duets between living performers and deceased music legends.
>
> **Model verdict:** Conventional ✓ — MODEL 1.6
>
> **Chart caption:** With no single sentence standing out as constructive, bars cluster tightly around zero. A few sentences tip slightly green — they contain language the model associates with constructive journalism (references to personal stories, childhood memories, artistic ambition) — but none are strong enough to move the article past the conventional threshold. The most conventional-signaling sentences (red) are the purely promotional ones: programme listings, broadcast times, channel mentions.

### What makes a sentence constructive? Three patterns

**Purpose:** Before the reader digs into case studies, show them concretely what the model is "seeing." Three sentences, three distinct patterns. Each has a directional signal bar and the article's overall confidence.

**Layout:** Three cards stacked vertically (or 3-column row on wide screens). Each card contains:
1. Type label — small-caps chip or label in `--ink-muted`
2. The sentence — blockquote style, serif, generous left border in `--sage`
3. A directional signal bar (spec below)
4. One-line meta: case study number + model verdict + model score (1–5)

**Three cards — copy verbatim from `data/metrics.json` → `sentence_showcase`:**

**Card 1 — "Named initiative with forward-looking outcome"** (article #1942, attr=1.0, model score 4.0)
> "On the icy shores of the North Sea, a 'graveyard' under construction is raising the hopes of climate experts: soon, the site will house a small portion of the CO₂ emitted by European industry, thus preventing it from ending up in the atmosphere."

*Bar position: full green (attr=1.0 → fills entire right half of track)*
*Meta: Case study #1942 — Model verdict: Constructive ✓ — MODEL 4.0*

**Card 2 — "Citizens filling an institutional gap"** (article #2140, attr=0.47, model score 3.5)
> "Gas shortage and energy shortage? The Swiss government has done little to remedy the problem. Therefore, citizens are taking their own measures."

*Bar position: half green (attr=0.47 → fills ~47% of right half)*
*Meta: Case study #2140 — Model verdict: Constructive ✓ — MODEL 3.5*

**Card 3 — "Collective action at historical scale"** (article #2469, attr=0.38, model score 3.9)
> "The cooperative movement was once at the forefront of the global struggle for consumer rights, affordable rents, and even... world peace."

*Bar position: moderate green (attr=0.38 → fills ~38% of right half)*
*Meta: Case study #2469 — Model verdict: Constructive ✓ — MODEL 3.9*

**Footnote below the three cards (small, `--ink-muted`):**
> "Each bar shows how far a sentence deviates from that article's own average signal — green means more constructive than the article's average, red means less. Bars are not directly comparable across articles. The model score (1–5) is the model's overall rating of the full piece."

---

#### Directional signal bar — HTML/CSS spec

The bar runs from "Conventional signal" (left, `--brick`) to "Constructive signal" (right, `--sage`). The center mark is neutral. For these three showcase sentences, all bars point right (constructive). In the case study section, sentences pushing toward conventional would fill left of center in `--brick`.

The `attr` value (0–1) drives `width` as a percentage of the right half: `width: calc(attr * 50%)`.

```html
<div class="signal-bar-wrap">
  <span class="signal-label">← Conventional</span>
  <div class="signal-track">
    <div class="signal-center"></div>
    <div class="signal-fill constructive" style="width: calc(1.0 * 50%);"></div>
    <!-- For conventional signals: class="signal-fill conventional", position right→left -->
  </div>
  <span class="signal-label">Constructive →</span>
</div>
<p class="signal-meta">Case study #1942 — Model verdict: <strong>Constructive</strong> ✓ — MODEL 4.0</p>
```

```css
.signal-bar-wrap {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin: 0.75rem 0;
}
.signal-track {
  flex: 1;
  height: 8px;
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: 4px;
  position: relative;
  overflow: hidden;
}
.signal-center {
  position: absolute;
  left: 50%;
  top: 0;
  width: 1px;
  height: 100%;
  background: var(--border);
}
.signal-fill {
  position: absolute;
  top: 0;
  height: 100%;
}
.signal-fill.constructive {
  left: 50%;
  background: var(--sage);
  border-radius: 0 4px 4px 0;
}
.signal-fill.conventional {
  right: 50%;
  background: var(--brick);
  border-radius: 4px 0 0 4px;
}
.signal-label {
  font-size: 0.75rem;
  color: var(--ink-muted);
  white-space: nowrap;
}
.signal-meta {
  font-size: 0.85rem;
  color: var(--ink-muted);
  margin-top: 0.25rem;
}
```

---

**Transition text between showcase and deep dive:**
> "These are the clearest cases. Below are all 9 articles the model analyzed in depth, including cases where it got it wrong."

---

### Section 3 — All Case Studies (Deep Dive)

**Purpose:** Full exploration of all 9 case studies. Editors who want to dig in can see every article, its score, and the model's reasoning.

**Layout:** A navigable grid or tab interface with 9 cards. Each card shows:
- Article ID and short description
- c_score (human rating, 1–5)
- Model score (1–5) — use `predicted_score` from each case study's `metadata.json`, displayed as `MODEL  X.X` alongside `C_SCORE  X`
- Type badge: "Correct ✓" or "Missed ✗" in appropriate color
- Category label: "confident-correct-constructive", "confident-wrong-constructive", etc.

On click/expand: show the full sentence chart (sentence_chart.png) and a link to the word-level HTML (`word_highlights_lig.html`) for deeper inspection.

**Case study categories with labels:**
| ID | Type | Story |
|----|------|-------|
| 1942 | Correct — constructive | Carbon capture, North Sea |
| 2469 | Correct — constructive | The cooperative movement and world peace |
| 2493 | Correct — constructive | French cities implement low-emission zones for air quality |
| 3072 | Correct — constructive | Danish pension funds divest from fossil fuels |
| 2421 | Correct — conventional | Lithuanian AI-generated TV duets show |
| 275 | Missed — model said constructive | VAT fraud and European tax Europol proposal |
| 1246 | Missed — model said conventional | EU accused of complicity in Balkan migrant pushbacks |
| 2037 | Boundary (score=4) | German coalition parties disagree on energy relief measures |
| 2140 | Boundary (score=4) | Swiss citizens buying generators amid energy shortage fears |

**Intro text for missed cases:**
> "These are the cases where the model got it wrong. We include them here because understanding where it fails is as important as seeing where it succeeds. In each case, we explain why."

**Explanatory text for article 275 (false positive):**
> "The Social Democrats' proposal for a pan-European tax enforcement body reads like a policy solution — coordinated action, cross-border cooperation. The model flagged it as constructive. But the article is a political news story, not solutions journalism: it reports a party position without exploring outcomes, community impact, or forward momentum. The model was fooled by the form, not the substance."

**Explanatory text for article 1246 (false negative):**
> "This is a deeply reported investigation into the treatment of migrants at the EU's Balkan border — exactly the kind of journalism the tool is designed to surface. The model called it conventional. The language is heavy: accused, violent, illegal, die. The model reads adversarial tone as a conventional signal, even when the journalism beneath it is solution-oriented. This is one of the clearest cases of the model mistaking surface tone for structural constructiveness."

**Note on word-level highlights for articles 275 and 1246:**
Both articles are from the DNM corpus, which anonymised named entities during data collection: people and organisations were replaced with the literal tokens `person` and `organisation` before training. As a result, the word-level highlight view for these two articles shows `person` and `organisation` where you would expect real names. This reflects exactly what the model read — it never saw the original names. The sentence-level bar chart is unaffected. The disclosure below should appear near both articles' detail views:

> "Named entities (people and organisations) in this article were anonymised before the model was trained — the model read `person` and `organisation` in place of real names. The word-level highlights reflect this: where you see those tokens, a specific name appeared in the original text."

---

### Section 4 — Performance

**Purpose:** The numbers section. Honest, framed positively where fair.

**Layout:** Two columns — numbers on left, interpretation on right. Embed performance charts below.

**Key numbers block:**
| | |
|--|--|
| Test articles | 567 |
| Score correlation with human ratings (Spearman r) | 0.45 |
| Conventional articles correctly scored low | 96.4% (451 of 468) |
| Constructive articles caught at score ≥ 3.0 | ~63% (62 of 99) |
| Precision at score ≥ 3.0 | ~38% — more than 2× the 17.5% base rate |

**Interpretation copy:**
> "The model is not a binary classifier — it assigns a score from 1 to 5. The right question is not 'does it flag the right articles?' but 'does a higher score mean more constructive journalism?' It does: the model's scores correlate with human ratings at Spearman r = 0.45 across 567 test articles. That is a moderate correlation — useful as a ranking and prioritization tool, not strong enough to be a final verdict.
>
> Where it is most reliable: conventional journalism. 96% of clearly conventional articles score low. Editors using the tool as a first-pass filter will see very little noise from content that obviously isn't constructive.
>
> Where it struggles: borderline content. At a review threshold of score ≥ 3.0, the model surfaces about 6 in 10 constructive articles. Articles scoring between 2.5 and 3.5 are the hardest cases — often deeply reported journalism that the model underscores because it has seen too few examples like them in training.
>
> How to use the score: treat it as a ranked reading queue, not a gate. Set your own threshold based on review capacity. Articles at 4+ are strong candidates; articles at 3–4 are worth a second look; articles below 3 are unlikely to be constructive."

**Charts to embed:**
1. `assets/performance/ordinal_error_distribution.png` — use only as supporting detail, not the lead visual. Caption: "Correct and incorrect predictions by human c_score. The model is nearly perfect on c_score 1–2 (clearly conventional) and struggles most at c_score 4 — the borderline constructive category with the fewest training examples. Note: the box plot on the left shows the model's internal score distribution, which maps to the 1–5 display scale; the bar chart on the right shows raw counts rather than rates."
2. `assets/performance/calibration_by_cscore.png` — lead visual for this section. Caption: "Mean model score by human c_score. Each step up in human rating corresponds to a higher model score — the ordering is consistent across all five levels. **Note on the chart title:** the 'Spearman rho=1.000' statistic measures only whether the five group *averages* are monotonically ordered — which they always will be for a working model. It is not a measure of individual prediction accuracy and should not be read as 'the model is perfect.'"
3. Do NOT embed `confusion_matrix.png`. It is generated at a fixed score threshold (≥ 4.0) that produces only 17% recall, which is inconsistent with the threshold-agnostic framing of this section and would mislead editors.

---

### Section 5 — Known Biases (Shortcut Audit)

**Purpose:** Pre-empt the skeptic's questions. Address the known failure modes directly.

**Intro copy:**
> "We checked four potential shortcuts — ways the model could be getting right answers for wrong reasons. Here's what we found."

**Bias 1 — Article length**
> **Finding:** Longer articles score slightly higher (Spearman ρ = 0.26, p < 0.001).
> **What it means:** The bias is real but modest. Length alone can't explain most predictions, and it makes some intuitive sense — deeply reported constructive journalism tends to be longer. But editors should be aware that a long conventional piece may score higher than a short constructive one.
> **Chart:** `assets/shortcuts/length_vs_prob.png` — Caption: "Longer articles tend to receive higher model scores. The y-axis shows the model's internal scoring (mapped to our 1–5 display scale). The correlation is statistically significant but modest — length is a weak predictor, not a dominant one."

**Bias 2 — Source corpus**
> **Finding:** Precision is 12% higher on articles from one source corpus (DNM) than the other (EBU). Below our 25% concern threshold.
> **What it means:** The model performs similarly across the two corpora we trained on. We don't have outlet-level breakdowns, so we can't guarantee it performs equally on all news sources.

**Bias 3 — Named entities**
> **Finding:** In 2 out of 3 test articles, masking named entities (people and organizations) shifts the model score by more than 5 percentage points.
> **What it means:** The model is sensitive to WHO an article is about, not just how it's written. A story about Greenpeace may score differently than the same story about a bank. This is partly expected — constructive journalism often involves specific organizations — but it's worth knowing.

**Bias 4 — Sentiment**
> **Finding:** Constructive articles are slightly more positive in tone (Cohen's d = 0.32), but the effect is small and below our flagging threshold.
> **What it means:** The model isn't simply a sentiment detector. Tone matters somewhat, but structure and substance matter more.

---

### Section 6 — How It Works (Expandable)

**Purpose:** Optional depth for editors who want to understand the method. Collapsed by default — a "How does this work?" disclosure.

**Copy:**
> **Training:** We started with a large language model pre-trained on millions of documents (Longformer-base-4096) and fine-tuned it on 2,534 news articles rated by human annotators on a 1–5 constructiveness scale. The model learned to predict that score.
>
> **Reading length:** Unlike most text classifiers, this model reads up to 4,096 tokens — roughly 3,000 words — in one pass. Most news articles fit within this window. Grey bars in the sentence charts indicate text beyond this limit.
>
> **The score:** The model predicts a number between 1 and 5. Articles scoring 4 or above are strong constructive candidates at our default threshold — but the threshold is a review-capacity decision, not a model property. An editor with more bandwidth might review everything above 3.0; an editor prioritizing precision might set 4.5. An article scoring 3.8 warrants different attention than one scoring 1.2, even if both fall below a given threshold.
>
> **The highlights:** We use a method called Layer Integrated Gradients (LIG) to ask: which parts of the article most influenced the score? We verified that these highlights are meaningful — hiding the highlighted sentences drops the model's score significantly more than hiding random sentences (5.7× more than random, measured across 211 articles).
>
> **Limitations:** The model was trained on a relatively small dataset with limited constructive examples (~17.5% of training data). More labeled data — especially for borderline content — would improve it. The tool is best used as a first-pass filter, not a final editorial verdict.

---

### Section 7 — Glossary

**Purpose:** Give readers a reference for terms used in the report. Keep it short.

**Terms:**
- **Constructiveness score (c_score):** Human rating from 1 to 5 assigned by annotators. 1 = clearly conventional news. 5 = strongly constructive journalism. 4+ = flagged as a constructive candidate.
- **Precision:** Of all articles the model calls constructive, what fraction actually are. Higher precision = less noise.
- **Recall:** Of all truly constructive articles, what fraction the model catches. Higher recall = fewer misses.
- **LIG (Layer Integrated Gradients):** A method for tracing which words most influenced the model's prediction, by measuring how the prediction changes as input is gradually introduced.
- **AOPC:** A faithfulness measure for LIG. Tests whether hiding the model's top-ranked words drops its confidence more than hiding random words. Our LIG is 5.7× above random.
- **Spearman ρ:** A correlation coefficient (−1 to 1). Used to measure how well model rankings agree with human rankings. 0.45 = moderate positive correlation.

---

## Navigation

Include a sticky top navigation bar with anchor links to all 7 sections. Use section short names:
- Overview
- Showcase
- Case Studies
- Performance
- Biases
- How It Works
- Glossary

---

## Interactive elements spec

### Sentence highlight chart
- Primary: embed `sentence_chart.png` as an `<img>` tag
- The chart is a horizontal bar chart — bars extending right (green) push toward constructive, left (red) push toward conventional
- Caption below explains the direction

### Word highlight view (secondary)
- Available as a "Word-level detail →" link that opens `word_highlights_lig.html` in a new tab or modal
- Do NOT try to replicate the word highlight inside the main report — link to the pre-built HTML
- The HTML files contain HTML-encoded text (e.g. `&#x27;` for apostrophes). Always render them via a `<iframe>` or direct browser link — never paste the raw HTML source as plain text, or entities will appear literally.

### Case study cards (Section 3)
- Each card collapsed by default, showing summary + verdict badge
- Expand on click to show sentence chart + link to word highlights
- Use a simple CSS accordion — no external JS dependencies

---

## What to carry from the existing draft

- The warm parchment color palette (#f4ede0, #ebe2cf, #d2c8b1) — distinctive and not generic
- The three-panel conclusions hero with sage/brick/gold accent colors
- The editorial serif typography feel
- The title: "The Lens and the Judge — A field report on machine-scored constructiveness"

## What to abandon

- AI-generic, hedge-stacked language — rewrite all copy using the voice rules above
- Any reference to RoBERTa or CORAL loss — those are old architecture details not relevant here
- Lack of hierarchy — the new version must have clear section navigation
- Single-page wall-of-text layout — use the 7-section structure above
- SHAP visualizations — excluded from the customer-facing report entirely

---

## Files in this brief package

```
claude-design-brief/
├── BRIEF.md                         ← this file
├── data/
│   ├── metrics.json                 ← all numbers, verified, verbatim-ready
│   └── case_dump.json               ← full case study data (top words, text preview)
└── assets/
    ├── showcase/
    │   ├── 1942_sentence_chart.png  ← constructive showcase chart
    │   ├── 1942_word_highlights_lig.html
    │   ├── 2421_sentence_chart.png  ← conventional showcase chart
    │   └── 2421_word_highlights_lig.html
    ├── case_studies/
    │   └── {id}/                    ← all 9 real case studies
    │       ├── sentence_chart.png
    │       ├── word_highlights_lig.html
    │       └── metadata.json
    ├── performance/
    │   ├── confusion_matrix.png
    │   ├── ordinal_error_distribution.png
    │   └── calibration_by_cscore.png
    └── shortcuts/
        ├── length_vs_prob.png
        └── dataset_source_precision_recall.png
```
