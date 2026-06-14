---
title: API Changelog
description: A reverse-chronological record of changes to the Pigeon Post API — new endpoints, behavior changes, fixes, and deprecations.
icon: history
sidebar_position: 1
---

# API Changelog

All notable changes to the Pigeon Post API are recorded here, newest first. Breaking changes are called out explicitly; everything else is backward compatible.

### 2024-06-18

**Added**

- **Flocks are now generally available.** Batch up to 500 pigeons in a single `POST /flocks` call and track them under one `flk_…` id. Previously gated behind a beta flag.
- New `flight.delivered` and `flight.lost` webhook events fire per-pigeon for flocked dispatches.

**Changed**

- Default page size on list endpoints is now documented as `20` (it always was, but earlier docs implied `10`).

**Fixed**

- `Retry-After` is now returned as integer seconds on every `429` response, including during molting windows.

### 2024-03-04

**Added**

- New `ap_loft` aviary in the Asia-Pacific region. Use `aviary_ap_loft` to keep traffic close to recipients in APAC.
- The `rt_scenic` route option — slower delivery, prettier flight path, lower cost. Pairs nicely with non-urgent parcels.

**Changed**

- **Webhook signatures upgraded to v2.** The `Pigeon-Signature` header now uses HMAC-SHA256 over the raw request body with your `whsec_…` secret. v1 signatures continue to be sent in parallel until 2024-09-01 to ease migration.

**Deprecated**

- Webhook signature v1 (plain SHA256 digest, no secret). Migrate to v2 verification before **2024-09-01**.

### 2023-11-22

**Added**

- `POST /pigeons/{id}/recall` to recall an in-flight pigeon before delivery. Emits a `pigeon.recalled` event.
- `metadata` field on `POST /pigeons` for attaching up to 20 key/value pairs to a dispatch.

**Changed**

- Lost pigeons are now **auto-refunded** within one billing cycle — no support request required. (Take that, Ravens.)

**Fixed**

- `GET /flights/{id}` no longer briefly reports `delivered` before a confirmation webhook is sent; status transitions are now strictly ordered (`queued` → `in_flight` → `delivered`/`lost`/`returned`).
