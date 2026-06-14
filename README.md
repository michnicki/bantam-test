---
title: Pigeon Post API
description: Carrier-pigeon-as-a-service for developers — send messages and parcels by bird.
icon: home
sidebar:
  position: 0
---

# Pigeon Post

Welcome to **Pigeon Post** — the only RESTful carrier-pigeon network built for
developers. Dispatch a pigeon with a single `POST`, track its flight in real
time, and get a webhook the moment it lands.

```bash
curl https://api.pigeonpost.dev/v1/pigeons \
  -H "Authorization: Bearer pgn_live_…" \
  -d recipient="ops@acme.example" \
  -d message="Deploy complete 🚀"
```

<Callout variant="info">

New here? Start with the [Quickstart](/getting-started/quickstart) — you'll
dispatch your first pigeon in under five minutes.

</Callout>

## What you can do

- **Dispatch pigeons** carrying messages or small parcels — see [Sending messages](/guides/sending-messages).
- **Track flights** from launch to landing — see [Tracking flights](/guides/tracking-flights).
- **Batch with flocks** to send hundreds at once — see [Flocks & fleets](/guides/flocks-and-fleets).
- **React to deliveries** with [Webhooks](/guides/webhooks).
- **Explore the full surface** in the [API reference](/api-reference).

## Documentation map

| Section | What's inside |
|---|---|
| [Getting started](/getting-started) | Install an SDK, authenticate, send your first pigeon |
| [Guides](/guides) | Task-focused how-tos for everyday work |
| [Concepts](/concepts) | How pigeons, routes, and aviaries actually work |
| [Tutorials](/tutorials) | End-to-end projects you can build |
| [API reference](/api-reference) | Every endpoint, generated from OpenAPI |
| [Reference](/reference) | SDKs, the CLI, and a glossary |

---

### About this repository

This repo is **sample content for [Bantam](https://github.com/bantam-app/bantam)**, a
self-hosted documentation platform. Git is the source of truth; point a Bantam
instance at this repo (with **docs root `.`** — content lives at the repository
root, not under a `docs/` folder) and it will compile and serve everything here:
Markdown and MDX pages, reusable snippets in `.snippets/`, and the OpenAPI spec
in `specs/`, all configured through `bantam.config.json`.

The Pigeon Post product is entirely fictional. Any resemblance to a real bird is
coincidental.
