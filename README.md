<h1 align="center">US Wildfires (1992–2015)</h1>

# US Wildfires Analysis (1992–2015)
## From Raw Data to Insights: A Data Analytics Journey

---

## 🔥 The Story Behind the Data

Every year, wildfires consume millions of acres across the United States, threatening lives, property, and ecosystems. But behind each flame is a data point—a record that tells us when it started, what caused it, how big it grew, and who responded. This project analyzes **1.88 million geo-referenced wildfire records** spanning 24 years (1992–2015), representing over **140 million acres burned**.

**Dataset Source**: [1.88 Million US Wildfires - Kaggle](https://www.kaggle.com/datasets/rtatman/188-million-us-wildfires)

### Why This Dataset Matters

This isn't just numbers in a spreadsheet. It's a comprehensive archive of environmental disaster, human impact, and response efforts across two decades. The dataset captures:

- **Scale**: Millions of records tracking fires from small brush burns to catastrophic blazes
- **Geospatial Richness**: Precise latitude/longitude coordinates, state, county, and land ownership details
- **Cause Classification**: Detailed breakdown of human vs. natural ignition sources
- **Multi-Agency Data**: Federal, state, local, and tribal fire management records unified in one place

This scale and complexity make it perfect for demonstrating real-world data analytics skills—from handling messy raw data to delivering polished, interactive dashboards.

---

## 🎯 What We're Investigating

Our analysis aims to answer critical questions about wildfire patterns and their implications:

### Temporal Trends
- How have wildfire frequency and severity changed over 24 years?
- Are large, catastrophic fires becoming more common?
- Which months and seasons pose the greatest fire risk?

### Geographic Patterns
- Which states and counties face the highest wildfire risk?
- How does fire behavior differ between regions?
- Where should fire prevention resources be concentrated?

### Causation & Prevention
- What are the leading causes of wildfires?
- How do human-caused fires differ from natural ignitions?
- Which causes vary by season or geography?

### Management & Response
- How does land ownership (federal, state, private, tribal) affect fire occurrence?
- Which agencies respond most effectively?
- Has containment efficiency improved over time?

### Impact Assessment
- What's the total environmental and economic cost?
- Can we identify conditions that predict large fires?
- What patterns emerge for future prevention strategies?

---

## 🛠️ Our Analytical Approach

This project showcases the complete data analytics workflow:

### 1. **Excel Preparation**
- Initial data exploration and subset analysis
- PivotTables for quick summaries (fire counts by year, state, cause)
- Conditional formatting to identify hotspots
- Documentation of all cleaning decisions

### 2. **SQL Analysis** 
- Complex queries for trend analysis and aggregations
- Fire size classification and cause breakdowns
- Multi-table joins for agency and ownership analysis
- Performance optimization for 1.88M records

### 3. **Power BI Dashboards**
- Interactive geographic heat maps
- Time-series visualizations of fire trends
- Drill-down capabilities by region, cause, and ownership
- User-friendly interface for non-technical stakeholders

### 4. **Storytelling**
- Synthesizing technical findings into actionable insights
- Clear narrative connecting data to real-world impact
- Professional documentation for portfolio presentation

---

## 📊 Expected Deliverables

By the end of this analysis, we'll have:

- ✅ **Clear Evidence** of how wildfire patterns have evolved over 24 years
- ✅ **Geographic Risk Maps** identifying high-priority regions for prevention
- ✅ **Cause Analysis** revealing the role of human activity vs. natural factors
- ✅ **Management Insights** comparing effectiveness across agencies and land types
- ✅ **Professional Dashboard** communicating findings to both technical and non-technical audiences
- ✅ **Portfolio-Ready Documentation** demonstrating end-to-end analytics capabilities

---

## 🧰 Technology Stack

| Tool | Purpose |
|------|---------|
| **Excel** | Initial exploration, PivotTables, cleaning documentation |
| **PostgreSQL** | Database management, complex queries, aggregations |
| **SQL** | Data extraction, transformation, and analysis |
| **Power BI** | Interactive dashboards and geospatial visualizations |

---

## 📂 Repository Structure
```
📂 us-wildfires-analysis
│
├── 📁 data
│   ├── raw/              → Original CSV dataset
│   └── processed/        → Cleaned data exports for analysis
│
├── 📁 sql
│   ├── queries/          → Production SQL scripts
│   └── exploration/      → Development and testing queries
│
├── 📁 excel
│   ├── pivot_tables.xlsx → Exploratory summaries
│   └── cleaning_log.xlsx → Data preparation documentation
│
├── 📁 dashboards
│   ├── powerbi/          → Power BI project files (.pbix)
│   └── screenshots/      → Dashboard images for documentation
│
├── 📁 docs
│   ├── data_prep.md      → Technical preparation details
│   └── analysis_report.md → Final insights and recommendations
│
└── README.md             → This file
```

---

## 🔧 Phase 1: Data Preparation Journey

### The Challenge: Making Sense of 1.88 Million Records

Working with real-world data is never straightforward. Our dataset arrived as a massive CSV file with **39 columns** and **1,880,456 rows**—far too large for Excel, filled with inconsistent formats, and spanning multiple reporting systems across decades.

### Dataset Specifications

| Attribute | Value |
|-----------|-------|
| **Source File** | `Fires.csv` |
| **Total Records** | 1,880,456 |
| **Time Coverage** | 1992–2015 (24 years) |
| **Total Columns** | 39 |
| **Key Data Types** | Julian dates, geolocation, fire metadata, agency codes |

---

### Building the Foundation: Database Schema Design

To handle this volume efficiently, we designed a PostgreSQL database. Here's our schema:
```sql
CREATE TABLE fires (
    objectid INT,
    fod_id BIGINT PRIMARY KEY,
    fpa_id TEXT,
    source_system_type TEXT,
    source_system TEXT,
    nwcg_reporting_agency TEXT,
    nwcg_reporting_unit_id TEXT,
    nwcg_reporting_unit_name TEXT,
    source_reporting_unit TEXT,
    source_reporting_unit_name TEXT,
    local_fire_report_id TEXT,
    local_incident_id TEXT,
    fire_code TEXT,
    fire_name TEXT,
    ics_209_incident_number TEXT,
    ics_209_name TEXT,
    mtbs_id TEXT,
    mtbs_fire_name TEXT,
    complex_name TEXT,
    fire_year INT,
    discovery_date NUMERIC,
    discovery_doy NUMERIC,
    discovery_time TEXT,
    stat_cause_code NUMERIC,
    stat_cause_descr TEXT,
    cont_date NUMERIC,
    cont_doy NUMERIC,
    cont_time TEXT,
    fire_size NUMERIC,
    fire_size_class CHAR(1),
    latitude NUMERIC(10,6),
    longitude NUMERIC(10,6),
    owner_code NUMERIC,
    owner_descr TEXT,
    state CHAR(2),
    county TEXT,
    fips_code TEXT,
    fips_name TEXT,
    shape TEXT
);
```

### Loading the Data
```sql
COPY fires
FROM 'D:/Projects/Capstone/CSV/Fires.csv'
DELIMITER ','
CSV HEADER;
```

Simple in theory. In practice? We hit three major roadblocks.

---

## 🚧 Obstacles We Overcame

### Problem 1: The Julian Date Mystery

**The Issue**: Date fields arrived as cryptic numbers like `2453403.5` instead of readable dates like `2005-01-15`.

These are **Julian day numbers**—a continuous count of days since January 1, 4713 BC, commonly used in astronomy and scientific applications. While useful for calculations, they're incompatible with PostgreSQL's `DATE` type.

**The Impact**: Our initial schema defined `discovery_date` and `cont_date` as `DATE`, causing immediate import failures.

**The Solution**: 
1. Redefined both fields as `NUMERIC` to accept the raw values
2. Created helper columns `discovery_date_greg` and `cont_date_greg`
3. Converted Julian dates to Gregorian calendar dates using PostgreSQL's timestamp arithmetic:
```sql
UPDATE fires
SET discovery_date_greg = (TIMESTAMP '4713-01-01 BC' + (discovery_date || ' days')::interval),
    cont_date_greg = (TIMESTAMP '4713-01-01 BC' + (cont_date || ' days')::interval);
```

**Why This Matters**: Now we can filter by year, month, and season—essential for identifying temporal patterns.

---

### Problem 2: Integers That Weren't Integers

**The Issue**: Fields like `STAT_CAUSE_CODE`, `OWNER_CODE`, and `DISCOVERY_DOY` contained values like `9.0` instead of `9`.

These should logically be integers (you can't have 9.5 as a cause code), but the source data included decimal points, likely due to how different agencies exported their records.

**The Impact**: PostgreSQL's `INT` type rejected these values, halting the import.

**The Solution**: Redefined all affected columns as `NUMERIC` to accommodate the decimal formatting, while maintaining the ability to query them as categorical variables.

**Lesson Learned**: Never assume data types match logical expectations—always validate against the actual source data.

---

### Problem 3: The Column Count Mismatch

**The Issue**: Our initial schema had 35 columns. The CSV file had 39.

**The Impact**: PostgreSQL's `COPY` command failed with the error: `"extra data after last expected column"`

**The Solution**: Carefully matched every column in the schema to the CSV header, accounting for all 39 fields—even those we might not use in analysis.

**Takeaway**: When importing large datasets, schema accuracy is non-negotiable. One missing column can derail the entire process.

---

## ✅ Validation: Confirming Data Integrity

After successfully importing all records, we validated the data quality:

| Validation Check | Result | Status |
|------------------|--------|--------|
| **Total Records** | 1,880,456 | ✅ Complete |
| **Year Range** | 1992–2015 | ✅ Expected span |
| **Julian Date Range** | 2448622.5 – 2457387.5 | ✅ Valid |
| **Fire Size Classes** | A–G | ✅ All categories present |
| **Owner Codes** | 0.0–15.0 | ✅ Within expected range |
| **Date Conversion** | Spot-checked 100 records | ✅ Accurate Gregorian dates |

**Sample Conversion Verification**:
- Julian `2453403.5` → `2005-01-15` ✓
- Julian `2457387.5` → `2015-12-31` ✓
- Julian `2448622.5` → `1992-01-01` ✓

---

## 🔜 Next Phase: Data Cleaning & Transformation

With 1.88 million records successfully imported and validated, we're ready for the next critical phase. Our data cleaning will focus on:

### 1. **Handling Missing Values**
   - Identify patterns in missing data (are certain agencies or years incomplete?)
   - Decide on imputation vs. exclusion strategies
   - Document all decisions for reproducibility

### 2. **Standardizing Categorical Fields**
   - Ensure consistent naming conventions across agencies
   - Consolidate duplicate categories (e.g., "Federal" vs. "FEDERAL")
   - Create clean lookup tables for cause codes and ownership types

### 3. **Geolocation Validation**
   - Verify latitude/longitude coordinates fall within US boundaries
   - Cross-reference state codes with geolocation
   - Flag and investigate anomalies

### 4. **Feature Engineering**
   - Extract month, season, and day-of-week from dates
   - Calculate fire duration (discovery date to containment date)
   - Create binary flags (human-caused vs. natural, federal vs. non-federal land)

### 5. **Preparing for Analysis**
   - Export cleaned subsets for Excel exploration
   - Create indexed views for common queries
   - Build aggregation tables to improve Power BI performance

---

## 🎯 Project Goals Recap

This analysis will demonstrate:

- ✅ **Data Engineering**: Handling 1.88M records from raw import to analysis-ready
- ✅ **SQL Proficiency**: Complex queries, joins, and aggregations on real-world data
- ✅ **Excel Skills**: PivotTables, conditional formatting, and documentation
- ✅ **Data Visualization**: Interactive Power BI dashboards with geographic mapping
- ✅ **Storytelling**: Translating technical findings into actionable insights
- ✅ **Problem-Solving**: Overcoming real obstacles in messy, real-world data

---

## 📖 Stay Tuned

This is just the beginning of our journey through 24 years of wildfire data. Follow along as we clean, analyze, and visualize patterns that could inform fire prevention strategies and resource allocation decisions.

**Next Update**: Data Cleaning & Transformation Results

---

*This project is part of a data analytics portfolio demonstrating end-to-end capabilities from raw data to polished insights.*
- Standardizing categorical fields
- Ensuring geolocation accuracy
- Preparing fields for analysis and dashboardingrding
