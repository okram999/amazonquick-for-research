# Weekly Pinal cotton advisory: manual build in the Flow editor

**Version 3.** The forecast now comes from the National Weather Service connector, not web search. The Draft advisory prompt is the simplified one. Heat units are left out.

## What the flow does
Every Monday in cotton season, it pulls last week's AZMet weather for Maricopa and Coolidge, works out heat, rain, crop water demand, heat-stress days and crop water use by planting date, adds the NWS 7-day forecast, and drafts a one-page advisory. Marcus reviews it and sends it to growers. Growers never use Quick.

## Final step order

Step numbers below are the order you **build** the steps. The order they **sit** in the editor is different, because Draft advisory has to stay at the bottom: a step can only @-reference steps above it.

| Position in editor | Step | Type | Build step | Pass |
|---|---|---|---|---|
| 1 | Week start | User input (text) | Step 1 | 1 |
| 2 | Maricopa week | Action: AZMet Daily Weather | Step 2 | 1 |
| 3 | Coolidge week | Action: AZMet Daily Weather | Step 3 | 1 |
| 4 | Maricopa forecast | Action: NWS Pinal Forecast | Step 5 | 2 |
| 5 | Coolidge forecast | Action: NWS Pinal Forecast | Step 6 | 2 |
| 6 | Forecast summary | General knowledge | Step 7 | 2 |
| 7 | Prior five years | Reasoning group | Step 8 | 3 |
| 8 | Draft advisory | General knowledge | Step 4 | 1 |

Pass 1 is complete with Steps 1 to 4. Every step added in Passes 2 and 3 goes **between Coolidge week and Draft advisory**.

## Before you start: two connectors

| Connector | File | Base URL | Status |
|---|---|---|---|
| AZMet Daily Weather | `azmet_openapi.json` | `https://api.azmet.arizona.edu` | Done and tested |
| NWS Pinal Forecast | `nws_forecast_openapi.json` | `https://api.weather.gov` | Create it for Pass 2 |

Both are public APIs, so choose **None** for authentication. To create one, go to Connectors -> Create for your team -> OpenAPI Specification -> Import Schema.

## Pass 1: get the draft working (Steps 1 to 4)

### Step 1: User input
- **Title:** `Week start`
- **Placeholder:** `Monday date, YYYY-MM-DD`
- **Default value:** `2026-08-24`

### Step 2: Actions -> AZMet Daily Weather -> getDailyObservations
- **Title:** `Maricopa week`
- **Prompt:**
```
Get daily observations for stationID az06. startDate is @Week start followed by T00:00, for example 2026-08-24T00:00. timeInterval is P6DT0H.
```

### Step 3: Actions -> AZMet Daily Weather -> getDailyObservations
- **Title:** `Coolidge week`
- **Prompt:** the same as Step 2, but with stationID **az05**.

### Step 4: General knowledge (directly below Coolidge week)
- **Title:** `Draft advisory`
- **Output preference:** Versatility and performance
- **Creativity:** low (about 1)
- **Prompt:** type each @ reference by typing @ and picking the step from the menu. Replace [phone] before running.
```
Write a weekly cotton advisory for Pinal County growers from @Maricopa week (station az06) and @Coolidge week (station az05). Each has a list of daily records. Ignore any value of -999 or lower.

For each station, report:
- average daily high (temp_air_maxF) and low (temp_air_minF)
- total rain (precip_total_in)
- total reference ET (eto_pen_mon_in)
- days with heatstress_cotton_meanF above 86 (Level 2) and above 82 up to 86 (Level 1)

Crop water use: for crops planted Mar 15, Apr 1, Apr 15, May 1 and May 15, count the days from planting to the Thursday of this week (D). If D is 80 to 139, Kc = 1.175. If D is 140 or more, Kc = 1.175 - 0.0105 x (D - 140), but not below 0.60. If D is under 80, Kc = 0.35 + 0.0165 x (D - 30), but not below 0.35. Water use = Kc x the station's total reference ET, rounded to 0.01 inch.

Format:
DRAFT - for Extension review
Pinal County Cotton Advisory, then the first and last dates in @Maricopa week, written like Aug 24 to Aug 30, 2026
A two-sentence summary in plain language.
One table per station for this week.
One table of crop water use by planting date, then the line: Compare this with the water you applied this week.
Questions? Contact Pinal County Cooperative Extension at [phone].

Cite the station ID and dates for each number. Don't recommend irrigation amounts. At the end, under Workings, list D and Kc for each planting date.
```

