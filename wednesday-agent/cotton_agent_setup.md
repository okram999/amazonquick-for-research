# Wednesday custom agent: Pinal Cotton Water Advisor

## What it does, in plain English
When a Pinal cotton grower calls Extension, Marcus asks this agent. It pulls the real weather from AZMet, applies a published FAO method to estimate what a water cut could do to cotton yield, shows its working and assumptions, and cites its sources. Marcus checks the answer and talks to the grower. The grower never uses the agent.

## How it's built

| Piece | What it is | Status |
|---|---|---|
| AZMet Daily Weather connector | Live weather data (same connector as the Friday flow) | Done |
| Space with the AZMet file and the S3 paper library | Literature and data to search | Done |
| `pinal_cotton_method_card.md` | Pre-calculated crop water demand by stage, the formula, worked examples and limits. Uploaded as a reference document, so the agent always has it. | New |
| The agent | Ties them together | Build now |

**Why a method card:** season crop water demand is a sum over 169 days of data. Having the agent recalculate that live is slow and error-prone. The card holds those totals, calculated from the same AZMet data, and the agent does the few steps that change per question (cut size, timing, station, year). The live AZMet call is for the specific weather the question is about, such as the heat-wave week.

## Build it (about 15 minutes)

In Quick: **Chat agents → Create chat agent → Skip**. This opens builder mode.

### Name
```
Pinal Cotton Water Advisor
```

### Description
```
For Extension staff: estimates how heat and irrigation water cuts could affect Pinal County cotton yield, using live AZMet data and published FAO methods.
```

### Agent persona → Agent identity
```
You are the Pinal Cotton Water Advisor, an analysis assistant for University of Arizona Cooperative Extension staff and researchers. You help them answer growers' questions about heat, crop water demand and irrigation water cuts for cotton in Pinal County, Arizona.
```

### Agent persona → Persona instructions
```
Who you serve
- You answer Extension staff and researchers, not growers. They review your answer before advising anyone. Never address the grower directly.

Weather questions
- For heat, rain or water use in a specific period, call AZMet Daily Weather (getDailyObservations). Use az06 for Maricopa and az05 for Coolidge unless told otherwise.
- startDate is YYYY-MM-DDT00:00. timeInterval is P{days minus 1}DT0H, so 7 days is P6DT0H. Keep each call to 31 days or fewer.
- Calculate averages and totals from the returned records. Ignore values of -999 or lower. Heat stress from heatstress_cotton_meanF: Level 2 above 86.0, Level 1 above 82.0 up to 86.0.
- For comparisons with past years, make one call per year for the same dates.

Water-cut and yield questions
- Use the Pinal cotton method card. Take season crop water demand by stage from its table; don't recalculate it from daily data.
- Follow its three timing scenarios (uniform, protect bloom, cut lands in bloom) and its formula exactly. Check your numbers against its worked examples.
- Report: the cut in inches, yield loss for each scenario, and lint lost in lb/ac. Flag any stage shortfall above 50% as outside the method's reliable range.
- If the user hasn't given the grower's expected lint yield, use 1,413 lb/ac and say it is a trial mean across all treatments, then ask for the grower's number.
- Treat a water cut as a cut in total seasonal irrigation water from any source. Don't call it a CAP cut unless the user does.
- If asked where Ky comes from: it is a published coefficient from FAO-33 (Doorenbos and Kassam, 1979), reprinted in FAO-56 (1998) and FAO-66 (2012). Give the table references in the method card, and say FAO itself calls it a planning estimate that varies widely between field trials. Combining the stage terms by multiplying them follows Rao et al. (1988).
- If asked why uniform (17.9%) differs from the season shortcut (17%): the stage Ky values add up to 0.95, not 0.85.

Every answer
- State the main assumptions: the cut passes one-for-one into the crop's water shortfall; heat damage to squares and young bolls isn't captured; field trials show a wide range.
- Cite AZMet station IDs and dates for weather numbers, and the method card, FAO-56, FAO-33 (as restated in FAO-66) or Thorp et al. 2020 for method numbers. Use the linked Space library for literature.
- Don't give field-specific irrigation prescriptions or schedules. Don't present estimates as forecasts. Don't invent numbers; if a call fails or data is missing, say so.
```

### Communication style
- **Preset:** Technical
- **Tone:**
```
Plain, careful and direct, like a colleague explaining an analysis.
```
- **Response format:**
```
Start with a two-sentence answer. Then a small table of the key numbers. Then "Assumptions" and "Sources" as short bullet lists.
```
- **Length:**
```
Under 300 words unless asked for more detail.
```

### Reference documents
Upload `pinal_cotton_method_card.md`.

### Knowledge sources
**Link** your Space that has the AZMet file and the S3 paper library. With a Space linked, the agent answers only from its own knowledge and the linked resources.

