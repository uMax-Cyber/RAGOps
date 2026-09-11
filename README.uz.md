<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>

# LightRAG ekspluatatsiya qoʻllanmasi

[![CI](https://github.com/uMax-Cyber/RAGOps/actions/workflows/ci.yml/badge.svg)](https://github.com/uMax-Cyber/RAGOps/actions/workflows/ci.yml)


![Namoyish](screenshots/demo.svg)
LightRAG (graph-RAG serveri) ni AI agentning uzoq muddatli xotirasi sifatida ishlab chiqarishda joylashtirish boʻyicha qoʻllanma: oʻrnatish, modellarni sozlash, API naqshlari, tipik nosozliklar va yangilash tartiblari.

## Arxitektura

```
AI Agent ──▶ LightRAG Serveri (9621-port)
                ├── Graf saqlash (NetworkX)
                ├── Vektor saqlash (NanoVectorDB)
                └── LLM + embedding (OpenAI-mos proxy orqali)
```

## Ishlab chiqarishdan asosiy saboqlar

### 1. Versiya yangilanishi deadlock ni tuzatishi mumkin
LightRAG v1.4.16 da konveyer deadlock mavjud edi: asinxron qulf oqishlari tufayli yuklash osilib qolardi, `pipeline_busy` abadiy `true` holatida qolib ketardi. v1.5.7 ga yangilash uni toʻliq tuzatdi.

### 2. Versiyalar orasidagi API oʻzgarishlari
| Versiya | Hujjatlar roʻyxati | Hujjatni oʻchirish |
|---------|---------------|-----------------|
| 1.4.x | `GET /documents` | `POST /documents/delete_document` |
| 1.5.x | `POST /documents/paginated` | `DELETE /documents/delete_document` bilan `{"doc_ids": [...]}` |

### 3. Soʻrov rejimlari muhim
- `mix`: katta indekslangan korpus bilan umumiy soʻrovlar uchun eng yaxshisi
- `local`: qisqa/bitta boʻlakli hujjatlar uchun yaxshiroq (mix bular uchun boʻsh qaytaradi!)
- `naive`: oddiy vektor qidiruv, grafsiz — har doim biror narsa qaytaradi

### 4. Embedding modellari mosligi
Embedding modelini almashtirish vektor oʻlchamlari mosligini tekshirishni talab qiladi. Bir xil oʻlchov ≠ moslik — turli modellarning vektorlari turli fazolarda yashaydi.

## Oʻrnatish

```bash
pip install lightrag-hku==1.5.7
# .env faylini LLM_BINDING_HOST, EMBEDDING_BINDING_HOST bilan sozlang
# Serverni ishga tushiring
lightrag-server --host 0.0.0.0 --port 9621
```

## API qisqa maʼlumotnoma

```bash
# Hujjat kiritish (fon rejimida qayta ishlash)
curl -X POST http://localhost:9621/documents/text \
  -H "Content-Type: application/json" \
  -d '{"text": "your fact here", "file_source": "my-source"}'

# Soʻrov (qisqa hujjatlar uchun local rejimi)
curl -X POST http://localhost:9621/query \
  -H "Content-Type: application/json" \
  -d '{"query": "your question", "mode": "local", "top_k": 20}'

# Hujjatlar holatini tekshirish
curl -X POST http://localhost:9621/documents/paginated \
  -H "Content-Type: application/json" -d '{}'

# Kiritish jarayonini kuzatish
curl http://localhost:9621/documents/track_status/<track_id>
```

## Litsenziya
MIT

## 📬 Aloqa

Savollaringiz bormi? Yozing: **[allumaxmail@gmail.com](mailto:allumaxmail@gmail.com)**

---

<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>
