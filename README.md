# LannaBench

<div align="center">

![license](https://img.shields.io/badge/license-MIT-blue)
![status](https://img.shields.io/badge/status-in%20progress-orange)
![languages](https://img.shields.io/badge/languages-NTD%20↔%20STD-green)
![adapters](https://img.shields.io/badge/LoRA%20adapters-v2%20%2B20%25%20ChrF-brightgreen)

*Evaluating large language models on Northern Thai ↔ Standard Thai translation — with multi-reference scoring, error typology, and human ratings*

[Overview](#-overview) • [Progress](#-progress) • [Codenames](#️-codename-reference) • [BaiLan](#-bailan-human-evaluation) • [Status](#-project-status)

</div>

-----

## 🎯 Overview

This repository is the **public companion site** for **LannaBench** — a research benchmark evaluating how well frontier and open-weight LLMs handle translation between Northern Thai and Standard Thai in both directions.

The main codebase is private while the project is in progress. This repo hosts public-facing reference pages, benchmark results, and documentation.

### Why Northern Thai?

Northern Thai (*kam mueang* / ᨣᩴᩤᨾᩮᩬᩥᨦ, ISO `nort2740`) is spoken across eight provinces of Northern Thailand by millions of speakers. Despite this, it is **severely under-represented in modern NLP benchmarks** — most Thai-language models treat it as noise or Standard Thai (`thai1261`) with unusual orthography.

LannaBench is a small step toward changing that.

-----

## 📈 Progress

### LoRA Adapter Improvements (NTD → STD)

Measured on a held-out slice of naturalistic conversational text:

|Iteration   |Date      |Δ ChrF over base|Notes                                                                                |
|------------|----------|:--------------:|-------------------------------------------------------------------------------------|
|Base model  |—         |0%              |Off-the-shelf Thai LLM; handles STD well, struggles with NTD vocabulary and particles|
|🔸 Adapter v1|2026-05-08|**+13%**        |Lightweight adaptation; model begins recognising NTD as NTD                          |
|🔹 [Adapter v2](https://huggingface.co/aminomewza/typhoon2-lora-v2)|2026-05-18|**+20%**        |Retrained on refined dataset; recovers repetition loops and dropped dialect particles|


> The +20% figure is **relative ChrF improvement** over the base model.

-----

### Multimodel Benchmark — API Cohort (v1 → v2 Prompt Template)

All scores are `chrf_avg`; lower `chrf_diff` = less prompt fragility.

**Translation quality (`chrf_avg`)**

|Model                        |v1   |v2   |Δ                   |
|-----------------------------|:---:|:---:|:------------------:|
|gpt-4o                       |70.94|70.18|−0.75               |
|claude-sonnet                |67.34|68.22|+0.88               |
|deepseek (v4-flash via alias)|68.94|72.39|⭐ **+3.45** ← leader|
|typhoon-s-thaillm            |47.34|51.34|+4.01               |

-----

### 💰 API Cost Record

**Period:** 2026-05-13 to 2026-05-16 · 3 providers

|Date     |OpenAI             |Anthropic           |DeepSeek  |Day Total|
|---------|------------------:|-------------------:|---------:|--------:|
|05-13    |$0.29 *(gpt-4o)*   |$0.84 *(sonnet-4-5)*|$0.011    |$1.14    |
|05-15    |$0.31 *(gpt-4o)*   |$0.85 *(sonnet-4-5)*|$0.011    |$1.17    |
|05-16    |$3.57 *(gpt-5.5)* 🔺|$0.86 *(sonnet-4-6)*|$0.074    |**$4.50**|
|**Total**|**$4.16**          |**$2.55**           |**$0.096**|**$6.81**|


> 🔺 The 05-16 spike is GPT-5.5 billing hidden reasoning tokens (~263 tok/req vs ~26 for gpt-4o — an **11.7× cost lift**). DeepSeek’s cost also jumped 7× that day due to 26× more output tokens under the explicit `deepseek-v4-flash` pin vs the prior `deepseek-chat` alias.

-----

## 🏛️ Codename Reference

Internally, the codebase uses **Northern Thai cultural objects** as codenames for its components — stove (*Ang Lo*), serving tray (*Khan Tok*), cosmology charts (*Trai Phum*), and so on.

The codenames are both a stylistic choice and a small way of **carrying the culture into the engineering layer** of the project.

### → [Lanna Codenames (v2)](https://n-sanitdee.github.io/northern-thai-llm-site/lanna-codenames-v2.html)

Each entry shows the literal object, its cultural role, and how the codename maps to a module or concept in the codebase.

-----

## 📋 BaiLan Human Evaluation

**BaiLan** is the human-rating instrument for LannaBench. Raters evaluate model translations on **six axes** (1–5 Likert scale each):

The form is a self-contained HTML instrument that submits via Google Apps Script with a JSONL backup download fallback.

-----

The full code repository will be opened once the project is ready to publish.
