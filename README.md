# Restaurant_sales
Description

A US fast-food market intelligence dashboard combining real location data (27,983 deduplicated restaurant locations from three overlapping Datafiniti extracts, across name/brand/address/lat-long) with a clearly-labeled synthetic sales layer (600 representative locations, brand-tier order volume/AOV assumptions, seasonal/weekday multipliers) — because no public dataset actually contains fast-food sales figures at this granularity. The project explicitly discloses which numbers are real vs. modeled, a deliberate portfolio choice to demonstrate responsible handling of synthetic data. Deduplication logic resolved 1,700+ overlapping name+city pairs between source pairs, and brand names were standardized (e.g. SUBWAY vs Subway) before rollup.

Key Skills Demonstrated
Multi-source deduplication and entity resolution
Ethical/transparent synthetic data generation with disclosed assumptions
Geospatial/location intelligence + sales analytics in one dashboard
Python ETL, SQL, Power BI
Files

python_etl_restaurant_sales.py · sql_restaurant_sales_analysis.sql · dim_restaurants.csv · fact_daily_sales.csv · brand_summary.csv · Restaurant_PowerBI_Guide.md · Restaurant_Project_Report.md
