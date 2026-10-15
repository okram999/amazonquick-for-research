# Pinal cotton method card

Reference document for the Pinal Cotton Water Advisor agent. Upload it under **Reference documents** so the agent always works from these numbers and rules.

## 1. Who uses this
University of Arizona Cooperative Extension staff and researchers. They review the agent's answers before advising any grower. Growers don't use the agent.

## 2. Stations

| Station ID | Name | County | Notes |
|---|---|---|---|
| az06 | Maricopa | Pinal | At the U of A Maricopa Agricultural Center, in the city of Maricopa (Pinal County, not Maricopa County) |
| az05 | Coolidge | Pinal | |

## 3. Water source context
Treat a "water cut" as a cut in the total irrigation water available for the season, from any source. Many Pinal County growers have relied mostly on groundwater since the 2022-2023 Colorado River shortage cuts to CAP agricultural water. Don't assume a cut is a CAP cut.

## 4. Season crop water demand (pre-calculated)
Calculated from AZMet daily reference ET (field eto_pen_mon_in), multiplied by FAO-56 (1998) cotton crop coefficients, for a crop planted April 15 and counted through September 30.

Crop coefficients by days after planting (D):
- D 0 to 29: Kc = 0.35
- D 30 to 79: Kc rises in a straight line from 0.35 to 1.175
- D 80 to 139: Kc = 1.175
- D 140 to 194: Kc falls in a straight line from 1.175 toward 0.60

Growth stages used for the yield calculation:
- **Vegetative:** D 0 to 79 (Apr 15 to Jul 3)
- **Bloom (flowering and boll set):** D 80 to 139 (Jul 4 to Sep 1)
- **Ripening:** D 140 to 168 (Sep 2 to Sep 30)

### Crop water demand by stage, inches

| Station | Season | Vegetative | Bloom | Ripening | Total |
|---|---|---|---|---|---|
| az06 Maricopa | 2021 | 16.09 | 18.72 | 6.66 | 41.47 |
| az06 Maricopa | 2022 | 15.59 | 20.74 | 7.08 | 43.40 |
| az06 Maricopa | 2023 | 15.37 | 22.48 | 7.33 | 45.18 |
| az06 Maricopa | 2024 | 15.56 | 22.08 | 8.04 | 45.68 |
| az06 Maricopa | 2025 | 15.63 | 21.84 | 6.49 | 43.96 |
| az06 Maricopa | 2026 | 16.01 | 21.28 | 6.60 | 43.88 |
| az06 Maricopa | 2021-2025 average | 15.65 | 21.17 | 7.12 | 43.94 |
| az05 Coolidge | 2021 | 15.73 | 16.70 | 5.68 | 38.11 |
| az05 Coolidge | 2022 | 15.57 | 19.28 | 6.80 | 41.66 |
| az05 Coolidge | 2023 | 15.05 | 21.75 | 6.76 | 43.55 |
| az05 Coolidge | 2024 | 15.74 | 20.89 | 7.68 | 44.32 |
| az05 Coolidge | 2025 | 16.13 | 21.57 | 6.78 | 44.49 |
| az05 Coolidge | 2026 | 16.52 | 21.13 | 6.53 | 44.17 |
| az05 Coolidge | 2021-2025 average | 15.65 | 20.04 | 6.74 | 42.43 |

Use these values. Don't recalculate season totals from daily data.

## 5. Yield response method
FAO Irrigation and Drainage Paper 33 (Doorenbos and Kassam, 1979), as restated in FAO Irrigation and Drainage Paper 66 (2012):

**1 - Ya/Ym = Ky x (1 - ETa/ETm)**

| Term | Meaning |
|---|---|
| Ya / Ym | Actual yield / maximum yield with full water. 1 - Ya/Ym is the yield loss. |
| ETa / ETm | Actual crop water use / full crop water demand. 1 - ETa/ETm is the water shortfall. |
| Ky | Yield response factor: a published coefficient for how strongly yield responds to a water shortfall. |

In words: relative yield loss = Ky x relative shortfall in crop water use. Ky above 1 means yield falls faster than water; below 1, slower.

Cotton yield response factors (Ky):
- Whole season: 0.85
- Vegetative: 0.20
- Bloom: 0.50
- Ripening: 0.25

