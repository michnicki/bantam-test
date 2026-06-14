---
title: Rate Limits
description: Stay within your per-minute ceiling, back off on 429s, and understand molting windows.
icon: gauge
sidebar_position: 5
---

# Rate Limits

Pigeon Post limits how many requests you can make per minute so the lofts stay healthy and every customer's birds get airtime. Limits are enforced per loft, counted on a rolling 60-second window.

## Your limits

| Mode | Key prefix | Limit | Notes |
|---|---|---|---|
| Test | `pgn_test_…` | 60 requests / min | Generous enough to build and test against the sandbox. |
| Live | `pgn_live_…` | 1000 requests / min | Production ceiling. Need more? Contact us about a larger loft. |

A [flock](/guides/flocks-and-fleets) counts as **one** request no matter how many pigeons it carries, so batching is the cheapest way to send at volume.

## When you hit the limit

Exceed your ceiling and the API responds with `429` and a `rate_limit_error`. A `Retry-After` header tells you how many seconds to wait before trying again.

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 12
Content-Type: application/json
```

```json
{
  "error": {
    "type": "rate_limit_error",
    "code": "too_many_requests",
    "message": "Rate limit exceeded. Retry after 12 seconds.",
    "param": null
  }
}
```

## Backing off

When you see a `429`:

1. Honor `Retry-After` first — sleep for at least that many seconds.
2. If no header is present, fall back to **exponential backoff**: wait 1s, then 2s, 4s, 8s, … up to a cap (e.g. 60s), with a little random jitter to avoid thundering-herd retries.
3. Cap your total attempts and surface a failure rather than retrying forever.

Because dispatches use an `Idempotency-Key`, retrying a `POST /pigeons` after a `429` will not launch a duplicate bird. See [error handling](/guides/error-handling) for the full retry strategy across error types.

## Molting windows

Occasionally a loft enters a **molting window** — scheduled maintenance during which we rest and re-feather the pigeons. Molting windows are announced in advance on the [dashboard](https://dashboard.pigeonpost.dev). During one:

- Read requests (`GET …`) keep working normally.
- New dispatches are **accepted and queued** rather than rejected — your pigeons enter `queued` and launch automatically once the window closes.

You do not need special handling for molting windows beyond the flight-status polling and webhooks you already use to follow a [flight](/guides/tracking-flights). It all counts toward our 99.9% wingtime SLA.
