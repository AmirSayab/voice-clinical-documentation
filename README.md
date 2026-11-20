# Voice Clinical Documentation

> A voice-to-documentation system combining Whisper transcription, structured note generation, multilingual triage, and ICD-10-aware human review.

![Python](https://img.shields.io/badge/Python-3.10+-blue) ![Whisper](https://img.shields.io/badge/Whisper-large--v2-red) ![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-green) ![License](https://img.shields.io/badge/License-Private-gray)

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      CAPTURE LAYER                           │
│  Browser mic (VAD) │ File upload │ WebSocket (real-time)    │
└────────────────────────────┬────────────────────────────────┘
                             │ audio stream
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                    TRANSCRIPTION                             │
│  faster-whisper (large-v2) │ Quality filter │ Session ctrl  │
│  GPU/CPU selection         │ 6-layer pipeline│ Concurrency  │
└────────────────────────────┬────────────────────────────────┘
                             │ transcript
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                     CLINICAL AI                              │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Triage       │  │ Note Gen     │  │ ICD-10       │      │
│  │ (OPQRST /   │  │ (SOAP /      │  │ Validator    │      │
│  │  SAMPLE)     │  │  Summary /   │  │ (2026 codes) │      │
│  │              │  │  RSNA-style) │  │              │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                              │
│  ┌──────────────┐                                            │
│  │ Coding       │  ◄── Radiology-specific ICD-10            │
│  │ Engine       │                                            │
│  └──────────────┘                                            │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                   ENTERPRISE BACKEND                         │
│  FastAPI v2 │ PostgreSQL │ Redis │ LLM provider (Ollama)   │
│  (modular)  │ (Alembic)  │ (coord)│                        │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                    REVIEW + OUTPUT                            │
│  React client │ Triage report │ Multilingual (EN/FR/AR)     │
│  (encounter)  │ (ESI levels)  │                              │
└─────────────────────────────────────────────────────────────┘
```

## Problem

Clinical documentation systems must connect several uncertain components: audio capture, transcription, structured generation, coding assistance, and review. A good demo can hide those seams; a useful system must make them observable and replaceable.

## Approach

- **Transcription**: faster-whisper with GPU/CPU selection, session controls, health checks
- **Clinical AI**: LLM-powered note generation (3 formats: SOAP, Summary, RSNA-style) with ICD-10 validation
- **Triage**: 20+ symptom pathways with ESI severity scoring (OPQRST/SAMPLE frameworks)
- **Architecture v2**: Modular FastAPI services with PostgreSQL (Alembic migrations), Redis coordination, and swappable LLM providers

## Evidence

| Metric | Value | Context |
|--------|-------|---------|
| Languages | **3** | English, French, Arabic paths |
| Note formats | **3** | SOAP, summary, RSNA-style |
| Architecture generations | **2** | Prototype → modular backend |

## Honest Maturity

An internal engineering audit assessed the system as **65% complete** and **5/10 production ready**. The newer backend addresses structural gaps, but the repository does not contain a publishable WER study, clinical accuracy study, or production load report.

## Tech Stack

- **Speech**: faster-whisper (large-v2), WebSocket streaming
- **Backend**: FastAPI, PostgreSQL, Redis, Alembic
- **AI**: Ollama (120B), ICD-10 2026 dataset
- **Frontend**: React (encounter management UI)

## What I Would Improve

- Publish WER/CER by language, accent, and noise condition
- Measure first-token and end-to-end p50/p95 latency
- Evaluate note completeness with blinded reviewers
- Formalize retention, encryption, and access controls

## Disclosure

No patient audio, transcripts, or clinical claims are published. All demo narratives use synthetic encounters. Built at Nixaam LLC.

---

**Portfolio**: [amir-sayab-portfolio.vercel.app](https://amir-sayab-portfolio.vercel.app/work/voice-clinical-documentation/) · **LinkedIn**: [linkedin.com/in/amir-sayab](https://www.linkedin.com/in/amir-sayab/)
