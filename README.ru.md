<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>

# Руководство по эксплуатации LightRAG

[![CI](https://github.com/uMax-Cyber/RAGOps/actions/workflows/ci.yml/badge.svg)](https://github.com/uMax-Cyber/RAGOps/actions/workflows/ci.yml)


![Демонстрация](screenshots/demo.svg)
Руководство по промышленному развёртыванию LightRAG (graph-RAG-сервер) в качестве долгосрочной памяти ИИ-агента: установка, конфигурация моделей, паттерны работы с API, типовые сбои и процедуры обновления.

## Архитектура

```
ИИ-агент ──▶ LightRAG Server (порт 9621)
                ├── Графовое хранилище (NetworkX)
                ├── Векторное хранилище (NanoVectorDB)
                └── LLM + эмбеддинги (через OpenAI-совместимый прокси)
```

## Ключевые уроки из продакшена

### 1. Обновления версий могут исправлять дедлоки
В LightRAG v1.4.16 был дедлок конвейера: загрузка зависала из-за утечек асинхронных блокировок, `pipeline_busy` навсегда застревал в `true`. Обновление до v1.5.7 полностью это исправило.

### 2. Изменения API между версиями
| Версия | Список документов | Удаление документа |
|---------|---------------|-----------------|
| 1.4.x | `GET /documents` | `POST /documents/delete_document` |
| 1.5.x | `POST /documents/paginated` | `DELETE /documents/delete_document` с `{"doc_ids": [...]}` |

### 3. Режимы запросов имеют значение
- `mix`: лучше всего для общих запросов при большом проиндексированном корпусе
- `local`: лучше для коротких/одночастевых документов (mix для них возвращает пустоту!)
- `naive`: обычный векторный поиск, без графа — всегда что-то возвращает

### 4. Совместимость моделей эмбеддингов
Смена модели эмбеддингов требует проверки совместимости размерности векторов. Одинаковая размерность ≠ совместимость — векторы разных моделей живут в разных пространствах.

## Установка

```bash
pip install lightrag-hku==1.5.7
# Настроить .env с LLM_BINDING_HOST, EMBEDDING_BINDING_HOST
# Запустить сервер
lightrag-server --host 0.0.0.0 --port 9621
```

## Краткая справка по API

```bash
# Вставка документа (фоновая обработка)
curl -X POST http://localhost:9621/documents/text \
  -H "Content-Type: application/json" \
  -d '{"text": "your fact here", "file_source": "my-source"}'

# Запрос (режим local для коротких документов)
curl -X POST http://localhost:9621/query \
  -H "Content-Type: application/json" \
  -d '{"query": "your question", "mode": "local", "top_k": 20}'

# Проверка статуса документов
curl -X POST http://localhost:9621/documents/paginated \
  -H "Content-Type: application/json" -d '{}'

# Отслеживание прогресса вставки
curl http://localhost:9621/documents/track_status/<track_id>
```

## Лицензия
MIT

## 📬 Контакты

Вопросы? Пишите: **[allumaxmail@gmail.com](mailto:allumaxmail@gmail.com)**

---

<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>
