---
name: blng-generate-a-3d-model
description: Turn a generated BLNG image into a 3D model (GLB, then USD/USDZ), polling the asynchronous generation job and honoring the per-journey operation lock.
api: BLNG Journey API
base_url: https://journeys.blng.ai/v2
spec: openapi/blng-journey-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/blng-journey-api-openapi.yml
operations:
  - startModelGeneration
  - listModelGenerations
  - getModelGeneration
  - createModel3D
  - getModel3D
  - listModelsForJourney
  - resubmitModelConversions
  - getAssetDownloadUrl
---

# Generate a 3D model from a BLNG design

Every operationId below was read out of BLNG's own definition at `https://journeys.blng.ai/swagger.yaml`.

Prerequisite: a journey with at least one generated image, produced by `blng-run-a-design-journey`.
Auth is the same AWS Cognito bearer JWT.

## The lock — read this first

`startModelGeneration` returns **423 Locked** when a `chatPrompt` or another `modelGeneration` is already
in flight for the journey. The 423 body is the single most useful error in this API because it tells you
*when* to come back:

```json
{
  "message": "chatPrompt operation in progress",
  "operationInFlight": "chatPrompt",
  "operationOwnerId": "...",
  "operationExpiresAt": "2026-08-13T17:04:00Z"
}
```

Wait until `operationExpiresAt`, then retry. Do not poll tighter than that — no other endpoint in this API
publishes a retry hint, and there are no rate-limit headers.

## Steps

### 1. Pick the source image

The image must be one of the prompt's own generated outputs. Read `generatedImageIds` from the prompt
(`getPromptById`, `GET /journeys/{journeyId}/prompts/{promptId}`) or list the journey's images with
`getAllImagesForJourney`.

Passing an `imageAssetId` that is not in that list returns **422**.

### 2. Start the generation — `startModelGeneration`

`POST /design/journeys/{journeyId}/generations` with a `ModelGenerationRequest`:

- `imageAssetId` — **required**, the image to model.
- `promptId` — the prompt that produced the image; optional for standalone generations.
- `imageSource` — `prompt` (default) or `layer`. Use `layer` when `imageAssetId` is a journey canvas image
  that is not listed among the prompt's generated outputs; it still requires `promptId` on the same journey
  for routing.

Returns **202 Accepted**. The spec states the pipeline creates a **GLB first, then automatically enqueues
conversions to USD and USDZ** — you do not request those separately.

Failure modes: **400** invalid body, **401** bad token, **404** journey/prompt/asset not found, **422** the
asset is not one of the prompt's outputs, **423** locked.

### 3. Poll the job — `getModelGeneration` / `listModelGenerations`

`GET /design/journeys/{journeyId}/generations/{generationId}` for one job,
`GET /design/journeys/{journeyId}/generations` for all of them.

The terminal payload is a `oneOf`:

- success — `{"ok": true, "generationId", "imageAssetId", "modelId", "glbAssetId"}`
- failure — `{"ok": false, "generationId", "imageAssetId", "error": "<reason>"}`

**Branch on `ok`, not on the HTTP status.** A failed generation is still delivered inside a 200.

### 4. Read the model — `getModel3D` / `listModelsForJourney`

`GET /design/journeys/{journeyId}/models/{modelId}` returns a `Model3D` with per-format conversion slots
(`Model3DSlot`), each carrying the `assetId` for that format. `GET /design/journeys/{journeyId}/models`
lists them all.

### 5. Download the asset — `getAssetDownloadUrl`

`GET /design/assets/{assetId}/download` issues a short-lived presigned URL. Fetch the GLB/USD/USDZ bytes
from that URL. Binary never transits the BLNG API.

### 6. If a conversion is missing — `resubmitModelConversions`

`POST /design/journeys/{journeyId}/models/{modelId}/conversions` re-enqueues the format conversions for an
existing model. Use this when the GLB exists but a USD/USDZ slot never filled — it is cheaper and safer
than re-running the whole generation.

`createModel3D` (`POST /design/journeys/{journeyId}/models`) registers a model directly from a
`sourceAssetId` without going through image generation.

## Rules

- **No idempotency key exists on any of these operations.** A retried `startModelGeneration` starts a
  second job and consumes generation capacity twice. Poll the existing `generationId` instead of retrying.
- Honor the 423 lock rather than racing it — the lock is per journey, not per user.
- Never treat `ok: false` as a transport error; it is a completed job with a failure reason in `error`.
- BLNG publishes OpenUSD file-format plugins at `https://github.com/blng-ai/USD-Fileformat-plugins`, which
  is the interchange format this pipeline emits. It is not a client library for this API.
