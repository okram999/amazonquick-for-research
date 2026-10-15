# Friday — Repeat

**Goal:** turn the one-off analysis into a **scheduled flow** that drafts a
weekly cotton advisory, so a useful program keeps running without one person
assembling it by hand.

## Background

AZMet published a weekly Cotton Development Advisory from 1991 to 2021, put
together and sent out by hand. It stopped when its author stepped back. The
final issue suggested Extension evaluate it and, if warranted, build a better
one. This flow is that successor.

## What's here

| File | What it is |
|------|-----------|
| `manual_build_steps.md` | The authoritative, step-by-step build guide for the flow (v3). Build it in the Quick Flows editor one step at a time. |
| `weekly_cotton_advisory_flow.md` | Narrative design of the flow — purpose, inputs, outputs and the advisory layout. Background reading; the build steps supersede it. |

## Connectors

The flow reuses the **AZMet connector** you built on Wednesday, plus the
**NWS forecast connector** (both OpenAPI specs are in
`../wednesday-agent/connectors/`). Build those connectors first if you haven't.

## What the flow does

Every Monday, in season (May–October), for Maricopa (az06) and Coolidge (az05):

1. **Pull** last week's AZMet data (same connector as Wednesday) and the NWS
   7-day forecast.
2. **Compute** crop water use by planting date and heat-stress days against the
   last five years.
3. **Draft** a one-page advisory with stations and dates cited.
4. **Review** — Marcus checks each issue before it reaches growers.

## Build and schedule — key points

- **Build it manually** in the editor, step by step. Generating the whole flow
  from one long text prompt tends to fall back to a generic one-step example;
  build the multi-step flow by hand instead.
- A step can only `@`-reference steps **above** it, so keep "Draft advisory" at
  the bottom and add new steps above it.
- The **input must be a date** (e.g. `2026-08-24`), not a phrase like
  "last week" — the AZMet steps build the start date from the input literally.
- Keep each AZMet call to **31 days or fewer**; `timeInterval` is
  `P{days−1}DT0H` (7 days = `P6DT0H`).
- **Schedule:** Mondays, 7 AM America/Phoenix (Arizona keeps no daylight saving),
  May through October. Schedules are private to whoever creates them, so run it
  from the account that should own it.
- **NWS caveat:** the NWS API expects a `User-Agent` header. If the connector
  returns a 403, test it in chat and, if needed, fall back to a manual forecast
  until the header is sorted.
- **Heat units** are intentionally left out: the AZMet field uses 94/55 °F
  thresholds, while AZMet's historical cotton advisories used 86/55 °F. Confirm
  the standard with AZMet/Extension before adding them back.
