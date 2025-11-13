---
title: "Exploring Air Quality Across the United States Using Public APIs"
date: 2025-02-14
---

# Exploring Air Quality Across the United States Using Public APIs

Understanding air quality has become increasingly important as cities across the United States experience rising pollution levels, seasonal smoke, temperature inversions, and other environmental pressures. For this project, I wanted to explore how air quality varies across geographic locations and identify which pollutants appear to drive the Air Quality Index (AQI). Rather than rely on pre-packaged datasets, I built my own dataset using an API-driven data collection pipeline in Python. This post summarizes what I found, how I gathered the data, and how other students can replicate a project like this.

## Motivating Question

I centered my project around the following question:

**How do AQI and major air pollutants vary across locations in the United States, and which pollutants appear most strongly associated with overall air quality?**

This question is relevant because AQI is a composite score, but in practice, particular pollutants (such as PM2.5 during wildfire season) have a disproportionate effect. I wanted to quantify these relationships and examine how weather variables influence or accompany pollution patterns.

## Ethical Considerations and API Use

I used the **AQICN API**, which provides real-time air quality information from monitoring stations worldwide. The API permits data retrieval for personal and academic use, but it does not allow publishing raw data publicly. Therefore, the dataset itself is not included in my GitHub repository. Instead, the repository includes a data-collection script (`get_data.py`) that allows anyone with their own API token to regenerate the dataset.

Weather data came from **Open-Meteo**, a free and open API with no authentication required and no redistribution restrictions.

To avoid exposing private keys, I stored my AQICN token as an environment variable rather than hardcoding it.

## Data Collection Overview

My data pipeline performs the following steps:

1. Generates a grid of random latitude and longitude points across the continental United States to ensure broad coverage and produce at least 200 observations.
2. Queries the AQICN API for each coordinate:  
   `https://api.waqi.info/feed/geo:{lat};{lon}/?token=YOUR_TOKEN`
3. Extracts AQI and pollutant concentrations, including:
   - PM2.5  
   - PM10  
   - Ozone (O3)  
   - Nitrogen dioxide (NO2)  
   - Sulfur dioxide (SO2)  
   - Carbon monoxide (CO)
4. Queries the Open-Meteo API for matching weather conditions:
   - Temperature  
   - Humidity  
   - Surface pressure  
   - Wind speed
5. Cleans missing or malformed values such as dashes or nulls.
6. Merges pollutant and weather data into a single tidy dataset.

This produces well over 200 rows with multiple numeric variables, meeting the STAT 386 data curation requirements.

My code is available here:  
[GitHub: blog_post_data](https://github.com/iamcall/blog_post_data)

## How Others Can Reproduce This

Anyone can build a similar dataset by following these steps:

1. Request an AQICN API token:  
   https://aqicn.org/api/
2. Clone my repository and set the environment variable:  
   `export AQI_TOKEN="your_token_here"`
3. Run the data collector:  
   `python get_data.py`
4. The script will generate a CSV with all merged pollutant and weather variables.
5. Run the provided notebook or script to recreate the EDA.

This approach can be adapted to any API that returns JSON.

## EDA Highlights

After cleaning, I examined distributions and correlations across the main variables.

### Distribution of Pollutants

Boxplots showed that PM2.5 and PM10 varied substantially more than O3, NO2, SO2, or CO. Sulfur dioxide and carbon monoxide remained low for most locations.

### AQI by Location

AQI values varied widely. Some rural or high-elevation areas showed cleaner air, while parts of the Southwest and densely populated urban areas tended toward higher AQI.

### Relationships Between Weather and AQI

Scatterplots suggested the following:

- AQI increases when **wind speed is low**, consistent with stagnant air conditions.
- Humidity and temperature had weaker associations, varying more by region.

### Correlation Heatmap

The heatmap revealed:

- **PM2.5 had the strongest correlation with AQI**, significantly higher than any other pollutant.
- PM10 correlated positively but less strongly.
- Ozone, NO2, SO2, and CO showed much weaker relationships overall.
- Wind speed tended to correlate negatively with AQI.

These findings align with established air quality dynamics: fine particulate matter is typically the largest contributor to unhealthy air conditions.

## Key Findings

1. **PM2.5 is the dominant driver of AQI**, far more influential than other pollutants.
2. Many monitoring stations are missing pollutant measurements, especially SO2 and CO.
3. Weather variables—particularly wind speed—appear tied to pollution concentration.
4. Air quality varies dramatically across relatively small geographic distances.

## Further Resources

- AQICN API documentation: https://aqicn.org/api/
- Open-Meteo API documentation: https://open-meteo.com/
- Public APIs list: https://github.com/public-apis/public-apis
- My GitHub repository:  
  https://github.com/iamcall/blog_post_data

## Conclusion

By building a dataset directly from two APIs, I was able to explore air quality patterns across the United States and identify which pollutants most strongly affect the Air Quality Index. PM2.5 emerged as the primary factor driving AQI variation, and weather variables provided helpful context about atmospheric conditions. This project demonstrates how collecting original data through APIs can lead to a meaningful and customizable dataset for exploratory analysis.

If others would like to extend this project, they could incorporate temporal trends, predictive modeling, or spatial visualizations.
