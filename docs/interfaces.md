# Interfaces

The contracts between customers, contributors, the cloud platform and the backend node. Field-level formats are in `schemas/`.
How the engine computes skeletons and gaits is proprietary and not described here.

## 1. Customer API (cloud)

| method | route | body / result |
|---|---|---|
| `POST` | `/api/orders` | body: an order (`schemas/order.schema.json`) → `{ "order_id", "status": "queued" }` |
| `GET` | `/api/orders/{order_id}` | `{ "order_id", "status": "queued" \| "generating" \| "ready" \| "failed", "dataset_id"? }` |
| `GET` | `/api/datasets/{dataset_id}` | the dataset manifest (`schemas/dataset-manifest.schema.json`) with time-limited download links |

Authentication: an API key per customer. Downloads are served from cloud storage, not from the backend node.

## 2. Contribution API (cloud)

| method | route | body / result |
|---|---|---|
| `POST` | `/api/contributions` | a partial species record (fields of `schemas/species-skeleton.schema.json`) plus optional photos or sketches → `{ "record_id", "status": "raw" }` |

Contributors accept terms that allow commercial use before submitting. Contributions are stored as raw data and wait for review.

## 3. Node API (backend ↔ cloud, outbound only)

The mini PC never accepts inbound connections. It polls the cloud for work and pushes results back.

| method | route | body / result |
|---|---|---|
| `POST` | `/api/node/jobs/claim` | `{ "node_id", "capabilities": ["extract", "review", "generate"] }` → one job or `204 No Content` |
| `POST` | `/api/node/jobs/{job_id}/result` | the job's output (below) → `{ "ok": true }` |
| `POST` | `/api/node/heartbeat` | `{ "node_id", "load" }` |

Authentication: a node token. A claimed job that gets no result within its lease returns to the queue.

| job type | input | output | runs on |
|---|---|---|---|
| `extract` | a public source (URL and licence) | raw species records; the source media is discarded after processing | UGen300 |
| `review` | a batch of raw record ids | one review decision per record (below) | UGen300 |
| `generate` | an order and its verified seed record ids | dataset files uploaded to cloud storage, plus the manifest | CPU |

## 4. Record lifecycle

```
raw  ──(AGENT REVIEW: pass)──▶  verified  ──(used by an order)──▶  seed of a dataset
 └───(fail)──▶  returned to the source with reasons
```

Review decision:

```json
{
  "record_id": "r_123",
  "decision": "pass",
  "fixes": [{ "field": "femur_length_m", "from": "21 cm", "to": 0.21 }],
  "flags": [],
  "reasons": []
}
```

A `fail` decision lists `reasons` (for example: no source, unit unclear, value outside the range of related species).

## 5. Licences on data

- Extraction only accepts sources under CC0 or CC BY; non-commercial records are skipped.
- Every value in a verified record keeps its source id and grade.
- Delivered datasets are licensed to the customer under the order's agreement.
