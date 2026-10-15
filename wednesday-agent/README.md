# Wednesday — Analysis

**Goal:** build a custom agent, the **Pinal Cotton Water Advisor**, that pulls
live weather through a connector and applies a published method to estimate how
a water cut could affect cotton yield.

## What's here

| File | What it is |
|------|-----------|
| `pinal_cotton_method_card.md` | The agent's **reference document**: pre-calculated season crop water demand (both Pinal stations, 2021–2026), the FAO formula, worked examples, assumptions and limits. |
| `cotton_agent_setup.md` | Step-by-step build guide: the exact text to paste into each builder field, five test prompts with an answer key, and a short demo script. |
| `connectors/azmet_openapi.json` | OpenAPI spec for the AZMet daily-weather connector (public API, no authentication). |
| `connectors/nws_forecast_openapi.json` | OpenAPI spec for the National Weather Service forecast connector (used by the Friday flow). |

## Build order

Follow `cotton_agent_setup.md` — it has the field-by-field text. In outline:

1. **Create the connectors first.** In Quick: **Connectors → Create for your
   team → OpenAPI Specification**, and import `connectors/azmet_openapi.json`.
   Because the spec declares no security scheme, it becomes a no-sign-in
   connector. Repeat for `nws_forecast_openapi.json` (needed Friday).
2. **Create the agent** (Chat agents → Create → Skip, for builder mode).
3. Paste in the name, description, identity, instructions and style from the
   setup guide.
4. **Upload `pinal_cotton_method_card.md` as a reference document** so the agent
   has it on every answer.
5. **Link your Space** (the one with the AZMet file and the paper library) as
   the knowledge source.
6. **Link the AZMet connector's** `getDailyObservations` action.
7. **Update preview**, run the five tests, then **Launch**.

## The method, in one line

```
1 − Ya/Ym = Ky × (1 − ETa/ETm)
```

Relative yield loss = Ky (cotton's published sensitivity to water) × the
relative shortfall in crop water use. Season Ky for cotton is 0.85; by stage it
is 0.2 (vegetative) / 0.5 (bloom) / 0.25 (ripening), which is why *when* the
water runs short matters as much as how much.

## Why a method card instead of live calculation

Season crop water demand is a sum over 169 daily values (ETo × Kc, April 15 to
September 30). Recalculating that live is slow and error-prone, so it is
pre-computed in the method card (43.9 in for Maricopa/az06 in 2026, with all six
seasons and both stations tabulated). The agent looks that up, then does the
quick per-question steps live: pull the relevant weather, apply the cut, run the
formula.

## A test to run

> A grower near Maricopa is planning next season. If irrigation water is cut
> 20% and we get a summer like 2026, what could that do to cotton yield, and
> does timing matter?

Expect roughly **16% / 18% / 21%** lint-yield loss for protect-bloom / uniform /
cut-hits-bloom timing, on a 43.9-in season demand and an 8.8-in cut. The agent
should state its assumptions and cite AZMet and the FAO papers. Full answer key
is in `cotton_agent_setup.md`.

## Important

This is a **published formula with stated assumptions, not a validated yield
model.** The agent should present a range, not a forecast, and keep a person in
the loop — Extension advice goes out under the university's name, so a staff
member reviews every answer before advising a grower. Growers never use the
agent.
