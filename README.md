# 📊 LendingClub Credit Risk & Portfolio Analytics Dashboard

A Power BI dashboard analyzing **2.26M+ loan records** and **$34B+ in loan volume** to uncover the relationship between loan growth, pricing strategy, and credit risk at LendingClub (2007–2018).

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-217346?style=for-the-badge&logo=microsoft&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=for-the-badge)

<p align="center">
  <img src="./assets/dashboard-preview.png" alt="LendingClub Dashboard Preview" width="850">
</p>

<p align="center">
  <em>Loan Summary page — purpose breakdown, default rate by grade, and interest rate trend</em>
</p>

---

## 📌 Overview

This project analyzes LendingClub's historical peer-to-peer loan portfolio to answer one core question:

> **Is loan growth healthy, or is credit risk quietly rising alongside it?**

The dashboard connects loan volume, interest rate pricing, and default rate across a 4-page report, built entirely in Power BI using custom DAX measures and verified external data (SEC filings, stock price history, ownership structure).

---

## 🖥️ Dashboard Pages

| Page | What It Covers |
|---|---|
| **Overview** | Company background, share ownership breakdown, historical stock price trend |
| **Loan Summary** | Loan purpose distribution, interest rate trend, loan status breakdown, default rate by grade |
| **Quarterly / Trend** | Year-over-year loan growth, default rate YoY growth, net revenue by quarter |
| **Risk Analysis** | Loan amount by state (map), loan purpose, employment length vs. salary, state-level detail table |

---

## 🖼️ Screenshots

<table>
  <tr>
    <td width="50%"><img src="./assets/overview.png" alt="Overview Page"><p align="center"><b>Overview</b></p></td>
    <td width="50%"><img src="./assets/loan-summary.png" alt="Loan Summary Page"><p align="center"><b>Loan Summary</b></p></td>
  </tr>
  <tr>
    <td width="50%"><img src="./assets/quarterly-trend.png" alt="Quarterly Trend Page"><p align="center"><b>Quarterly / Trend</b></p></td>
    <td width="50%"><img src="./assets/risk-analysis.png" alt="Risk Analysis Page"><p align="center"><b>Risk Analysis</b></p></td>
  </tr>
</table>

---

## 🔑 Key Findings

- 📈 **Loan volume grew ~8x** from 2010 to 2018, with the steepest acceleration between 2012–2014
- ⚠️ **Default rate peaked at 17–18%** during 2014–2016 — the exact window of fastest growth — suggesting underwriting standards may have loosened during scaling
- 💰 **Interest rate pricing aligned with risk grade** (highest for Grade G, lowest for Grade A), but most loan *volume* sat in mid-risk grades B and C
- 📉 Default rate declined after 2016 as growth plateaued, indicating a likely tightening of standards

---

## 🧮 DAX Measures & Calculated Columns

| Name | Type | Purpose |
|---|---|---|
| `Default Rate` | Measure | % of loans charged off or defaulted |
| `Default Rate YoY Growth %` | Measure | Year-over-year change in default rate |
| `Net Revenue` | Measure | Clean sum of quarterly net revenue |
| `Client Status` | Calculated Column | Groups loan_status into Active / Paid Off / Defaulter / Other |
| `DTI Range` | Calculated Column | Buckets debt-to-income ratio into risk bands |
| `SortOrder` | Calculated Column | Forces correct chronological quarter sorting |

**Example — YoY Growth (built without a dedicated date table):**
```dax
Default Rate YoY Growth % =
VAR CurrentYear = MAX(loan[issue_d].[Year])
VAR CurrentRate = [Default Rate]
VAR PriorRate =
    CALCULATE(
        [Default Rate],
        FILTER(ALL(loan[issue_d].[Year]), loan[issue_d].[Year] = CurrentYear - 1)
    )
RETURN
    DIVIDE(CurrentRate - PriorRate, PriorRate)
```

```dax
Client Status =
SWITCH(
    TRUE(),
    loan[loan_status] = "Current", "Active",
    loan[loan_status] = "Fully Paid", "Paid Off",
    loan[loan_status] = "Charged Off", "Defaulter",
    loan[loan_status] = "Default", "Defaulter",
    "Other/Late"
)
```

---

## 🛠️ Tech Stack

- **Platform:** Microsoft Power BI Desktop
- **Data Analysis:** DAX — `CALCULATE`, `FILTER`, `SWITCH`, `DIVIDE`, `DATATABLE`, time intelligence
- **Data Sources:**
  - [Kaggle — LendingClub Loan Dataset](https://www.kaggle.com/) (2.26M+ records, 2007–2018)
  - SEC EDGAR filings (8-K, 10-K, 10-Q) — quarterly revenue verification
  - LendingClub Investor Relations — stock price & ownership data
- **Visuals used:** Line, Column/Bar, Donut/Pie, Area, Combo (Line + Column), Map, Matrix, KPI Cards

---

## 🐛 A Real Debugging Story

Two metrics — **YoY Growth** and **Quarterly Revenue** — initially used Power BI's built-in *"% of Grand Total"* quick measure by mistake instead of true time-intelligence logic. This produced a misleading 0–200% axis scale that looked plausible until the underlying DAX was inspected. Both were diagnosed and rebuilt from scratch using proper `CALCULATE` + `FILTER(ALL())` patterns — a good reminder that a chart rendering without errors doesn't mean the math behind it is correct.

---

## 📂 Repository Contents

```
├── assets/
│   ├── dashboard-preview.png       # Main preview image (top of README)
│   ├── overview.png
│   ├── loan-summary.png
│   ├── quarterly-trend.png
│   └── risk-analysis.png
├── DASHBOARD.pbix                  # Full Power BI report file
├── LendingClub_Project_Report.pdf  # Written project summary & analysis
└── README.md                       # You are here
```

---

## 🚀 How to View

1. **Download** [`DASHBOARD.pbix`](./DASHBOARD.pbix)
2. Open it in **Power BI Desktop** (free — [download here](https://www.microsoft.com/en-us/power-platform/products/power-bi/downloads))
3. Explore all 4 pages using the tabs at the bottom of the report

> 💡 No Power BI installed? Screenshots of each page are included in the project report PDF above.

---

## 📄 Data Governance Note

Borrower age and gender were intentionally **not** included in this analysis. LendingClub's public dataset excludes these fields in compliance with the **Equal Credit Opportunity Act (ECOA) / Regulation B**, which restricts lenders from using such attributes in credit decisions. Rather than fabricate this data, the analysis relies on legitimate available fields — employment length, debt-to-income ratio, and loan grade.

---

## 📬 Contact

Feel free to reach out if you have questions about the methodology or want to discuss the analysis.

⭐ If you found this project useful, consider giving it a star!
