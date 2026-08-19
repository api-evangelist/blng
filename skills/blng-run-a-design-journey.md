---
name: blng-run-a-design-journey
description: Start a BLNG design journey, upload a reference image, submit a design prompt, and retrieve the generated renderings.
api: BLNG Journey API
base_url: https://journeys.blng.ai/v2
spec: openapi/blng-journey-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/blng-journey-api-openapi.yml
operations:
  - startNewJourney
  - generateImageUploadURL
  - submitChatPromptV3
  - listDesignPlansForJourney
  - getDesignPlanForJourney
  - getAllImagesForJourney
  - generateImageDownloadUrl
  - getJourney
---

# Run a BLNG design journey

Every operationId below was read out of BLNG's own definition at `https://journeys.blng.ai/swagger.yaml`.
Nothing here is invented.

**Before you start.** BLNG runs no developer program. The definition is public but undocumented, there is
no API key, and the only credential is an AWS Cognito JWT issued at `https://auth.app.blng.ai` for a real
BLNG account. Treat this skill as an operating guide for a client that already holds a user token, not as
an invitation to call the service anonymously.

## Auth

Send `Authorization: Bearer <cognito_access_token_or_id_token>` on every request. Tokens come from the
`cognitoUserAuth` scheme (implicit flow, `authorizationUrl` `https://auth.app.blng.ai/oauth2/authorize`,
scopes `email profile openid aws.cognito.signin.user.admin`). The implicit grant requires a browser
redirect — there is no client-credentials path for third parties, so an unattended agent cannot mint its
own token.

## Steps

### 1. Start a journey — `startNewJourney`

`POST /journeys` with `{"userId": "<userId>", "type": "design"}`. `userId` and `type` are required;
`type` is `design` or `shopping`. You may supply your own `id`; otherwise the server generates one.
Returns **201** with the journey. **400** on invalid input.

There is no `Idempotency-Key` on this operation. If you retry after a timeout you will create a second
journey. If you need retry safety, generate the `id` client-side and pass it, so a repeat call is at least
detectable.

### 2. Get an upload URL for your reference image — `generateImageUploadURL`

`POST /design/journeys/{journeyId}/images/upload` with
`{"contentType": "image/jpeg"}` (allowed: `image/jpeg`, `image/jpg`, `image/png`, `image/gif`).
Returns **201** with a short-lived presigned URL. **404** if the journey does not exist.

For non-journey assets — models, stamps — use `createAssetUploadUrl`
(`POST /design/assets/upload`, `type` one of `image`, `model`, `stamps`, plus `contentType`).

### 3. PUT the bytes to the presigned URL

Send the file directly to the returned URL with exactly the `Content-Type` you asked for — the MIME type
is baked into the presign and a mismatch will be rejected. Image bytes never transit the BLNG API itself.

### 4. Submit the design prompt — `submitChatPromptV3`

`POST /design/journeys/{journeyId}/chat-prompt-v3` with a `ChatPromptRequestV3` body. Required fields:
`type`, `timestamp`, `userAgent`, `userMessage`, `variantCount`, `mode`, `scene`, `assetDetails`,
`expirationTime`.

- `mode` is `auto`, `fast` or `thinking`.
- `scene` is one of: a `Color` object, `{"preset": "<name>"}` (for example `classic`, `arctic`, `aurora`,
  `noir`, `transparent`), or `{"assetId": "<uuid>"}` to use an uploaded image as the scene.
- `assetDetails` carries `references[]` and an optional `lassoMaskId` to scope generation to a masked region.
- `variantCount` defaults to 1.

Returns **200**. On **429** the body is `{"code": "CONCURRENCY_LIMIT_EXCEEDED", "message": "..."}` — you
have too many prompts in flight for this user. **There is no `Retry-After` header**, so back off on your
own schedule and retry; do not tight-loop.

`submitChatPrompt` (`POST .../chat-prompt`) is the older v2 operation. It is not marked deprecated, but
prefer v3.

### 5. Poll for the result — `listDesignPlansForJourney` / `getDesignPlanForJourney`

Generation is asynchronous. Poll `GET /design/journeys/{journeyId}/plans` (or
`GET /design/journeys/{journeyId}/plans/{planId}`) until the plan's stages reach a terminal state. Each
`DesignPlanStage` carries the `assetId`s of the images it produced.

**Use conditional requests when polling.** Both plan reads return an `ETag`. Send it back as
`If-None-Match` and a **304** tells you nothing changed, with an empty body. This is the cheapest polling
loop the API offers. Note the spec's own warning: paginated listings return a different ETag per page, and
the page-1 ETag changes as newer records arrive — validators are page-scoped, so do not reuse one ETag
across pages.

### 6. Fetch the renderings — `getAllImagesForJourney`, `generateImageDownloadUrl`

`GET /design/journeys/{journeyId}/images` lists the journey's images.
`GET /design/journeys/{journeyId}/images/{imageId}/download` returns a presigned download URL. Fetch the
bytes from that URL, not from the API.

`getJourney` (`GET /journeys/{journeyId}`) returns the whole journey including `specifics` — the canvas
layers, colors and the currently visible image references. It also supports `If-None-Match` / 304.

## Cancelling

- `cancelPromptProcessingBeforePlan` — `POST /journeys/{journeyId}/prompts/{promptId}/cancel-processing`.
  **409** if a plan already exists or the prompt is already terminal.
- `cancelDesignPlanForPromptInJourney` —
  `POST /journeys/{journeyId}/prompts/{promptId}/plans/{planId}/cancel`.

## Errors you must handle

| Status | Meaning | What to do |
|---|---|---|
| 400 | Invalid input or parameters | Validate against the schema; the body is prose, with no field-level detail |
| 401 | Missing/expired Cognito token | Refresh the token and retry |
| 403 | Workspace role does not permit the action, or you left the workspace | Do not retry |
| 404 | Journey, prompt, asset, image or plan not found | Confirm the id; 404 is also returned where 403 would leak existence |
| 409 | Plan already exists, or prompt already terminal | Re-read state before acting |
| 422 | `imageAssetId` is not one of this prompt's `generatedImageIds` | Use an id from the prompt's own outputs |
| 429 | Concurrency limit (`CONCURRENCY_LIMIT_EXCEEDED`) | Back off; no `Retry-After` is sent |

Errors are **not** RFC 9457 — no `application/problem+json`, no `type` URI. See
`errors/blng-problem-types.yml`.

## Rules

- **Never blind-retry a POST.** No operation in this API accepts an idempotency key.
- Do not call `deleteJourney` or `deleteImage` from an autonomous flow. `restoreJourney` exists but
  returns **403** unless the caller is the workspace owner.
- Respect the journey lock: see `blng-generate-a-3d-model`.
