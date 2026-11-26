# nusantara-clip-retrieval

**Bilingual image ↔ text retrieval for Indonesian culture, built on frozen CLIP encoders.**

Given a photo from Indonesian culture (food, dance, architecture, textiles, …), find its caption. Given a caption, find the photo. Captions come in **Indonesian (ID)** and **English (EN)**. The main question is the **language gap**: how much worse retrieval gets when searching in Indonesian instead of English, and how to close it.

> 🇮🇩 Ringkasan bahasa Indonesia ada di [bagian bawah](#ringkasan).

## Overview

| Stage | Notebook | Description | Status |
|---|---|---|---|
| M1: Baseline | `notebooks/m1_baseline.ipynb` | Frozen CLIP encoders, 4 retrieval directions, Recall@K / mAP@10, language gap | in progress |
| M2: Adapter | `notebooks/m2_adapter.ipynb` | Small trainable adapter on the Indonesian text side | planned |
| M3: Hard cases | `notebooks/m3_kasus_sulit.ipynb` | Negation, relations, attributes, cultural markers | planned |
| M4: Evaluation | `notebooks/m4_protokol.ipynb` | Evaluation protocol and reproduction script | planned |

## Task

- **Data:** SEA-VL Indonesian subset, 2072 training and 275 validation image–caption pairs. Each image has an ID and an EN caption.
- **Directions:** image → ID text, image → EN text, ID text → image, EN text → image.
- **Metrics:** Recall@1/5/10 and mAP@10 on the validation split.

## Approach

- Images and English captions are encoded with **CLIP ViT-B/32**. Indonesian captions are encoded with **clip-ViT-B-32-multilingual-v1**, which maps text into the same 512-dimensional space.
- The encoders are **frozen**. Retrieval is a cosine similarity between normalised embeddings.
- From M2 onward, a small adapter is trained on top of the Indonesian text embeddings to close the language gap.

## Project structure

```
nusantara-clip-retrieval/
├── pipeline/           # shared code: config, data, embedding, metrics
├── notebooks/          # one notebook per stage
├── results/            # result tables per stage
├── docs/               # write-ups, study notes, decision log
├── requirements.txt
└── README.md
```

## Setup

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# the dataset is not included: link your copy of it as data/
ln -s /path/to/sea-vl-indonesian data      # must contain train.csv, val.csv and images/
```

Then open the notebooks from the repository root. Encoders are downloaded from Hugging Face on first run, and the device is chosen automatically (GPU if available).

## Results

*Added per stage.*

## Background

This project started as a university group project. I rebuilt it step by step as a personal project, with help from an AI assistant.

## Data and attribution

**SEA-VL** Indonesian subset, KORIKA-AI / SEACrowd. Cahyawijaya et al., *SEA-VL: A Multicultural Vision-Language Dataset for Southeast Asia*, ACL 2025. Licensed under **CC BY-SA 4.0**. The dataset is not redistributed here.

Encoders: OpenAI CLIP ViT-B/32 and `clip-ViT-B-32-multilingual-v1`, via sentence-transformers.

---

## Ringkasan

Proyek ini membangun sistem **pencarian gambar ↔ teks** untuk konten budaya Indonesia dalam bahasa Indonesia dan Inggris. Encoder CLIP dipakai dalam keadaan beku. Fokus utamanya adalah ***language gap***: seberapa tertinggal pencarian dengan bahasa Indonesia dibanding bahasa Inggris, dan bagaimana cara menutupnya. Catatan belajar ada di `docs/belajar/`, dan alasan tiap keputusan ada di `docs/keputusan.md`.