### Test Pass 1
Run with `2026-08-24` and check the result against **Week 1** in the answer key below.

## Pass 2: add the forecast (Steps 5 to 7, plus one line)

### Create and test the NWS connector
1. Import `nws_forecast_openapi.json` and name it **NWS Pinal Forecast**. It has two actions with no inputs: `getMaricopaForecast` and `getCoolidgeForecast`.
2. Test it in chat: "Using NWS Pinal Forecast, get the Maricopa forecast and list each period's name and temperature." You should get 14 periods.
3. If it fails with a 403 error, NWS rejected the request because Quick didn't send a User-Agent header. Skip Pass 2 for the talk.

Insert Steps 5, 6 and 7 in this order **between Coolidge week and Draft advisory**.

### Step 5: Actions -> NWS Pinal Forecast -> getMaricopaForecast
- **Title:** `Maricopa forecast`
- **Prompt:** `Get the Maricopa forecast.`

### Step 6: Actions -> NWS Pinal Forecast -> getCoolidgeForecast
- **Title:** `Coolidge forecast`
- **Prompt:** `Get the Coolidge forecast.`

### Step 7: General knowledge
- **Title:** `Forecast summary`
- **Creativity:** low (about 1)
- **Prompt:**
```
From @Maricopa forecast and @Coolidge forecast, use properties.periods. Day periods (isDaytime true) give highs; night periods give lows. Write two short plain sentences for Pinal County cotton growers: the range of highs and lows over the next 7 days at Maricopa and at Coolidge, and the highest chance of rain. Then add one line: Forecast issued [date from properties.updateTime, in Arizona time] by the National Weather Service. Plain text only.
```

### Add one line to Draft advisory
In the Format section, after the "Compare this with the water you applied this week" line:
```
Next week: copy @Forecast summary exactly as written.
```

### Test Pass 2
Open the **Forecast summary** output first, then check that the same sentences appear under "Next week" in the draft. The forecast is always for the week after the day you run the flow, even when you test on a past week.

## Pass 3: add the five-year comparison (Step 8, plus one line)

### Step 8: Flow logic -> Reasoning group
Place it between Forecast summary and Draft advisory.
- **Title:** `Prior five years`
- **Instructions:**
```
For each of the five years before the year in @Week start, get the same seven calendar dates for station az06 and station az05 using the action inside this group. Collect all ten results.
```
- **Inside the group,** add one action step: AZMet Daily Weather -> getDailyObservations, with this prompt:
```
Get daily observations for the station and year being processed. startDate is the same month and day as @Week start in that year, followed by T00:00. timeInterval is P6DT0H.
```

### Add one line to Draft advisory
```
Also calculate average high and low, rain, reference ET and heat-stress days for each prior year in @Prior five years, average them, and add a 5-year average column to each station's table.
```

### Test Pass 3
Check against the 5-year averages in the answer key below.

## Optional: heat-stress wording
Once Marcus provides Extension's approved wording, add this line to the Format section of Draft advisory:
```
Heat stress: use exactly this wording: [Marcus's approved sentence].
```
Until then, use the neutral version: "Level 1 and Level 2 are AZMet estimates of heat stress in the cotton canopy. Level 2 is the more severe."

## Answer key

### Week 1 (main test): Aug 24 to Aug 30, 2026, Monday to Sunday

| This week | Maricopa (az06) | Coolidge (az05) |
|---|---|---|
| Avg high / low (°F) | 109.3 / 79.5 | 108.0 / 77.3 |
| Rain (in) | 0.00 | 0.00 |
| Reference ET (in) | 1.99 | 2.08 |
| Level 2 / Level 1 heat-stress days | 5 / 2 | 2 / 5 |

