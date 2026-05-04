# Methodology

This document describes the cross-cutting practices that shape every dataset in this repository. Dataset-specific methodology lives in each folder's README.

## Source documents

We work from authoritative source documents — typically the bulk exports published by federal regulators (IRS, FEC) or state utility commissions — rather than from third-party APIs or aggregator sites. Each dataset's README names its source URL and the cadence at which the source publishes updates.

When a source publishes both a bulk file and an API, we generally prefer the bulk file because it gives us a complete snapshot of a known publication moment. APIs can return partial results during paging, may apply server-side caching, and don't always expose every field present in the underlying records.

## Pipeline structure

Each dataset is produced by an automated pipeline that runs on a fixed schedule:

1. **Download** the latest bulk file from the source.
2. **Parse** it into a normalized internal format.
3. **Filter** to the rows of interest (e.g., contributions to specific organizations).
4. **Match** entity names against curated reference lists (e.g., a utility-company alias list with parent-company rollups).
5. **Deduplicate** to handle amended and supplemental filings.
6. **Validate** against prior runs and human-curated comparison datasets where available.
7. **Publish** the resulting CSVs to this repository.

Failures at any step page a maintainer; we do not publish a dataset that didn't complete validation.

## Entity matching

For datasets that involve matching free-text contributor or filer names against a known set of entities (utility companies, fossil-fuel firms, etc.), we use a layered approach:

- **Exact match** — the contributor name is character-for-character identical to a known alias.
- **Substring match** — a known alias appears as a substring of the contributor name (e.g., the alias "Duke Energy" matches the filer "Duke Energy Corporation").
- **Word-boundary match** — for short aliases (under 8 characters), we require word boundaries to avoid false positives (e.g., "AES" matches "AES Corporation" but not "PRAESEPE").

False positives are filtered through a curated regex list that we maintain alongside the alias database. Each dataset's README discloses the labeled match type for every row, so you can apply your own confidence threshold.

## Deduplication

Many of our source files have overlapping reporting periods. The same contribution can appear in an original filing, a later amendment, and a supplemental filing — three rows for one real-world transaction. We apply a two-pass deduplication:

1. **Exact-date pass**: rows are grouped by (recipient, contributor name, date, amount); we keep the maximum number of true occurrences observed in any single filing.
2. **Near-date pass**: same logic but grouped by year-month, to catch cases where an amendment shifted a date by a few days.

Removed rows are preserved in the source pipeline's audit logs (not published here). This logic is specific to the IRS Form 8872 amendment pattern and may differ for other source types.

## Validation

Where a manually curated reference dataset exists, we publish a side-by-side comparison in the source pipeline. For the 527 dataset, we compare against a long-running, manually curated EPI reference; differences in both directions exist and are documented in [`527/README.md`](./527/README.md).

Cross-source validation is harder. We do not currently cross-check IRS 8872 filings against state-level disclosures or FEC records; most contributions in our datasets fall under one regulator's jurisdiction.

## What we don't do

A few decisions worth being explicit about:

- **We do not edit the source records.** If the IRS or FEC reports a contribution, we report it. If a contributor's name is misspelled in the filing, we preserve the misspelling in the `contributor_name` field while attempting to match it to a parent company.
- **We do not impute missing values.** Rows with missing contribution amounts, missing dates, or unparseable contributor names are excluded from the published CSVs.
- **We do not infer intent.** A matched contribution from a utility to a 527 organization is a legally disclosed transaction; it is not, on its own, evidence of any specific policy outcome.

## Reproducibility

The matching, classification, and curation code is maintained in private repositories because some of our reference data (utility executive lists, internal QA columns) is not appropriate for public release. The published CSVs are sufficient to reproduce any chart or aggregate we publish, and we are happy to walk reporters through the underlying methodology in detail — see the contact information in the main [README](./README.md).
