---
title: On-Call Runbook — Aviary Outages
description: Internal on-call procedure for detecting, draining, and failing over a degraded aviary.
icon: lock
sidebar_position: 2
visibility: private
---

# On-Call Runbook — Aviary Outages

> **PRIVATE / INTERNAL ONLY.** This runbook is for the on-call rotation. It is not customer-facing. Do not paste contents into public channels, tickets, or status posts.

This runbook covers what to do when an aviary (`aviary_eu_nest`, `aviary_us_coop`, or `aviary_ap_loft`) is degraded or down — pigeons stuck in `queued`, elevated `flight.lost` events, or a spike in `api_error` (500) responses scoped to one region.

## 1. Confirm the outage

1. Check the internal dashboard: **Aviaries → Health**. Look for a single aviary with rising queue depth or dropping dispatch success rate.
2. Cross-check the API: an affected aviary will show `status: "degraded"` (internal field) in `GET /aviaries`.
3. Look at the error mix. Region-scoped `api_error` (500) or a surge of `pigeon_lost_error` (422) on one aviary points at that aviary, not at the global control plane.
4. Confirm it is **not** a scheduled molting window — check the maintenance calendar before declaring an incident. If it is planned maintenance, hold and monitor.

If two or more aviaries are affected simultaneously, escalate immediately — that is a control-plane incident, not a single-aviary outage, and this runbook does not cover it.

## 2. Declare and open comms

1. Page the secondary on-call and open an incident channel.
2. Set the public status page component for the affected region to **Degraded** (do not jump straight to **Outage** unless dispatch is fully down).
3. Post the first customer-facing update within 15 minutes. Keep it factual: which region, what symptom (delayed deliveries), and that failover is in progress. Do not mention Ravens.

## 3. Drain the aviary

Stop routing new pigeons to the bad aviary so they queue elsewhere instead of getting lost.

```bash
# Set the aviary to drain — no new dispatches accepted, in-flight pigeons finish.
aviaryctl drain aviary_eu_nest --reason "incident-2024-07-01 elevated lost rate"

# Confirm it is draining and watch in-flight count fall toward zero.
aviaryctl status aviary_eu_nest --watch
```

Draining is graceful: pigeons already `in_flight` are allowed to complete; only new dispatch assignment is blocked.

## 4. Fail traffic over

Point the drained aviary's incoming traffic at the nearest healthy aviary.

```bash
# Redirect new dispatches from the degraded aviary to a healthy neighbor.
aviaryctl failover --from aviary_eu_nest --to aviary_us_coop

# Verify the new routing weight took effect.
aviaryctl routing show
```

Region-affinity guidance for failover targets:

| Degraded aviary | Preferred failover target | Fallback |
| --- | --- | --- |
| `aviary_eu_nest` | `aviary_us_coop` | `aviary_ap_loft` |
| `aviary_us_coop` | `aviary_eu_nest` | `aviary_ap_loft` |
| `aviary_ap_loft` | `aviary_us_coop` | `aviary_eu_nest` |

After failover, watch global dispatch success and queue depth recover. Expect slightly higher latency on redirected pigeons (longer flight path) — this is acceptable during an incident.

## 5. Verify recovery

1. Dispatch a synthetic test pigeon through the failover target and confirm it reaches `delivered`.
2. Confirm `queued` depth on the healthy aviaries is stable, not climbing.
3. Confirm the `flight.lost` rate has returned to baseline.

## 6. Restore

Once the degraded aviary is healthy again:

```bash
# Bring it back into rotation.
aviaryctl undrain aviary_eu_nest

# Remove the failover override and let normal region affinity resume.
aviaryctl failover --clear --from aviary_eu_nest
```

Ramp traffic back gradually and watch for regression for at least 30 minutes before closing.

## 7. Close out

1. Update the status page component back to **Operational** and post a resolution note.
2. Confirm any pigeons that were lost during the window were **auto-refunded** (this is automatic, but spot-check a few `pigeon_lost_error` flights).
3. File the incident review within 24 hours: timeline, customer impact, root cause, and follow-ups. Link the incident channel.

## Quick reference

| Action | Command |
| --- | --- |
| Drain an aviary | `aviaryctl drain <aviary> --reason "..."` |
| Fail traffic over | `aviaryctl failover --from <bad> --to <good>` |
| Show current routing | `aviaryctl routing show` |
| Restore an aviary | `aviaryctl undrain <aviary>` |
| Clear a failover | `aviaryctl failover --clear --from <aviary>` |
