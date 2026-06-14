---
title: Tracking Flights
description: Read a flight's status, follow the delivery lifecycle, and choose between polling and webhooks.
icon: radar
sidebar_position: 2
---

# Tracking Flights

When you dispatch a pigeon, Pigeon Post creates a **flight** to carry it from the aviary to the recipient. Each flight has its own id (`flt_…`) and a `status` that moves through a small, predictable lifecycle. The pigeon object always carries the id of its current flight in the `flight` field, so you can hop straight to `GET /flights/{id}`.

## Read a flight

```bash
curl https://api.pigeonpost.dev/v1/flights/flt_b21Lm9 \
  -H "Authorization: Bearer pgn_live_…"
```

```json
{
  "id": "flt_b21Lm9",
  "object": "flight",
  "pigeon": "pgn_8f3kQ2",
  "status": "in_flight",
  "route": "rt_express",
  "aviary": "aviary_eu_nest",
  "launched_at": 1718352060,
  "delivered_at": null
}
```

## The status lifecycle

Most flights walk a straight path: `queued` → `in_flight` → `delivered`. Two branches exist for the unhappy paths — a bird may get `lost`, or it may come home as `returned`.

| Status | Meaning | Terminal? |
|---|---|---|
| `queued` | Accepted and waiting for the next launch window (or a molting window to clear). | No |
| `in_flight` | The pigeon is airborne and en route to the recipient. | No |
| `delivered` | The recipient received the message or parcel. | Yes |
| `lost` | The pigeon failed to arrive. Lost flights auto-refund. | Yes |
| `returned` | The pigeon came back undelivered (e.g. a recall, or no valid recipient). | Yes |

A flight only ever reaches one terminal state. Once it is `delivered`, `lost`, or `returned`, its status will not change again. If you recall a pigeon with `POST /pigeons/{id}/recall` before it lands, the flight resolves to `returned`.

## Polling vs. webhooks

You have two ways to learn how a flight ends:

- **Polling** — call `GET /flights/{id}` on an interval. Simple to build, but it costs requests against your [rate limit](/guides/rate-limits) and adds latency. If you must poll, back off as the flight ages and stop once you hit a terminal status.
- **Webhooks** — subscribe once and let Pigeon Post call you. You receive `flight.launched`, `flight.delivered`, and `flight.lost` (plus `pigeon.returned` and `pigeon.recalled`) the moment they happen. This is the recommended approach for anything beyond a quick script.

For production systems, prefer webhooks and treat polling as a fallback for reconciliation. See [webhooks](/guides/webhooks) to get set up.
