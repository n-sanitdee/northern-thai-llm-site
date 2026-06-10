# LannaBench

<div align="center">

![license](https://img.shields.io/badge/license-MIT-blue)
![status](https://img.shields.io/badge/status-in%20progress-orange)
![languages](https://img.shields.io/badge/languages-NTD%20↔%20STD-green)
![adapters](https://img.shields.io/badge/LoRA%20adapters-v2%20%2B20%25%20ChrF-brightgreen)

*This page is the public companion site for **LannaBench** — a research benchmark evaluating how well frontier and open-weight LLMs handle translation between Northern Thai and Standard Thai in both directions. It pairs automated scoring (a multi-reference Triple-ChrF metric) with human ratings.*

[Overview](#-overview) · [LoRA Adapters](#-lora-adapter-improvements) · [Benchmark](#-multimodel-benchmark) · [API Costs](#-api-cost-record) · [Codenames](#️-codename-reference) · [BaiLan](#-bailan-human-evaluation)

</div>

---

## 🎯 Overview

Northern Thai (Lanna / ᨣᩴᩤᨾᩮᩬᩥᨦ, ISO `nort2740`) is spoken across eight provinces of Northern Thailand by millions of speakers. Despite this, it is severely under-represented in modern NLP benchmarks — the models that handle Standard Thai with ease routinely stumble on Northern Thai vocabulary, particles, and tonal patterns.

The study uses a 3-prompt robustness protocol on a held-out slice of naturalistic conversational text, evaluating 9 models including Typhoon2, SeaLLM, LLaMA-3.1-8B, Qwen2.5, and a three-provider API cohort (OpenAI / Anthropic / DeepSeek). LoRA fine-tuning is scoped to Typhoon2 using QLoRA NF4 4-bit.

---

## 📈 LoRA Adapter Improvements

Measured on a held-out slice of naturalistic conversational text · NTD → STD · Δ ChrF over base model

| Iteration | Date | Δ ChrF | Notes |
|-----------|------|:------:|-------|
| Base model | — | 0% | Off-the-shelf Thai LLM; handles STD well, struggles with NTD vocabulary and particles |
| 🔸 Adapter v1 | 2026-05-08 | **+13%** | Lightweight adaptation; model begins recognising NTD as NTD |
| 🔹 [Adapter v2](https://huggingface.co/aminomewza/typhoon2-lora-v2) | 2026-05-18 | **+20%** | Retrained on refined dataset; recovers repetition loops and dropped dialect particles |

The +20% figure is **relative ChrF improvement** over the base model.

---

## 📊 Multimodel Benchmark

API cohort · v1 → v2 prompt template comparison · all scores are `chrf_avg`

v2 prompt is redesigned to include a clearer boundary marking the NTD sentence and a more specific imperative instruction.

**Translation quality (`chrf_avg`)**

| Model | v1 | v2 | Δ |
|-------|:--:|:--:|:-:|
| gpt-4o | 70.94 | 70.18 | −0.75 |
| claude-sonnet | 67.34 | 68.22 | +0.88 |
| deepseek (v4-flash, alias) | 68.94 | **72.39** | ⭐ **+3.45** ← leader |
| typhoon-s-thaillm | 47.34 | 51.34 | +4.01 |

---

## 💰 API Cost Record

**Period:** 2026-05-13 → 2026-05-16 · 3 providers · ~3,990 API calls

| Provider | Total | Share |
|----------|------:|------:|
| OpenAI | $4.16 | 61% |
| Anthropic | $2.55 | 37% |
| DeepSeek | $0.096 | 1.4% |
| **Grand total** | **$6.81** | |

### Per-day, per-model breakdown

| Date | Provider | Model | Req | In tok | Out tok | Cost |
|------|----------|-------|----:|-------:|--------:|-----:|
| 05-13 | OpenAI | gpt-4o-2024-11-20 | 397 | 74,639 | 10,359 | $0.2902 |
| 05-13 | Anthropic | claude-sonnet-4-5 | 397 | 159,354 | 23,807 | $0.8400 |
| 05-13 | DeepSeek | deepseek-v4-flash | 397 | 65,337 | 11,221 | $0.0105 |
| 05-15 | OpenAI | gpt-4o-2024-11-20 | 397 | 79,270 | 10,018 | $0.3076 |
| 05-15 | OpenAI | gpt-5.5-2026-04-23 *(smoke ×2)* | 2 | 364 | 248 | ~$0.00 |
| 05-15 | Anthropic | claude-sonnet-4-5 | 397 | 168,550 | 22,501 | $0.8500 |
| 05-15 | Anthropic | claude-sonnet-4-6 *(smoke ×1)* | 1 | 396 | 10 | ~$0.00 |
| 05-15 | DeepSeek | deepseek-v4-flash | 396 | 68,482 | 9,423 | $0.0107 |
| 05-16 | OpenAI | gpt-5.5-2026-04-23 🔺 | 401 | 79,768 | **105,593** | **$3.5666** |
| 05-16 | Anthropic | claude-sonnet-4-6 | 397 | 168,946 | 23,115 | $0.8600 |
| 05-16 | DeepSeek | deepseek-v4-flash *(explicit pin)* 🔺 | 398 | 68,812 | **255,540** | $0.0743 |

### Key findings

> 🔺 **GPT-5.5 hidden-reasoning tax (05-16).** Output tokens spiked to 105,593 on gpt-5.5 vs ~10k for gpt-4o on the same task — that's ~263 tok/req vs ~26, an **11.7× cost lift**. The billing API counts reasoning tokens in output. Budget planning using GPT-4o-era estimates underestimates GPT-5 cost by ~10×; pre-budget at ~$0.009 per call for any GPT-5 family member.

> 🔺 **DeepSeek alias vs explicit pin (05-16).** Switching from the floating `deepseek-chat` alias to the pinned `deepseek-v4-flash` produced 26× more output tokens (255,540 / 398 = 642 tok/req vs ~24 on alias runs). Same prompt template, same dataset. The alias may have been routing to a terser snapshot, or the explicit pin enables thinking/reasoning by default.

> ✅ **DeepSeek cache efficiency jumped 4× under the explicit pin (05-16).** Cache hit rate improved from ~17–20% (alias runs) to **73%** (50,432 cache-hit / 18,380 cache-miss). Even at 642 tok/req, DeepSeek was 47× cheaper per call than gpt-4o.

**Cost rule of thumb · per API call:**
GPT-5 flagship ≈ 11× GPT-4o ≈ 4× Sonnet ≈ 50× DeepSeek-flash (explicit pin) ≈ 330× DeepSeek-flash (alias). Anthropic pricing is the most predictable: all four Sonnet runs landed within $0.84–$0.86 with no reasoning surprise. For floating-alias provider lines, the billing CSV is the ground truth.

---

## 🏛️ Codename Reference

Internally, the codebase uses **Northern Thai cultural terms** as codenames for its components. The 28-item system maps one-to-one to software modules. The codenames are both a stylistic choice and a way of carrying the culture into the engineering layer of the project.

The monorepo is structured as four uv-workspace Python packages:

| Package | Role |
|---------|------|
| `lanna_khuang` | Data |
| `lanna_kuafai` | Adaptation |
| `lanna_jorfa` | Evaluation |
| `oob_meta` | Shared utilities |

→ [**Lanna Codenames (v2)**](https://n-sanitdee.github.io/northern-thai-llm-site/lanna-codenames-v2.html) — each entry shows the cultural term and how it maps to a module or concept in the codebase.

---

## 📋 BaiLan Human Evaluation

**BaiLan** is the human-rating instrument for LannaBench. Raters evaluate model translations on **six axes** (1–5 Likert scale each) in a card-by-card quiz flow with streak counters, bilingual Thai/English content, and Lanna temple aesthetics.

The form shows one prompt output per card, split into natural context dependent (NCD) and natural context independent (NCI). It is a self-contained HTML file that submits via Google Apps Script with a JSONL backup download fallback.

---

*An ongoing benchmark and adaptation study for **Kham Mueang** — Northern Thai, the Lanna Dialect.*
`nort2740 · ISO 639-3 · nod`

> The full code repository will be opened once the project is ready to publish.
