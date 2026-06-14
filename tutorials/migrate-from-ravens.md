---
title: Migrate from Ravens
description: A step-by-step cutover from the legacy Ravens API to Pigeon Post, with concept and endpoint mappings.
icon: arrow-right-left
sidebar_position: 2
---

# Migrate from Ravens

If you're coming from **Ravens**, welcome. The two APIs solve the same problem — getting a message from A to B by bird — but Pigeon Post is built on plain REST, signed webhooks, and a 99.9% wingtime SLA. This tutorial walks you through a clean cutover, mapping every Raven concept to its Pigeon Post equivalent so you can migrate one piece at a time.

You won't need to flip everything at once. Pigeon Post can run alongside Ravens during the transition, and lost pigeons are auto-refunded, so a partial cutover never costs you money for messages that don't arrive.

## Concept mapping

Most Raven nouns have a direct Pigeon Post counterpart. The big change is auth and webhook verification.

| Ravens | Pigeon Post | Notes |
| --- | --- | --- |
| Raven | Pigeon | A single dispatched message or parcel (`pgn_…`). |
| Nest | Aviary | A dispatch hub / region. List yours via [`GET /aviaries`](/api-reference#list-aviaries). |
| Murder (batch send) | Flock | A batch of up to 500 pigeons (`flk_…`). |
| Caw webhook | Pigeon-Signature webhook | HMAC-SHA256 signed via the `Pigeon-Signature` header (`whsec_…`). |
| Rookery account | Loft | Your account; holds keys, billing, and aviaries. |
| `X-Raven-Token` header | `Authorization: Bearer pgn_live_…` | Standard bearer auth; keys are `pgn_live_…` / `pgn_test_…`. |
| Flight log | Flight | Delivery lifecycle object (`flt_…`), status `queued`/`in_flight`/`delivered`/`lost`/`returned`. |

## Endpoint mapping

| Ravens endpoint | Pigeon Post endpoint | Notes |
| --- | --- | --- |
| `POST /ravens/send` | `POST /pigeons` | Body `recipient` + `message` or `parcel`; optional `route`, `metadata`. |
| `GET /ravens` | `GET /pigeons` | Cursor pagination (`limit` ≤ 100, `starting_after`). |
| `GET /ravens/{id}` | `GET /pigeons/{id}` | |
| `POST /ravens/{id}/cancel` | `POST /pigeons/{id}/recall` | Recall a pigeon that hasn't been delivered yet. |
| `GET /ravens/{id}/status` | `GET /flights/{id}` | Pigeon Post separates the message from its flight. |
| `POST /murders` | `POST /flocks` | Batch send, max 500 per flock. |
| `GET /nests` | `GET /aviaries` | |
| `POST /caws` | `POST /webhooks` | Events are dotted, e.g. `flight.delivered`. |

A few endpoints have no Ravens equivalent and are simply new capabilities: per-event webhook subscriptions and idempotent POSTs (send an `Idempotency-Key` header to make retries safe).

## Migration steps

1. **Create a loft and a test key.** Sign up at the [dashboard](https://dashboard.pigeonpost.dev), then create a `pgn_test_…` key. Point your client at the sandbox base URL `https://api.sandbox.pigeonpost.dev/v1` while you port code.
2. **Install an SDK.** Replace your Ravens client with `@pigeonpost/node` (`npm i @pigeonpost/node`), `pigeonpost` (`pip install pigeonpost`), or the `pigeon` CLI.
3. **Swap auth.** Drop the `X-Raven-Token` header and send `Authorization: Bearer pgn_test_…` instead.
4. **Port your send call.** Map `POST /ravens/send` to [`POST /pigeons`](/api-reference#dispatch-a-pigeon). Rename `payload` to `message` (or `parcel` for non-text). Add an `Idempotency-Key` to each POST so retries don't duplicate dispatches.
5. **Port status checks.** Anywhere you read a Raven's status, call [`GET /flights/{id}`](/api-reference#retrieve-a-flight) and read `status` (`queued` → `in_flight` → `delivered`). Treat `lost` and `returned` as terminal.
6. **Re-register webhooks.** Recreate each Caw as a [Pigeon Post webhook](/guides/webhooks) via `POST /webhooks`, subscribing to specific events (`pigeon.dispatched`, `flight.launched`, `flight.delivered`, `flight.lost`, `pigeon.returned`, `pigeon.recalled`). Switch your verification from Ravens' scheme to the `Pigeon-Signature` HMAC-SHA256 check using the `whsec_…` secret.
7. **Update pagination.** Replace Ravens' page-number pagination with cursors: pass `starting_after` with the last id and loop while the response's `has_more` is `true`.
8. **Cut over traffic.** Once the sandbox flow is green, swap your `pgn_test_…` key for `pgn_live_…` and the base URL for `https://api.pigeonpost.dev/v1`. Ramp gradually — Pigeon Post and Ravens can run side by side until you're confident.

## Gotchas

> **Watch out for these during the cutover:**
>
> - **Rate limits differ.** Test mode is **60 req/min**, live is **1000 req/min**. Ravens' limits were per-nest; Pigeon Post limits are per-loft. On a `429 rate_limit_error`, honor the `Retry-After` header — don't hammer.
> - **Message vs. parcel.** Ravens crammed everything into one `payload` field. Pigeon Post splits text (`message`) from attachments (`parcel`) — send exactly one per pigeon.
> - **Webhook signature is over the raw body.** Unlike Ravens' token-in-query approach, the `Pigeon-Signature` HMAC is computed over the raw request bytes. Read the body before any JSON parsing or every signature check will fail.
> - **Batches cap at 500.** Ravens "murders" were unbounded; a Pigeon Post flock rejects anything over 500. Chunk larger sends.
> - **Recall, don't cancel.** A pigeon already `delivered` can't be recalled — `POST /pigeons/{id}/recall` only works while the flight is `queued` or `in_flight`.

Once you're live, keep the [API reference](/api-reference) handy and skim the [concepts](/concepts) section — the flight lifecycle model is the one mental shift that trips up most Ravens migrants.
