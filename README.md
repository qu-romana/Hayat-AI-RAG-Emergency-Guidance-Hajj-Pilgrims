<div align="center">

<h1>💚 HAYAT AI</h1>
<h3>Emergency Medical Guidance System for Hajj Pilgrims</h3>
<p>
A Retrieval-Augmented Generation (RAG) system providing immediate, verified emergency medical guidance for Hajj pilgrims.<br/>
Built with <b>sentence-transformers</b> + <b>FAISS</b> + <b>Mistral-7B-Instruct</b>.
</p>

![Python](https://img.shields.io/badge/Python-3.10-blue)
![FAISS](https://img.shields.io/badge/FAISS-Cosine%20Similarity-green)
![Mistral](https://img.shields.io/badge/Mistral-7B--Instruct-purple)
![Gradio](https://img.shields.io/badge/Gradio-Interface-orange)
![Accuracy](https://img.shields.io/badge/Retrieval%20Accuracy-84.6%25-brightgreen)
![Protocols](https://img.shields.io/badge/Protocols-17%20Verified-red)

</div>

---

> ⚠️ **Disclaimer:** Hayat AI is designed for guidance only and does not replace emergency medical services. Always call **937** (Pilgrims Call Center) or **977** (Saudi Red Crescent) first in a life-threatening emergency.

---

## 🚀 Live Demo

**Try Hayat AI instantly - no setup required:**

👉 [**Launch Hayat AI**](https://huggingface.co/spaces/RomanaQureshi/Hayat-AI-Hajj-Medical-Emergency-Guidance)

> Live demo uses Groq API. Notebook version uses Mistral-7B-Instruct locally.

---


## 📋 Table of Contents

- [Overview](#overview)
- [Why Hayat AI](#why-hayat-ai)
- [System Architecture](#system-architecture)
- [Knowledge Base](#knowledge-base)
- [Demo](#demo)
- [Clarification Dialogue](#clarification-dialogue)
- [Evaluation](#evaluation)
- [Installation](#installation)
- [Sources](#sources)
- [Future Work](#future-work)
- [Author](#author)

---

## Overview

Hayat AI is a domain-specific RAG system built for Hajj pilgrims and their companions. It identifies emergency medical conditions from natural language descriptions and provides immediate, step-by-step guidance grounded in peer-reviewed medical research.

The system covers **17 emergency protocols** including heat stroke, heart attack, severe dehydration, crush injuries, fractures, hypoglycemia, asthma attacks, and more - all conditions documented in published Hajj medical literature.

---

## Why Hayat AI

Every year over **2 million pilgrims** from 180+ countries gather in Makkah for Hajj. Medical emergencies are common:

- Temperatures exceed **55°C** in Arafat during summer Hajj (Rahman et al., 2017)
- Infectious diseases account for **53.26%** of all medical cases (Khan et al., 2018)
- Cardiovascular disease causes **45.8%** of deaths (Al Shimemeri, 2012)
- Crowd densities reach **12 people per square metre** near the Kaaba (Rahman et al., 2017)
- **49.3%** of pilgrims walk in direct sunlight instead of using available transport (Samarkandi et al., 2025)
- **51.7%** of pilgrims do not take prescribed medications during Hajj (Samarkandi et al., 2025)

In an emergency, pilgrims and their companions need immediate guidance in plain language. Hayat AI fills this gap.

---

## System Architecture

```
User Query (plain language emergency description)
        ↓
Input Validation (emergency keyword check)
        ↓
Sentence Embedding (all-MiniLM-L6-v2)
        ↓
FAISS Cosine Similarity Search (IndexFlatIP + normalized vectors)
        ↓
Score Difference Check
   ├── Confident match (diff > 0.06) → Generate Response directly
   └── Ambiguous (diff ≤ 0.06) → Clarification Dialogue
                                        ↓
                               Radio Button Question shown
                                        ↓
                               User selects one option
                                        ↓
                               Correct protocol selected
        ↓
Mistral-7B-Instruct (4-bit NF4 quantized) generates structured guidance
        ↓
Emergency Guidance with actions, DO NOTs, escalation signs, emergency contacts
```

**Key technical decisions:**
- **Cosine similarity** (IndexFlatIP + L2 normalization) instead of L2 distance for better semantic matching
- **Enriched document text** includes symptoms, keywords, risk factors and distinguishing features per protocol
- **Clarification dialogue** triggered when top-2 cosine scores differ by less than 0.06
- **4-bit NF4 quantization** allows Mistral-7B to run on free Google Colab GPU

---

## Knowledge Base

17 emergency protocols grounded in 5 peer-reviewed papers and official Saudi Ministry of Health guidelines:

| Category | Protocols |
|----------|-----------|
| Heat-related | Heat Stroke, Heat Exhaustion |
| Cardiovascular | Heart Attack (AMI) |
| Respiratory | Severe Pneumonia, Acute Asthma Attack, COPD Exacerbation |
| Metabolic | Hypoglycemia, Severe Dehydration |
| Trauma | Fracture, Ankle Sprain, Crush Injuries |
| Neurological | Fainting / Syncope |
| Gastrointestinal | Acute Gastroenteritis |
| ENT | Nosebleed (Epistaxis) |
| Skin & Musculoskeletal | Skin Irritation / Chafing, Muscle Cramps |
| Diabetic | Diabetic Foot Infection / Cellulitis |

Each protocol contains: description, symptoms, immediate actions, DO NOTs, escalation signs, risk factors, and distinguishing features for disambiguation.

---

## Demo

### Heat Stroke — LIFE-THREATENING
![Heat Stroke Demo](assets/hayat_demo.png)

### Severe Dehydration — URGENT
![Dehydration Demo](assets/hayat_demo3.png)

### Input Validation — Non-Emergency Query Rejected
![Validation Demo](assets/hayat_validation.png)

*The system detects non-emergency inputs and redirects to emergency contacts instead of giving irrelevant medical guidance.*

---

## Clarification Dialogue

A key innovation in Hayat AI is the **clarification dialogue system**. When the retrieval model detects two similar conditions with cosine scores within 0.06 of each other, instead of guessing it asks the user one targeted question via radio buttons.

This is critical for medical safety - wrong protocol identification in an emergency can cause harm. One extra question takes 3 seconds but ensures correct guidance.

### Step-by-step walkthrough

**Step 1 — User describes the emergency in plain language:**

![Clarification Question](assets/hayat_clarification_question.png)

**Step 2 — System detects ambiguity between two similar conditions:**

![Clarification Triggered](assets/hayat_clarification_triggered.png)

**Step 3 — Radio buttons appear with clear options — one tap, no typing:**

![Clarification Buttons](assets/hayat_clarification_buttons.png)

**Step 4a — User selects CROWD → Crush Injuries protocol correctly identified:**

![Crush Result](assets/hayat_clarification_crush_result.png)

![Crush Response](assets/hayat_clarification_crush_response.png)

**Step 4b — Same query, user selects FELL ALONE → Fracture protocol correctly identified:**

![Fracture Result](assets/hayat_clarification_fracture_result.png)

![Fracture Response](assets/hayat_clarification_fracture_response.png)

*The same ambiguous query produces two different correct answers based on one clarifying question.*

### Why this matters

| Ambiguous Pair | Clarifying Question | Why It Matters |
|----------------|---------------------|----------------|
| Dehydration vs Hypoglycemia | Is person diabetic? | Treatment is water vs sugar - opposite interventions |
| Fracture vs Crush Injuries | Fell alone or crowd? | Crush injuries risk crush syndrome - different emergency response |
| Pneumonia vs Asthma | High fever present? | One needs antibiotics, other needs inhaler |
| Heart Attack vs Fainting | Chest pain before collapse? | One is immediately life-threatening cardiac event |

---

## Evaluation

### Test Set

52 queries grounded strictly in peer-reviewed Hajj medical research. Every query traces back to a documented case, patient presentation, or clinical statistic from the 5 source papers. No hallucinated symptoms or invented scenarios.

**Query distribution across 17 protocols:**

| Protocol Group | Queries |
|----------------|---------|
| Heat-related | 5 |
| Cardiac | 5 |
| Respiratory | 7 |
| Metabolic | 7 |
| Trauma | 9 |
| Other | 19 |

### Accuracy Results

| Version | Change Made | Accuracy |
|---------|-------------|----------|
| Baseline | L2 index, basic enriched text | 75.0% (39/52) |
| v2 | Richer keywords + distinguishing features | 78.8% (41/52) |
| v3 | Cosine similarity — IndexFlatIP + normalization | **84.6% (44/52)** |
| v4 | Clarification dialogue — simulated upper bound* | 90.4% (47/52) |

*90.4% represents the upper bound assuming users correctly answer clarifying questions for ambiguous pairs. **84.6% is the verified baseline** without any clarification dialogue.

### Failure Analysis

The 8 remaining failures at 84.6% occur exclusively between semantically adjacent conditions that share overlapping symptom language:

- **Dehydration ↔ Hypoglycemia** — both cause weakness, dizziness, confusion
- **Dehydration ↔ Fainting** — dizziness when standing appears in both
- **Pneumonia ↔ COPD** — both cause breathing difficulty and productive cough
- **Fracture ↔ Crush Injuries** — both involve trauma and severe pain
- **Heart Attack ↔ Fainting** — both involve sudden collapse

This is a known limitation of single-label dense retrieval with general-purpose embedding models. The clarification dialogue directly addresses 3 of these 5 ambiguous pairs.

---

## Installation

```bash
# Clone the repository
git clone https://github.com/qu-romana/Hayat-AI-RAG-Emergency-Guidance-Hajj-Pilgrims.git
```

Open in Google Colab (recommended - free T4 GPU):
- `HayatAICode.ipynb` — base system
- `HayatAICode_clarification.ipynb` — with clarification dialogue feature

Run all cells in order. Mistral-7B loads in approximately 3-4 minutes with 4-bit quantization on free Colab T4.

**Requirements (auto-installed in Cell 2):**
```
sentence-transformers
faiss-cpu
transformers
bitsandbytes
gradio
torch
```

---

## Sources

All 17 emergency protocols are grounded in peer-reviewed research. Full citations in [SOURCES.md](SOURCES.md).

1. **Khan et al. (2018).** Morbidity and mortality amongst Indian Hajj pilgrims. *Journal of Infection and Public Health*, 11(2), 165–170. https://doi.org/10.1016/j.jiph.2017.06.004

2. **Al Shimemeri (2012).** Cardiovascular disease in Hajj pilgrims. *Journal of the Saudi Heart Association*, 24(2), 123–127. https://doi.org/10.1016/j.jsha.2012.02.004

3. **Samarkandi et al. (2025).** Health Risk Behaviors Among Hajj 2024 Pilgrims. *Risk Management and Healthcare Policy*, 18, 2233–2245. https://doi.org/10.2147/RMHP.S521097

4. **Hashim et al. (2016).** Respiratory illness among Malaysian pilgrims in 2013 Hajj season. *Journal of Travel Medicine*, 23(2), tav019. https://doi.org/10.1093/jtm/tav019

5. **Rahman et al. (2017).** Mass Gatherings and Public Health: Case Studies from the Hajj. *Annals of Global Health*, 83(2), 386–393. https://doi.org/10.1016/j.aogh.2016.12.001

6. **Saudi Ministry of Health / Nusuk.** Official Hajj Health Guidelines. https://hajj.nusuk.sa/nusuk/health/guidelines. Accessed January 2026.

---

## Future Work

- **Arabic language support** for Arab-speaking pilgrims
- **Medical-specific embeddings** (BioBERT/MedBERT) for better disambiguation of similar conditions
- **Multi-label retrieval** for mixed-symptom queries
- **Offline mode** for use without internet during Hajj
- **Formal clinical evaluation** with medical professionals
- **Expanded knowledge base** beyond 17 protocols

---

## Author

**Romana Qureshi**
MS Artificial Intelligence — King Saud University, Riyadh

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/romanaqureshi1613)
[![GitHub](https://img.shields.io/badge/GitHub-qu--romana-black)](https://github.com/qu-romana)

---

<div align="center">
<p>Built with 💚 for the safety of Hajj pilgrims worldwide</p>
<p>⚠️ For guidance only — always call 937 or 977 in a life-threatening emergency</p>
</div>
