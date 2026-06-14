---
title: Aviaries
description: Regional launch sites pigeons take off from, with data residency and capacity notes.
icon: map-pin
sidebar_position: 3
---

# Aviaries

An **aviary** is a regional nest — a launch site where pigeons rest, load up, and take off. Choosing an aviary determines where your dispatch originates, where its data lives, and which corridors are nearest at hand. There are three regional aviaries today.

## Example response

```http
GET /aviaries
```

```json
{
  "object": "list",
  "data": [
    { "id": "aviary_eu_nest", "region": "eu-west",      "label": "EU Nest",  "capacity": "high" },
    { "id": "aviary_us_coop", "region": "us-east",      "label": "US Coop",  "capacity": "high" },
    { "id": "aviary_ap_loft", "region": "ap-southeast", "label": "AP Loft",  "capacity": "medium" }
  ]
}
```

## Regions

| Aviary | Region | Residency | Capacity |
|---|---|---|---|
| `aviary_eu_nest` | `eu-west` | EU | High |
| `aviary_us_coop` | `us-east` | US | High |
| `aviary_ap_loft` | `ap-southeast` | APAC | Medium |

## Data residency

A pigeon's record — recipient, message, and flight history — is stored in the region of the aviary it launches from. If you have residency requirements, pin dispatches to the matching aviary rather than relying on the default selection.

## Capacity

Each aviary has a finite number of perches. During traffic spikes, high-capacity aviaries (`aviary_eu_nest`, `aviary_us_coop`) absorb more dispatches before pigeons begin queuing for an open perch. `aviary_ap_loft` runs at medium capacity.

## How an aviary is selected

By default the API picks the **nearest** aviary to your loft, minimizing time-to-launch. You can override this by setting the `aviary` field on a pigeon — useful for data residency, or for launching close to the recipient. Route and aviary are independent: any [Route](/concepts/routes) can launch from any aviary. See the [API reference](/api-reference) for details.
