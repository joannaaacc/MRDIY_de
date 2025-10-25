## Full Execution Guide (Data transformation using Python in VS Code) 
This guide walks you through setting up a clean Python environment in Visual Studio Code and running the provided script that reads an Excel file, calculates category-level contribution metrics, pivots them by month, and writes a nicely formatted output sheet back to the same workbook.

### Python Environment Setup in Visual Studio Code
Make sure Python extension is installed and the virtual environment (venv) is created in your respective workspace/folder.
```bash
# 1) Create & activate venv
python -m venv .venv
.venv\Scripts\Activate.ps1

# 2) Install / Upgrade  pip (if needed)
python -m pip install --upgrade pip

# 3) Install dependencies
pip install pandas
pip install numpy
pip install openpyxl
```
### 0) Import Packages
```bash
import pandas as pd
import numpy as np
from openpyxl.utils import get_column_letter
```

### 1) Configuration and Load data into a dataframe
```bash
INPUT_XLSX = "excel_sample_data_de.xlsx"
RAW_SHEET  = "sql_test-raw"
OUT_SHEET  = "sql_test-expected"

df = pd.read_excel(INPUT_XLSX, sheet_name=RAW_SHEET)
```

### 2) Normalize month to a monthly period
Convert month column to monthly period as periods sort/group cleanly by month and avoid day-level noise.
```bash
df["month"] = pd.to_datetime(df["month"]).dt.to_period("M")
```

### 3) Coerce numeric columns (validate numeric data)
```bash
for c in ["sales_qty", "sales_amt", "sales_cost"]:
    df[c] = pd.to_numeric(df[c], errors="coerce").fillna(0)
```

### 4) Aggregate to (month, product, category)
This step is to summarize duplicates and compute profit. 
```bash
agg = (
    df.groupby(["month", "product", "category"], as_index=False)
      .agg(sales_qty=("sales_qty","sum"),
           sales_amt=("sales_amt","sum"),
           sales_cost=("sales_cost","sum"))
)
agg["profit"] = agg["sales_amt"] - agg["sales_cost"]
```

### 5) Compute monthly category totals
This is to get the totals for each month by category
```bash
totals = (
    agg.groupby(["month","category"], as_index=False)
       .agg(total_qty=("sales_qty","sum"),
            total_amt=("sales_amt","sum"),
            total_cost=("sales_cost","sum"),
            total_profit=("profit","sum"))
)
```

### 6) Join + compute contributions
```bash
x = agg.merge(totals, on=["month","category"], how="left")

def pct(n, d):
    return np.where((d == 0) | pd.isna(d), np.nan, n / d)

metrics = [
    "sales qty contribution by category",
    "sales amt contribution by category",
    "sales cost contribution by category",
    "profit contribution by category",
]

x["sales qty contribution by category"]  = pct(x["sales_qty"],  x["total_qty"])
x["sales amt contribution by category"]  = pct(x["sales_amt"],  x["total_amt"])
x["sales cost contribution by category"] = pct(x["sales_cost"], x["total_cost"])
x["profit contribution by category"]     = pct(x["profit"],     x["total_profit"])
```

### 7) Pivot: columns come out as (metric, month) -> swap to (month, metric)
Make columns organized by month, with the 4 metrics under each month.
```bash
wide = x.pivot_table(
    index=["product","category"],
    columns="month",
    values=metrics,
    aggfunc="first"
)
wide = wide.swaplevel(0, 1, axis=1).sort_index(axis=1, level=0)  # (month, metric)

# Helper for month labels
def month_label(m):
    return m.strftime('%b-%y') if hasattr(m, "strftime") else str(m)

# Rename month level to  strings like 'Jan-25'
wide.columns = pd.MultiIndex.from_tuples(
    [(month_label(m), metric) for (m, metric) in wide.columns],
    names=['month', 'metric']
)
```

### 8) Build the ordered column grid
Values stay numeric (fractions); we format as % only in Excel.
```bash
months = [month_label(m) for m in sorted(x["month"].unique())]
ordered_cols = pd.MultiIndex.from_product([months, metrics])
ordered_num = wide.reindex(columns=ordered_cols)  # keeps numeric values
```

### 9) Prepare a DataFrame to write
Move index (product, category) into columns before writing.
```bash
data = pd.concat(
    [ordered_num.index.to_frame(index=False),
     ordered_num.reset_index(drop=True)],
    axis=1
)
```

### 10) Write to Excel, add header rows, apply % format
Create the final sheet with two header rows and percentage formatting.
```bash
with pd.ExcelWriter(INPUT_XLSX, engine="openpyxl", mode="a", if_sheet_exists="replace") as w:
    data.to_excel(w, sheet_name=OUT_SHEET, index=False, header=False, startrow=2)
    ws = w.sheets[OUT_SHEET]

    # Header rows:
    row1 = ["", ""] + [m for (m, metric) in ordered_num.columns]     # months
    row2 = ["product", "category"] + [metric for (m, metric) in ordered_num.columns]  # metrics

    for i, v in enumerate(row1, start=1):
        ws.cell(row=1, column=i, value=v)
    for i, v in enumerate(row2, start=1):
        ws.cell(row=2, column=i, value=v)

    # % number format over the numeric block
    n_rows = data.shape[0]
    n_cols = ordered_num.shape[1]
    first_data_row, first_data_col = 3, 3  # C3
    last_data_row  = first_data_row + n_rows - 1
    last_data_col  = first_data_col + n_cols - 1

    for r in range(first_data_row, last_data_row + 1):
        for c in range(first_data_col, last_data_col + 1):
            ws.cell(row=r, column=c).number_format = '0%'
```

### Expected outcome in Excel
<img width="1850" height="271" alt="{1EDEEA7E-EFCB-47E5-919E-AA983276716D}" src="https://github.com/user-attachments/assets/12e2e2d4-383c-480e-80cc-2e203481c527" />
