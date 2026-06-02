# LannaBench

Public companion site for **LannaBench** — a research project evaluating
large language models on the Northern Thai (Lanna, ISO `nort2740`) ↔ Standard
Thai (`thai1261`) translation task.
The main codebase is kept private while the project is in progress. This
repo hosts public-facing reference pages.

## About the project

Northern Thai (*kam mueang* / ᨣᩴᩤᨾᩮᩬᩥᨦ) is the language traditionally spoken
across the eight provinces of Northern Thailand. Despite millions of
speakers, it is severely under-represented in modern NLP benchmarks. The
project builds **LannaBench**, a curated evaluation suite, and measures how
well frontier and open-weight LLMs translate between Northern Thai and
Standard Thai in both directions — with multi-reference scoring, error
typology, and human ratings.

## Codename reference

Internally, the codebase uses Northern Thai cultural objects as codenames
for its components — stove (*Ang Lo*), serving tray (*Khan Tok*),
cosmology charts (*Trai Phum*), and so on. The codenames are both a
stylistic choice and a small way of carrying the culture into the
engineering layer of the project.

The codenames, with descriptions and photos, are documented here:

### → [Lanna codenames (v2)](https://n-sanitdee.github.io/northern-thai-llm-site/lanna-codenames-v2.html)

Each entry shows the literal object, its cultural role, and how the
codename maps to a module or concept in the codebase.

## Progress

### LoRA adapter improvements (NTD→STD)

Measured on a held-out slice of naturalistic conversational text
(Northern Thai → Standard Thai direction):

| Iteration | Date | Δ over base | Notes |
|---|---|---|---|
| Base model | — | 0% | Off-the-shelf Thai LLM; handles Standard Thai well, struggles with Northern Thai vocabulary and particles |
| Adapter v1 | 2026-05-08 | +13% | Lightweight adaptation on the natural-conversation slice; model begins recognising Northern Thai as Northern Thai |
| Adapter v2 | 2026-05-18 | +20% | Retrained on refined dataset with tuned recipe; recovers earlier failure cases (repetition loops, dropped dialect particles) |

The +20% figure is relative ChrF improvement over the base model.

---

### Multimodel benchmark — API cohort (v1 → v2 prompt template)

All scores are `chrf_avg`; lower `chrf_diff` is better (less prompt fragility).

**Translation quality (chrf\_avg)**

| Model | v1 | v2 | Δ |
|---|---|---|---|
| gpt-4o | 70.94 | 70.18 | −0.75 |
| claude-sonnet | 67.34 | 68.22 | +0.88 |
| deepseek (v4-flash via alias) | 68.94 | 72.39 | **+3.45** ← leader |
| typhoon-s-thaillm | 47.34 | 51.34 | +4.01 |

---

### API cost record

2026-05-13 to 2026-05-16 · 3 providers

| Date | OpenAI | Anthropic | DeepSeek | Day total |
|---|---|---|---|---|
| 05-13 | $0.29 *(gpt-4o)* | $0.84 *(sonnet-4-5)* | $0.011 | $1.14 |
| 05-15 | $0.31 *(gpt-4o)* | $0.85 *(sonnet-4-5)* | $0.011 | $1.17 |
| 05-16 | $3.57 *(gpt-5.5)* | $0.86 *(sonnet-4-6)* | $0.074 | **$4.50** |
| **Total** | **$4.16** | **$2.55** | **$0.096** | **$6.81** |

The 05-16 spike is GPT-5.5 billing hidden reasoning tokens (~263 tok/req vs ~26 for gpt-4o, an 11.7× cost lift). DeepSeek's cost also jumped 7× that day due to 26× more output tokens under the explicit `deepseek-v4-flash` pin vs the prior `deepseek-chat` alias.

---

### BaiLan human-evaluation form (T1) — sample

**BaiLan** is the human-rating instrument for LannaBench. Raters evaluate
model translations on six axes (1–5 Likert each).

The form is a self-contained HTML form that submits via Google Apps Script with a JSONL backup download fallback.

---

The full code repository will be opened once the project is ready to publish.
