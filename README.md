# Gaming Industry Executive Dashboard

An interactive, browser-based dashboard examining the structure, economics, risks, and geographic dynamics of the gaming industry.

The dashboard combines historical console-sales data with modern Steam catalog data to answer executive-level questions about:

* Market concentration
* Platform and publisher competition
* Genre risk and return
* The relationship between quality and commercial performance
* Steam release traction
* Pricing strategy
* Regional market differences
* Portfolio downside resilience

The dashboard is delivered as a single HTML file and runs directly in a modern web browser.

---

## Executive Summary

The analysis produces four main conclusions:

1. **The market consolidated while release volume expanded.**
   The top 10 publishers’ pooled console unit share increased from approximately 58% in 1994–1998 to approximately 75% in 2014–2018. At the same time, Steam dramatically lowered the barriers to publishing, resulting in thousands of new releases each year.

2. **Genre averages hide substantial portfolio risk.**
   Genres rank differently depending on whether performance is measured through total sales, median sales, hit rate, or dependence on blockbuster titles. Portfolio decisions should therefore consider all four dimensions rather than relying on averages alone.

3. **Most new Steam releases remain commercially obscure.**
   Using a meaningful traction threshold of more than 20,000 estimated owners or at least 50 reviews, only approximately 16% of the new-release cohort achieved traction. The median title received only two reviews.

4. **Regional differences are strategically useful, but the portfolio scenarios do not identify a clear winning posture.**
   Certain genres strongly over-index in particular regions—for example, role-playing and strategy games in Japan. However, the downside scenario analysis shows similar resilience across concentrated AAA, diffuse mid-market, and barbell portfolio strategies.

---

## Dashboard Pages

### Overview

Summarizes the complete analytical journey:

* Original data sources
* Identified data gaps
* Additional datasets
* Cleaning and validation pipeline
* Executive findings
* Guidance for interpreting the dashboard

### Data & Cleaning

Documents how the raw files were transformed into an audited analytical model.

This page covers:

* Duplicate removal
* Missing-value treatment
* Sentinel-value correction
* Score-scale normalization
* Steam owner-bucket processing
* Schema harmonization
* Dataset joins
* Reliability boundaries
* Automated quality tests
* Row-level exception logging

### 1 · Market

Examines:

* Console unit sales by platform family
* Steam release growth
* Publisher concentration
* Top-10 publisher share
* Herfindahl-Hirschman Index trends
* Genre-platform opportunities for non-major publishers

### 2 · Portfolio

Examines:

* Median sales by genre
* Hit rates by genre
* Blockbuster dependence
* Critic-score bands and median sales
* The relationship between Steam sentiment and ownership scale

### 3 · PC Reality

Examines:

* Outcomes for new Steam releases
* Alternative definitions of traction
* Traits associated with higher traction
* Price-tier ownership distributions
* User sentiment by price tier
* Price movements between the two Steam snapshots

### 4 · Geo & Risk

Examines:

* Regional genre skews
* Regional sales mix by platform
* Portfolio downside scenarios
* Data and market risks
* Decision-relevant uncertainty

### Methodology

Provides definitions, filters, statistical methods, validation rules, and analytical limitations.

---

## Data Sources

| File                                      | Role                                                               |
| ----------------------------------------- | ------------------------------------------------------------------ |
| `vgchartz-2024.csv`                       | Primary historical console-sales fact table                        |
| `vg_data_dictionary.csv`                  | Documentation for the VGChartz dataset                             |
| `Video_Games_Sales_as_at_22_Dec_2016.csv` | Critic scores, user scores, ESRB ratings, and developer enrichment |
| `games_may2024_cleaned.csv`               | Steam catalog snapshot from May 26, 2024                           |
| `games_march2025_cleaned.csv`             | Steam catalog snapshot from March 10, 2025                         |

### Dataset Boundaries

The datasets measure different parts of the market:

* VGChartz records estimated console unit sales.
* Steam records catalog attributes, review activity, prices, genres, and estimated owner ranges.
* Console units and Steam owner estimates are not directly comparable.
* The dashboard never adds console units and Steam owners together.
* The 2016 dataset is used only for score and metadata enrichment. Its sales columns are excluded to avoid mixing sales sources.

---

## Data Preparation

The raw data could not be used directly for executive analysis. The preparation pipeline addressed several material issues.

### Duplicate Records

