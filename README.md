# Malaysia Airlines vs AirAsia Customer Review Analysis

## Project Overview

This project performs a comparative analysis of customer reviews between **Malaysia Airlines** and **AirAsia** using **Apache Hive** and **PySpark**.

The analysis evaluates customer satisfaction by examining customer ratings, service quality attributes, recommendation patterns, and sentiments extracted from review texts.

The goal is to understand passenger opinions and identify the strengths and weaknesses of both airlines based on real customer experiences.

---

## Objectives

- Compare customer satisfaction between Malaysia Airlines and AirAsia.
- Analyze customer ratings across different service quality dimensions.
- Clean and preprocess airline review data.
- Perform exploratory data analysis (EDA).
- Apply sentiment analysis using PySpark.
- Generate insights for airline service evaluation.

---

# Dataset

The dataset contains airline customer reviews collected from an airline review platform.

## Attributes

- Airline name
- Review title
- Author
- Author country
- Flight date
- Review content
- Aircraft
- Traveller type
- Cabin class
- Flight route
- Overall rating
- Seat comfort rating
- Cabin staff service rating
- Food & beverages rating
- In-flight entertainment rating
- Ground service rating
- Wi-Fi connectivity rating
- Value for money rating
- Recommendation status

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

## 1. Data Loading

The airline review dataset was imported from CSV into Hive.

Hive table:

```
airline_reviews
```

---

# 2. Data Cleaning

## Checking Missing Ratings

```sql
SELECT *
FROM airline_reviews
WHERE overall_rating IS NULL;
```

Count missing ratings:

```sql
SELECT COUNT(*) AS missing_ratings
FROM airline_reviews
WHERE overall_rating IS NULL;
```

---

## Filtering Malaysia Airlines and AirAsia

Only relevant airlines were selected:

```sql
CREATE TABLE mas_airasia_reviews AS
SELECT *
FROM airline_reviews
WHERE airline_name IN ('Malaysia Airlines','AirAsia');
```

---

## Duplicate Checking

Duplicates were identified using:

- Airline name
- Review title
- Author
- Flight date


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

## Removing Duplicate Reviews

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

# Review Distribution After Cleaning

| Airline | Total Reviews |
|---|---:|
| Malaysia Airlines | 484 |
| AirAsia | 308 |

---

# Exploratory Data Analysis

## Average Overall Rating

```sql
SELECT
airline_name,
ROUND(AVG(overall_rating),2)
AS average_overall_rating
FROM mas_airasia_reviews
GROUP BY airline_name;
```

Result:

| Airline | Rating |
|---|---:|
| AirAsia | 6.45 |
| Malaysia Airlines | 6.44 |

### Analysis

Both airlines have almost identical overall ratings.

AirAsia slightly scores higher because customers appreciate affordability and convenience.

---

# Seat Comfort Comparison

| Airline | Rating |
|---|---:|
| AirAsia | 3.19 |
| Malaysia Airlines | 3.54 |

### Analysis

Malaysia Airlines provides better seating comfort due to its full-service airline experience.

---

# Cabin Staff Service Comparison

| Airline | Rating |
|---|---:|
| AirAsia | 3.38 |
| Malaysia Airlines | 3.58 |

### Analysis

Malaysia Airlines receives better cabin staff ratings because passengers highlight:

- Professional crew
- Better assistance
- Service quality

---

# Value for Money Comparison

| Airline | Rating |
|---|---:|
| AirAsia | 3.67 |
| Malaysia Airlines | 3.81 |

### Analysis

AirAsia is known for affordable travel, while Malaysia Airlines provides stronger overall service value.

---

# Recommendation Analysis

| Airline | Recommended | Count |
|---|---|---:|
| AirAsia | Yes | 205 |
| AirAsia | No | 100 |
| Malaysia Airlines | Yes | 307 |
| Malaysia Airlines | No | 156 |

Both airlines received more positive recommendations than negative feedback.

---

# Customer Experience Analysis

## Malaysia Airlines

Positive feedback:

- Professional cabin crew
- Comfortable seats
- Good food quality
- Premium service experience

Negative feedback:

- Flight delays
- Older aircraft
- Food inconsistency

---

## AirAsia

Positive feedback:

- Affordable tickets
- Good value for money
- Convenient routes

Negative feedback:

- Extra charges
- Limited onboard services
- Flight delays
- Less comfortable seats

---

# Sentiment Analysis Using PySpark

Sentiment analysis was performed using **TextBlob**.

Reviews were classified into:

- Positive
- Neutral
- Negative

---

## Sentiment Processing Code

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

# Sentiment Results

| Airline | Positive | Neutral | Negative |
|---|---:|---:|---:|
| Malaysia Airlines | 377 | 8 | 87 |
| AirAsia | 245 | 3 | 57 |

---

# Sentiment Findings

Both airlines received mostly positive customer sentiment.

## Malaysia Airlines

Positive sentiment:

- Cabin service
- Comfort
- Premium experience

Negative sentiment:

- Delays
- Aircraft condition

---

## AirAsia

Positive sentiment:

- Affordable fares
- Convenience
- Budget travel

Negative sentiment:

- Additional charges
- Limited services

---

# Save Sentiment Results to Hive

```python
df_sentiment.write.mode("overwrite").saveAsTable(
    "aviation.airline_sentiment_analysis"
)
```

---

# Conclusion

The analysis shows that both airlines provide satisfactory customer experiences.

## Malaysia Airlines Strengths

- Better seat comfort
- Higher cabin staff service rating
- Strong full-service experience

## AirAsia Strengths

- Affordable pricing
- Budget-friendly travel
- Good accessibility

Overall:

Malaysia Airlines is preferred for comfort and service quality, while AirAsia remains competitive due to affordability and value for money.

The sentiment analysis supports the rating analysis by showing that customers generally have positive opinions toward both airlines.

---

