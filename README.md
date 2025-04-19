# Predicting the Reach of Power Outages

By Tyler Ditmars (tditmars@umich.edu) and Neha Pinnu (npinnu@umich.edu)

## Introduction
This dataset was obtained by Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure and contains information about major outages that were witnessed by different U.S. States in the continental U.S. between January 2000 and July 2016. Alongside information about different outages, this dataset also contains the geographical location of the outage, the regional climate information, land-use characteristics, electricity consumption patterns, and economic characteristics of states that were affected by outages.

After looking through the data and getting a better understaning of what sort of data that was collected, we decided to research the question:

>**What are the common characteristics of severe power outages, and can those characteristics be used to predict the number of customers affected by a future power outage?**

The information we are analyzing through this data analysis will give us a better understanding on how different factors can impact the range and severity of a power outage, which could potentailly allow electric companies to prepare better for the extent of an outage when it does occur.

This dataset originally contained contains 1535 rows and 57 columns. We narrowed it down to 24 relavent columns below that we believe are potential factors that could have correlation with the number of customers affected. 

| Column Name in Outages Dataset | Description of Column |
|--------|--------------|
| POSTAL.CODE | Represents the postal code of the U.S. states|
| NERC.REGION | The North American Electric Reliability Corporation (NERC) regions involved in the outage event|
| CLIMATE.REGION | U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.)|
| ANOMALY.LEVEL | This represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season. It is estimated as a 3-month running mean of ERSST.v4 SST anomalies in the Niño 3.4 region (5°N to 5°S, 120–170°W) |
| CLIMATE.CATEGORY | This represents the climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI) |
| CAUSE.CATEGORY | Categories of all the events causing the major power outages |
| CAUSE.CATEGORY.DETAIL | Detailed description of the event categories causing the major power outages |
| OUTAGE.DURATION |	Duration of outage events (in minutes) |
| DEMAND.LOSS.MW | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
| CUSTOMERS.AFFECTED| Number of customers affected by the power outage event |
| TOTAL.PRICE |	Average monthly electricity price in the U.S. state (cents/kilowatt-hour) |
| TOTAL.SALES | Total electricity consumption in the U.S. state (megawatt-hour)|
| TOTAL.CUSTOMERS |	Annual number of total customers served in the U.S. state |
| PC.REALGSP.REL | Relative per capita real GSP as compared to the total per capita real GDP of the U.S. (expressed as fraction of per capita State real GDP & per capita US real GDP) |
| UTIL.REALGSP | Real GSP contributed by Utility industry (measured in 2009 chained U.S. dollars) |
| PI.UTIL.OFUSA | State utility sector׳s income (earnings) as a percentage of the total earnings of the U.S. utility sector׳s income (in %) |
| POPULATION| Population in the U.S. state in a year |
| POPPCT_URBAN | Percentage of the total population of the U.S. state represented by the urban population (in %) |
| POPDEN_UC | Population density of the urban clusters (persons per square mile) |
| POPDEN_RURAL | Population density of the rural areas (persons per square mile) |
| PCT_WATER_TOT | Percentage of water area in the U.S. state as compared to the overall water area in the continental U.S. (in %) |
| PCT_WATER_INLAND | Percentage of inland water area in the U.S. state as compared to the overall inland water area in the continental U.S. (in %) |
| OUTAGE.START | DateTime object that indicates the day and time when the outage event started (as reported by the corresponding Utility in the region) |
| OUTAGE.RESTORATION | DateTime object that indicates the day and time when power was restored to all the customers (as reported by the corresponding Utility in the region) |

## Data Cleaning and Exploratory Data Analysis
The first step we did was remove the first two columns in the dataset: 'variable' and 'OBS.' The 'variable' column only contained NaN values, and the 'OBS' column was just another index column that we didn't need.

The next aspect of our data cleaning was combining 'OUTAGE.START.DATE' and 'OUTAGE.START.TIME' into OUTAGE.START. Similarly, we also combined 'OUTAGE.RESTORATION.DATE' and 'OUTAGE.RESTORATION.TIME' into OUTAGE.RESTORATION. We dropped the original four columns in order to avoid multicollinearity.

We decided to only keep the 27 Columns listed above, because we felt that the other columns were either repeating similar data, or weren't relavent to analysis the sevarity and customers affected of an outage.


### Data Cleaning

### Univariate Analysis

### Bivariate Analysis

## Framing a Prediction Problem

## Baseline Model

## Final Model