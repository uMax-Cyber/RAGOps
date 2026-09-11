# LightRAG Troubleshooting

## Pipeline Deadlock (v1.4.x)
**Symptom**: `pipeline_busy: true` forever, documents stuck in "processing"
**Fix**: Upgrade to v1.5.7. If stuck: force_reset endpoint (with confirm=true)

## Query Returns Empty
**Symptom**: Query returns "no context found" for data that was just inserted
**Cause 1**: Mode=mix doesn't work well with single-chunk documents → use mode=local
**Cause 2**: Document still processing (check track_status)
**Cause 3**: Embedding model mismatch (vectors in different spaces)

## Insert Timeout
**Symptom**: POST /documents/text times out
**Cause**: LLM extraction taking too long (large document + slow model)
**Fix**: Split large documents into smaller chunks; increase timeout; check LLM proxy health

## Memory Usage
LightRAG with NetworkX graph + NanoVectorDB loads everything into RAM.
For large graphs (>100k nodes), expect 4-6 GB memory usage at startup.
Plan VM sizing accordingly.
