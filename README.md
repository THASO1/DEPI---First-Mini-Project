# Superstore Sales & Profitability Analysis

[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Jupyter](https://img.shields.io/badge/Notebook-Jupyter-orange.svg)](https://jupyter.org/)
[![pandas](https://img.shields.io/badge/pandas-ready-150458.svg)](https://pandas.pydata.org/)

An OOP-based Python data pipeline that loads, cleans, engineers, and analyzes Superstore retail orders — giving a multinational retailer an automated view of **sales, profitability, customer behavior, and operational performance**.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Sample Visualizations](#sample-visualizations)
- [Key Business Insights / KPIs](#key-business-insights--kpis)
- [Installation & Usage](#installation--usage)
- [Author](#author)

---

## Features

End-to-end pipeline implemented as Python classes in `mini_project_superstore.ipynb`:

- **`DataLoader`** (inherits `BaseDataHandler`) — loads `Sample - Superstore 2019.xls` (`Orders` sheet) with `FileNotFoundError` handling; prints dataset info and shape.
- **`DataCleaner`** — standardizes column names (lowercase, underscores), drops duplicates, fills missing `postal_code` values, caps `sales` / `profit` outliers with the IQR method.
- **`FeatureEngineer`** — adds `shipping_duration` (days between order and ship date), `profit_margin` (%), and `sales_performance` (`Low` / `Medium` / `High` from sales bins).
- **`EDA`** — statistical summary (`describe` + correlation matrix + heatmap) and charts: sales distribution, discount vs. profit margin, sales trend over time, category/sub-category profit, shipping mode duration, top 10 products by sales, and more.
- **`KPIDashboard`** — prints an **Executive KPI Summary**: Total Revenue, Total Profit, Overall Profit Margin %, Total Unique Orders, Average Shipping Duration.
- **`DataExporter`** — writes the final cleaned dataset to `Cleaned_Superstore_Data.csv`.

---

## Tech Stack

| Tool | Role |
|------|------|
| **Python** | Pipeline and analysis logic |
| **pandas** | Data loading, cleaning, aggregation |
| **numpy** | Numeric ops (IQR clipping, feature math) |
| **matplotlib** | Figure layout and chart rendering |
| **seaborn** | Statistical plots (heatmaps, bars, lines, boxplots) |
| **Jupyter Notebook** | Interactive execution of the full pipeline |

Excel I/O uses **xlrd** (`.xls`) and **openpyxl** (xlsx-compatible workflows).

---

## Project Structure

```text
Mini-Project 1 - SuperStore/
├── mini_project_superstore.ipynb    # OOP pipeline (Load → Clean → Features → EDA → KPI/Export)
├── Sample - Superstore 2019.xls     # Source workbook (Orders sheet)
├── Cleaned_Superstore_Data.csv      # Exported cleaned + engineered dataset
├── requirements.txt                 # Python dependencies
├── README.md
└── outputs/                          # Saved EDA figures for GitHub
    ├── Average Profit by Sales Performance.png
    ├── Correlation Matrix of Numeric Features.png
    ├── Distribution of Sales.png
    ├── Impact of Discount on Profit Margin.png
    ├── Sales Trend Over Time.png
    ├── Sales & Profit for Each Region.png
    ├── Shipping Time Distribution by Shipping Mode.png
    ├── Top 10 Products by Sales.png
    ├── Total Profit by Category.png
    ├── Total Profit by Sub-Category.png
    └── Total Sales by Category.png
```

> Place chart PNGs in `outputs/` (see [Sample Visualizations](#sample-visualizations)). The notebook currently saves figures next to the notebook; copy or move them into `outputs/` so GitHub can render the embeds.

---

## How It Works

The notebook runs five sequential stages. Each class owns one concern and returns (or prints from) a pandas `DataFrame`.

### 1. Load

`BaseDataHandler` stores `file_path` and calls `load_data()`. `DataLoader` reads the Excel **Orders** sheet:

```python
pd.read_excel(self.file_path, sheet_name="Orders")
```

On success it prints `df.info()` and row/column counts. Missing files are caught with `FileNotFoundError`; other failures go through a generic `except`.

### 2. Clean

`DataCleaner.clean_data()`:

- Normalizes headers: `strip` → `lower` → spaces/`-` → `_`
- `drop_duplicates()`
- Fills missing `postal_code` with `5401`, then casts to `int`
- Caps **sales** and **profit** with IQR: clip values outside `[Q1 − 1.5×IQR, Q3 + 1.5×IQR]`

### 3. Feature Engineer

`FeatureEngineer.engineer_features()` adds:

| Column | Logic |
|--------|--------|
| `shipping_duration` | `(ship_date − order_date).dt.days` |
| `profit_margin` | `(profit / sales) * 100` |
| `sales_performance` | `pd.cut(sales, bins=[0, 50, 200, inf])` → Low / Medium / High |

### 4. EDA

`EDA` sets a seaborn `whitegrid` theme, then:

- **`perform_statistical_analysis()`** — `describe()` on numeric columns, correlation matrix, coolwarm heatmap
- **`generate_initial_visualizations()`** — sales histogram; total sales by category
- **`generate_advanced_visualizations()`** — profit by category; sales & profit by region; top 10 products; discount vs. profit margin
- **`generate_final_visualizations()`** — profit by sub-category; monthly sales trend; average profit by sales-performance bin; shipping duration by ship mode (boxplot)

### 5. KPI & Export

`KPIDashboard.generate_summary()` aggregates:

- Total revenue = `sales.sum()`
- Total profit = `profit.sum()`
- Overall margin = profit / sales × 100
- Unique orders = `order_id.nunique()`
- Average shipping days = `shipping_duration.mean()`

`DataExporter.save_to_csv()` writes `Cleaned_Superstore_Data.csv` (`index=False`) with a try/except around the write.

---

## Sample Visualizations

Copy the PNGs into `outputs/` using the filenames below (spaces included).

### Average Profit by Sales Performance

![Average Profit by Sales Performance](/Average%20Profit%20by%20Sales%20Performance.png)

### Correlation Matrix of Numeric Features

![Correlation Matrix of Numeric Features](/Correlation%20Matrix%20of%20Numeric%20Features.png)

### Distribution of Sales

![Distribution of Sales](/Distribution%20of%20Sales.png)

### Impact of Discount on Profit Margin

![Impact of Discount on Profit Margin](/Impact%20of%20Discount%20on%20Profit%20Margin.png)

### Sales Trend Over Time

![Sales Trend Over Time](/Sales%20Trend%20Over%20Time.png)

### Sales & Profit for Each Region

![Sales & Profit for Each Region](/SALES%20&%20PROFIT%20FOR%20EACH%20REGION.png)

### Shipping Time Distribution by Shipping Mode

![Shipping Time Distribution by Shipping Mode](/Shipping%20Time%20Distribution%20by%20Shipping%20Mode.png)

### Top 10 Products by Sales

![Top 10 Products by Sales](/Top%2010%20Products%20by%20Sales.png)

### Total Profit by Category

![Total Profit by Category](/Total%20Profit%20by%20Category.png)

### Total Profit by Sub-Category

![Total Profit by Sub-Category](/Total%20Profit%20by%20Sub-Category.png)

### Total Sales by Category

![Total Sales by Category](/total%20sales%20by%20Category.png)

---

## Key Business Insights / KPIs

After cleaning and feature engineering, `KPIDashboard` prints an **Executive KPI Summary**. Fill in the figures from your latest notebook run:

| KPI | Description | Value |
|-----|-------------|-------|
| **Total Revenue (Sales)** | Sum of `sales` | `$XXX,XXX.XX` |
| **Total Profit** | Sum of `profit` | `$XXX,XXX.XX` |
| **Overall Profit Margin** | Total profit ÷ total sales × 100 | `XX.XX%` |
| **Total Unique Orders** | Distinct `order_id` count | `X,XXX` |
| **Avg Shipping Duration** | Mean `shipping_duration` (days) | `X.XX Days` |

These KPIs sit on top of the EDA views: category/sub-category profit, regional sales vs. profit, discount vs. margin, product ranking, and shipping-mode duration.

---

## Installation & Usage

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
```

### 2. Create a virtual environment (recommended)

```bash
python -m venv .venv
```

**Windows (PowerShell):**

```powershell
.\.venv\Scripts\Activate.ps1
```

**macOS / Linux:**

```bash
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Add the source dataset

Place **`Sample - Superstore 2019.xls`** in the project root (same folder as the notebook). The loader reads the **Orders** sheet only.

### 5. Run the notebook

```bash
jupyter notebook mini_project_superstore.ipynb
```

Or open the file in VS Code / Cursor and run all cells in order:

1. Imports and class definitions  
2. `DataLoader` → `DataCleaner` → `FeatureEngineer`  
3. `EDA` (stats + visualizations)  
4. `KPIDashboard` → `DataExporter`

The cleaned export is written as **`Cleaned_Superstore_Data.csv`**.

---

## Author

**[Your Name]**  
Portfolio data analysis project — Superstore sales & profitability pipeline.

- LinkedIn: [linkedin.com/in/your-profile](https://linkedin.com/in/your-profile)
- GitHub: [github.com/your-username](https://github.com/your-username)
