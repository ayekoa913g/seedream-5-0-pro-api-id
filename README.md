# Seedream 5.0 Pro API (`seedream-5-0-pro`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **from $0.014625 per image** (1K-layer $0.014625 · 1K $0.02925 · 2K-layer $0.02925) — satu endpoint yang kompatibel dengan OpenAI di `https://api.apimart.ai/v1`, tanpa paket bulanan. *(diamati 2026-10-12)*

**[Dapatkan API key](https://go.apimart.ai/k-f608af)** · **[Harga live](https://go.apimart.ai/k-58fd5c)** · **[Halaman model](https://go.apimart.ai/k-6dc540)** · [⚡ Quickstart 60 detik](#quickstart)

**Alasan tim memakai Seedream 5.0 Pro (`seedream-5-0-pro`) lewat APIMart**

- **Satu key untuk seluruh katalog.** base URL `https://api.apimart.ai/v1` dan header `Authorization` yang sama menjangkau Seedream 5.0 Pro (`seedream-5-0-pro`) plus 300+ model gambar, video, dan bahasa lainnya — cukup ganti field `model`, bukan klien Anda.
- **Minimum $1, bayar sesuai pemakaian.** Tanpa langganan dan tanpa paket prabayar yang harus dihitung di awal: isi saldo mulai $1 dan pakai untuk panggilan.
- **Biaya dikembalikan di respons.** Setiap task yang selesai melaporkan jumlah yang ditagih (`cost` / `credits_cost`); task berstatus `failed` dikembalikan dananya.
- **Asinkron sejak awal.** Kirim, ambil `task_id`, polling `GET /v1/tasks/{id}` — batching dan retry hanyalah pekerjaan antrean biasa, bukan integrasi khusus.

<!-- /conv-kit:v1 -->



Seedream 5.0 Pro is ByteDance's 1K/2K image model with layered pricing, precise text rendering and multi-reference editing.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Per-image | `seedream-5-0-pro` | per delivered image, by resolution | see the [model page](https://go.apimart.ai/k-6dc540) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/images/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| default | 0.045 | 0.036 |
| 1K | 0.0365625 | 0.02925 |
| 1K-layer | 0.01828125 | 0.014625 |
| 2K | 0.073125 | 0.0585 |
| 2K-layer | 0.0365625 | 0.02925 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-58fd5c) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | what to generate, or what to change when editing |
| `size` | string | `auto` / `1:1` | aspect ratio, or `auto` where the route supports it |
| `resolution` | string | `1K` | output resolution tier — cost scales with it |
| `n` | integer | `1` | number of images per task, where the route allows more than one |
| `image_urls` | string[] | — | reference images as public HTTP(S) URLs or base64 data URLs |
| `nsfw_check` | boolean | `false` | runs `omni-moderation-latest` before submitting |

The table lists the fields accepted by the APIMart `images/generations` endpoint. Check the [model page](https://go.apimart.ai/k-6dc540) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"seedream-5-0-pro","prompt":"A bamboo forest at sunrise, slow push-in","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "seedream-5-0-pro", "prompt": "A bamboo forest at sunrise, slow push-in",
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["images"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Apa id model Seedream 5.0 Pro?**

`seedream-5-0-pro`. Lihat tabel rute di atas.

**Bagaimana penagihannya?**

**from $0.014625 per image** (1K-layer $0.014625 · 1K $0.02925 · 2K-layer $0.02925). Task yang selesai melaporkan jumlah tepatnya di field `cost`; task yang gagal dikembalikan dananya.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-f608af) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [seedream-5-0-pro-prompts-id](https://github.com/ayekoa913g/seedream-5-0-pro-prompts-id)
- **Same model, other languages** — 9 more language builds: 中文 [seedream-5-0-pro-api-cn](https://github.com/a2hnks5fo-t/seedream-5-0-pro-api-cn) · Deutsch `seedream-5-0-pro-api-de` · Español [seedream-5-0-pro-api-es](https://github.com/pblexl325ktue/seedream-5-0-pro-api-es) · Français [seedream-5-0-pro-api-fr](https://github.com/jef7is8ris/seedream-5-0-pro-api-fr) · Relay/gateway `seedream-5-0-pro-api-gateway` · 日本語 [seedream-5-0-pro-api-ja](https://github.com/d2td7nw6r/seedream-5-0-pro-api-ja) · 한국어 [seedream-5-0-pro-api-ko](https://github.com/d2td7nw6r/seedream-5-0-pro-api-ko) · Português [seedream-5-0-pro-api-pt](https://github.com/znscxwmvl8m7m/seedream-5-0-pro-api-pt) · Русский [seedream-5-0-pro-api-ru](https://github.com/rqox1kopnhnd7c/seedream-5-0-pro-api-ru)
- **More models in this language** — [midjourney-api-id](https://github.com/cvrwq0/midjourney-api-id) · [gemini-2-5-flash-image-preview-api-id](https://github.com/gmrg0s/gemini-2-5-flash-image-preview-api-id) · [nano-banana-api-id](https://github.com/vsa185x9ecrd/nano-banana-api-id) · [gemini-3-1-flash-image-preview-api-id](https://github.com/p61qrgpg/gemini-3-1-flash-image-preview-api-id) · [nano-banana-2-api-id](https://github.com/n1rf4ezzz/nano-banana-2-api-id) · [gemini-3-pro-image-preview-api-id](https://github.com/s78ve5v4x9ji2/gemini-3-pro-image-preview-api-id)
- **Topic hubs** — every build of this model, all languages: [seedream-5-0-pro](https://github.com/topics/seedream-5-0-pro) · [seedream-5-0-pro-api](https://github.com/topics/seedream-5-0-pro-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-6dc540> | `apimart.ai/id/model/seedream-5-0-pro` |
| Live pricing | <https://go.apimart.ai/k-58fd5c> | `apimart.ai/id/pricing` |
| Get an API key | <https://go.apimart.ai/k-f608af> | `apimart.ai/id/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedream 5.0 Pro is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
