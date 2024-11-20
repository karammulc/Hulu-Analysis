
# Hulu Dataset Analysis

This repository documents the cleaning, transformation, and exploratory analysis of the **Hulu Dataset**. The analysis focuses on preparing the dataset for further exploration and visualization in Tableau, while maintaining data integrity and ensuring compatibility with SQL-based queries in BigQuery.

---

## **Project Overview**

The Hulu dataset contains information about movies and TV shows, including genres, IMDb ratings, release years, and availability in specific countries. The goal of this project is to clean and transform the dataset for advanced exploration and generate insights through SQL and Tableau visualizations.

---

## **Dataset Details**

The dataset includes the following columns:
- **title**: Title of the movie or TV show.
- **type**: The type of content (`movie` or `tv`).
- **genres**: Genre(s) of the content.
- **releaseyear**: The release year of the content.
- **imdbId**: IMDb ID for each title.
- **imdbaveragerating**: IMDb rating (on a scale of 1–10).
- **imdbnumbervotes**: Number of votes on IMDb.
- **availablecountries**: Availability of content in specific countries (`US`, `JP`, or `JP US`).

---

## **Process**

### 1. **Data Cleaning and Transformation**
- Renamed columns for consistent capitalization.
- Standardized the `availablecountries` field:
  - Replaced `JP, US` with `JP US`.
  - Verified using Google Sheets filters and manual checks.
- Added new boolean columns (`US`, `JP`):
  - Created logic to mark availability in the US or Japan.
  - Used formulas:
    ```excel
    =IF(OR(A2="US", A2="JP US"), "Yes", "No")
    =IF(OR(A2="JP", A2="JP US"), "Yes", "No")
    ```
- Removed commas and special characters (`&`) from `genres`:
  - Used `SUBSTITUTE` and `TRIM` functions to clean the column.
  - Removed 283 duplicate rows.

### 2. **Transition to BigQuery**
- Exported the cleaned dataset as a CSV and imported it into BigQuery.
- Created a new table, `huluv3`, by handling `NULL` values:
  | Column              | Replacement Value         |
  |---------------------|---------------------------|
  | `title`             | "Unknown Title"           |
  | `type`              | "Unknown Type"            |
  | `genre`             | "Unknown"                 |
  | `releaseyear`       | 0                         |
  | `imdbId`            | "Not Available"           |
  | `imdbaveragerating` | (No Replacement)          |
  | `imdbnumbervotes`   | (No Replacement)          |
  | `availablecountries`| "Not Specified"           |
  | `US`                | "No"                      |
  | `JP`                | "No"                      |

### 3. **Exploratory SQL Queries**
- Counted total rows and filtered rows based on availability in the US, Japan, or both.
- Identified top-rated titles using IMDb average ratings.
- Analyzed the most active release years using group-by queries.

---