The VGChartz dataset contained duplicate title-platform records. These were removed before aggregation to prevent double-counting.

### Missing Sales

A large proportion of console records contain no reported sales value.

Missing sales were retained as null values rather than replaced with zero. Replacing them with zero would incorrectly reduce averages, medians, and market totals.

### Sentinel Values

Some numerical zeros in the Steam data represented missing information rather than real measurements.

For example:

* A Metacritic score of `0` was interpreted as not scored.
* A price of `0` remained a valid price.
* A review count of `0` remained a valid count.

Sentinel correction was therefore applied on a field-by-field basis.

### Score Alignment

The datasets use different score scales:

* Some VGChartz critic scores use a 0–10 scale.
* The enrichment dataset uses a 0–100 scale.
* Steam sentiment is represented as a percentage of positive reviews.

Compatible critic-score fields were normalized where appropriate. Scores from different providers were not blended into one universal score.

### Steam Owner Buckets

Steam ownership is recorded as ranges such as:

* `0–20,000`
* `20,000–50,000`
* `50,000–100,000`
* `100,000–200,000`

These ranges were converted into ordered categories. Artificial numeric midpoints were not created.

### Free-to-Play Classification

A zero price does not necessarily mean that a game is genuinely free-to-play. It may also represent:

* A demo
* A temporarily free product
* A delisted title
* Missing pricing information

A title was classified as free-to-play only when supported by an explicit descriptor. Unverified zero-price titles were kept separate.

### Reliability Boundary

VGChartz retail-sales coverage becomes less representative as digital distribution grows.

For this reason:

* Long-term console trend analysis is limited to 2018 and earlier.
* The year 2018 is treated as a reporting boundary.
* Declines after 2018 are not interpreted as proof of market decline.

---

## Data Validation

The analytical pipeline completed:

* **15 of 15 critical data-quality tests**
* **40 of 40 dashboard build checks**
* **12 of 12 control-total reconciliations**

Validation checks included:

* Unique table grain
* Duplicate detection
* Foreign-key integrity
* Source-file hash verification
* Join cardinality
* Row-count reconciliation
* Regional-sales reconciliation
* Exception logging
* Suppression of unreliable small samples

A total of 2,222 records with complete regional information were recalculated against reported totals. All 2,222 reconciled within the permitted tolerance.

Warnings were retained and documented rather than hidden. A warning indicates a known analytical limitation, not necessarily a pipeline failure.

---

## Key Definitions

### Reported Console Release

A console title-platform record containing a reported sales value.

Missing sales values are excluded from sales calculations rather than interpreted as zero.

### Hit

A console release with at least one million reported units sold.

### Traction: Pipeline Rule

A Steam title with:

* Any estimated owners, **or**
* At least 50 reviews

This rule produces a traction rate of approximately 79.5%, but it is very loose because the first owner bucket includes titles with up to 20,000 owners.

### Traction: Strict Rule

A Steam title with:

* More than 20,000 estimated owners, **or**
* At least 50 reviews

This is the primary planning definition used in the dashboard and produces a traction rate of approximately 16.1%.

### Traction: Strictest Rule

A Steam title with:

* More than 20,000 estimated owners, **and**
* At least 50 reviews

This produces a traction rate of approximately 3.4%.

### Publisher Concentration

Two measures are used:

#### Top-10 Publisher Share

The proportion of reported market units controlled by the ten largest publishers.

#### Herfindahl-Hirschman Index

The sum of the squared market shares of all publishers.

A higher HHI indicates that market activity is concentrated among fewer, larger publishers.

### Regional Skew Index

A value showing whether a genre sells more or less strongly in a region relative to the overall market.

* `1.00` = sells in line with the market
* Above `1.25` = meaningful over-index
* Below `0.80` = meaningful under-index

Example:

A regional skew of `3.00` means that the genre’s share in that region is approximately three times its expected share based on the overall market distribution.

---

## Statistical Methods

The dashboard uses:

* Medians
* Hit rates
* Top-5% sales concentration
* Bootstrap confidence intervals
* Kruskal-Wallis tests
* Mann-Whitney tests
* Spearman rank correlation
* Chi-square tests
* Descriptive scenario analysis

Bootstrap confidence intervals use:

* 2,000 resamples
* Fixed random seed `42`

The statistical results identify relationships and differences in the observed data. They do not prove causation.

For example, higher critic scores are associated with higher median sales, but this does not prove that critic scores directly cause sales. Franchise strength, marketing investment, development budget, and distribution reach may influence both variables.

