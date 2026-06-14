---
title: Routes
description: Routing presets that trade speed against cost and map directly to ETA and price.
icon: route
sidebar_position: 2
---

# Routes

A **route** is a routing preset that tells a pigeon how to fly. Rather than fiddling with altitude, tailwinds, and rest periods yourself, you pick a named preset and we handle the flight plan. Every pigeon must reference a route.

Two presets cover most needs:

- **`rt_express`** — the fastest path, with priority handling. Your pigeon takes the most direct corridor and skips optional rest periods. Best when delivery time matters more than cost.
- **`rt_scenic`** — the cheaper, slower path. The pigeon takes a relaxed corridor, enjoys a rest period or two, and arrives later for less. Best for non-urgent dispatches.

## How a route maps to ETA and cost

Choosing a route fixes two things: the **estimated time of arrival** and the **price** charged when the pigeon lands. Express buys speed at a premium; scenic trades patience for savings. The route does not change *whether* a pigeon arrives — both are covered by our 99.9% wingtime SLA, and lost pigeons auto-refund regardless of route.

## Comparison

| Route | Speed | Cost | Priority | Best for |
|---|---|---|---|---|
| `rt_express` | Fastest | Higher | Yes | Time-sensitive dispatches |
| `rt_scenic` | Slower | Lower | No | Bulk or non-urgent dispatches |

## Choosing a route

Reach for `rt_express` when a human or system is waiting on the message — alerts, confirmations, deploy notifications. Reach for `rt_scenic` when you're sending [Flocks](/api-reference) of low-priority dispatches and want to keep your loft's bill down. You can set a different route per pigeon, even within the same flock.

See the [API reference](/api-reference) for the full list of available presets and how to set one at dispatch time.
