# nusantara-clip-retrieval

**Bilingual image ↔ text retrieval for Indonesian culture, built on frozen CLIP encoders, CPU only.**

Given a photo of something from Indonesian culture (food, dance, architecture, textiles, …), find its caption. Or, given a caption, find the photo. Captions are in **Indonesian (ID)** and **English (EN)**. The central question is the **language gap**: how much worse retrieval is when you search in Indonesian instead of English, and how far small trainable layers on top of frozen encoders can close it.

> 🇮🇩 Ringkasan bahasa Indonesia ada di [bagian bawah](#ringkasan-bahasa-indonesia).

---

## Status

| Milestone | Folder | What it does | Status |
|---|---|---|---|
| M1: Baseline | `m1_baseline/` | Frozen CLIP encoders, 4 retrieval directions, Recall@K / mAP@10, language gap with bootstrap CI | in progress |
| M2: Adapter | `m2_adapter/` | Small trainable adapter on the Indonesian text side, dev split from train | planned |
| M3: Hard cases | `m3_kasus_sulit/` | Stress test on negation, relations, attributes and cultural markers | planned |
| M4: Protocol | `m4_protokol/` | Evaluation protocol and reproduction script | planned |

Results are added here only after the milestone notebook reproduces the original numbers exactly (each notebook ends with a verification cell).

---

## Task

- **Data:** SEA-VL Indonesian subset (2072 train / 275 validation image–caption pairs, each with an ID and an EN caption).
- **Four directions:** image → ID text, image → EN text, ID text → image, EN text → image.
- **Metrics:** Recall@1/5/10 and mAP@10 on the validation split.
- **Constraints:**
  - Pretrained encoders stay **frozen**. Only small layers on top of them may be trained, and nothing is trained from scratch.
  - No extra data.
  - Everything runs on **CPU** and is reproducible.
  - Hyperparameters and examples come from the training split only. Validation is used for reporting only.

## Repository layout

```
nusantara-clip-retrieval/
├── README.md
├── requirements.txt
├── common/                 # shared pipeline code (data loading, encoders, cache, scoring)
├── m1_baseline/            # notebook + README + aggregate results per milestone
├── m2_adapter/
├── m3_kasus_sulit/
├── m4_protokol/
└── docs/
    ├── belajar/            # study notes per milestone (Indonesian)
    └── keputusan.md        # decision log with reasons (Indonesian)
```

## Setup

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# the dataset is not included: point data/ to your copy of it (a symlink is enough)
ln -s /path/to/sea-vl-indonesian data      # must contain train.csv, val.csv and images/
```

The code uses only paths relative to the repository (`data/`, `cache/`). Both are git-ignored. The device is chosen automatically: GPU if available, otherwise CPU. The original results were computed on CPU, so a GPU run may differ slightly in the last digits. Each milestone notebook reports such differences in its final verification cell.

The encoders are downloaded from Hugging Face on first run.

Reference environment: Linux x86_64, Python 3.14.4, torch 2.13.0, sentence-transformers 6.3.0. A clean install from `requirements.txt` has not been tested yet.

## Results

*Added per milestone after verification.*

## Provenance and contributions

This repository reorganises my work on a **five-person university group project** on multimodal machine learning (2026). I have re-run each milestone step by step, so the code here produces the same numbers as the version my group submitted.

- My official role in the team was **baseline and pipeline**: data loading, frozen-encoder embeddings and caching, evaluation metrics, and reproducibility.
- Other parts of the original project were assigned to teammates. Where their work is used here, it is credited.
- Development was assisted by an AI assistant (Claude). I reviewed, ran and verified every result myself.

## Data and attribution

**SEA-VL** Indonesian subset, KORIKA-AI / SEACrowd. Cahyawijaya et al., *SEA-VL: A Multicultural Vision-Language Dataset for Southeast Asia*, ACL 2025. Licensed under **CC BY-SA 4.0** (https://creativecommons.org/licenses/by-sa/4.0/).

Encoders: OpenAI CLIP ViT-B/32 and `clip-ViT-B-32-multilingual-v1`, both via sentence-transformers.

## License

Code license to be decided. Any material derived from the dataset follows CC BY-SA 4.0.

---

## Ringkasan bahasa Indonesia

Repo ini berisi sistem **pencarian gambar ↔ teks** untuk konten budaya Indonesia dalam dua bahasa (Indonesia dan Inggris). Encoder pra-latih (CLIP) **dibekukan**, dan semua proses berjalan di **CPU**. Fokus utamanya adalah ***language gap***: seberapa jauh kinerja pencarian dengan bahasa Indonesia tertinggal dari bahasa Inggris, dan seberapa jauh lapisan kecil yang dilatih di atas encoder beku bisa menutup selisih itu.

Repo ini disusun ulang dari proyek kelompok mata kuliah (5 orang). Peran resmi saya adalah **baseline dan pipeline**. Setiap milestone dijalankan ulang langkah demi langkah dan diverifikasi menghasilkan angka yang **identik** dengan versi yang dikumpulkan. Catatan belajar per milestone ada di `docs/belajar/`, dan alasan tiap keputusan ada di `docs/keputusan.md`. Pengerjaan dibantu asisten AI (Claude).