The 2025 revised FAO-56 lists a seasonal cotton Ky range of 0.85 to 1.25. Mention it as a sensitivity: with Ky 1.25, a 20% shortfall gives about 25% loss.

### Where Ky comes from
Ky is an empirical coefficient, fitted to field trials. The cotton values originate in FAO-33 and are reprinted in later FAO papers:

| Value | Where it is printed |
|---|---|
| Season Ky 0.85 | FAO-66 (2012), Table 1, Seasonal Ky values from FAO-33 (PDF p. 22); FAO-56 (1998), Table 24 (PDF p. 207) |
| Season Ky 0.85 to 1.25 | FAO-56, 2025 revision, Table 8.7 (PDF p. 311) |
| Stage Ky 0.20 / 0.50 / 0.25 | FAO-66 (2012), Table 2, FAO-33 column (PDF p. 27, printed p. 12) |

**How FAO-66 Table 2 labels the stages.** The table uses a four-stage scheme and lists FAO-33's cotton values under: initial stage 0.20, crop development 0.50, midseason (no FAO value), late season 0.25. This card applies them as vegetative 0.20, bloom 0.50 and ripening 0.25, so the 0.50 falls on bloom (flowering and boll set). Using bloom as the most water-sensitive stage matches Thorp et al. (2020), who found full irrigation was needed from peak bloom on. However, the stage labels in FAO-66 Table 2 don't line up one-to-one with this card's stages. If asked, say so plainly, and present the stage results as an illustration of why timing matters, not as precise stage-by-stage numbers.

**How much Ky varies.** FAO-66 Table 2 also compares FAO-33's values with IAEA field trials (FAO Water Report 22, 2002). For cotton:
- Whole season: FAO 0.85; IAEA 0.71 to 1.02.
- Initial stage: FAO 0.20; IAEA 0.75 to 0.80.
- Crop development: FAO 0.50; IAEA 0.48 to 0.76.

FAO-66 notes that Ky can vary about as much within one crop as between crops. It calls the method useful for planning and quick estimates, but of limited use for on-farm decisions. Treat every result as a planning estimate.

## 6. How to calculate a water-cut scenario
Inputs: station, season (default 2026), cut as a share of season crop water demand (for example 20%).

1. **Cut in inches** = cut share x Total for that station and season.
2. Work out the shortfall in each stage under three timings:
   - **A. Uniform:** every stage short by the same share as the cut.
   - **B. Protect bloom:** take the cut from Vegetative first, up to 40% of that stage's demand. Take any remainder from Ripening, up to 50% of that stage. Take anything still left from Bloom.
   - **C. Cut lands in bloom:** take the whole cut from Bloom.
3. Stage shortfall = inches taken from that stage / that stage's demand.
4. **Yield loss** = 1 - (1 - 0.20 x vegetative shortfall) x (1 - 0.50 x bloom shortfall) x (1 - 0.25 x ripening shortfall).
   Each bracket is the section 5 formula applied to one stage with that stage's Ky. Multiplying the stage terms is the multiplicative form of Rao et al. (1988); FAO-33 gives the stage Ky values but not how to combine shortfalls in several stages.
5. **Lint lost (lb/ac)** = yield loss x expected lint yield.
6. If any stage shortfall is above 50%, say the result is outside the range where the FAO method is reliable.
7. Also report the season shortcut: 0.85 x cut share.

**Why uniform and the season shortcut differ.** A 20% uniform cut gives 17.9%, not 17.0%, because the three stage Ky values add up to 0.95 (0.20 + 0.50 + 0.25), not 0.85. The arithmetic is right; the two FAO numbers just don't add up exactly. Report both.

## 7. Worked examples (use these to check your arithmetic)

### Maricopa (az06), 2026, Total 43.88 in

| Cut | Cut (in) | A. Uniform | B. Protect bloom | C. Cut lands in bloom | Season shortcut |
|---|---|---|---|---|---|
| 10% | 4.39 | 9.2% | 5.5% | 10.3% | 8.5% |
| 20% | 8.78 | 17.9% | 16.3% | 20.6% | 17.0% |
| 30% | 13.17 | 26.1% | 26.1% | 30.9% (bloom shortfall 62%: outside reliable range) | 25.5% |

Stage shortfalls for the 20% case:
- A: 20% in every stage.
- B: vegetative 6.40 in (40%); ripening 2.37 in (36%); bloom 0.
- C: bloom 8.78 in (41%).

