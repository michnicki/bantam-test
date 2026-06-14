---
title: Homing Beacon (Design Note)
description: Internal WIP design note for the unreleased Homing Beacon feature — delivery-confirmation photos.
icon: flask-conical
sidebar_position: 1
draft: true
---

# Homing Beacon — Internal Design Note

> **DRAFT / WORK IN PROGRESS.** Nothing here is final, shipped, or public. Do not reference Homing Beacon in customer-facing docs, support replies, or sales calls until this note is promoted out of `draft` status. Numbers, field names, and pricing below are placeholders.

## Summary

**Homing Beacon** lets a pigeon return a photo confirming delivery — a literal picture of the parcel at the doorstep. Today a `flight.delivered` event tells the developer *that* a delivery happened; Homing Beacon tells them *what it looked like*. Think proof-of-delivery, but cute.

## Motivation

- Top recurring feature request from logistics and e-commerce lofts ("can we get proof?").
- Differentiates us from the Ravens, who can barely confirm delivery at all.
- Reduces "did it actually arrive?" support volume, which spikes around molting windows.

## Proposed shape (tentative)

A pigeon opts in to a beacon at dispatch:

```json
POST /pigeons
{
  "recipient": "ada@example.com",
  "parcel": "parcel_9f2a",
  "beacon": { "confirm_photo": true }
}
```

On delivery, the flight gains a `beacon` block and we fire a new event:

- New webhook event: `flight.confirmed` (fires *after* `flight.delivered`, only when a photo is captured).
- New field on the flight object: `beacon.photo_url` (signed, short-lived URL) and `beacon.captured_at`.

```json
{
  "object": "flight",
  "id": "flt_8c1f2a9b",
  "status": "delivered",
  "beacon": {
    "photo_url": "https://cdn.pigeonpost.dev/beacons/flt_8c1f2a9b.jpg?sig=...",
    "captured_at": "2024-07-01T14:22:03Z"
  }
}
```

## Open questions

- **Storage & retention.** How long do we keep photos? Leaning 30 days, then purge. Needs legal review (the photo may capture a doorstep / person).
- **Privacy / consent.** Recipients in some regions may need to consent to being photographed. Likely gate behind aviary region (`aviary_eu_nest` stricter).
- **Failure mode.** If the beacon can't capture a photo, delivery still succeeds — `flight.confirmed` simply never fires. Confirm this doesn't break the auto-refund logic for `pigeon_lost_error`.
- **Pricing.** Per-photo surcharge vs. flat add-on. TBD with billing.
- **Signature.** `flight.confirmed` rides the same `Pigeon-Signature` HMAC-SHA256 v2 scheme as every other event — no new secret type.

## Out of scope (for v1)

- Video beacons.
- Photo capture for flocked dispatches (single pigeons only at first).

## Status

Design exploration only. No implementation work started. Owner: Delivery Platform team. Revisit after the Q3 aviary capacity work lands.