| Crop water use (in/week) | Maricopa (az06) | Coolidge (az05) |
|---|---|---|
| Planted Mar 15 (D = 165, Kc 0.91) | 1.82 | 1.90 |
| Planted Apr 1 (D = 148, Kc 1.09) | 2.17 | 2.27 |
| Planted Apr 15 (D = 134, Kc 1.175) | 2.34 | 2.44 |
| Planted May 1 (D = 118, Kc 1.175) | 2.34 | 2.44 |
| Planted May 15 (D = 104, Kc 1.175) | 2.34 | 2.44 |

D is counted to Thursday, Aug 27. The single-coefficient method is within 0.01 in of a day-by-day calculation.

### Week 2 (heat wave): Jul 27 to Aug 2, 2026, Monday to Sunday

| This week | Maricopa (az06) | Coolidge (az05) |
|---|---|---|
| Avg high / low (°F) | 110.5 / 86.2 | 108.6 / 82.1 |
| Rain (in) | 0.02 | 0.02 |
| Reference ET (in) | 2.51 | 2.43 |
| Level 2 / Level 1 heat-stress days | 7 / 0 | 7 / 0 |
| Water use, planted Mar 15 to May 1 | 2.95 | 2.86 |
| Water use, planted May 15 (D = 76, Kc 1.109) | 2.78 | 2.69 |

### 5-year averages, 2021 to 2025, same dates

| | Aug 24-30: Maricopa | Aug 24-30: Coolidge | Jul 27-Aug 2: Maricopa | Jul 27-Aug 2: Coolidge |
|---|---|---|---|---|
| Avg high / low (°F) | 105.6 / 77.4 | 104.3 / 73.9 | 105.5 / 79.7 | 104.0 / 75.9 |
| Rain (in) | 0.17 | 0.12 | 0.04 | 0.11 |
| Reference ET (in) | 1.83 | 1.75 | 2.10 | 1.99 |
| Level 2 / Level 1 days | 1.8 / 3.8 | 0.8 / 3.4 | 2.2 / 4.4 | 1.0 / 3.6 |

For Aug 24-30, 2026 had the highest reference ET of the six years at both stations.

### Forecast check (forecast issued Oct 2, 2026, 11:32 am Arizona time)
- Maricopa: highs 93 to 99°F, lows 73 to 76°F, rain chance 1% or less
- Coolidge: highs 90 to 96°F, lows 70 to 74°F, rain chance 1% or less

NWS updates the forecast several times a day, so later runs will show different numbers.

## Why heat units were removed
The API's `heat_units_9455F` field uses 94/55°F thresholds. AZMet's older cotton advisories used 86/55°F, which is the scale growers' stage rules of thumb are based on. For example, the 2021 Coolidge advisory reported 3,539 heat units from Mar 15 to Aug 29. The API's 94/55 field adds up to 3,935 for the same dates, while 86/55 calculated from daily highs and lows gives 3,547. Ask AZMet or Extension which standard they use for cotton before adding heat units back.

## Things Marcus should own
- The heat-stress wording
- The contact line and phone number
- Reviewing every issue before it goes to growers

## Scheduling
- **Input:** the AZMet steps build the start date from `@Week start`, so the input must be a date (YYYY-MM-DD). The words `last week` won't work. Scheduled runs reuse the same default input every time, so update the date each week. A step that works out last Monday's date automatically is a later improvement.
- **Schedule:** weekly on Mondays at 7:00 am, America/Phoenix, May 1 to Oct 31, from Marcus's account so the completion email goes to him.
- **Action permissions:** both connectors are read-only, so **Run with no confirmation** is safe.

## Troubleshooting

### "Invalid input detected" on Draft advisory
1. **Check the references.** Each @ reference should appear as a chip. If one is plain text or blank, delete it, type @ and pick the step from the menu.
2. **Remove the newest addition and run again.** If it works, add that line back and check its @ reference.
3. **Use plain characters only:** the letter x and an ordinary hyphen in formulas, and straight quotes.

### The connector test returns nothing
Check the inputs: `az06`, `2026-08-24T00:00`, `P6DT0H`. A missing `T00:00` or a wrong interval such as `P7D` returns nothing.

### Forecast summary says no data
Run the Maricopa forecast step on its own. If it fails with 403, skip the forecast for the talk.
