# 527 Utility Contributions

Contributions to the seven major partisan 527 political organizations from utility companies and their trade associations.

## Files

| File | Rows (approx.) | Description |
|---|---|---|
| `top_utility_recipients.csv` | 50 | The 50 utility parent companies that have contributed the most across all years and all 527 orgs. |
| `yearly_totals_by_org.csv` | ~150 | Total contributions and contribution counts broken out by year and recipient organization. One row per (year, org) pair. |
| `all_contributions.csv` | ~3,600 | The full searchable contribution table. One row per matched contribution, sorted by year then amount, both descending. |
| `aggregated_by_utility_year_org.csv` | ~1,800 | Pre-aggregated rollup with one row per (year, utility parent, recipient org). Useful for filterable tables and pivot-style charts where you want to slice by utility *and* recipient without ingesting every individual contribution. |

## Source

- **Source document:** IRS Form 8872 bulk data, published weekly by the IRS Tax Exempt Organization section.
- **Download URL:** `https://forms.irs.gov/app/pod/dataDownload/fullData`
- **Cadence:** The IRS publishes updates Sunday at approximately 1:00 AM UTC. Our pipeline runs every Monday at 8:00 AM UTC and mirrors the resulting CSVs into this repository.
- **Coverage:** Contributions from 2002 through the most recent IRS publication.

## Recipient organizations

The seven 527 organizations tracked in this dataset:

| `org_short` | Legal name | EIN |
|---|---|---|
| RGA | Republican Governors Association | 11-3655877 |
| DGA | Democratic Governors Association | 52-1304889 |
| RSLC | Republican State Leadership Committee | 05-0532524 |
| DLCC | Democratic Legislative Campaign Committee | 52-1870839 |
| RAGA | Republican Attorneys General Association | 46-4501717 |
| DAGA | Democratic Attorneys General Association | 13-4220019 |
| GOPAC | GOPAC | 52-1237780 |

## Column reference

### `top_utility_recipients.csv`

| Column | Description |
|---|---|
| `parent_company` | Utility parent company (e.g., "NextEra," "Duke Energy"). Subsidiaries are rolled up to the parent. |
| `total_contributions_usd` | Sum of matched contributions across all years and all seven 527 orgs, rounded to whole dollars. |
| `n_contributions` | Number of individual matched contributions. |
| `first_year`, `last_year` | Earliest and latest year in which a matched contribution was observed. |
| `top_recipient_orgs` | Comma-separated list of the 527 orgs that received money from this parent, ordered by total received (highest first). |

### `yearly_totals_by_org.csv`

| Column | Description |
|---|---|
| `year` | Calendar year of the contribution. |
| `org_short` | Recipient 527 organization (see table above). |
| `total_contributions_usd` | Sum of matched contributions in this (year, org) cell. |
| `n_contributions` | Number of individual matched contributions in this cell. |

### `all_contributions.csv`

| Column | Description |
|---|---|
| `year` | Calendar year. |
| `org_short` | Recipient 527 organization. |
| `utility_parent_company` | Parent company the contributor was matched to. |
| `utility_entity_matched` | Specific subsidiary or alias that produced the match (may equal `utility_parent_company`). |
| `contributor_name` | Contributor name as it appears in the IRS filing. Preserved verbatim, including any misspellings. |
| `amount_usd` | Contribution amount, rounded to whole dollars. |
| `contribution_date` | Date in `YYYY-MM-DD` format. |
| `match_type` | How the contributor name was matched: `entity_exact`, `entity_simplified` (alias appears as substring), or `entity_word_boundary` (short alias with word boundaries). See [methodology](../METHODOLOGY.md#entity-matching). |

### `aggregated_by_utility_year_org.csv`

| Column | Description |
|---|---|
| `year` | Calendar year of the contributions in this row. |
| `utility_parent_company` | Parent company (subsidiaries rolled up). |
| `org_short` | Recipient 527 organization. |
| `total_contributions_usd` | Sum across the (year, parent, org) cell, rounded to whole dollars. |
| `n_contributions` | Number of individual matched contributions in the cell. |

Sorted by `year` descending, then `total_contributions_usd` descending. The dataset spans the same coverage window as `all_contributions.csv`; it is simply pre-aggregated for tools that don't want to compute group-bys client-side.

## Matching and dedup

Utility-name matching follows the layered approach described in [`METHODOLOGY.md`](../METHODOLOGY.md#entity-matching), against a curated alias list with parent-company rollups. Two-pass deduplication is applied to handle IRS amended and supplemental filings; see [`METHODOLOGY.md`](../METHODOLOGY.md#deduplication) for details.

## Known limitations

- **Amended filings sometimes shift dates by a few days.** Our near-date dedup pass catches most of these, but residual duplicates may exist in edge cases (e.g., an amendment that both changed the date and changed the amount). Cross-check before reporting on any single contribution.

- **Contributor names are preserved as filed.** A single utility may appear under several spellings (e.g., "DUKE ENERGY," "Duke Energy Corporation," "DUKE ENERGY CORP"). The `utility_parent_company` column rolls these up; the `contributor_name` column does not.

- **Comparison against a manually curated EPI reference set shows differences in both directions.** For some (utility, org) pairs our totals exceed the manual reference by tens of thousands of dollars (likely from amended filings the manual process missed); for other pairs we under-count, often where a small subsidiary or regional name isn't in our alias list yet. Net direction varies by company.

- **Trade associations are included.** Edison Electric Institute (EEI), Nuclear Energy Institute (NEI), and similar industry groups appear as parent companies in this dataset because their member-utility-funded contributions are politically significant. They are *not* utility operating companies. Treat their totals as trade-association giving, not direct utility-company giving.

- **In-kind expenditures are excluded.** Only cash contributions are reported. In-kind contributions (e.g., venue donations, services) are filtered out upstream.

- **Sub-parent corporate restructurings are not back-applied.** When a utility merger or acquisition happens, contributions made under the old corporate name remain attributed to the old parent for periods before the deal closed. We do not retroactively re-attribute history.

- **No CEO or executive personal contributions.** This dataset covers contributions from corporate entities and trade associations only. Personal contributions from utility executives are tracked separately and not included here.

## Suggested chart embeds

These files are sized and shaped for direct ingestion by Datawrapper, Flourish, and similar tools:

- `top_utility_recipients.csv` → ranked bar chart of the top 50.
- `yearly_totals_by_org.csv` → multi-line chart with one series per `org_short`.
- `all_contributions.csv` → searchable / filterable data table.
- `aggregated_by_utility_year_org.csv` → searchable table for filtering by utility and recipient.

The raw URLs (suitable for chart "external dataset" fields) are:

```
https://raw.githubusercontent.com/Energy-and-Policy-Institute/epi-public-data/main/527/top_utility_recipients.csv
https://raw.githubusercontent.com/Energy-and-Policy-Institute/epi-public-data/main/527/yearly_totals_by_org.csv
https://raw.githubusercontent.com/Energy-and-Policy-Institute/epi-public-data/main/527/all_contributions.csv
https://raw.githubusercontent.com/Energy-and-Policy-Institute/epi-public-data/main/527/aggregated_by_utility_year_org.csv
```

## Citation

> Energy and Policy Institute, *527 Utility Contributions* (derived from IRS Form 8872 bulk filings), https://github.com/Energy-and-Policy-Institute/epi-public-data/tree/main/527
