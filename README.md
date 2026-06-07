# Information Retrieval Project — Genomics Document Retrieval

## Overview
Biomedical document retrieval system evaluated on the FIR-s05 TREC genomics dataset (~263,000 PubMed abstracts). Compares lexical retrieval (Elasticsearch) against dense neural re-ranking using three BERT variants, evaluated via standard TREC metrics.

## Pipeline

1. **Indexing** — Bulk-index the MEDLINE collection (`FIR-s05-medline.json`) into Elasticsearch (`genomics-base` index) over title (`TI`) and abstract (`AB`) fields
2. **Elasticsearch Retrieval** — Multi-match query search; top-1000 results written to TREC run format
3. **BERT Embedding** — Precompute and cache document embeddings (batch size 32/320) from title, abstract, and metadata fields; query embeddings cached separately
4. **BERT Re-ranking** — Cosine similarity between query and document embeddings; standalone BERT runs and hybrid ES+BERT re-ranking
5. **QRELS Mapping** — PMID-to-index remapping for compatibility between TREC qrels and Elasticsearch internal IDs
6. **Evaluation** — TREC-style metrics via `performance_evaluation.py`: success@k, precision@k, precision-recall curve, and MAP

## Models Compared

| Model | MAP |
|---|---|
| Elasticsearch (baseline) | 0.1116 |
| PubMed BERT | 0.003758 |
| Bio BERT | 0.003159 |
| Normal BERT | 0.002115 |

Elasticsearch substantially outperforms all BERT variants on this dataset, indicating matching keywords in the titel and abstract are more relevant than matching semantic / embeddings.
