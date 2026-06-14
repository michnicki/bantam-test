---
title: Overview
description: Carrier-pigeon-as-a-service for developers — what Pigeon Post is and how to get started.
icon: bird
sidebar_position: 0
---

# Pigeon Post API

Pigeon Post is carrier-pigeon-as-a-service for developers. You dispatch a pigeon with a message or a parcel, we route it through the nearest aviary, and you track its flight to delivery — all over a plain, predictable REST API. No managed message bus to babysit, no Ravens to wrangle. Just dispatch and watch the wingtime.

Under the whimsy it behaves like any well-mannered HTTP API: JSON request and response bodies, Bearer authentication, cursor pagination, idempotent writes, and webhooks for the events you care about. If you've integrated with a payments or messaging API before, you already know the shape of this one.

## Three steps to value

1. **Install an SDK** — grab `@pigeonpost/node`, the `pigeonpost` Python package, or the `pigeon` CLI.
2. **Get a key** — create an API key in your [dashboard](https://dashboard.pigeonpost.dev). Test keys (`pgn_test_…`) fly the sandbox aviary; live keys (`pgn_live_…`) fly for real.
3. **Dispatch a pigeon** — `POST /pigeons` with a recipient and a message, then poll the flight until it's `delivered`.

The live API lives at `https://api.pigeonpost.dev/v1`; the sandbox at `https://api.sandbox.pigeonpost.dev/v1`. Everything runs on a 99.9% wingtime SLA, and any pigeon that goes `lost` is refunded automatically.

## Where to go next

| Page | What it covers |
| --- | --- |
| [Quickstart](/getting-started/quickstart) | Dispatch your first pigeon in five minutes. |
| [Authentication](/getting-started/authentication) | API keys, live vs. test, and key rotation. |
| [Your first pigeon](/getting-started/your-first-pigeon) | A guided tutorial: dispatch, retrieve, and track. |

When you're ready to go deeper, the [guides](/guides) cover batching with flocks and verifying webhooks, the [concepts](/concepts) section explains the data model, and the full [API reference](/api-reference) documents every endpoint.
