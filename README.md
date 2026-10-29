# Colorado Equal Pay for Equal Work Act and the Gender Wage Gap

By: Maxwell Tejera
Original analysis: April 2024 (master's capstone, University of Colorado Denver)
Refreshed: October 2026

Difference-in-differences analysis of whether Colorado's Equal Pay for Equal Work Act (in effect January 2021) reduced the gender wage gap, using monthly Current Population Survey microdata from IPUMS CPS. The code is written in Python and runs in a Jupyter Notebook, with markdown comments throughout.

For a general description of the original findings, see the [Substack post](https://maxtejera.substack.com/p/colorados-pay-transparency-law-closing).

## What is in this repository

| Folder | Contents |
|---|---|
| [`EPEWA Refresh/`](EPEWA%20Refresh) | The October 2026 refresh: the notebook, setup instructions, results through September 2026, charts and requirements. **Start with its README.** |
| [`archive/`](archive) | The original July 2024 notebook, kept unchanged for reference. |

## Headline result of the refresh

Colorado has reduced the gender wage gap faster than any other state since the law took effect. Women's earnings as a share of men's rose by 7.5 cents on the dollar in Colorado (7.3 with industry controls), compared with 4.6 in Washington, 2.1 in California and 1.5 in the rest of the country. California and Washington are measured from January 2023, when their own laws took effect.

The Colorado estimate depends on how the top of the earnings distribution is handled: it falls to between 4.3 and 5.5 cents under three alternative earnings cuts, and a placebo test that treats each of 49 other states as the treated state ranks Colorado first, second or third in every case. Some inputs are provisional; the refresh README lists them.