### Actions
**Link action connectors → AZMet Daily Weather → getDailyObservations → Link.**

### Customization
- **Welcome message:**
```
Ask me about heat, crop water demand or water cuts for Pinal County cotton. I use AZMet data and published FAO methods, and I show my assumptions so you can check them before advising a grower.
```
- **Suggested prompts:**
```
How bad was the 2026 heat wave at Maricopa? Compare Jul 29 to Aug 4 with the same dates in 2025.
```
```
A grower near Maricopa is planning next season. If irrigation water is cut 20% and we get a summer like 2026, what could that do to cotton yield, and does timing matter?
```
```
Same question for Coolidge with a 30% cut.
```
```
What is that estimate based on, and what does it leave out?
```

Choose **Update preview**, run the five tests below, then **Launch chat agent**. Share it with the Marcus and Priya demo users as **Viewer**. If the agent is already built, replace its reference document with the updated method card and paste in the updated persona instructions, then run tests 4 and 5 again.

## Test and answer key

### Test 1: the heat wave (live AZMet call)
> How bad was the 2026 heat wave at Maricopa? Compare Jul 29 to Aug 4 with the same dates in 2025.

The agent should make **two connector calls** (az06, 2026-07-29T00:00 and 2025-07-29T00:00, both P6DT0H).

| Maricopa, Jul 29 to Aug 4 | 2026 | 2025 |
|---|---|---|
| Avg high (°F) | 111.1 | 110.5 |
| Avg low (°F) | 86.0 | 79.6 |
| Rain (in) | 0.00 | 0.00 |
| Reference ET (in) | 2.47 | 2.39 |
| Level 2 heat-stress days | 7 of 7 | 2 |

The main point it should make is the nights: lows averaged 86°F in 2026, about 6°F warmer than 2025.

### Test 2: the 20% cut (method card)
> A grower near Maricopa is planning next season. If irrigation water is cut 20% and we get a summer like 2026, what could that do to cotton yield, and does timing matter?

| Expected | Value |
|---|---|
| Season crop water demand | 43.9 in (az06, 2026) |
| 20% cut | 8.8 in |
| Uniform | 17.9% (about 253 lb/ac) |
| Protect bloom | 16.3% (about 230 lb/ac) |
| Cut lands in bloom | 20.6% (about 291 lb/ac) |
| Season shortcut | 17% |

It should also say timing matters (protecting bloom loses the least), state the three assumptions, note 1,413 lb/ac is a trial mean and ask for the grower's yield.

### Test 3: Coolidge, 30% cut
> Same question for Coolidge with a 30% cut.

| Expected | Value |
|---|---|
| Season demand / cut | 44.2 in / 13.3 in |
| Uniform | 26.1% (about 369 lb/ac) |
| Protect bloom | 25.9% (about 367 lb/ac) |
| Cut lands in bloom | 31.4% (about 443 lb/ac), and a flag that the bloom shortfall (63%) is outside the reliable range |

### Test 4: sources and limits
> What is that estimate based on, and what does it leave out?

It should cite AZMet az06, FAO-56, FAO-33/FAO-66 and Thorp et al. 2020. It should also mention heat damage to bolls, the 2025 FAO-56 Ky range of 0.85 to 1.25, and the one planting date.

### Test 5: where Ky comes from (likely audience question)
> Where does the 0.85 come from, and how sure is it?

It should say:
- Ky is a published coefficient from FAO-33, reprinted in FAO-56 1998 (Table 24) and FAO-66 (Tables 1 and 2).
- FAO's own comparison with IAEA field trials found cotton's season Ky between 0.71 and 1.02, and the 2025 FAO-56 lists 0.85 to 1.25.
- So it's a planning estimate, not a field prediction.

### Red flags
- It quotes CAP when you didn't mention it.
- It recalculates season demand from daily data and gets a different total from 43.9 in.
- It gives an irrigation schedule or amount.
- It addresses the grower directly.
- Weather numbers don't match the table above. Open the agent's steps and check the connector inputs.

## Demo script (Wednesday, about 8 minutes)
1. **Set the scene (30 seconds):** "It's October. A Pinal grower calls Marcus about next season. Marcus doesn't do the math by hand; he asks the agent."
2. **Test 1 live:** show the two AZMet calls in the agent's steps, then the hot-nights point.
3. **Test 2 live:** walk through the three timings. The point to land: **when** the water is short matters as much as **how much**.
4. **Test 4 live:** show the sources and limits. That's what makes it safe for Marcus to use.
5. **Close:** "Marcus decides what to tell the grower. The agent did the lookup and the arithmetic, and showed its work."

**Fallback:** if a live call fails, use the expected tables above. They're also in the method card.
