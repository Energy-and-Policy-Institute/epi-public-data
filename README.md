# EPI Public Data

Open datasets published by the [Energy and Policy Institute](https://www.energyandpolicy.org/) for journalists, researchers, and the public.

These files are produced by automated pipelines that we maintain in private repositories and mirror here on a regular schedule. They are intended for direct embedding in tools like Datawrapper, Flourish, and Observable, and for download into spreadsheets or notebooks.

## What's in this repo

| Dataset | Folder | Cadence | Description |
|---|---|---|---|
| 527 utility contributions | [`527/`](./527/) | Weekly (Mondays) | Contributions to the seven major partisan 527 organizations from utility companies and their trade associations, parsed from IRS Form 8872 bulk filings. |

Future additions may include FEC contribution data, utility-CEO political giving, and rate-case outcome summaries. Each dataset lives in its own folder with its own README describing scope, methodology, and known limitations.

## How to cite

If you use these datasets in published work, please credit:

> Energy and Policy Institute, *EPI Public Data*, https://github.com/Energy-and-Policy-Institute/epi-public-data

For dataset-specific citation guidance, see the README inside each folder.

## Methodology

Each folder's README describes the dataset-specific methodology in detail. For cross-cutting notes — how we handle source attribution, dedup, name matching, and known caveats — see [`METHODOLOGY.md`](./METHODOLOGY.md).

## Update cadence and freshness

Datasets are refreshed automatically. The "Last updated" timestamp on each file (visible in the GitHub UI) reflects the most recent successful pipeline run. If a file hasn't been updated in more than two cycles past its expected cadence, the upstream pipeline may have broken — please [open an issue](https://github.com/Energy-and-Policy-Institute/epi-public-data/issues).

## Stability and breaking changes

Column names and file paths are intended to be stable. If we need to make a breaking change (rename a column, split a file), we'll announce it in the repository's release notes and keep the old format available for at least one update cycle where feasible.

## Limitations

These are open datasets derived from public records. We've put significant effort into matching, deduplication, and validation, but the underlying records contain typos, amended filings, and ambiguous attribution. Each dataset's README documents known limitations specific to that source. Please read them before drawing strong conclusions from individual rows.

## License

The data in this repository is derived from U.S. public records (IRS, FEC, state utility commissions) and is in the public domain. The matching, classification, and curation work that produced these files is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — use it freely, with attribution.

## Contact

Questions, corrections, or dataset requests: please [open an issue](https://github.com/Energy-and-Policy-Institute/epi-public-data/issues) or contact us through [energyandpolicy.org](https://www.energyandpolicy.org/).
