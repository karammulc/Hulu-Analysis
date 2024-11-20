# Hulu Content Analysis
<img src="https://github.com/karammulc/Hulu-Analysis/blob/main/Hulu-Logo.jpg" alt="Hulu Logo" width="400"/>

## Project Overview
This project analyzes Hulu's content distribution across regions, focusing on genre patterns, ratings, and release year trends. Starting with messy, real-world streaming platform data, I transformed it into a clean, analysis ready structure through careful data processing and SQL transformations.

## Tools Used
- Google Sheets: Initial data cleaning
- BigQuery: SQL transformations
- Tableau: Visualization (in progress)

## Data Source
Original dataset sourced from [Kaggle's Hulu Dataset](https://www.kaggle.com/datasets/octopusteam/full-hulu-dataset)

*Note: This analysis is based on a snapshot of Hulu's content and may not reflect current offerings.*
  
## Data Processing Challenges and Solutions

The initial data presented several interesting challenges. The genre field was particularly tricky, containing multiple values smooshed together with inconsistent separators. Regional availability was similarly complex, with values like "JP, US" that needed careful handling. Rather than rush into analysis, I took a systematic approach to cleaning these issues.

For genres, I first standardized the separators in Google Sheets, then later created boolean flags in SQL. This turned messy strings like "Drama, Sci-Fi & Thriller" into clean true/false columns for each genre. Similarly, for regional availability, I created clear boolean columns for US and JP availability, making it much easier to analyze content distribution.

A careful approach to NULL values was crucial - while it made sense to replace missing titles with "Unknown Title", I deliberately preserved NULL values for ratings and vote counts to maintain statistical integrity. Each decision was made with the end goal of accurate analysis in mind.

## Data Transformation Process

### Initial Cleanup (Google Sheets):
Starting with the raw dataset, I first:
- Standardized all column names for consistency
- Cleaned up country availability formatting
- Removed duplicate entries (found 283)
- Verified data integrity at each step
- Fixed genre formatting issues

### SQL Transformations:
The SQL work focused on creating two key tables:

#### huluv3: Basic Cleanup
Created a foundation with standardized NULL handling:
- Missing titles → "Unknown Title"
- Missing types → "Unknown Type"
- Missing genres → "Unknown"
- Missing years → 0
- Missing IMDb IDs → "Not Available"
- Ratings/votes → preserved NULL
- Missing availability → "Not Specified"

#### huluv4: Advanced Structure
Built on huluv3 by adding boolean flags for:
- Each possible genre
- Regional availability
- Content type classifications

## Key Findings
Through this process, I discovered:
- Total content: 9,596 titles
- US availability: 2,855 titles
- JP availability: 6,996 titles
- Overlap: 255 titles available in both regions

Release year distribution shows interesting patterns:
- 2022 leads with 763 titles
- Recent years (2019-2023) show strong content growth
- Historical content preserved but less numerous

## Next Steps: Tableau Visualization
With clean, structured data in place, I'm moving to Tableau to explore:
- Genre popularity across regions and by release date
- Rating patterns and trends
- Content release distribution
- Regional content strategy insights



