# FIFA World Cup 2022: Team Performance Analysis

**What separates successful teams from unsuccessful ones at the 2022 World Cup: possession, passing, line-breaking, shooting, or discipline?**

A question-driven analysis of match-level team statistics from all 64 matches of the FIFA World Cup 2022 (128 team-match rows, one per team per match). It began as a classroom exercise on `groupby` and z-normalisation and was extended into a full investigation, including data-quality auditing, correlation analysis, non-parametric hypothesis testing with multiple-comparison correction, and group-wise (within-team) z-normalisation.

> **Caveat:** this is observational data from a single tournament, with only 3 to 7 matches per team. Findings describe associations in this sample and are not causal claims.

## Questions This Notebook Answers

1. **Data quality** — is the data trustworthy, and what has to be corrected before analysis?
2. **Team performance** — who scored, conceded, and qualified, and how do the group standings reconcile with the raw data?
3. **Style vs. results** — do possession, passing volume, line-breaking, and shot volume translate into goals and wins?
4. **Efficiency** — which teams scored more (or fewer) goals than their shot volume suggests?
5. **Discipline** — who was booked most, and what goes with it?
6. **Within-team variation (z-normalisation)** — which matches were unusual *for the team that played them*, and do the pooled relationships still hold once team style is separated out?
7. **Winners vs. losers** — in the same match, which statistics actually differ between the two sides, after correcting for multiple comparisons?

## Key Findings

1. **Scoring followed shot quality, not style.** Goals correlate with shots on target (ρ = 0.56) and weakly with attempts (0.30); possession, passing, pass accuracy, and line breaks all have |ρ| ≤ 0.11 with goals across matches.
2. **Possession did not predict results.** Winners averaged 42.4% possession against 44.6% for losers (ρ with goal difference = 0.03); teams with 60%+ possession won 4, lost 4, and drew 1.
3. **Only shooting separated winners from losers.** Of all metrics tested across the 49 decisive matches, shot accuracy and shots on target were the only two to survive Bonferroni correction.
4. **Passing volume is a team trait, not a match-winning lever.** Teams that pass more are far more accurate (ρ = 0.95) and complete more line breaks, but the team-level link between passing and goals is modest and fragile (0.35, falling to 0.29 without Spain) and vanishes between individual matches (0.03).
5. **Line-break attempts did not convert into goals** (team-level ρ = 0.11); line-break success is largely pass accuracy under another name (ρ = 0.84 with it).
6. **Discipline separated qualifiers from eliminated teams.** Qualifiers were booked about half as often in the group stage (1.21 vs. 2.27 yellow cards per match) — the only qualifier-vs-eliminated difference that survives correction.
7. **Group standings reconcile exactly with the cleaned data:** ranking on points, goal difference, and goals scored reproduces all 16 qualifiers.
8. **Within-team normalisation changes the picture.** Raw pooled correlations between passing and line-breaking (0.59–0.85) shrink noticeably within teams (0.43–0.79) — part of that association was team style, not match-to-match behavior.
9. **Data quality mattered.** A misspelt opponent name, a wrong points value, shoot-out points mixed into the raw `Pts` column, and an unexplained extreme value would each have distorted the results if left uncaught.

## Conclusion

The statistics that went with success at this World Cup were about efficiency and discipline rather than control of the ball. Teams that had more possession, or that passed and line-broke more, were no more likely to win; the sides that got shots on target, converted them, and stayed out of the referee's book progressed. Team style is real and strong (passing volume, accuracy, and line-break success move together), but it behaves as a fixed property of the team rather than a lever that improves results match by match. Group-wise z-normalisation was the tool that made this visible, since it separates a team's habitual style from how a specific match departed from it.

## Data

- **Source:** match-level team statistics for all 64 matches of the 2022 World Cup, one row per team per match (128 rows), 35 numeric columns covering possession, passing, line-breaking, shooting, and discipline.
- **Loading:** the notebook first looks for a local CSV next to it and falls back to the original published Google Sheet if no local copy is found — no manual download is required to run it as-is, though pointing it at a local file is recommended for reproducibility.
- **No data dictionary is provided in the source file** — several columns (e.g. line breaks, `Fouls Against`, `Receptions D`) are undefined and their exact definitions are inferred or flagged as uncertain in Section 2 and Section 3 of the notebook.

## Data Quality Issues Found and Corrected

| Issue | Action |
|---|---|
| Misspelt opponent name ("Fram") in the France–Morocco semi-final | Corrected to "France" |
| Wales v Iran `Pts` recorded incorrectly | Raw `Pts` left untouched; `Group Points` recomputed from the score and used for standings |
| Knockout-round `Pts` conflates shoot-out winners with the run of play | `Pts` not used for knockout-stage analysis |
| Extra-time matches inflate raw counts (~32% more passes on average) | Flagged with a `Went to Shoot-out` indicator and used as a robustness check |
| `Receptions D` contains one unexplained extreme value (Germany, match 10), and is undefined in the source | Flagged, not silently dropped or imputed |

## Methods

- Feature engineering: opponent join, tournament stage from match number, derived result/points, pass and shot accuracy ratios.
- `groupby` / named aggregation for team-level and group-standings summaries.
- Correlation analysis (Pearson and Spearman) between style metrics and outcomes.
- Non-parametric hypothesis testing (Mann–Whitney, Wilcoxon) comparing winners vs. losers, with a Bonferroni-corrected significance threshold to account for testing many metrics at once.
- Group-wise z-normalisation (`z = (x - team mean) / team standard deviation`) to separate a team's habitual style from match-to-match variation within that team.

## Limitations

- **Small samples.** Teams played 3 to 7 matches each (16 teams played exactly 3), so team-level rates and within-team z-scores are volatile.
- **Association, not causation.** Only final match totals are available — game state, lineups, injuries, and refereeing are not recorded.
- **Extra time and undefined columns.** Five matches went to extra time, and several columns are not defined in the source file.
- **Multiple comparisons.** Many tests were run; a Bonferroni threshold is reported where it matters, and unadjusted p-values elsewhere should be read as hints rather than confirmed effects.

## Next Steps

- Add a per-90-minute normalisation (or exclude extra-time matches) to remove the extra-time distortion from raw counts.
- Model match outcome (e.g. logistic regression on shot and possession differences) and evaluate it out of sample.
- Bring in a second tournament to give more matches per team and properly test whether the team-style findings replicate.

## Skills Demonstrated

Data validation and cleaning, feature engineering, `groupby` / named aggregation, group-wise z-normalisation with `apply`, correlation and non-parametric hypothesis testing, multiple-testing correction, and communication through charts.

## Tools

- Python
- Pandas
- NumPy
- SciPy (`scipy.stats`)
- Matplotlib
- Seaborn

## Project Structure

```text
fifa-world-cup-2022-analysis/
├── datasets/
│   └── Fifa Worldcup 2022 - Sheet1.csv
├── notebooks/
│   └── FIFA_World_Cup_2022_Analysis.ipynb
└── README.md
```

## How to Run

```bash
git clone <your-repository-url>
cd fifa-world-cup-2022-analysis
pip install pandas numpy scipy matplotlib seaborn jupyter
jupyter notebook
```

Open `notebooks/FIFA_World_Cup_2022_Analysis.ipynb` and run the cells sequentially. The notebook will use a local CSV if one is placed next to it (check the data-loading cell for the exact filename it looks for), and otherwise falls back to fetching the original Google Sheet over the network.
