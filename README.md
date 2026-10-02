# What's Driving the Surge in Consumer Financial Complaints?

An analysis of nearly 18 million complaints from the Consumer Financial Protection Bureau (CFPB), exploring why complaint volume has exploded in recent years, which companies receive those complaints, and how reliably companies report their responses.

**[View the interactive dashboard on Tableau Public](https://public.tableau.com/app/profile/christopher.howard3423/viz/CFPBConsumerComplaintsAnalysis/CFPBComplaintsDashboard)**

![Dashboard preview](images/dashboard.png)

## Key Findings

**1. Credit reporting drives nearly all recent growth.** From 2021 to 2025, credit reporting complaints grew about 15.6x (from roughly 308,000 to 4.8 million per year), while all other financial products combined grew about 3.4x. In 2025, credit reporting accounted for about 88% of all complaints.

**2. Three companies receive almost all credit reporting complaints.** Equifax, TransUnion, and Experian received 95.1% of credit reporting complaints in 2025. The next largest company received less than 1%.

**3. Consumers are mostly disputing what's on their reports.** Among complaints to the three bureaus in 2025, three issues accounted for 99.4% of the total, led by "Incorrect information on your report" at 58.7%.

**4. Company-reported outcomes are not a reliable basis for comparison.** In 2025, Experian closed 99.6% of credit reporting complaints with an explanation only, compared to about 36% for Equifax and TransUnion, which at first suggested Experian was an outlier. However, the trend data showed that all three bureaus' outcome patterns swing sharply from year to year. For example, TransUnion's share dropped from 84% to 15% between 2021 and 2022. Shifts this large and abrupt more likely reflect changes in how companies classify their responses than changes in how consumers are treated, so this field should be interpreted with caution.

## Data

- **Source:** [CFPB Consumer Complaint Database](https://www.consumerfinance.gov/data-research/consumer-complaints/)
- **Size:** 17,940,664 complaints, December 2011 through 2026
- **Key fields:** date received, product, issue, company, state, company response, timely response

The raw file is not included in this repository due to its size. See "How to Reproduce" below.

## Tools

- **DuckDB** for loading and querying the full dataset with SQL, without loading it all into memory
- **Python** (Jupyter notebook) for running queries and exporting summary tables
- **Tableau Public** for the interactive dashboard

## Methodology

### Loading the data
The full CSV was too large for Excel and slow to handle in pandas, so I loaded it into a DuckDB database file. This allowed SQL queries across all 17.9 million rows on a standard laptop.

### Data quality checks
I profiled missing values in key columns before analysis:

| Column | % missing | Interpretation |
|---|---|---|
| Sub-product | 1.3% | Expected; some products have no sub-categories |
| Sub-issue | 5.2% | Expected; some issues have no sub-categories |
| State | 0.4% | Excluded from geographic analysis |
| Company public response | 45% | Meaningful, not an error: companies can choose not to respond publicly |
| Timely response | 0% | Complete |

### Standardizing product categories
The CFPB renamed and restructured its product categories several times, so the same product appears under different labels across years. For example, credit reporting appears under three different names, and complaints in 2023 are split between the old and new names. I created a `product_group` column mapping 21 raw product labels into 12 consistent groups. Decisions worth noting:

- The retired "Consumer Loan" category was split using its sub-product field: vehicle loans were mapped to "Vehicle loan or lease," and the rest to "Payday/personal loan."
- The older combined "Credit card or prepaid card" category was mapped to "Credit card," which slightly overstates credit card complaints (and understates prepaid card complaints) in earlier years.

### Handling partial periods
The data begins mid-2011 and ends partway through 2026. Year-over-year comparisons use complete years (2021 vs. 2025), and the monthly trend chart excludes the final, incomplete month.

### Preparing data for Tableau
Rather than connecting Tableau to 17.9 million rows, I used SQL to export three aggregated summary tables (monthly trends by product, company, and response; complaints by state; and credit reporting issues for the three bureaus). I cross-checked the dashboard figures against the original SQL query results to confirm they matched.

## Limitations

- **Complaints are not the same as problems.** Volume depends on consumer awareness of the CFPB and on how complaints are filed. The rapid growth in credit reporting complaints could partly reflect changes in filing behavior (for example, third parties submitting complaints on consumers' behalf) rather than a proportional rise in reporting errors. This dataset alone can't distinguish between those explanations.
- **Company responses are self-reported.** As noted in Finding 4, classification practices appear to change over time and may differ across companies.
- **Narrative text was not analyzed.** The version of the dataset used here did not include the consumer complaint narrative column.

## Next Steps

- Investigate the one-month spike in non-credit-reporting complaints in early 2025
- Normalize complaints by state population using Census data to build a fair geographic comparison
- Analyze complaint narratives with text analysis to identify common themes

## How to Reproduce

1. Download the full complaint dataset (CSV) from the [CFPB website](https://www.consumerfinance.gov/data-research/consumer-complaints/) and extract it.
2. Install DuckDB: `pip install duckdb`
3. Update the file path in the notebook to point to your downloaded CSV, then run the notebook from top to bottom. It creates the database, the cleaned table, and the summary CSVs used by the dashboard.

## Repository Structure

```
├── README.md
├── cfpb_analysis.ipynb       # Data loading, cleaning, analysis, and exports
├── dashboard_data/           # Aggregated CSVs used in Tableau
│   ├── monthly_summary.csv
│   ├── state_summary.csv
│   └── credit_reporting_issues.csv
└── images/
    └── dashboard.png         # Dashboard screenshot
```

## Author

**Christopher Howard**
[LinkedIn](https://www.linkedin.com/in/your-profile) · [Tableau Public](https://public.tableau.com/app/profile/christopher.howard3423)
