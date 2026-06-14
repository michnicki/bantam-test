---
title: Authentication
description: API keys, live vs. test keys, and how to rotate them safely.
icon: key
sidebar_position: 2
---

# Authentication

Every request to the Pigeon Post API authenticates with a Bearer API key. Pass your key in the `Authorization` header — there are no cookies, sessions, or signed query strings to manage.

```http
Authorization: Bearer pgn_live_…
```

If the header is missing, malformed, or carries a revoked key, the API responds with `401` and an `authentication_error`:

```json
{
  "error": {
    "type": "authentication_error",
    "code": "invalid_api_key",
    "message": "No valid API key provided.",
    "param": null
  }
}
```

## Live vs. test keys

Your loft has two kinds of keys, and the prefix tells you which is which:

| Prefix | Environment | Base URL |
| --- | --- | --- |
| `pgn_live_…` | Production aviaries | `https://api.pigeonpost.dev/v1` |
| `pgn_test_…` | Sandbox only | `https://api.sandbox.pigeonpost.dev/v1` |

Test keys only reach the sandbox aviary — they cannot dispatch a real pigeon, and they leave your live data untouched. Use them freely in CI and local development. Live keys move real pigeons and count against your live rate limit, so guard them accordingly.

## Where to find your keys

Open the [dashboard](https://dashboard.pigeonpost.dev) and go to **Developers → API keys**. There you can view your test key, reveal your live key once at creation time, and create additional keys scoped to a specific environment.

## Rotating keys

Keys can be rotated at any time without downtime:

1. Create a new key in the dashboard.
2. Deploy it to your application's configuration.
3. Confirm traffic is flowing on the new key.
4. Revoke the old key.

Because both keys are valid during the overlap, you can roll forward gradually and revoke only once you're confident nothing still depends on the old key. Revocation takes effect immediately.

> **Keep keys secret.** A live key can dispatch pigeons and read your loft's history. Never commit keys to source control, embed them in client-side code, or paste them into logs. Store them in your platform's secret manager and inject them as environment variables. If a key leaks, revoke it from the dashboard right away.

For event-driven integrations, webhook payloads are authenticated separately — see [Webhooks](/guides/webhooks) for signature verification.
