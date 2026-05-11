# WA Energy Pivot: Lithium vs Fossil Fuel Inflation
### A Power BI Business Intelligence Dashboard

![Dashboard Preview](screenshots/Dashboard%201.png)

## Project Overview

This project is a two-page interactive Power BI dashboard that connects global oil price volatility to the Western Australian lithium opportunity. Built as part of a data science and finance portfolio, it demonstrates macro-economic analysis, multi-source data integration, and strategic business intelligence thinking.

The dashboard was built in response to the 2025-2026 Middle East energy crisis, which pushed Brent Crude Oil above $108 USD and Perth petrol prices beyond $2.40 per litre. The analysis tracks four of WA's most significant ASX-listed lithium producers — Pilbara Minerals (PLS), IGO Limited (IGO), Mineral Resources (MIN) and Liontown Resources (LTR) — and reveals that while fossil fuel prices surged, these four companies collectively outperformed the Brent Oil index by 49 points over the same period.

## Problem Statement

The ongoing Middle East conflict triggered a sustained surge in global oil prices, directly impacting the cost of living for WA consumers, the investment decisions of global capital markets, and the strategic planning of EV manufacturers seeking stable long-term battery material supply. Despite this crisis, WA's lithium sector — anchored by Pilbara Minerals, IGO Limited, Mineral Resources and Liontown Resources — remains underutilised as a strategic solution to global energy insecurity.

## Project Objective

To build an interactive Power BI dashboard connecting global oil price volatility, Perth petrol prices, and ASX lithium stock performance into a single data story — proving that WA's lithium sector is not just a commodity play, but a strategic energy security asset for governments, investors, and EV manufacturers worldwide.

## Key Finding

> **Pilbara Minerals (PLS), IGO Limited (IGO), Mineral Resources (MIN) and Liontown Resources (LTR) collectively outperformed the Brent Oil index by 49 points between January 2025 and March 2026. Liontown Resources delivered the strongest individual performance of the four companies over that period.**

## Dashboard Structure

### Page 1 — "The Crisis"
Answers: What is the problem and who is winning?

| Visual | Description |
|--------|-------------|
| KPI Cards | Perth petrol today · Brent crude today · Lithium alpha vs oil · Top performer |
| Hero Line Chart | Three index lines rebased to 100 at Jan 2025 — Stock vs Oil vs Petrol |
| Volume Combo Chart | Trading volume bars vs Lithium Index line — institutional signal |
| Weekly Fuel Cycle | Average Perth petrol price by day of week — Perth fuel cycle analysis |
| Slicers | Ticker (PLS · IGO · MIN · LTR) and Date range (Jan 2025 – Mar 2026) |

### Page 2 — "The Opportunity"
Answers: What should WA do about it and how big is the prize?

| Visual | Description |
|--------|-------------|
| Capacity Bar Chart | 2026 production capacity for all four companies |
| EV Scenario Card | Revenue impact model at 10% global EV adoption increase |
| Mine Map | WA mine locations with bubble size representing production capacity |

## Data Sources

