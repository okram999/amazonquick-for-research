# Monday — Data

**Goal:** upload one cleaned weather file to Amazon Quick and ask questions of
it in plain language.

## The file

`azmet_daily_yuma_pinal_2021_2026.csv` — 10,367 daily records from five AZMet
stations, January 1 2021 through September 30 2026.

| Column | Meaning |
|--------|---------|
| `date` | Observation date (YYYY-MM-DD) |
| `station_id` | AZMet station: `az02`, `az14`, `az36`, `az06`, `az05` |
| `station_name` | Human-readable station name |
| `county` | Yuma or Pinal |
| `temp_max_f`, `temp_min_f` | Daily max / min air temperature (°F) |
| `rh_mean_pct` | Mean relative humidity (%) |
| `wind_mean_mph` | Mean wind speed (mph) |
| `precip_in` | Precipitation (inches) |
| `eto_in` | Reference evapotranspiration ETo (inches) — the number growers use to schedule water |
| `needs_review` | `1` if AZMet flagged the record for review, else `0` |

The five stations: **az02** Yuma Valley, **az14** Yuma North Gila, **az36**
Yuma South (Yuma County); **az06** Maricopa, **az05** Coolidge (Pinal County).

## Why it is already cleaned

Raw AZMet data uses sentinel codes such as `-999` for missing values. Summed
as-is, two such days once made a station's season ETo total come out negative.
Those codes have been removed and 11 flagged rows carry `needs_review = 1`. The
cleaning is the point of Monday: the file is prepared once, then everyone asks
questions of the same clean copy.

## Steps

1. Open the **Space** you created in the top-level README and **upload**
   `azmet_daily_yuma_pinal_2021_2026.csv` to it. (You can also upload it to a
   plain chat to try it quickly, but the Space is where the rest of the week
   builds from.)
2. Wait for the file to finish processing.
3. Ask the questions below.

## Example questions and expected answers

> How many days hit 105 °F or more at each Yuma station, January to September,
> every year since 2021?

Expect **Yuma Valley: 72 days in 2026**, second only to 2024 (91).

> Compare January-to-September reference ET (ETo) at each Yuma station: 2026 vs
> the 2021–2025 average.

Expect the **highest of six years at all three** Yuma stations; Yuma Valley
**68.3 in** vs a 64.6 in average.

> What was the hottest 7-day stretch this summer at each station, and how much
> ETo?

Expect **Yuma South, Jul 29 – Aug 4: 115.8 °F** average high, 2.97 in.

## The takeaway for the room

2026 was **not** the hottest year by hot-day count — 2024 was. But 2026 was the
**thirstiest and driest** of the six years at all three Yuma stations. That is
the kind of nuance that gets lost in a hand-built Monday summary.
