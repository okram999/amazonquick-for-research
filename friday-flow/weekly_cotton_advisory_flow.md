# Weekly Pinal cotton advisory: build guide

> **Superseded for building the flow.** Use `manual_build_steps.md` (version 3) in this folder. This file is still current for **Step 1: Create the connector**. Don't use its `last week` scheduling advice or its heat units; see `manual_build_steps.md`.

## What the flow does, in plain English
Every Monday during cotton season, the flow looks up last week's weather at the Maricopa and Coolidge AZMet stations. It compares that week with the same week in each of the last five years and works out three things: how hot it was, how much water cotton likely used, and how many days the crop was under heat stress. It then writes a one-page draft advisory. Marcus reads it, fixes anything that's wrong, and sends it to growers. Growers never use Quick.

## What you need
| Piece | Status | Why |
|---|---|---|
| **AZMet connector** (`azmet_openapi.json`) | **Build this first** | The Space holds a snapshot that ends Sep 30, 2026. A weekly flow needs new data every week. |
| Space with the AZMet CSV | Already done | Used by the Monday chat and dashboards. Not needed by this flow. |
| S3 knowledge base (papers) | Already done | Optional: lets the draft cite a paper, for example on irrigation timing. |

The connector isn't code. It's a JSON file describing AZMet's public API. AZMet needs no sign-in, and Quick treats a spec with no authentication scheme as a no-auth connector. The same connector also serves the Wednesday cotton agent.

## Step 1: Create the connector (about 5 minutes)
1. In Quick, go to **Connectors → Create for your team → OpenAPI Specification**.
2. Choose **Import Schema** and select `azmet_openapi.json`, then choose **Next**.
3. Name it **AZMet Daily Weather**. You should see one action: `getDailyObservations`.
4. Choose **Create and continue**, then share it with Priya and Marcus.
5. Test it in chat with this prompt:
   > Using AZMet Daily Weather, get daily data for station az06 starting 2026-07-27T00:00 with interval P6DT0H. List each day's date and temp_air_maxF.

   **Expect** 7 days, Jul 27 to Aug 2, 2026, with highs between about 108 and 114°F.

## Step 2: Create the flow
Go to **Flows → Create Flow**, paste the prompt below and choose **Generate Flow**.

```
Create a flow called "Weekly Pinal cotton advisory".

Input: "Week". Either the words "last week" or a Monday date in YYYY-MM-DD form.

1. Work out the Monday-to-Sunday week. If the input is "last week", use the most recent complete Monday-to-Sunday week before today.

2. Using the AZMet Daily Weather connector (getDailyObservations), get that week's daily data for station az06 (Maricopa) and station az05 (Coolidge). Use startDate = the Monday in the form YYYY-MM-DDT00:00 and timeInterval = P6DT0H.

3. Get the same calendar dates for each of the five previous years, for both stations, the same way.

4. For each station, calculate for this week and for the five-year average:
   - average daily high and low (temp_air_maxF, temp_air_minF)
   - total rain (precip_total_in)
   - total reference ET (eto_pen_mon_in)
   - total heat units, 94/55 F (heat_units_9455F)
   - number of days at each cotton heat-stress level, from heatstress_cotton_meanF: Level 2 above 86.0 F, Level 1 above 82.0 F, otherwise none.
   Ignore any value of -999 or lower and any record with meta_needs_review = 1. Report how many were skipped.

5. Estimate this week's cotton water use as total reference ET x crop coefficient. Assume planting on April 15 and use FAO-56 cotton coefficients: 0.35 for days 0-29 after planting, rising in a straight line to 1.175 by day 80, 1.175 from day 80 to day 140, then falling to 0.60 by day 195.

6. Write a one-page draft "Pinal County Cotton Advisory" for growers, with:
   - a two-sentence summary
   - a table for each station: this week vs the five-year average
   - heat-stress days, and estimated crop water use in inches
   - one short, general practical note
   Cite the station ID and dates for every number. Put "DRAFT - for Extension review" at the top. Don't give field-specific prescriptions.

7. Show the draft as the final output.
```

**After it generates, check:**
- Steps 2 and 3 are **action steps** that use AZMet Daily Weather.
- Steps 4 and 5 sit in a **reasoning group**. Step 3 means 10 calls, 5 years for each of 2 stations, so a loop is expected.
- Optional: add a chat agent step that uses your lab-library Space to add one cited sentence on irrigation timing.

## Step 3: Test it on the heat-wave week
Switch to **Run** mode and enter `2026-07-27` (a Monday). Compare the result with this answer key, which I calculated from the AZMet data.

| Jul 27 to Aug 2, 2026 | Maricopa (az06) 2026 | Maricopa, 2021-25 avg | Coolidge (az05) 2026 | Coolidge, 2021-25 avg |
|---|---|---|---|---|
| Avg high (°F) | 110.5 | 105.5 | 108.6 | 104.0 |
| Avg low (°F) | 86.2 | 79.7 | 82.1 | 75.9 |
| Rain (in) | 0.02 | 0.04 | 0.02 | 0.11 |
| Reference ET (in) | 2.51 | 2.10 | 2.43 | 1.99 |
| Heat units, 94/55°F | 258.6 | 237.3 | 247.4 | 225.2 |
| Level 2 heat-stress days | **7 of 7** | 2.2 | **7 of 7** | 1.0 |
| Est. cotton water use (in) | 2.95 | 2.47 | 2.86 | 2.34 |

Cotton water use here is reference ET x 1.175, because days 103-109 after planting fall in mid-season.

The heat-stress cutoffs (above 82°F is Level 1, above 86°F is Level 2) are inferred from AZMet's published Maricopa labels. For example, 82.0 is labeled none, 82.4 is Level 1, 86.0 is Level 1 and 86.2 is Level 2. Confirm the exact cutoffs with AZMet before the pilot.

## Step 4: Schedule it
- **Repeat:** weekly, Monday 7:00 am, time zone **America/Phoenix**. Arizona doesn't observe daylight saving time.
- **Start and end dates:** May 1 to Oct 31. AZMet updates cotton heat-stress values through October 31, so the flow can run for real this month.
- **Default input:** `last week`. A fixed date would repeat the same week every time.
- **Action permissions:** the connector is read-only, so **Run with no confirmation** is safe.
- **Owner:** schedules are private, and the completion email goes to the schedule owner. Create the schedule from **Marcus's** account so the draft goes straight to his inbox.

## Not in version 1
- **Heat units since planting.** That requires pulling about 5 months of daily records per station in every run. Add it later with a small service that returns only the season total. That's the API Gateway and Lambda pattern on the production slide.
- **Sending to growers automatically.** Marcus reviews and sends each issue by design.
