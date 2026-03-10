## Project Overview

The **Capital Bikeshare hourly usage dataset** contains bike rental activity in Washington, DC from **January 1, 2011 to December 31, 2012**. Each observation represents hourly ridership counts along with environmental and temporal conditions such as weather, temperature, humidity, and wind speed.

Before performing exploratory analysis, I approached the dataset as a **data product audit**. The first step was verifying that the dataset is **internally consistent, complete, and reliable** for downstream analysis.

The goal of this project was to evaluate **data quality, temporal completeness, and ridership patterns**, and demonstrate how an analyst would investigate questions raised by downstream users of the dataset.

**This analysis aims to uncover:**

1. Whether the dataset is **internally consistent and regression-ready**
2. Whether the time series data is **complete and continuous**
3. What patterns exist between **casual and registered riders**
4. How analysts can investigate **user-reported anomalies in a data product**

**Tools & Libraries Used**

- Pandas: Data cleaning, transformation, aggregation
- DuckDB / SQL: Data validation queries
- Matplotlib: Data visualization
- Seaborn: Heatmaps and statistical plots
- Jupyter Notebook: Analytical workflow

---

# Data Structure Overview

The dataset contains **hourly bike rental observations paired with environmental variables**.

<div align="center">

| Column | Description |
|------|-------------|
| datetime | Timestamp for each observation |
| season | Seasonal category |
| weather | Weather condition |
| temp | Temperature |
| humidity | Humidity level |
| windspeed | Wind speed |
| casual | Casual riders |
| registered | Registered riders |
| cnt | Total riders |

</div>

A key relationship within the dataset is:
casual + registered = cnt

This relationship was validated during the **data integrity checks**.

---

# Executive Summary

### 1. Dataset Integrity

- The relationship **casual + registered = cnt** holds across the entire dataset.
- No mismatches were detected between the rider segments and total count.
- Environmental variables (temperature, humidity, windspeed) contained **no null values**.

**Conclusion:**  
The dataset is **internally consistent and suitable for analysis.**

---

### 2. Temporal Completeness

- Dataset correctly spans **January 1, 2011 → December 31, 2012**
- Includes **731 days**, correctly accounting for the **2012 leap year**
- While all days are present, **not every day contains a full 24 hours of data**

**Conclusion:**  
The dataset contains **minor hourly logging gaps**, which should be considered when calculating hourly averages.

---

### 3. Ridership Behavior

Two distinct user behaviors were identified:

**Registered Riders**
- Stable demand
- Consistent commuting patterns
- Less sensitive to weather

**Casual Riders**
- Highly seasonal
- Strongly influenced by temperature and weekends
- Large demand swings between winter and summer

---

### 4. Key Insight

The bike sharing system serves **two distinct user segments**:

- **Commuters (Registered Users)** — stable, weekday-heavy demand
- **Tourists / Recreational Riders (Casual Users)** — highly weather and season dependent

---

# Deep Dive Analysis

## Q1: Is the dataset internally consistent?

The first step was validating that the dataset’s metrics behave as expected.

### Validation Checks

- Confirmed **casual + registered = cnt**
- Checked for **NULL values in environmental variables**
- Verified **logical ranges for temperature, humidity, and windspeed**

<div align="center">

| Category | Audit | Metric | Finding | Status |
|--------|------|------|------|------|
| Integrity | casual + registered = cnt | 0 mismatches | Mathematical relationship holds across all rows | Pass |
| Completeness | Weather Null Check | 0 NULL values | Environmental variables complete | Pass |

</div>

**Interpretation:**  
These checks confirm that the dataset is **regression-ready and internally reliable**.

---

# Q2: Is the time series data complete?

Temporal completeness is critical for time series analysis.

### Checks Performed

- Start date validation
- End date validation
- Leap year verification
- Hourly coverage per day

Results show that the dataset correctly spans the expected two-year window.

However, analysis revealed **missing hourly rows on some days**, indicating occasional logging gaps.

