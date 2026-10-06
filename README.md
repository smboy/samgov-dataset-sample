# Federal Procurement Dataset — Free Sample (September 2026)

**The full U.S. federal procurement picture for one month — opportunities, awards, and the vendor registry — cleaned, joined, and analysis-ready.**

No scraping. No API keys. No rate limits. Download, query, done.

| | |
|---|---|
| 📋 Notices (September 2026, complete) | **26,817** — solicitations, presolicitations, sources sought, award notices |
| 💰 Award actions (sample: top 5,000 by value) | PIID, UEI, obligated/current/potential $, period of performance, agency, NAICS/PSC, IDV parent |
| 🏢 Vendor attributes | Business types (8(a), SDVOSB…), registration health — **pre-joined**, 99%+ match |
| 📬 POC contacts | 26,517 notices include the contracting officer's published email/phone |
| 🗂 Formats | Parquet (zstd) inline below; **CSV versions in the [release zip](../../releases)** |

## Download

- **Parquet**: the two `*_SAMPLE.parquet` files in this repo — query them in place
- **CSV + everything zipped**: grab `q3_2026_sample.zip` from [Releases](../../releases)

## Quick start

```python
import duckdb

# Top awards in the sample — one line, no setup
duckdb.sql("""
    SELECT recipient_name, awarding_sub_agency_name AS agency,
           round(obligated_amount/1e6, 1) AS oblig_m, naics_code
    FROM 'awards_enriched_top5000_SAMPLE.parquet'
    ORDER BY obligated_amount DESC LIMIT 5
""").show()
```

```
NATIONAL TECHNOLOGY & ENGINEERING SOLUTIONS OF SANDIA  | DoE | $43,199M | 561210
LAWRENCE LIVERMORE NATIONAL SECURITY, LLC              | DoE | $41,607M | 541710
TRIAD NATIONAL SECURITY, LLC                           | DoE | $35,414M | 561210
CONSOLIDATED NUCLEAR SECURITY, LLC                     | DoE | $34,654M | 561210
BATTELLE MEMORIAL INSTITUTE                            | DoE | $30,913M | 541710
```

```python
# Open pipeline: September solicitations with deadlines + buyer contacts
duckdb.sql("""
    SELECT title, agency, response_deadline, primary_contact_email
    FROM 'opportunities_2026-09_SAMPLE.parquet'
    WHERE type = 'Solicitation' AND response_deadline != ''
    LIMIT 10
""").show()
```

Works the same in pandas (`pd.read_parquet`), Polars, Spark, R, or any BI tool.

## Sample vs. full dataset

| | **Free sample** | **Q3 2026 dataset** |
|---|---|---|
| Opportunities | September (26,817) | **Full quarter (39,564)** |
| Award actions | Top 5,000 | **All 500,707** |
| Awards × vendor registry join | ✓ | ✓ |
| Full vendor registry (895,932 entities) | — | ✓ |
| Data dictionary | ✓ | ✓ |

**[→ Get the full Q3 2026 dataset — $49 (launch price)](https://gumroad.com/l/YOUR-PRODUCT)**

## Why this exists

SAM.gov and USAspending publish all of this — but raw: opportunities behind a
10-requests/day API cap with a 1,000-result query ceiling, awards as 286-column
CSVs, and the vendor registry as a 566 MB pipe-delimited file with no header.
This dataset is the joined, deduplicated, documented version of all three.

## Sources & licensing

Built from U.S. government public data: SAM.gov public Contract Opportunities
bulk extract, USAspending.gov, SAM.gov public entity extract. DUNS/D&B-derived
fields and non-public (FOUO/Sensitive) tiers are deliberately excluded.
Contact fields are exactly as agencies published them in public notices.

Free to use internally and in derived analysis. Attribution appreciated:
*"Source data: SAM.gov & USAspending.gov public data."*

## Caveats

- `obligated_amount` is cumulative per award at latest action — for rankings, not monthly spend sums.
- Vendor attributes reflect the September 2026 registry snapshot.
- Sample notices are September 2026; the full dataset covers Jul–Sep 2026.

## Updates

Quarterly releases (Q4 2026 next), built on a pipeline that snapshots SAM.gov
nightly — so history only gets deeper from here.
