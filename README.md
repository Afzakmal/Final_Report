# Final_Report

Here is the complete content as **one single `README.md` file**:

```markdown
# Malaysia Airlines vs AirAsia Customer Review Analysis

## Project Overview

This project performs a comparative analysis of customer reviews between **Malaysia Airlines** and **AirAsia** using **Apache Hive** and **PySpark**.

The analysis evaluates customer satisfaction by examining:

- Customer ratings
- Service quality dimensions
- Recommendation patterns
- Sentiment extracted from customer review texts

The purpose of this project is to understand passenger perceptions and identify the strengths and weaknesses of both airlines based on real customer experiences.

---

# Objectives

The objectives of this project are:

- Compare customer satisfaction between Malaysia Airlines and AirAsia.
- Analyze customer ratings across different service quality dimensions.
- Clean and preprocess airline review data.
- Perform exploratory data analysis (EDA).
- Apply sentiment analysis using PySpark.
- Generate insights for airline service evaluation.

---

# Dataset Description

The dataset contains airline customer reviews collected from an airline review platform.

Each review contains structured ratings and written customer feedback.

## Dataset Attributes

| Attribute | Description |
|---|---|
| Airline name | Airline company |
| Review title | Review title |
| Author | Customer name |
| Author country | Customer country |
| Flight date | Date of flight |
| Review content | Customer review text |
| Aircraft | Aircraft type |
| Traveller type | Passenger category |
| Cabin class | Travel class |
| Flight route | Flight route |
| Overall rating | Overall satisfaction score |
| Seat comfort rating | Seat comfort score |
| Cabin staff service rating | Staff service score |
| Food & beverages rating | Food quality score |
| In-flight entertainment rating | Entertainment score |
| Ground service rating | Airport service score |
| Wi-Fi connectivity rating | Wi-Fi score |
| Value for money rating | Price satisfaction |
| Recommendation status | Customer recommendation |

---

# Tools and Technologies

- Apache Hive
- Hadoop
- PySpark
- SQL
- Zeppelin Notebook
- CSV Dataset

---

# Project Workflow

## Step 1: Data Loading

The airline review dataset was loaded from CSV format into Hive.

Hive table created:

```

airline_reviews

````

The dataset was stored in Hive to support SQL-based data processing and analysis.

---

# Step 2: Data Cleaning in Hive

## 2.1 Checking Missing Ratings

Missing overall ratings were checked.

```sql
SELECT *
FROM airline_reviews
WHERE overall_rating IS NULL;
````

Counting missing values:

```sql
SELECT COUNT(*) AS missing_ratings
FROM airline_reviews
WHERE overall_rating IS NULL;
```

This step ensures incomplete rating records do not affect analysis.

---

## 2.2 Filtering Relevant Airlines

Only Malaysia Airlines and AirAsia reviews were selected.

```sql
CREATE TABLE mas_airasia_reviews AS
SELECT *
FROM airline_reviews
WHERE airline_name IN ('Malaysia Airlines','AirAsia');
```

This creates a focused dataset for comparison.

---

## 2.3 Duplicate Review Checking

Duplicate reviews were identified using:

* Airline name
* Review title
* Author
* Flight date

```sql
SELECT airline_name,
       title,
       author,
       flight_date,
       COUNT(*) AS duplicate_count
FROM mas_airasia_reviews
GROUP BY airline_name,
         title,
         author,
         flight_date
HAVING COUNT(*) > 1;
```

---

## 2.4 Removing Duplicate Reviews

Duplicates were removed using ROW_NUMBER().

```sql
CREATE TABLE mas_airasia_reviews_clean AS
SELECT *
FROM (
    SELECT *,
           ROW_NUMBER() OVER(
           PARTITION BY airline_name,
                        title,
                        author,
                        flight_date
           ORDER BY flight_date
           ) AS rn
    FROM mas_airasia_reviews
)t
WHERE rn = 1;
```

---

## 2.5 Verify Duplicate Removal

```sql
SELECT airline_name,
       title,
       author,
       flight_date,
       COUNT(*) AS duplicate_count
FROM mas_airasia_reviews_clean
GROUP BY airline_name,
         title,
         author,
         flight_date
