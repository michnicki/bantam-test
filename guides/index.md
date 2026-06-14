---
title: Guides
description: Practical, task-focused walkthroughs for getting the most out of the Pigeon Post API.
icon: book-open
sidebar_position: 0
---

# Guides

Welcome to the Pigeon Post guides — task-focused walkthroughs for developers who need their pigeons dispatched, tracked, and accounted for. If you are looking for endpoint-by-endpoint detail, head to the [API reference](/api-reference). If you are brand new, start with [getting started](/getting-started) and the [core concepts](/concepts).

Everything below assumes you have a loft (your account) and at least one API key from the [dashboard](https://dashboard.pigeonpost.dev). Test keys (`pgn_test_…`) fly against the sandbox; live keys (`pgn_live_…`) send real birds.

## Choose your guide

| Guide | What it covers |
|---|---|
| [Sending messages](/guides/sending-messages) | Dispatch a single pigeon with a message or a parcel, pick a route, and use idempotency keys. |
| [Tracking flights](/guides/tracking-flights) | Read a flight's status, follow the delivery lifecycle, and decide between polling and webhooks. |
| [Webhooks](/guides/webhooks) | Subscribe to events, verify the `Pigeon-Signature`, and handle retries. |
| [Flocks & fleets](/guides/flocks-and-fleets) | Send up to 500 pigeons in one batch and handle partial failures. |
| [Rate limits](/guides/rate-limits) | Stay under your per-minute ceiling, back off on `429`, and survive molting windows. |
| [Error handling](/guides/error-handling) | Understand the error envelope, every error type, and how to retry safely. |

## Conventions used in these guides

- **Base URL** is `https://api.pigeonpost.dev/v1` (sandbox: `https://api.sandbox.pigeonpost.dev/v1`).
- Authenticate with `Authorization: Bearer pgn_live_…`.
- Code samples are shown for curl, the Node SDK (`@pigeonpost/node`), and the Python SDK (`pigeonpost`) where relevant. There is also a `pigeon` CLI.

Migrating off the legacy Ravens service? Most concepts map across cleanly — pigeons just come back when they get lost (and so does your money).