---

## Scenario Model

The dashboard compares three illustrative portfolio postures:

1. **Concentrated AAA**
2. **Diffuse mid-market**
3. **Barbell**

Each posture is indexed to a base value of `100`.

The model applies four downside conditions:

* Traction reduced by 25%
* Hit rate reduced by 25%
* Adverse quality outcome
* Combined downside

Under the combined downside, all three portfolio postures retain approximately 75% of their indexed base outcome.

This means the scenario model does not provide strong evidence that one posture is structurally superior to the others.

The scenario model is not a financial forecast because the source data does not include:

* Development costs
* Marketing costs
* Platform fees
* Revenue-sharing arrangements
* Operating expenses
* Acquisition costs
* Lifetime-value estimates
* Profit margins

The outputs should therefore be interpreted as relative robustness indicators, not return-on-investment estimates.

---

## Technical Requirements

The dashboard uses:

* HTML5
* CSS
* Vanilla JavaScript
* Chart.js 4.4.1

Chart.js is loaded from the following CDN:

```html
https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js
```

An internet connection is therefore required when the dashboard is opened unless Chart.js is downloaded and hosted locally.

No backend, database, package installation, or build process is required.

---

## Interaction Guide

* Use the navigation buttons at the top to switch between dashboard pages.
* Hover over charts to view exact values and sample sizes.
* Darker blue table cells generally represent stronger positive concentration or over-indexing.
* Red cells or borders indicate warnings, under-indexing, or breached risk thresholds.
* Grey `n<30` cells are suppressed because the sample is too small for reliable interpretation.
* Read the scope and caveat box at the bottom of each page before drawing conclusions.

---

## Important Limitations

### Console Coverage

VGChartz sales figures are estimates and may not capture the entire market. Digital sales are particularly underrepresented in later years.

### Steam Ownership

Steam ownership is recorded in broad ranges rather than exact values. Ownership scale should therefore be treated as an ordered category rather than a precise count.

### Missing Cost Data

The datasets do not contain development or marketing costs. The dashboard can evaluate traction and sales outcomes but cannot determine profitability.

### Scored-Title Selection Bias

Titles with critic scores tend to be more visible and commercially successful than unscored titles. Quality analysis is therefore conditioned on the subset of games that received scores.

### Regional Completeness Bias

Complete regional-sales reporting is more common for larger titles. Regional results may not fully represent the complete release population.

### Multi-Genre Titles

Steam titles may belong to multiple genres. Genre totals are therefore non-additive unless calculated using distinct application IDs.

### Right-Censoring

Recent releases have had less time to accumulate:

* Owners
* Reviews
* Ratings
* Price changes

The first and final months of the cohort window are partial months and should be interpreted cautiously.

### Association Is Not Causation

Traits such as achievements, downloadable content, genre, price tier, and critic scores may be associated with stronger outcomes without directly causing those outcomes.

---

## Reproducibility

The dashboard identifies its analytical build through:

```text
Pipeline: run_20260719_204125
Random seed: 42
Critical data tests: 15/15 passed
Dashboard build checks: 40/40 passed
```

A complete reproducible project should retain:

* Original source files
* Cleaning scripts
* Data-quality test results
* Cleaning event logs
* Row-level exception logs
* Analytical model outputs
* Methodology documentation
* Validated findings register
* Dashboard HTML file

Recommended supporting files include:

```text
cleaning_event_summary.csv
data_quality_test_results.csv
row_level_exceptions.csv
model_input_audit.csv
analytical_methodology.md
validated_findings_register.csv
powerbi_validation_report.md
```

---

## Intended Use

This dashboard is designed for:

* Executive presentations
* Strategic market analysis
* Portfolio discussions
* Greenlight decision support
* Academic data-visualization projects
* Gaming-industry research
* Demonstrating reproducible analytical workflows

It should support decisions, not replace commercial due diligence. Decisions involving investment, product development, market entry, or portfolio allocation should also include financial, operational, competitive, and user-research evidence.

---

## Final Decision Principle

The most important lesson from the analysis is not that one genre, price, platform, or portfolio posture always wins.

It is that the result changes depending on the metric and definition being used.

Any executive conclusion should therefore state:

1. The population being measured
2. The performance definition
3. The time period
4. The sample size
5. The relevant uncertainty
6. What the data cannot measure