HAVING COUNT(*) > 1;
```

After cleaning, duplicate records were removed.

---

# Review Distribution After Cleaning

| Airline           | Total Reviews |
| ----------------- | ------------: |
| Malaysia Airlines |           484 |
| AirAsia           |           308 |

Malaysia Airlines contains more reviews compared with AirAsia.

---

# Exploratory Data Analysis (EDA)

## 1. Average Overall Rating

SQL:

```sql
SELECT
airline_name,
ROUND(AVG(overall_rating),2)
AS average_overall_rating
FROM mas_airasia_reviews
GROUP BY airline_name;
```

Result:

| Airline           | Average Rating |
| ----------------- | -------------: |
| AirAsia           |           6.45 |
| Malaysia Airlines |           6.44 |

### Analysis

Both airlines have almost identical overall satisfaction ratings.

AirAsia slightly exceeds Malaysia Airlines, mainly due to affordability and convenience.

---

# 2. Seat Comfort Comparison

Result:

| Airline           | Seat Comfort |
| ----------------- | -----------: |
| AirAsia           |         3.19 |
| Malaysia Airlines |         3.54 |

### Analysis

Malaysia Airlines provides better seat comfort.

Passengers generally prefer Malaysia Airlines because of:

* More comfortable seating
* Full-service cabin experience

---

# 3. Cabin Staff Service Comparison

Result:

| Airline           | Cabin Staff Rating |
| ----------------- | -----------------: |
| AirAsia           |               3.38 |
| Malaysia Airlines |               3.58 |

### Analysis

Malaysia Airlines received higher cabin staff ratings.

Positive feedback included:

* Professional cabin crew
* Better customer service
* Passenger assistance

---

# 4. Value for Money Comparison

Result:

| Airline           | Value Rating |
| ----------------- | -----------: |
| AirAsia           |         3.67 |
| Malaysia Airlines |         3.81 |

### Analysis

Both airlines provide good value.

AirAsia is preferred for budget travel, while Malaysia Airlines provides better overall service value.

---

# 5. Recommendation Analysis

Result:

| Airline           | Recommendation  | Count |
| ----------------- | --------------- | ----: |
| AirAsia           | Recommended     |   205 |
| AirAsia           | Not Recommended |   100 |
| Malaysia Airlines | Recommended     |   307 |
| Malaysia Airlines | Not Recommended |   156 |

### Analysis

Both airlines received more positive recommendations than negative responses.

---

# Customer Experience Analysis

## Malaysia Airlines

### Positive Feedback

Customers highlighted:

* Cabin crew professionalism
* Seat comfort
* Food quality
* Premium service experience

### Negative Feedback

Common issues:

* Flight delays
* Older aircraft
* Food inconsistency

---

## AirAsia

### Positive Feedback

Customers appreciated:

* Affordable ticket prices
* Value for money
* Convenient routes

### Negative Feedback

Common complaints:

* Extra service charges
* Limited onboard facilities
* Flight delays
* Lower seating comfort

---

# Sentiment Analysis Using PySpark

Sentiment analysis was performed using **TextBlob** and **PySpark**.

Each review was classified into:

* Positive
* Neutral
* Negative

---

## Sentiment Classification Code

```python
from textblob import TextBlob
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType


def get_sentiment(text):

    score = TextBlob(text).sentiment.polarity

    if score > 0:
        return "Positive"

    elif score < 0:
        return "Negative"

    else:
        return "Neutral"


sentiment_udf = udf(get_sentiment, StringType())


df_sentiment = df.withColumn(
    "sentiment",
    sentiment_udf(df.content)
)
```

---

# Sentiment Result

| Airline           | Positive | Neutral | Negative |
| ----------------- | -------: | ------: | -------: |
| Malaysia Airlines |      377 |       8 |       87 |
| AirAsia           |      245 |       3 |       57 |

---

# Sentiment Analysis Findings

Both airlines received mostly positive customer feedback.

## Malaysia Airlines

Positive sentiment focused on:

* Cabin service
* Comfort
* Premium experience

Negative sentiment focused on:

* Flight delays
* Aircraft condition

---

## AirAsia

Positive sentiment focused on:

* Affordable fares
* Convenience
* Budget travel

Negative sentiment focused on:

* Additional charges
* Limited onboard services

---

# Saving Sentiment Results to Hive

The final sentiment dataset was stored back into Hive.

```python
df_sentiment.write.mode("overwrite").saveAsTable(
    "aviation.airline_sentiment_analysis"
)
```

---

# Final Conclusion

The analysis shows that both Malaysia Airlines and AirAsia provide satisfactory customer experiences.

## Malaysia Airlines Strengths

* Better seat comfort
* Higher cabin staff rating
* Strong full-service experience

## AirAsia Strengths

* Affordable pricing
* Budget-friendly travel
* Good accessibility

Overall:

* Malaysia Airlines is preferred for comfort, service quality, and passenger experience.
* AirAsia remains competitive due to affordability and value for money.

The sentiment analysis supports the rating analysis by showing that passengers generally express positive opinions toward both airlines.

```
```
---

