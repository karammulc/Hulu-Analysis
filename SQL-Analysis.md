# SQL Analysis - Hulu Dataset

## Overview
This documentation covers the SQL transformations performed on the Hulu dataset after initial cleaning in Google Sheets. The focus was on creating clean, analysis-ready data structures while maintaining data integrity.

## Data Preparation
Initial dataset was cleaned in Google Sheets before SQL work:
- Standardized column names
- Removed commas from `availablecountries` values
- Created boolean columns for regional availability
- Cleaned genre formatting
- Removed 283 duplicate rows
- Data exported as "Hulu V2" for BigQuery import

## NULL Value Handling Strategy
Created a new table with standardized NULL handling:

| Column              | Data Type    | Replacement Value         | Notes                              |
|---------------------|--------------|---------------------------|------------------------------------|
| `title`             | STRING       | "Unknown Title"           | Replaces `NULL` with a placeholder for missing titles. |
| `type`              | STRING       | "Unknown Type"            | Replaces `NULL` with a placeholder for missing content type. |
| `genre`             | STRING       | "Unknown"                 | Replaces `NULL` with "Unknown" to indicate undefined genres. |
| `releaseyear`       | INTEGER      | 0                         | Replaces `NULL` with 0 to represent missing release year. |
| `imdbId`            | STRING       | "Not Available"           | Replaces `NULL` with "Not Available" for missing IMDb IDs. |
| `imdbaveragerating` | FLOAT        | (No Replacement)          | Preserved `NULL` to avoid skewing averages. |
| `imdbnumbervotes`   | INTEGER      | (No Replacement)          | Preserved `NULL` to avoid affecting vote counts. |
| `availablecountries`| STRING       | "Not Specified"           | Replaces `NULL` with "Not Specified" for missing availability info. |
| `US`                | STRING       | "No"                      | Replaces `NULL` with "No" to indicate non-availability in the US. |
| `JP`                | STRING       | "No"                      | Replaces `NULL` with "No" to indicate non-availability in Japan. |

## SQL Transformations

### Creation of huluv3
```sql
CREATE TABLE `hulu.huluv3` AS
SELECT
  COALESCE(title, 'Unknown Title') AS title,
  COALESCE(type, 'Unknown Type') AS type,   
  COALESCE(genre, 'Unknown') AS genre,    
  COALESCE(CAST(releaseyear AS INT64), 0) AS releaseyear,
  COALESCE(imdbId, 'Not Available') AS imdbId,
  imdbaveragerating,
  imdbnumbervotes,    
  COALESCE(availablecountries, 'Not Specified') AS availablecountries,
  COALESCE(US, 'No') AS US,
  COALESCE(JP, 'No') AS JP
FROM `hulu.huluv2`;
```

### Creation of huluv4 with Genre Flags
```sql
CREATE TABLE `hulu.huluv4` AS
SELECT 
  title,
  type,
  releaseyear,
  imdbId,
  imdbaveragerating,
  imdbnumbervotes,
  availablecountries,
  US,
  JP,
  REGEXP_CONTAINS(genre, 'Action') as has_Action,
  REGEXP_CONTAINS(genre, 'Adventure') as has_Adventure,
  REGEXP_CONTAINS(genre, 'Animation') as has_Animation,
  REGEXP_CONTAINS(genre, 'Biography') as has_Biography,
  REGEXP_CONTAINS(genre, 'Comedy') as has_Comedy,
  REGEXP_CONTAINS(genre, 'Crime') as has_Crime,
  REGEXP_CONTAINS(genre, 'Documentary') as has_Documentary,
  REGEXP_CONTAINS(genre, 'Drama') as has_Drama,
  REGEXP_CONTAINS(genre, 'Family') as has_Family,
  REGEXP_CONTAINS(genre, 'Fantasy') as has_Fantasy,
  REGEXP_CONTAINS(genre, 'Game-Show') as has_GameShow,
  REGEXP_CONTAINS(genre, 'History') as has_History,
  REGEXP_CONTAINS(genre, 'Horror') as has_Horror,
  REGEXP_CONTAINS(genre, 'Music') as has_Music,
  REGEXP_CONTAINS(genre, 'Musical') as has_Musical,
  REGEXP_CONTAINS(genre, 'Mystery') as has_Mystery,
  REGEXP_CONTAINS(genre, 'News') as has_News,
  REGEXP_CONTAINS(genre, 'Reality-TV') as has_RealityTV,
  REGEXP_CONTAINS(genre, 'Romance') as has_Romance,
  REGEXP_CONTAINS(genre, 'Sci-Fi') as has_SciFi,
  REGEXP_CONTAINS(genre, 'Short') as has_Short,
  REGEXP_CONTAINS(genre, 'Sport') as has_Sport,
  REGEXP_CONTAINS(genre, 'Talk-Show') as has_TalkShow,
  REGEXP_CONTAINS(genre, 'Thriller') as has_Thriller,
  REGEXP_CONTAINS(genre, 'War') as has_War,
  REGEXP_CONTAINS(genre, 'Western') as has_Western
FROM `hulu.huluv3`;
```

## Data Verification Results

### Basic Row Counts
```sql
SELECT COUNT(*) AS total_rows FROM `hulu.huluv3`;
```

-- Result: 9,596 rows


```sql
SELECT COUNT(*) AS us_available 
FROM `hulu.huluv3` 
WHERE US = TRUE;
```

-- Result: 2,855 rows

```sql
SELECT COUNT(*) AS jp_available 
FROM `hulu.huluv3` 
WHERE JP = TRUE;
```
-- Result: 6,996 rows

```sql
SELECT COUNT(*) AS both_available 
FROM `hulu.huluv3` 
WHERE JP AND US = TRUE;

```
Result: 255 rows

### Release Year Distribution
| Release Year | Release Count |
|--------------|---------------|
| 2022         | 763           |
| 2023         | 646           |
| 2021         | 628           |
| 2019         | 626           |
| 2018         | 535           |
| 2020         | 501           |
| 2017         | 460           |
| 2016         | 429           |
| 2015         | 425           |
| 2014         | 391           |
| 2024         | 382           |
| 2013         | 364           |
| 2012         | 259           |
| 2011         | 247           |
| 2010         | 230           |

### Genre Analysis Structure
Creating boolean flag columns for each genre transformed how we can analyze the content. Instead of dealing with complex string parsing, each title now has clear true/false values for every possible genre. 
This makes it much simpler to analyze how genres interact with each other and with other variables in the dataset. For example, we can easily see what genres are most popular in different regions, or how certain genre combinations tend to be rated. 
The structure is particularly valuable for Tableau visualization, as it allows for intuitive filtering and grouping of content.

