---
title: Concepts
description: The mental model behind the Pigeon Post API — pigeons, routes, aviaries, and flights.
icon: workflow
sidebar_position: 0
---

# Concepts

The Pigeon Post API is carrier-pigeon-as-a-service. Underneath the feathers it behaves like any modern REST API, but it helps to hold the right picture in your head before you make your first call.

## The mental model

You **dispatch a Pigeon** carrying a message (or a small parcel) to a recipient. You pick a **Route** — a routing preset that trades speed against cost — and the bird launches from an **Aviary**, one of our regional nests. From that moment on, the pigeon's journey is tracked as a **Flight**: a record you can poll or subscribe to until the bird lands.

In short:

> You dispatch a **Pigeon** along a **Route** from an **Aviary**, and its journey is a **Flight**.

Everything else in the API — Flocks (batches of up to 500 pigeons), Webhooks, and your loft (account) settings — builds on these four ideas. Lost pigeons auto-refund, and every Route is backed by our 99.9% wingtime SLA, so you can build confidently on top of them.

## The four core concepts

| Concept | What it is | Read more |
|---|---|---|
| Pigeons | A single dispatch carrying a message or parcel | [Pigeons](/concepts/pigeons) |
| Routes | Routing presets that map to ETA and cost | [Routes](/concepts/routes) |
| Aviaries | Regional launch sites pigeons take off from | [Aviaries](/concepts/aviaries) |
| Delivery lifecycle | How a flight moves from queued to delivered | [Delivery lifecycle](/concepts/delivery-lifecycle) |

New here? Start with [Getting started](/getting-started), then come back to fill in the model. When you're ready for code, the full [API reference](/api-reference) documents every endpoint.
