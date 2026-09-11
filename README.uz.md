<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>

# LightRAG ekspluatatsiya qoʻllanmasi

[![CI](https://github.com/uMax-Cyber/RAGOps/actions/workflows/ci.yml/badge.svg)](https://github.com/uMax-Cyber/RAGOps/actions/workflows/ci.yml)


![Namoyish](screenshots/demo.svg)
LightRAG (graph-RAG server) ni AI agentning uzoq muddatli xotirasi sifatida productionda yuritish boʻyicha qoʻllanma: oʻrnatish, model sozlashlari, API bilan ishlash naqshlari, tez-tez uchraydigan nosozliklar va yangilash tartibi.

## Arxitektura

```
AI Agent ──▶ LightRAG serveri (9621-port)
                ├── Graf saqlash (NetworkX)
                ├── Vektor saqlash (NanoVectorDB)
                └── LLM + embedding (OpenAI-mos proxy orqali)
```

## Productiondan olingan asosiy saboqlar

### 1. Versiya yangilash deadlockni hal qilishi mumkin
LightRAG 1.4.16 versiyasida pipeline deadlock bor edi: ingest asinxron qulflar oqishi tufayli osilib qolardi, `pipeline_busy` abadiy `true` boʻlib qolaverardi. 1.5.7 ga oʻtgandan keyin muammo butunlay yoʻqoldi.

### 2. Versiyalar orasida API oʻzgaradi
| Versiya | Hujjatlar roʻyxati | Hujjatni oʻchirish |
|---------|---------------|-----------------|
| 1.4.x | `GET /documents` | `POST /documents/delete_document` |
| 1.5.x | `POST /documents/paginated` | `DELETE /documents/delete_document`, `{"doc_ids": [...]}` bilan |

### 3. Soʻrov rejimi muhim
- `mix`: katta indekslangan korpusdagi umumiy soʻrovlar uchun eng yaxshisi
- `local`: qisqa, bir boʻlakli hujjatlar uchun yaxshiroq (mix bunday hujjatlarga boʻsh natija qaytaradi!)
- `naive`: grafsiz oddiy vektor qidiruv — har doim biror narsa topadi

### 4. Embedding modeli mosligi
Embedding modelini almashtirishda vektor oʻlchamining mosligini tekshiring. Oʻlchami bir xil boʻlishi moslikni kafolatlamaydi — har xil modelning vektorlari turli fazoda yashaydi.

## Oʻrnatish

```bash
pip install lightrag-hku==1.5.7
# .env fayliga LLM_BINDING_HOST, EMBEDDING_BINDING_HOST ni yozing
# Serverni ishga tushiring
lightrag-server --host 0.0.0.0 --port 9621
```

## API qisqa maʼlumotnoma

```bash
# Hujjat kiritish (fon rejimida qayta ishlanadi)
curl -X POST http://localhost:9621/documents/text \
  -H "Content-Type: application/json" \
  -d '{"text": "your fact here", "file_source": "my-source"}'

# Soʻrov (qisqa hujjatlar uchun local rejimi)
curl -X POST http://localhost:9621/query \
  -H "Content-Type: application/json" \
  -d '{"query": "your question", "mode": "local", "top_k": 20}'

# Hujjatlar holatini koʻrish
curl -X POST http://localhost:9621/documents/paginated \
  -H "Content-Type: application/json" -d '{}'

# Kiritish jarayonini kuzatish
curl http://localhost:9621/documents/track_status/<track_id>
```

## Litsenziya
MIT

## 📬 Aloqa

Savol boʻlsa yozing: **[allumaxmail@gmail.com](mailto:allumaxmail@gmail.com)**

---

<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>
