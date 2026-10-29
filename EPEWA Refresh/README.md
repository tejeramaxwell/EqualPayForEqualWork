# EPEWA: Colorado Equal Pay for Equal Work Act and the gender wage gap

Difference-in-differences analysis of whether Colorado's Equal Pay for Equal Work Act (EPEWA, in effect January 2021) reduced the gender wage gap, using monthly Current Population Survey (CPS) microdata from IPUMS CPS.

Originally written in Stata for a master's capstone (University of Colorado Denver, fall 2023) and translated to Python in 2024. Refreshed in October 2026 to extend the sample from May 2024 through September 2026.

**Status:** research work in progress, shared for review. Some inputs are provisional (see [Known limitations](#known-limitations)).

For a general description of the original findings, see the [Substack post](https://maxtejera.substack.com/p/colorados-pay-transparency-law-closing).

## What is in this repository

| File | Purpose |
|---|---|
| `EPEWA_refresh.ipynb` | The full analysis, from data download to results, refreshed through September 2026. Start here. |
| [`../archive/EPEWA 7.5.24.ipynb`](../archive/EPEWA%207.5.24.ipynb) | The original July 2024 notebook (master's capstone version), kept unchanged for reference. It lives in the `archive` folder at the top of the repository. It uses hardcoded local file paths and is not meant to be run as is. |
| `README.md` | This file. |
| `requirements.txt` | Python packages and the versions the notebook was last run with. |
| `charts/` | The eight charts from the current refresh, as standalone HTML files (open in a browser; GitHub does not display them inside the notebook). |
| `results_2010-01_2026-09/` | The saved result tables (CSV) behind the numbers in the notebook, for the January 2010 to September 2026 window. |

Raw IPUMS data are **not** included. IPUMS terms of use do not allow redistribution, and the extract is about 2.5 GB. The notebook downloads it for you using your own IPUMS account.

## Headline results

Change in women's earnings as cents on the dollar of men's after EPEWA, from the difference-in-differences model. Women's earnings as a share of men's are computed as exp(coefficient), so the change is the exact difference between the post-law and pre-law ratios.

| October 2026 refresh (through Sep 2026) | No industry controls | Industry controls |
|---|---|---|
| Colorado | +7.5 cents (80.4 to 88.0) | +7.3 cents (83.7 to 91.0) |
| Washington | +4.6 | +4.4 |
| California | +2.1 | +2.1 |
| Rest of the US | +1.5 | +1.5 |

California and Washington are measured from January 2023, when their own laws took effect; Colorado and the rest of the US from January 2021. The July 2024 analysis (through May 2024) reported the interaction coefficient (log points x 100) with a January 2021 start for everyone: Colorado 7.7 / 7.5, Washington 5.4 / 5.9, California 3.2 / 3.3, rest of the US 2.0 / 2.0. The comparable October 2026 coefficients are Colorado 9.0 / 8.3, Washington 5.5 / 5.0, California 2.4 / 2.3, rest of the US 1.8 / 1.8. The notebook's Conclusion section has the full comparison table.

Colorado had the largest reduction among the 50 states and DC in both specifications. The notebook's Conclusion section has the full discussion, including state rankings, the industry breakdown and the urban/rural split.

**The Colorado effect depends on the earnings cut.** The main sample drops workers earning $2,884.61 a week or more, a fixed nominal value that removes a growing share of workers over time. Section 6 of the notebook tests three alternatives: a $150,000 cap deflated by the CPI, a trim of the top 12% of earners each month, and a within-gender trim. The Colorado effect is positive under all of them but smaller (4.3 to 5.5 cents, against 7.3 to 7.5 under the main cut). The gap between Colorado and the rest of the US is 3.5 to 6.1 cents and statistically significant in every case. A placebo test that treats each of 49 other states as the treated state ranks Colorado first, second or third under every cut and specification (no more than 6.1% of states at or above it).

Colorado dollar effect, nominal dollars per year (industry controls / no industry controls): $3,935 / $4,264 (July 2024 analysis: $3,304 / $3,358). Dollar figures are in nominal dollars as reported in each year, use the main earnings cut, and are not adjusted for inflation, by design.

## How to run it

### 1. Requirements

- Python 3.12 or later (last run on 3.14).
- The packages in `requirements.txt` (key ones: `pandas` 2.3, `numpy`, `statsmodels`, `plotly`, `ipumspy`, `requests`, `beautifulsoup4`, `certifi`, `nbformat`, `ipykernel`).
- A free [IPUMS CPS](https://cps.ipums.org) account and an **API key** from <https://account.ipums.org/api_keys>.

### 2. Set two environment variables

The notebook never stores a key or a personal path. It reads them from the environment:

| Variable | Meaning |
|---|---|
| `IPUMS_API_KEY_FILE` | Path to a text file containing only your IPUMS API key (or set `IPUMS_API_KEY` directly). Keep this file **outside** the repository. |
| `EPEWA_DATA_DIR` | Folder for downloaded IPUMS files, cached data and saved results. Defaults to `./data`. |

Windows PowerShell example (restart your editor afterwards so it sees them):

```powershell
[Environment]::SetEnvironmentVariable("IPUMS_API_KEY_FILE", "C:\path\to\ipums_api_key.txt", "User")
[Environment]::SetEnvironmentVariable("EPEWA_DATA_DIR", "C:\path\to\epewa_data", "User")
```

### 3. Run the notebook

Open `EPEWA_refresh.ipynb`, select your Python environment, and run all cells.

The configuration cell near the top controls the run:

| Setting | Meaning |
|---|---|
| `SAMPLE_START`, `SAMPLE_END` | Study window. `(2010, 1)` to `(2026, 9)` is the current refresh. `(2010, 1)` to `(2024, 5)` reproduces the July 2024 analysis. |
| `SUBMIT_NEW_EXTRACT` | Leave `False` to reuse the newest extract in `EPEWA_DATA_DIR`. **Set to `True` on the first run and whenever you change the window**, so a new extract is requested, waited for and downloaded. Set it back to `False` afterwards so "Run All" never files duplicate requests. |

Timing: a new IPUMS extract takes several minutes to build, and the first load of a given window takes about 30 minutes. The filtered data are then cached under a name that includes the window, so later runs load in seconds. The notebook stops with a message if the loaded data do not cover the configured window.

### 4. Outputs

- Tables and charts appear in the notebook.
- Each chart is also saved as a standalone HTML file in `EPEWA_DATA_DIR/results/<window>/charts/` (GitHub does not display Plotly charts inside notebooks). Download the file and open it in a browser; it loads Plotly's JavaScript from the web, so it needs an internet connection. The `charts/` folder in this repository holds the charts from the current refresh.
- The final cell saves 14 result tables to `EPEWA_DATA_DIR/results/<window>/` (the eight main tables plus the Section 6 robustness, difference-test and placebo tables), so runs for different windows can be compared side by side.

### Running behind a corporate network

If HTTPS traffic on your network is re-signed by a security proxy, Python may fail with `CERTIFICATE_VERIFY_FAILED`. Typical fix:

1. Append your organization's root certificate(s) to the `certifi` bundle (`python -m certifi` prints its location). A package update that replaces `certifi` undoes this.
2. Python 3.13+ also rejects some proxy certificates that lack an Authority Key Identifier. The notebook's TLS cell works around this by keeping full certificate verification and dropping only that one extra strictness flag. Remove that cell if you do not need it.

## Data sources

| Input | Source |
|---|---|
| CPS monthly microdata, 2010-2026 | IPUMS CPS, via the IPUMS API (`ipumspy`) |
| State minimum wages, 2010-2024 | U.S. DOL, [historical table](https://www.dol.gov/agencies/whd/state/minimum-wage/history) (January 1 rates) |
| State minimum wages, 2026 | U.S. DOL, [consolidated table](https://www.dol.gov/agencies/whd/mw-consolidated) (snapshot; effective date read from the page) |
| State minimum wages, 2025 | [Wikipedia](https://en.wikipedia.org/wiki/List_of_U.S._states_by_minimum_wage) (**provisional**, see below) |
| Industry crosswalk (IND to NAICS sectors) | IPUMS, [CPS/ACS industry crosswalk](https://usa.ipums.org/usa/volii/indtoindnaics18.shtml) |
| CPS 2025+ industry codes | IPUMS, [Industry Codes: 2025 onward](https://cps.ipums.org/cps/codes/ind_2025_codes.shtml) |

## Method in brief

- Sample: full-time workers aged 25-54 in the last month in the CPS rotation (`MISH == 8`), with weekly earnings between $290 and $2,884.61 (roughly the middle 60% of the earnings distribution), excluding workers below the state minimum wage for their state and year.
- Model: OLS on log annual earnings with `female`, `epewa` (post-law period) and their interaction, plus age, race/ethnicity and education, with and without industry controls. HC3 robust standard errors. The interaction is the estimated change in the gender wage gap, in log points; the notebook converts it to exact cents on the dollar, exp(β + γ) − exp(β).
- Post-law period: January 2021 for Colorado and the rest of the US; January 2023 for California and Washington, when their own laws took effect.
- Comparison groups: Colorado, Washington, California, and the rest of the country (47 states and DC). Colorado is also compared against each of the 50 states and DC individually.
- Robustness (Section 6): the main analysis is repeated under three alternative earnings cuts (CPI-deflated $150,000 cap, top 12% trimmed, within-gender trim), with a test of the Colorado minus rest-of-US difference and a placebo test that treats each state as the treated state. The CPI-U series is downloaded from the BLS when the section runs, so no extra input is needed.
- Supplementary (Section 7): men-only comparison, Colorado by industry, urban vs. rural Colorado, and nominal dollar effects.

## Known limitations

These are marked with `TODO` in the notebook.

1. **2025 minimum wages come from Wikipedia**, because DOL does not currently publish a 2025 table. 2026 uses a July 1, 2026 snapshot, which can overstate the January-June threshold in the few places that change rates mid-year. The effect on the results has not yet been tested.
2. **2025+ industry codes are bridged, not crosswalked.** The CPS adopted the 2022 Census industry classification in January 2025, and IPUMS has not published a crosswalk for it. The 34 new codes are assigned to the sector of the nearest lower code; the assignments are printed and were checked by hand.
3. **October 2025 is missing.** The sample does not exist because the data were not collected during the federal government shutdown.
4. **The earnings cut matters.** The fixed $2,884.61 weekly cut gives the largest Colorado estimate; the alternative cuts in Section 6 give smaller ones. The unadjusted dollar results use the main cut only.
5. **The comparison group is not clean.** The rest of the US includes states that enacted their own pay transparency laws in 2023-2026 (14 states now have such laws), which likely understates the effect for Colorado. An extended analysis treating these states separately is planned.
5. **Small samples.** Colorado has roughly 50 observations per month. Industry and urban/rural results are noisy; the code reports an industry only if every gender-by-period group has at least 10 observations and the regression returns valid standard errors.
6. **No inflation adjustment** to dollar figures or the earnings top code.

## Reproducibility notes

- Re-creating the July 2024 window (`SAMPLE_END = (2024, 5)`) reproduces the original results to within about 0.001 on the headline coefficients. Differences come from IPUMS data revisions, one renamed sample (`cps2023_05b` is now `cps2023_05s`), and using January rather than July 2024 minimum wages (26 observations).
- The IPUMS API always serves the current data release, so future runs may differ slightly.

## Citation

IPUMS requires all users to cite the data. The notebook prints the exact citation for the extract you download. At the time of this refresh it was:

> Sarah Flood, Miriam King, Renae Rodgers, Steven Ruggles, J. Robert Warren, Daniel Backman, Etienne Breton, Grace Cooper, Julia A. Rivera Drew, Stephanie Richards, David Van Riper, and Kari C.W. Williams. IPUMS CPS: Version 13.0 [dataset]. Minneapolis, MN: IPUMS, 2025. https://doi.org/10.18128/D030.V13.0

## Author

Max Tejera ([LinkedIn](https://www.linkedin.com/in/maxwelltejera/))
