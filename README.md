# northern-thai-llm-site

Public companion site for **northern-thai-llm** — a research project evaluating
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

The +20% figure is relative ChrF improvement over the base model on the same held-out set.

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

Total spend across all inference runs to date (Typhoon bills separately via opentyphoon.ai and is not included):

| Date | Model | Requests | Cost |
|---|---|---|---|
| 2026-05-13 | gpt-4o-2024-11-20 | 397 | $0.2902 |
| 2026-05-13 | claude-sonnet-4-5 | 397 | $0.8400 |
| 2026-05-13 | deepseek-v4-flash *(alias)* | 397 | $0.0105 |
| 2026-05-15 | gpt-4o-2024-11-20 | 397 | $0.3076 |
| 2026-05-15 | gpt-5.5-2026-04-23 *(smoke)* | 2 | ~$0.00 |
| 2026-05-15 | claude-sonnet-4-5 | 397 | $0.8500 |
| 2026-05-15 | claude-sonnet-4-6 *(smoke)* | 1 | ~$0.00 |
| 2026-05-15 | deepseek-v4-flash *(alias)* | 396 | $0.0107 |
| 2026-05-16 | gpt-5.5-2026-04-23 | 401 | $3.5666 |
| 2026-05-16 | claude-sonnet-4-6 | 397 | $0.8600 |
| 2026-05-16 | deepseek-v4-flash *(explicit pin)* | 398 | $0.0743 |
| **4-day total** | | | **$6.8100** |

**Note on model provenance:** the 05-13/15 DeepSeek runs used the floating `deepseek-chat` alias. The 05-16 refresh switched to the explicit `deepseek-v4-flash` pin — which produced 26× more output tokens per request (~642 tok/req vs ~24), likely explaining the −2.07 chrf_avg regression in the diagnostic findings.

An unexpected cost finding from the refresh: GPT-5.5 produced ~20× throughput collapse under an English-imperative prompt (`p2`) vs a Thai-imperative prompt (`p1`) — same source text, same dataset. Hidden reasoning-token spend averaged ~263 tok/req for GPT-5.5 (vs ~26 tok/req for GPT-4o), yielding an 11.7× cost lift per request. Cost prediction for reasoning-class models must factor in the prompt template's reasoning-trigger profile, not just row count.

---

### BaiLan human-evaluation form (T1) — sample

**BaiLan** is the human-rating instrument for LannaBench. Raters evaluate
model translations on six axes (1–5 Likert each).

The form is a self-contained HTML form that submits via Google Apps Script with a JSONL backup download fallback.

---

The full code repository will be opened once the project is ready to publish.
