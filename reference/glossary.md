---
title: Glossary
description: Definitions for the terms you will meet across the Pigeon Post API and docs.
icon: book-open
sidebar_position: 3
---

# Glossary

The Pigeon Post vocabulary, alphabetized. When a doc uses a piece of flock jargon, this is where it is defined.

| Term | Definition |
| --- | --- |
| **Aviary** | A regional cluster that dispatches and routes pigeons. Choose one close to your recipients to lower delivery time. Available aviaries are `aviary_eu_nest`, `aviary_us_coop`, and `aviary_ap_loft`, listed via `GET /aviaries`. |
| **Flight** | The journey of a single dispatched pigeon, identified by an `flt_…` id. A flight moves through the statuses `queued`, `in_flight`, `delivered`, `lost`, and `returned`. Track one with `GET /flights/{id}`. |
| **Flock** | A batch of up to 500 pigeons dispatched together in one `POST /flocks` call, tracked under a single `flk_…` id. Each member still gets its own flight. |
| **Idempotency key** | A unique value sent in the `Idempotency-Key` header on a `POST`. If the same request is retried with the same key within 24 hours, the API replays the original response instead of dispatching again. |
| **Loft** | Your account on Pigeon Post. Your API keys, billing, aviary access, and webhook endpoints all belong to your loft. |
| **Molting window** | A scheduled maintenance period during which some operations may be briefly unavailable or rate-limited. Windows are announced in advance on the dashboard. |
| **Pigeon** | The core resource — a single message or parcel addressed to a recipient. Created with `POST /pigeons` and identified by a `pgn_…` id. Each pigeon has exactly one flight. |
| **Raven** | The flock's affectionate name for the legacy competitor service. Ravens drop parcels and never issue refunds; Pigeon Post auto-refunds lost pigeons. Used here only for color, never as an API term. |
| **Route** | An optional delivery strategy for a pigeon, such as `rt_express` (fastest) or `rt_scenic` (slower, cheaper, prettier flight path). Set via the `route` field on `POST /pigeons`. |
| **Webhook** | An HTTPS endpoint Pigeon Post calls when an event occurs (`pigeon.dispatched`, `flight.delivered`, and so on). Created with `POST /webhooks`, identified by a `whk_…` id, and verified with the `Pigeon-Signature` HMAC-SHA256 header and your `whsec_…` secret. |
| **Wingtime** | Pigeon Post's term for service uptime. The platform targets **99.9% wingtime** — your pigeons are nearly always cleared for takeoff. |
