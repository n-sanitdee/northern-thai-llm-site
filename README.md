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

Total spend across all inference runs to date (rounded):

| Run / date | Models | Approx. cost |
|---|---|---|
| 2026-05-13 baseline | gpt-4o, claude-sonnet-4-5, deepseek-chat, typhoon-v2.5 | ~$1.20 |
| 2026-05-15 v2 comparison | same 4-model set, 3 prompt variants | ~$1.40 |
| 2026-05-16 refresh | gpt-5, claude-sonnet-4-6, claude-opus-4-7, gemini-2.5-pro, deepseek-v4-flash | ~$1.50 (est.) |

**Note on model provenance:** the registry entry `deepseek-v3` used a
floating alias (`deepseek-chat`). New runs use an explicit
pinned model ID (`deepseek-v4-flash`).

An unexpected cost finding from the refresh: GPT-5 (`gpt-5.5` internally)
produced ~20× throughput collapse under an English-imperative prompt
(`p2`) vs a Thai-imperative prompt (`p1`) — same source text, same dataset.
Hidden reasoning-token spend averaged ~266 tok/req for GPT-5 on p2 vs ~25
tok/req for GPT-4o on the same task, yielding a 9× cost lift per request.
This is documented as a methodology note: cost prediction for
reasoning-class models must factor in the prompt template's
reasoning-trigger profile, not just row count.

---

### BaiLan human-evaluation form (T1) — sample

**BaiLan** is the human-rating instrument for LannaBench. Raters evaluate
model translations on six axes (1–5 Likert each).

The form is a self-contained HTML form that submits via Google Apps Script with a JSONL backup download fallback.

---

The full code repository will be opened once the project is ready to publish.