At 1,413 lb/ac expected lint, a 20% cut means about 253 lb/ac (A), 230 lb/ac (B) or 291 lb/ac (C).

### Coolidge (az05), 2026, Total 44.17 in

| Cut | Cut (in) | A. Uniform | B. Protect bloom | C. Cut lands in bloom | Season shortcut |
|---|---|---|---|---|---|
| 20% | 8.83 | 17.9% | 15.8% | 20.9% | 17.0% |
| 30% | 13.25 | 26.1% | 25.9% | 31.4% (bloom shortfall 63%: outside reliable range) | 25.5% |

## 8. Expected yield
If the user doesn't give the grower's expected lint yield, use **1,413 lb/ac**, and say it's an illustration. It converts the mean fiber yield of 1,583.7 kg/ha across **all** irrigation treatments, including the deficit ones, in Thorp et al. (2020), Maricopa 2016-2018. A fully irrigated field would usually yield more, so ask for the grower's own number.

## 9. 2026 heat waves (identified from AZMet daily data)
Pull these dates live from the AZMet connector to confirm and present them:
- **Maricopa (az06), Jul 29 to Aug 4, 2026:** the hottest 7-day stretch of the year. Highs averaged 111.1°F (peak 113.7°F). Lows averaged 86.0°F (82.4 to 88.7°F). No rain. Reference ET 2.47 in. 7 of 7 days at Level 2 cotton heat stress. Same dates, 2021-2025 average: highs 106.4°F, lows 80.2°F, reference ET 2.11 in, 2.6 Level 2 days.
- **Coolidge (az05), Aug 22 to 28, 2026:** highs averaged 109.1°F, lows 77.1°F, rain 0.17 in, reference ET 2.09 in, 3 Level 2 and 4 Level 1 days. 2021-2025 average: highs 103.7°F, reference ET 1.76 in.

Cotton heat-stress levels come from heatstress_cotton_meanF (estimated canopy temperature): Level 2 above 86.0°F; Level 1 above 82.0°F up to 86.0°F. These cutoffs were inferred from AZMet's published labels; confirm them with AZMet.

## 10. Assumptions and limits (always state the first three)
1. The cut passes one-for-one into the crop's water shortfall. There's no soil-water buffer and no extra water from another source.
2. The heat wave's main risk is heat stress on squares and young bolls, which this water method doesn't capture. Season water demand in 2026 (43.9 in at Maricopa) matched the 2021-2025 average.
3. Field results vary widely. Thorp et al. (2020) found early-season cuts of up to about 70 mm (6% of total water) cost no yield, but full irrigation was needed from peak bloom on. A flood-irrigated Arizona trial (Agronomy, 2025) saw 38-45% fiber-yield loss at 80% of crop ET.
4. One planting date (April 15) and the 1998 FAO-56 coefficients. The 2025 FAO-56 revision lists lower cotton coefficients (0.40 / 1.10 / 0.50), which give about 42.2 in season demand at Maricopa in 2026.
5. This is a published formula, not a validated yield model.

## 11. AZMet connector quick reference
- Action: getDailyObservations (stationID, startDate, timeInterval).
- startDate: YYYY-MM-DDT00:00. timeInterval: P{number of days minus 1}DT0H. For example, 7 days is P6DT0H.
- Keep each call to 31 days or fewer.
- Useful fields: temp_air_maxF, temp_air_minF, precip_total_in, eto_pen_mon_in, heatstress_cotton_meanF, meta_needs_review.
- Ignore values of -999 or lower.

## 12. Sources
- AZMet daily observations, api.azmet.arizona.edu (stations az06 and az05).
- Allen et al. (1998), FAO Irrigation and Drainage Paper 56, Tables 11 and 12.
- FAO Irrigation and Drainage Paper 56, second edition, revised 2025.
- Doorenbos and Kassam (1979), FAO Irrigation and Drainage Paper 33; Steduto et al. (2012), FAO Irrigation and Drainage Paper 66.
- Thorp, Thompson and Bronson (2020), Agricultural Water Management 234:106146.
- Simulating Water Use and Yield for Full and Deficit Flood-Irrigated Cotton in Arizona, USA. Agronomy 2025, 15(9), 2023.
