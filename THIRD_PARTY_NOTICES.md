# Third-party notices

This repository contains documents and JSON schemas only (MIT, see `LICENSE`). **No model weights, code from third parties or
species data are included.** The LifeArk system described here uses or plans to use the following third-party components.

## AI models (run on the ASUS UGen300; weights not distributed)

| model | license | use |
|---|---|---|
| Qwen2-VL-2B-Instruct | Apache-2.0 | reads public animal images and sketches during extraction |
| Qwen2-1.5B-Instruct | Apache-2.0 | AGENT REVIEW: format and plausibility checks. The licence of the function-calling fine-tune benchmarked by ASUS will be confirmed before deployment. |

## Physics

| component | license | use |
|---|---|---|
| MuJoCo (Google DeepMind) | Apache-2.0 | cross-validation of the proprietary engine's results |

## Hardware and platform

| component | terms | use |
|---|---|---|
| ASUS UGen300 AI Accelerator (Hailo-10H) and its runtime | vendor terms | AI inference on the backend node |
| Cloudflare Workers, D1, R2 | Cloudflare terms | website, API, cloud database and storage |

## Data

| source type | accepted licences | note |
|---|---|---|
| public images, literature and open databases | CC0, CC BY | non-commercial (NC) records are excluded; source media is discarded after extraction |
| contributor submissions | contributor terms that allow commercial use | stored as raw data until reviewed |

The LifeArk name is not covered by any of the above; see `TRADEMARKS.md`.
