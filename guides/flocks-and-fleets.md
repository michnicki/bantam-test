---
title: Flocks & Fleets
description: Send up to 500 pigeons in a single batch with POST /flocks, and handle partial failures gracefully.
icon: layers
sidebar_position: 4
---

# Flocks & Fleets

A **flock** dispatches many pigeons in one request. Instead of firing 500 separate `POST /pigeons` calls, you send a single `POST /flocks` with an array of up to 500 dispatches. Pigeon Post fans them out across your aviaries, returns a flock object (`flk_…`), and tells you exactly which birds took off and which did not.

## Send a flock

```bash
curl https://api.pigeonpost.dev/v1/flocks \
  -H "Authorization: Bearer pgn_live_…" \
  -H "Idempotency-Key: nightly-digest-2026-06-14" \
  -H "Content-Type: application/json" \
  -d '{
    "pigeons": [
      { "recipient": "ops@acme.example", "message": "Deploy complete 🚀", "route": "rt_express" },
      { "recipient": "sales@acme.example", "message": "Q2 numbers attached", "route": "rt_scenic" }
    ]
  }'
```

## The flock response

The response lists each pigeon that launched and, separately, any entries that were rejected — so one bad address does not sink the whole batch.

```json
{
  "id": "flk_3Jd7Pq",
  "object": "flock",
  "created_at": 1718352000,
  "dispatched": [
    { "id": "pgn_8f3kQ2", "recipient": "ops@acme.example", "status": "queued", "flight": "flt_b21Lm9" },
    { "id": "pgn_9c1Rt4", "recipient": "sales@acme.example", "status": "queued", "flight": "flt_c70Nx2" }
  ],
  "failed": [],
  "summary": { "total": 2, "dispatched": 2, "failed": 0 }
}
```

## Partial failures

Flocks accept the valid entries and report the rest — the request still returns `200`. Each item in `failed` echoes the offending input alongside the error that applies to it (the same shape described in [error handling](/guides/error-handling)):

```json
{
  "failed": [
    {
      "index": 7,
      "recipient": "not-an-address",
      "error": {
        "type": "invalid_request_error",
        "code": "recipient_invalid",
        "message": "recipient is not a valid address",
        "param": "pigeons[7].recipient"
      }
    }
  ]
}
```

Inspect `failed` on every flock, fix the inputs, and resend just those entries — reusing an `Idempotency-Key` is safe and prevents double-dispatching the birds that already flew.

## Flocks vs. many single dispatches

| Use a flock when… | Use single dispatches when… |
|---|---|
| You have a known batch (digests, fan-out alerts, mailing-list style sends). | Dispatches arrive one at a time, event-driven. |
| You want one round trip and one summary. | You need a flight id back immediately per call. |
| You are mindful of [rate limits](/guides/rate-limits) — one flock counts as one request. | Volume is low and latency per send does not matter. |

A flock of 500 counts as a **single** request against your per-minute ceiling, which makes flocks the most rate-limit-friendly way to send at volume. Keep each batch at or under 500 pigeons; larger arrays are rejected with an `invalid_request_error`.
