## Overview
Understanding a national market like US fast food requires two lenses: **where** competitors are physically located (location intelligence) and **how** they're likely performing (sales analytics). Real, public location data answers the first question well; sales performance at brand/location granularity generally isn't publicly available, so this project demonstrates how to build a useful, *honest* sales-style dashboard anyway — combining real location data with a clearly disclosed, brand-tier-based synthetic sales layer.

## ⚠️ Important Data Disclosure
This project deliberately mixes two kinds of data and keeps them visually distinct in the dashboard:
- `dim_restaurants` — **REAL data.** 27,983 deduplicated fast food locations across the US, sourced from three overlapping Datafiniti location extracts.
- `fact_daily_sales` — **SYNTHETIC data.** The source files contain no sales/order/revenue information at all. A model-generated daily sales dataset was layered on top of 600 representative locations using brand-tier assumptions (industry-ballpark order volumes/AOV), weekday/seasonal multipliers, and randomized noise — explicitly labeled as modeled, not real, throughout the dataset and dashboard.

## Problem Statement
A market analyst wants to understand competitive density and modeled sales performance across the US fast food industry, but the only available public dataset is a location directory with no sales data. The challenge: build a useful, sales-style dashboard from that without misrepresenting synthetic numbers as real ones — while also cleaning up a source dataset riddled with duplicate listings and inconsistent brand naming.

## Dataset
Three overlapping Datafiniti location extracts (`Datafiniti_2018`, `Datafiniti_Jun19`, `FastFoodRestaurants`) unioned and deduplicated, plus a synthetic 600-location, 365-day daily sales layer.

## Methodology & Techniques

**1. Data Profiling**
Confirmed all three source files are pure location directories with no sales columns, and identified significant row overlap between all three files — evidence they're overlapping snapshots of the same underlying restaurant universe rather than independent datasets, which set up the deduplication problem.

**2. Data Cleaning & Deduplication (Python / pandas)**
- Unioned all three files into a common schema, tagging each row with its source file for lineage tracking.
- Standardized brand-name variants (`SUBWAY` → `Subway`, `McDonalds` → `McDonald's`, etc.) via a mapping dictionary — necessary before any brand-level rollup would be accurate.
- Deduplicated on a composite key (Brand + Address + City + Postal Code), keeping the first occurrence: **2,017 duplicate listings removed**, taking 30,000 raw rows down to 27,983 unique locations.
- Classified every location into one of 8 `CuisineType` categories (Burgers, Chicken, Pizza, Mexican, Sandwich, Coffee & Bakery, Ice Cream & Dessert, Other) via keyword matching against the `categories` field and brand name.

**3. Synthetic Sales Modeling (Python) — the technical highlight of this project**
- Built a brand-tier lookup table (24 major named brands + a default tier for smaller/regional chains) with defensible base daily order-volume and average-order-value assumptions.
- Sampled 600 locations, **stratified proportionally to the real brand mix**, keeping the synthetic file Power-BI-friendly in size while staying representative of the true brand distribution.
- Generated a full year (365 days, 2025) of daily orders/revenue per sampled location, applying a weekday multiplier (Friday/Saturday busiest), a seasonal multiplier (summer + December busiest), and randomized noise for realism.
- Generated a synthetic payment-method split (Card / Mobile App / Cash / Delivery Platform) per day using a **Dirichlet distribution** around a target mix, so the split varies realistically day-to-day instead of being a flat constant — a deliberate statistical choice to avoid an obviously fake-looking output.
- Tagged every synthetic row with `DataType = "SYNTHETIC..."` so the disclosure travels with the data itself, not just the accompanying report.

**4. Analysis Layer (SQL)**
Re-implemented the schema as staging tables plus 11 KPI/breakdown queries covering location density, brand competition, cuisine mix, sales trend, and payment methods.

**5. Visualization (Power BI)**
Built DAX measures for both the real location KPIs and the synthetic sales KPIs, keeping them in visually separate dashboard tabs/sections so the real-vs-synthetic distinction stays obvious to any viewer.

## Tech Stack
`Python (pandas, numpy)` · `SQL` · `Power BI (DAX)`

## Skills Demonstrated
Multi-source deduplication and entity resolution · fuzzy/rule-based brand-name standardization · **responsible synthetic data generation with explicit, disclosed assumptions** (a skill many junior analysts skip) · statistical simulation (Dirichlet-distributed category splits, seasonal/weekday multipliers) · geospatial + sales analytics combined in one dashboard

## Repo Files
`python_etl_restaurant_sales.py` · `sql_restaurant_sales_analysis.sql` · `dim_restaurants.csv` · `fact_daily_sales.csv` · `brand_summary.csv` · `Restaurant_PowerBI_Guide.md` · `Restaurant_Project_Report.md`