| Source | Description | Format |
|--------|-------------|--------|
| [Investing.com](https://www.investing.com) | Brent Crude Oil daily prices | CSV |
| [WA FuelWatch](https://www.fuelwatch.wa.gov.au) | Perth daily petrol averages by suburb | CSV (monthly) |
| [Yahoo Finance](https://finance.yahoo.com) | ASX closing prices for PLS · IGO · MIN · LTR | CSV |
| Company_Profiles.xlsx | Manual lookup table — mine names, capacity, strategic role | Excel |

**Date range:** January 1 2025 to March 31 2026 (15 months)

## Companies Analysed

| Ticker | Company | Mine | 2026 Capacity |
|--------|---------|------|---------------|
| PLS | Pilbara Minerals | Pilgangoora | 1,000,000 tonnes |
| IGO | IGO Limited | Greenbushes | 1,500,000 tonnes |
| MIN | Mineral Resources | Wodgina & Mt Marion | 490,000 tonnes |
| LTR | Liontown Resources | Kathleen Valley | 500,000 tonnes |

**Total WA capacity: 3.49 million tonnes**

## Technical Stack

- **Tool:** Microsoft Power BI Desktop (June 2025)
- **Data modelling:** Star Schema with Master_Calendar as hub table
- **Languages:** DAX · Power Query (M)
- **Data sources:** 5 connected tables
- **Relationships:** One-to-many from Master_Calendar to all fact tables

## DAX Measures

```dax
-- Rebase stock price to index of 100 at Jan 2025
Stock_Index =
VAR StartPrice =
    CALCULATE(
        AVERAGE(Lithium_Stocks[Close]),
        FILTER(ALL(Master_Calendar),
        Master_Calendar[Date] = DATE(2025,1,6))
    )
VAR LatestPrice =
    CALCULATE(
        AVERAGE(Lithium_Stocks[Close]),
        LASTDATE(Master_Calendar[Date])
    )
RETURN DIVIDE(LatestPrice, StartPrice) * 100

-- Lithium outperformance vs oil
Lithium_Alpha =
VAR SelectedTicker = SELECTEDVALUE(Lithium_Stocks[Ticker], "PLS")
VAR OilDates = CALCULATETABLE(VALUES(Brent_Oil[Date]), ALL(Brent_Oil))
VAR LatestCommonDate =
    CALCULATE(
        MAX(Lithium_Stocks[Date]),
        FILTER(ALL(Lithium_Stocks),
            Lithium_Stocks[Ticker] = SelectedTicker &&
            Lithium_Stocks[Date] IN OilDates
        )
    )
VAR StockIdx = DIVIDE(
    CALCULATE(AVERAGE(Lithium_Stocks[Close]),
        Lithium_Stocks[Date] = LatestCommonDate &&
        Lithium_Stocks[Ticker] = SelectedTicker),
    CALCULATE(AVERAGE(Lithium_Stocks[Close]),
        FILTER(ALL(Lithium_Stocks),
            Lithium_Stocks[Ticker] = SelectedTicker &&
            Lithium_Stocks[Date] = DATE(2025,1,6)))
) * 100
VAR OilIdx = DIVIDE(
    CALCULATE(SUM(Brent_Oil[Oil_Price_USD]),
        Brent_Oil[Date] = LatestCommonDate),
    CALCULATE(SUM(Brent_Oil[Oil_Price_USD]),
        FILTER(ALL(Brent_Oil),
            Brent_Oil[Date] = DATE(2025,1,6)))
) * 100
RETURN ROUND(StockIdx - OilIdx, 1)

-- EV revenue scenario model
EV_Revenue_Impacts =
ROUND(
    DIVIDE([EV_Adoption_Pct Value], 100) * 3490000 * 15000 / 1000000000,
    2
)
```

## Data Model

```
Master_Calendar (hub)
    ├── Lithium_Stocks (1:*)
    ├── Brent_Oil (1:1)
    ├── FuelWatch (1:1)
    └── Company_Profiles (standalone)
```

## How to Use

1. Clone or download this repository
2. Open `Lithium_Dashboard.pbix` in Power BI Desktop
3. Go to Transform Data and update the file paths for each CSV source to match your local folder structure
4. Click Refresh to load the data
5. Use the Ticker slicer on Page 1 to switch between companies
6. Use the Date slicer to zoom into specific periods
7. Navigate to Page 2 to explore the opportunity analysis

## Strategic Insight

This dashboard makes three arguments simultaneously.

**For EV manufacturers:** WA is the world's most stable, geopolitically safe lithium supply partner. Unlike oil, WA lithium is not subject to Middle East conflict or shipping lane disruption.

**For the WA government:** At 10% global EV adoption increase, the revenue impact on WA's lithium industry runs to $5.24 billion AUD. The infrastructure, jobs, and royalties that come with being the world's most reliable lithium supplier are within reach.

**For investors:** The data shows that lithium stocks outperformed the oil index by 49 points over 15 months, suggesting the market is pricing in the energy transition faster than most people realise.

## Skills Demonstrated

- Multi-source data integration (ASX · commodity · retail · government)
- Star Schema data modelling in Power BI
- Advanced DAX (index normalisation · LASTDATE · INTERSECT · SELECTEDVALUE)
- Power Query data cleaning and transformation
- Macro-economic analysis and data storytelling
- Interactive dashboard design and UI/UX

## Disclaimer

This analysis is for educational and portfolio purposes only. It does not constitute financial advice. All data is publicly available and sourced as indicated above.

## About the Author

**Divyanshu Narula**
Analytics Engineer and Power BI Developer — Perth, Western Australia
Bachelor of Science (Data Science and Finance), University of Western Australia
[LinkedIn](https://www.linkedin.com/in/divyanshu-narula-) · [GitHub](https://github.com/divyanshunarula)

## Acknowledgements

Data sourced from Investing.com, WA FuelWatch, and Yahoo Finance. Dashboard built using Microsoft Power BI Desktop.
