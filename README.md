# Coffee Sales Analysis

An exploratory project about what sells, when recorded sales occur, and how payment patterns vary in coffee vending-machine data. The complete analysis, executed tables, six charts, findings, and recommendations live in [coffee_sales_analysis.ipynb](coffee_sales_analysis.ipynb).

The questions are practical: which products lead by transaction count and recorded sales, which hours and weekdays are busiest, how product preferences change during the day, how common each payment method is, and whether monthly comparisons are fair. The project uses descriptive analysis; machine learning is unnecessary for these questions.

This project follows the organization and notebook style of the [inspected customer-churn reference revision](https://github.com/BusinessCrab/customer-churn-analysis/tree/e3e3523050f9a4647464687ad698a867c367d689). All coffee data, analysis, wording, and findings are new. That revision embeds its charts and has no separate chart/report artifact, so this project also keeps charts in the notebook and does not produce a PDF.


## Analysis approach

The notebook proceeds from inspection to cleaning, metrics, charts, and interpretation:

- Verifies the packaged file's hash and schema, then inspects dimensions, inferred types, missing values, blanks, duplicates, category labels, and amount ranges. Recorded amounts range from **15.00 to 40.00** across the release.
- Preserves the input and works on a copy. Core transaction fields are complete; no values are imputed and no rows are removed.
- Parses timestamps with both millisecond and second precision, checks that the existing `date` agrees with `datetime`, and derives hour, weekday, and year-month because they are absent from the source.
- Normalizes only `Americano with milk` to `Americano with Milk` (**44 records**). This reduces **34 original product labels to 33**; similarly named drinks remain separate.
- Treats the **89 missing card identifiers in `index_1.csv` as potentially expected for its cash transactions**. `index_2.csv` has no `card` column at all. The packaged presence flag distinguishes absence (`False`) from an unavailable source field (blank); neither is filled as customer data.
- Retains **two exact repeat occurrences** in `index_2.csv`, which has second-resolution timestamps and no transaction IDs. Identical fields do not establish whether these are repeated exports or separate same-second transactions. Removing them would yield **3,896 records and 122,271.58 recorded sales**, a decrease of **2 records and 50.00**.
- Checks for shared transaction keys across the files, including timestamps rounded to seconds. No matches were found; this reduces one overlap concern without proving that the underlying populations are independent.
- Calculates counts, recorded sales, average amounts, product mix, and payment shares. Sales are summed in integer hundredths to avoid floating-point accumulation noise.
- Compares hours and weekdays, uses shares within each time band for product preferences, and checks source/date coverage before interpreting monthly totals.

**Tools:** Python 3.12, Pandas, NumPy, Matplotlib, Seaborn, and Jupyter. Direct dependencies are pinned in [requirements.txt](requirements.txt). All six charts have labeled axes and remain embedded in the executed notebook: product leaders, hourly activity, weekday activity, product/time mix, payment patterns, and monthly source coverage.

## Limitations and improvements

- Quantities, costs, inventory, operating hours, and store locations are absent. The analysis cannot establish units sold, profit, stockouts, lost demand, or location-level behavior.
- This is a view of recorded transactions, not a verified complete sales log. The source relationship, repeat rows, and dates without records remain uncertain.
- Currency, timezone, ingredient descriptions, menu availability, and recording practices are unconfirmed. Product names are labels, not verified ingredients.
- Card identifiers may be anonymized and are absent for cash or unavailable in an entire source. They are removed from the distributed data and are not a complete customer list.
- Differences describe associations. Price, menu, uptime, or source composition could explain them. About one annual cycle is insufficient to establish recurring seasonality.

Next, request transaction IDs and collection/uptime documentation, then compare product mix and payment share within consistent source and coverage windows. Costs or inventory would support a separate profitability or replenishment study; neither is inferred here.

## Install and run

The identifier-free dataset is included, so running this snapshot does not require a Kaggle account. Open a terminal in the directory containing this README. **Python 3.12 is recommended**; the project was executed with Python 3.12.14.

PowerShell:

```powershell
py -3.12 -m venv .venv
& ./.venv/Scripts/Activate.ps1
python -m pip install -r requirements.txt
jupyter notebook coffee_sales_analysis.ipynb
```

macOS/Linux:

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter notebook coffee_sales_analysis.ipynb
```

In Jupyter, choose **Python 3 (ipykernel)** and **Run All Cells** from the beginning. The notebook validates the included snapshot before analysis, then recalculates the tables, findings, and six charts.

Optional headless execution from the activated environment:

```bash
jupyter nbconvert --to notebook --execute --inplace coffee_sales_analysis.ipynb
```

### Downloading the original release for verification

Use the [version-21 download](https://www.kaggle.com/api/v1/datasets/download/ihelon/coffee-sales?datasetVersionNumber=21), or open the Kaggle dataset page and select version 21 before downloading and extracting its ZIP. Keep original files private in ignored `raw_downloads/`: they contain raw card identifiers. They are not needed for the normal notebook run.

PowerShell download:

```powershell
New-Item -ItemType Directory -Force -Path raw_downloads | Out-Null
Invoke-WebRequest -Uri 'https://www.kaggle.com/api/v1/datasets/download/ihelon/coffee-sales?datasetVersionNumber=21' -OutFile 'raw_downloads/coffee-sales-v21.zip'
Expand-Archive -LiteralPath 'raw_downloads/coffee-sales-v21.zip' -DestinationPath 'raw_downloads/v21' -Force
```

macOS/Linux download:

```bash
mkdir -p raw_downloads/v21
curl -L 'https://www.kaggle.com/api/v1/datasets/download/ihelon/coffee-sales?datasetVersionNumber=21' -o raw_downloads/coffee-sales-v21.zip
unzip raw_downloads/coffee-sales-v21.zip -d raw_downloads/v21
```