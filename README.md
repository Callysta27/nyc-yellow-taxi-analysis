# 🚕 NYC Yellow Taxi Trip Analysis (Jan–Mar 2026)

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat-square&logo=pandas&logoColor=white)
![Google Colab](https://img.shields.io/badge/Google_Colab-F9AB00?style=flat-square&logo=googlecolab&logoColor=white)
![Rows](https://img.shields.io/badge/Rows-11.07M-blue?style=flat-square)

End-to-end exploratory analysis of **11,077,206 yellow taxi trips** in New York City, from raw data to business recommendations for city transportation management.

📓 **Notebook:** [Open in Google Colab](https://colab.research.google.com/drive/1yHrRVo3FsC11HKq3kQjsvdYUeEy8eTBy?usp=sharing)  
📄 **Full report (Bahasa Indonesia):** see [`report/`](./report)

> Project for the *Big Data Analytics* course, Information Systems, Universitas Tarumanagara (2026).

---

## 📌 Key Findings

| | Finding |
|---|---|
| ⏰ | Demand peaks at **18:00 (693,915 trips)**, about **7.8×** higher than the low point at 04:00 |
| 📍 | **85.84%** of pickups happen in Manhattan; JFK Airport is the **#4** pickup zone |
| 🧍 | Trips are short and solo: median **1.90 miles / 13.7 minutes**, and **83.21%** carry one passenger |
| 💳 | Credit card dominates (**64.30%**), but app-based Flex Fare leads in Brooklyn (**65.3%**) and Staten Island (**81.0%**) |
| 💲 | Short trips cost the most per mile: **$11.07/mile** under 1 mile vs **$3.47/mile** over 20 miles |

---

## ❓ Business Questions

| Code | Question |
|---|---|
| BQ1 | At what hours and days is taxi demand highest? |
| BQ2 | What are the typical trip characteristics (distance, duration, passengers)? |
| BQ3 | Which payment methods are used most, and do they vary by time and location? |
| BQ4 | Which zones and boroughs have the highest pickup and drop-off activity? |
| BQ5 | How are fare and tip related to distance, time, and payment method? |

---

## 🗂️ Dataset

| Source | Format | Records |
|---|---|---|
| [NYC TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page): Yellow Taxi, Jan–Mar 2026 | Parquet (~182 MB) | 11,077,206 rows × 20 columns |
| Taxi Zone Lookup | CSV | 265 zones |

The data exceeds Excel's 1,048,576-row limit, so it was processed with Python and Pandas in Google Colab. Raw data is not included in this repo; download it from the TLC website.

---

## ⚙️ Workflow

![Architecture](images/01_architecture.png)

**1. Data integration:** concatenated 3 monthly Parquet files and joined them with the Taxi Zone Lookup on `LocationID` to get zone and borough names.

**2. Data profiling:** found 3,057,123 systematically missing values (all Flex Fare trips), 134,801 trips with non-positive duration, 86,908 negative fares, 370,198 zero-distance trips, and extreme outliers (e.g. a 328,522-mile trip).

**3. Two-stage data cleaning:**

| | Records | % |
|---|---|---|
| Before cleaning | 11,077,206 | 100.00 |
| Removed | 624,413 | 5.64 |
| **After cleaning** | **10,452,793** | **94.36** |

Flex Fare trips were kept and flagged instead of dropped, since their time, location, distance, and fare fields were complete; removing them would have discarded 27.6% of the data.

**4. Feature engineering (7 features):** pickup hour, day of week, weekday/weekend flag, trip duration, average speed, fare per mile, and tip percentage.

**5. EDA:** descriptive statistics, 13 visualizations, and Pearson correlation analysis.

---

## 📊 Analysis Highlights

### BQ1 · When is demand highest?
![Trips per hour](images/02_trips_per_hour.png)
![Heatmap](images/04_heatmap_day_hour.png)

Demand climbs through the day and peaks at 18:00. The busiest windows are Thursday to Saturday evenings (18:00–21:00). Saturday has the highest daily average (132,371 trips), and weekends average slightly more trips per day than weekdays (118,052 vs 115,366) due to late-night activity.

### BQ2 · What does a typical trip look like?
![Distance and duration](images/06_distance_duration_distribution.png)

Distance and duration are right-skewed (median 1.90 miles, 13.73 minutes). Median speed is only 9.30 mph, reflecting heavy city traffic.

### BQ3 · How do people pay?
![Payment by hour](images/07_payment_by_hour.png)

| Borough | Credit card | Flex Fare | Cash |
|---|---|---|---|
| Manhattan | 65.9% | 25.3% | 8.1% |
| Queens | 64.8% | 19.1% | 14.4% |
| Brooklyn | 33.3% | 65.3% | 1.3% |
| Bronx | 51.3% | 47.1% | 1.5% |
| Staten Island | 7.1% | 81.0% | 11.7% |

### BQ4 · Where is activity concentrated?
![Top 10 zones](images/09_top10_zones.png)

Upper East Side South (~457K pickups) is the busiest zone. Queens has the longest trips (median 10.02 miles, $44.30 fare), driven by airport traffic.

### BQ5 · What drives fare and tip?
![Fare per mile](images/12_fare_per_mile_by_distance.png)
![Correlation matrix](images/14_correlation_matrix.png)

Fare correlates very strongly with distance (r = 0.87) and duration (r = 0.75). A visible cluster at **$70** reflects the flat JFK–Manhattan fare. Credit card trips average a **$4.12 tip**, while cash tips are not recorded in the data.

---

## 💡 Recommendations

| Recommendation | Action | Based on |
|---|---|---|
| **Dynamic fleet allocation** | Add vehicles in Manhattan at 17:00–19:00 on weekdays and 00:00–02:00 on weekends; reduce at 03:00–05:00 | BQ1 |
| **Optimize JFK airport service** | Dedicated pickup zone and organized taxi queue at JFK | BQ4 |
| **Expand Flex Fare outside Manhattan** | Partner with app providers to widen Flex Fare coverage in Brooklyn, Bronx, and Staten Island | BQ3 |
| **Review fleet type & short-trip fares** | Consider compact/electric vehicles for short solo trips and review the base fare | BQ2, BQ5 |

---

## ⚠️ Limitations

- Cash tips are not recorded, so tip analysis covers credit card trips only.
- Flex Fare trips (27.6%) have no passenger count or surcharge data.
- Three months of data cannot capture full-year seasonality.
- The analysis is descriptive and correlational, not causal.

---

## 📁 Repository Structure

```
├── images/      # Visualizations from the analysis
├── notebook/    # Google Colab notebook (.ipynb)
├── report/      # Full report (Bahasa Indonesia, PDF)
└── README.md
```

---

## 👩‍💻 Author

**Callysta Elysia Theona Dethan** · Information Systems, Universitas Tarumanagara  
[LinkedIn](https://www.linkedin.com/in/callysta-dethan-a3112225b) · [GitHub](https://github.com/Callysta27)
