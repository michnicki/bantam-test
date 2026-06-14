---
title: Pigeons
description: The pigeon resource — a single dispatch, its fields, and its lifecycle states.
icon: bird
sidebar_position: 1
---

# Pigeons

A **pigeon** is a single dispatch: one message (or parcel) headed to one recipient. Creating a pigeon is how you send something through the Pigeon Post API. Each pigeon has an id prefixed with `pgn_`, and once launched it is tracked by an associated [Flight](/concepts/delivery-lifecycle).

## Example

```json
{
  "id": "pgn_8f3kQ2",
  "object": "pigeon",
  "recipient": "ops@acme.example",
  "message": "Deploy complete 🚀",
  "route": "rt_express",
  "aviary": "aviary_eu_nest",
  "status": "in_flight",
  "flight": "flt_b21Lm9",
  "created_at": 1718352000
}
```

## Fields

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique identifier, prefixed `pgn_`. |
| `object` | string | Always `pigeon`. |
| `recipient` | string | Where the dispatch is headed. |
| `message` | string | The text the pigeon carries. Mutually exclusive with `parcel`. |
| `parcel` | object | A structured payload, for when a plain message won't do. |
| `route` | string | The [Route](/concepts/routes) preset, e.g. `rt_express`. |
| `aviary` | string | The [Aviary](/concepts/aviaries) the pigeon launches from. |
| `status` | string | Current state — see below. |
| `flight` | string | The associated flight id, prefixed `flt_`. |
| `created_at` | integer | Unix timestamp of dispatch. |
| `metadata` | object | Up to 16 key/value pairs of your own. |

## Message vs parcel

A pigeon carries **either** a `message` (a simple string — the common case) **or** a `parcel` (a structured object for richer payloads). Set one, not both. Heavier parcels may nudge a pigeon toward a longer rest period en route.

## Lifecycle states

A pigeon's `status` mirrors its flight: `queued`, `in_flight`, `delivered`, `lost`, or `returned`. Lost pigeons auto-refund. For the full journey and its transitions, see the [delivery lifecycle](/concepts/delivery-lifecycle) and the guide on [tracking flights](/guides/tracking-flights).
