# Final_Report

# Malaysia Airlines vs AirAsia Customer Review Analysis

## Project Overview

This project performs a comparative analysis of customer reviews for **Malaysia Airlines** and **AirAsia** using Apache Hive and PySpark. The analysis evaluates customer satisfaction by examining review ratings, service quality attributes, and customer sentiments extracted from review texts. The goal is to understand how passengers perceive both airlines and identify the strengths and weaknesses of each airline based on real customer experiences.

## Objectives

* Compare customer satisfaction between Malaysia Airlines and AirAsia.
* Analyze customer ratings across different service quality dimensions.
* Clean and preprocess airline review data for reliable analysis.
* Perform exploratory data analysis (EDA) to summarize review patterns.
* Apply sentiment analysis to classify customer reviews into Positive, Neutral, and Negative sentiments.
* Generate meaningful insights that support airline service evaluation.

---

## Dataset

The dataset consists of airline customer reviews collected from an airline review platform. Each review contains structured ratings together with written customer feedback.

### Attributes

* Airline name
* Review title
* Author
* Author country
* Flight date
* Review content
* Aircraft
* Traveller type
* Cabin class
* Flight route
* Overall rating
* Seat comfort rating
* Cabin staff service rating
* Food & beverages rating
* In-flight entertainment rating
* Ground service rating
* Wi-Fi connectivity rating
* Value for money rating
* Recommendation status

---

## Tools and Technologies

* Apache Hive
* Hadoop
* PySpark
* SQL
* Zeppelin Notebook
* CSV Dataset

---

# Project Workflow

### 1. Data Loading

* Created the `airline_reviews` table in Hive.
* Imported the airline review dataset from CSV into Hive for analysis.

---

### 2. Data Cleaning

To ensure accurate analysis, several preprocessing steps were performed:

* Checked for missing overall ratings.
* Counted missing rating records.
* Inspected incomplete review entries.
* Verified data consistency before analysis.

---

### 3. Data Filtering

Since the original dataset contains reviews from many airlines, the project filters only:

* Malaysia Airlines
* AirAsia

A new Hive table (`mas_airasia_reviews`) was created containing only reviews from these two airlines for focused comparison.

---

# Exploratory Data Analysis (EDA)

Several SQL queries were performed to better understand the dataset before conducting comparisons.

### Review Distribution

The total number of customer reviews was calculated for each airline to determine dataset balance.

* Malaysia Airlines: **484 reviews**
* AirAsia: **308 reviews**

---

### Service Quality Comparison

Customer ratings were analyzed across multiple service dimensions, including:

* Overall Rating
* Seat Comfort
* Cabin Staff Service
* Food & Beverages
* In-flight Entertainment
* Ground Service
* Wi-Fi Connectivity
* Value for Money
* Recommendation Rate

These metrics allow direct comparison of passenger satisfaction between both airlines.

---

### Customer Experience Analysis

Customer review texts were examined to identify commonly mentioned experiences such as:

**Malaysia Airlines**

* Cabin crew professionalism
* Food quality
* Seat comfort
* Aircraft condition
* Flight punctuality
* In-flight entertainment
* Premium service quality

**AirAsia**

* Ticket affordability
* Value for money
* Flight delays
* Additional service charges
* Seating comfort
* Food quality
* Budget travel experience

Sample reviews indicate that Malaysia Airlines passengers often praised cabin service and comfort while criticizing delays or aging aircraft, whereas AirAsia reviews frequently highlighted affordability but mentioned delays, optional paid services, and limited onboard amenities.

---

# Sentiment Analysis

A sentiment analysis pipeline was implemented using PySpark.

Each customer review was classified into one of three categories:

* Positive
* Neutral
* Negative

The sentiment results were grouped by airline to compare overall customer perception.

### Sentiment Summary

| Airline           | Positive | Neutral | Negative |
| ----------------- | -------: | ------: | -------: |
| Malaysia Airlines |      377 |       8 |       87 |
| AirAsia           |      245 |       3 |       57 |

The sentiment analysis shows that both airlines received predominantly positive customer feedback, although Malaysia Airlines accumulated a larger number of reviews overall.

The resulting sentiment dataset was then saved back into Hive for further analysis and reporting.

---

# Key Findings

The analysis reveals several important observations:

* Both airlines receive more positive than negative customer reviews.
* Malaysia Airlines is generally recognized for its cabin crew, comfort, and full-service experience, although some reviews highlight issues with delays, food consistency, and older aircraft.
* AirAsia is widely appreciated for affordability and value for money, but customers frequently mention additional service charges, limited onboard services, and occasional flight delays.
* Sentiment analysis complements the numerical ratings by providing insights into passengers' overall opinions expressed in review text.

---

