# fish_par_combined_viewer

One viewer for all four waves of the West Coast Fisheries Participation Survey
(2017, 2020, 2023, 2026), replacing the per-year viewer apps. A Survey Year
control switches every chart and table between waves; questions a wave did not
ask grey out, and known comparability caveats surface as a banner on the
affected chart.

Static HTML/JS (Google Charts), no build server: open `index.html` or serve the
folder with any static file server.

## Generated, not hand edited

`index.html` and `wcpDataAll.js` are produced by `build_combined.py`, which
reads the four per-year viewer repos (expected as siblings of this one:
`fish_par_2017_viewer`, `fish_par_2020_viewer`, `fish_par_2023_viewer`,
`fish_par_2026_viewer`) and patches the 2026 viewer's page into the combined
tool. To rebuild after any per-year viewer's data changes:

```
cp ../fish_par_2026_viewer/index.html index.html
python3 build_combined.py
```

Edit `build_combined.py`, never the generated files.

## What the build does

- **Merges the four `wcpData.js` files** into `WCP[year][slot]`, keyed on the
  slot naming convention fixed with the 2020 tool.
- **2017 crosswalk.** The 2017 viewer predates that convention and its
  question numbering is shifted (its `q19` is the later waves' `q17`, etc.).
  Slots are remapped by unique header match; anything ambiguous or unmatched
  is dropped rather than guessed (25 of 31 slots carry over). Six 2017-only
  questions and six 2020-only questions are not yet included.
- **Re-sorts percent-bracket categories numerically** — the prep script
  exports them string-sorted, which puts "100%" between "1-24%" and "25-49%".
  (Fixed at the source for 2026 in `webtool_data_prod_2026.R`; the 2023-era
  `webtool_data_prod.R` still has it.)
- **Harmonizes category labels across waves** so year toggling and compare
  mode join on identical strings: doubled spaces collapse (2017/2020 q34),
  the 2017 hyphen pay brackets map onto the later waves' en-dash labels
  (q18), and the 2017 wording of the q10 "not part" option is aligned.
- **Normalizes display order for questions the waves exported differently**
  (q8 ran All→None in 2017/2020 but None→All later; Yes/No flipped on q26
  and q36; q10/q18 varied) so the column order no longer changes when the
  year toggle flips.
- **Pins each question's y-axis** to its maximum across all waves and states,
  so switching years never rescales the axis.
- **Detects ordered-scale (Likert) matrix questions** from their headers and
  draws them with a light-to-dark ordinal ramp; other multi-series charts use
  a colorblind-validated categorical palette.
- **Adds the year control, state radios, compare-years mode** (grouped bars,
  one fixed color per wave), a per-question respondent count, and per-year
  caveat notes (e.g. the 2023 contacts-question scale runs in the reverse
  direction from the other waves).
- **Wraps long category labels**, sizes tables to their labels, and lays the
  page out responsively (columns stack below 980px; charts redraw on resize).

## Data caveats carried in the tool

Question wording shown is from the 2026 instrument; wording and response
options vary slightly between waves, and the survey-year label in a question
(income reference years especially) is rewritten to match the selected wave.
The caveat banner and the "not asked in this year" greying are driven by
tables in the build script — extend `CAVEATS` there as new comparability
issues are found.
