# Sources and licenses

## Weather data

- **Arizona Meteorological Network (AZMet)**, University of Arizona.
  Daily observations API: `https://api.azmet.arizona.edu`. Data served from
  January 1, 2021. Stations used: az02, az14, az36 (Yuma County); az06, az05
  (Pinal County).

## Papers bundled in `papers/`

- **FAO-56 (1998).** Allen, R.G., Pereira, L.S., Raes, D. & Smith, M. (1998).
  *Crop evapotranspiration — Guidelines for computing crop water requirements.*
  FAO Irrigation and Drainage Paper 56. Food and Agriculture Organization of
  the UN. Supplies the cotton crop coefficients (Kc).
- **FAO-56, 2025 revision.** Second edition of the above; lists a revised
  cotton Ky range of 0.85–1.25.
- **FAO-66 (2012).** Steduto, P., Hsiao, T.C., Fereres, E. & Raes, D. (2012).
  *Crop yield response to water.* FAO Irrigation and Drainage Paper 66. Restates
  the FAO-33 yield-response formula and the cotton Ky values.
- **Agronomy (2025).** *Simulating Water Use and Yield for Full and Deficit
  Flood-Irrigated Cotton in Arizona, USA.* Agronomy, 15(9), 2023. MDPI, open
  access (CC BY). DOI: 10.3390/agronomy15092023. Bundled as
  `agronomy_2025_flood_deficit_cotton.pdf`.

FAO documents are reproduced under FAO's open-access terms; retain the
attribution above. The MDPI article is CC BY.

## Paper referenced but not bundled

- **Thorp, K.R., Thompson, A.L. & Bronson, K.F. (2020).** *Irrigation rate and
  timing effects on Arizona cotton yield, water productivity, and fiber
  quality.* Agricultural Water Management, 234, 106146.
  DOI: 10.1016/j.agwat.2020.106146.
  **Elsevier-licensed — download your own copy from the DOI.** The Wednesday
  method card uses its mean fiber yield (1,583.7 kg/ha ≈ 1,413 lb/ac, a mean
  across all irrigation treatments) as an illustrative yield baseline.

## How these are used in the demo

- The **Kc** values (FAO-56) convert AZMet reference ET into cotton water
  demand.
- The **Ky** values and the formula `1 − Ya/Ym = Ky × (1 − ETa/ETm)` (FAO-33,
  restated in FAO-66) turn a water shortfall into an estimated yield loss.
- Thorp (2020) and the Agronomy (2025) trial provide independent field evidence
  that bloom is the most water-sensitive stage.