<div align="center">

![Hourly Data Density Heatmap](https://github.com/emilyzhu44/bike-sharing-project/blob/main/hourly%20record%20density.png)

</div>

**Interpretation**

- Darker cells represent **full expected hourly coverage**
- Lighter cells reveal **missing hourly observations**
- February and 30-day months naturally appear lighter due to fewer days

This visualization helps researchers quickly identify **structural gaps in the dataset.**

---

# Q3: Investigating a User-Reported Data Discrepancy

A user reported that some records showed:
casual + registered ≠ cnt

### Investigation Steps

**Step 1: Verify dataset integrity**

A SQL query confirmed **0 mismatches** across all rows.

**Step 2: Examine reported values**

The user referenced totals around **3,600 riders**, which is unusually high for a single hour.

Analysis of the raw dataset revealed:

- Maximum hourly value = **997 riders**

This indicates the user was likely referencing **daily aggregated data rather than hourly records**.

### Conclusion

The discrepancy was caused by a **granularity mismatch**, not a data integrity issue.

---

# Q4: Investigating Volatile Casual Ridership

Another user questioned whether the **large fluctuations in casual riders** indicated corrupted data.

### Step 1: Validate Daily Trends

Daily aggregation confirmed the observed volatility.

Casual ridership ranges from:

- ~200 riders per day in winter
- ~3,000 riders per day in summer

<div align="center">

![Daily Casual vs Registered Ridership](https://github.com/emilyzhu44/bike-sharing-project/blob/main/Daily%20Casual%20vs%20Registered%20Ridership.png)

</div>

---

### Step 2: Identify Possible Drivers

Monthly aggregation was used to reduce noise and identify broader trends.

Findings:

- Casual ridership increases **6.5x from winter to summer**
- Registered riders remain relatively stable year-round

<div align="center">
  
| Month | Avg Daily Casual Riders | Avg Daily Registered Riders |
|------|--------------------------|------------------------------|
| January | 194.23 | 1982.11 |
| February | 262.51 | 2392.79 |
| March | 716.84 | 2975.42 |
| April | 1013.37 | 3471.53 |
| May | 1214.27 | 4135.50 |
| June | 1231.77 | 4540.60 |
| July | 1260.60 | 4303.08 |
| August | 1161.92 | 4502.50 |
| September | 1172.05 | 4594.47 |
| October | 963.87 | 4235.35 |
| November | 610.05 | 3637.13 |
| December | 349.89 | 3053.92 |

</div>

---

### Step 3: Environmental Drivers

Additional analysis examined:

- Temperature correlations
- Weekend effects
- Holiday patterns

<div align="center">

![Temperature vs Ridership Heatmap](https://github.com/emilyzhu44/bike-sharing-project/blob/main/Temperature%20vs%20Ridership%20Heatmap.png)

</div>

<div align="center">

![Seasonal analysis](https://github.com/emilyzhu44/bike-sharing-project/blob/main/Seasonal%20Analysis.png)

</div>

---

### Conclusion

The volatility in casual riders is **not a data issue**, but rather reflects **real behavioral differences**.

Two distinct user groups emerge:

**Registered Riders**
- Commuters
- Stable weekday demand
- Less temperature sensitive

**Casual Riders**
- Recreational users
- Highly seasonal
- Strongly temperature dependent

---

# Recommendations

### Data Product Recommendations

- Document **known hourly data gaps** for researchers
- Include **data quality notes** when publishing the dataset
- Provide both **hourly and daily aggregated datasets** to prevent granularity confusion

---

### Analytical Recommendations

Future analysis could explore:

- Weekday vs weekend ridership patterns
- Holiday impact on casual riders
- Predictive modeling of ridership based on weather

---

# Author

Emily Zhu

This project demonstrates:

- Data validation and integrity auditing
- Time series data quality assessment
- SQL-based analysis
- Exploratory data analysis
- Investigating real-world user questions about datasets


