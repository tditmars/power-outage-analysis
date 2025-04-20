# Predicting the Reach of Power Outages

By Tyler Ditmars (tditmars@umich.edu) and Neha Pinnu (npinnu@umich.edu)

## Introduction
The dataset used in this report was collected by Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure and contains information about major power outages that occurred across various U.S. States in the continental U.S. between January 2000 and July 2016. Alongside information about the outages themselves, this dataset also contains the geographical location of the outage, regional climate information, land-use characteristics, electricity consumption patterns, and economic characteristics of the states that were affected.

After looking through the dataset and getting a better understanding of what sort of data it contained, we decided to center our research around the following question:

>**What are the common characteristics of severe power outages, and can those characteristics be used to predict the number of customers affected by a future power outage?**

Our analysis throughout this report will help us gain a better understanding of how different factors can impact the severity and reach of a power outage, which could potentially allow utility providers to better estimate and prepare for the extent of an outage when it does occur.

This dataset originally contained contained 1535 rows and 57 columns. We narrowed it down to 24 relevant columns (shown below) that we consider potential factors that could be correlated with the number of customers affected by a given outage. The column descriptions in the table below are all sourced directly from the [data dictionary](https://www.sciencedirect.com/science/article/pii/S2352340918307182) provided with the dataset.

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
### Data Cleaning
After downloading the raw dataset, our data cleaning process began by trimming off the first few rows of the file which served as a file description as well as removing the first two columns of the dataset: '`variable`' and '`OBS`'. The '`variable`' column did not contain any actual data and '`OBS`' was an index column provided for us which we chose not to use. We also removed a row of the file which denoted the units for each column.

Next, we dropped all columns not listed in the table above which we felt would not be especially relevant to our research question or repeated similar data in a different fashion than another remaining column in the dataset, which we feared might introduce multicollinearity. With the remaining columns, we then converted all known numeric columns into the appropriate `dtypes`, since each column was loaded as `object` by default. 

After this, we combined '`OUTAGE.START.DATE`' and '`OUTAGE.START.TIME`' into one `pd.Timestamp` column, '`OUTAGE.START`'. Similarly, we also combined '`OUTAGE.RESTORATION.DATE`' and '`OUTAGE.RESTORATION.TIME`' into '`OUTAGE.RESTORATION`'. We then dropped the original four columns in order to avoid introducing multicollinearity.

Lastly, we dropped all duplicate rows and were left with a cleaned dataset, the `head` of which is shown below:

| POSTAL.CODE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   TOTAL.PRICE |   TOTAL.SALES |   TOTAL.CUSTOMERS |   PC.REALGSP.REL |   UTIL.REALGSP |   PI.UTIL.OFUSA |   POPULATION |   POPPCT_URBAN |   POPDEN_UC |   POPDEN_RURAL |   PCT_WATER_TOT |   PCT_WATER_INLAND | OUTAGE.START        | OUTAGE.RESTORATION   |
|:--------------|:--------------|:-------------------|----------------:|:-------------------|:-------------------|:------------------------|------------------:|-----------------:|---------------------:|--------------:|--------------:|------------------:|-----------------:|---------------:|----------------:|-------------:|---------------:|------------:|---------------:|----------------:|-------------------:|:--------------------|:---------------------|
| MN            | MRO           | East North Central |            -0.3 | normal             | severe weather     | nan                     |              3060 |              nan |                70000 |          9.28 |   6.56252e+06 |       2.5957e+06  |          1.07738 |           4802 |             2.2 |  5.34812e+06 |          73.27 |      1700.5 |           18.2 |         8.40733 |            5.47874 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
| MN            | MRO           | East North Central |            -0.1 | normal             | intentional attack | vandalism               |                 1 |              nan |                  nan |          9.28 |   5.28423e+06 |       2.64074e+06 |          1.08979 |           5226 |             2.2 |  5.45712e+06 |          73.27 |      1700.5 |           18.2 |         8.40733 |            5.47874 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
| MN            | MRO           | East North Central |            -1.5 | cold               | severe weather     | heavy wind              |              3000 |              nan |                70000 |          8.15 |   5.22212e+06 |       2.5869e+06  |          1.06683 |           4571 |             2.1 |  5.3109e+06  |          73.27 |      1700.5 |           18.2 |         8.40733 |            5.47874 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
| MN            | MRO           | East North Central |            -0.1 | normal             | severe weather     | thunderstorm            |              2550 |              nan |                68200 |          9.19 |   5.78706e+06 |       2.60681e+06 |          1.07148 |           5364 |             2.2 |  5.38044e+06 |          73.27 |      1700.5 |           18.2 |         8.40733 |            5.47874 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
| MN            | MRO           | East North Central |             1.2 | warm               | severe weather     | nan                     |              1740 |              250 |               250000 |         10.43 |   5.97034e+06 |       2.67353e+06 |          1.09203 |           4873 |             2.2 |  5.48959e+06 |          73.27 |      1700.5 |           18.2 |         8.40733 |            5.47874 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |

### Univariate Analysis
<iframe
src="assets/cause_category_distribution.html"
width = '800'
height='600'
frameborder='0'
></iframe>

<iframe
src="assets/customers_affected_distribution.html"
width = '800'
height='600'
frameborder='0'
></iframe>

<iframe
src="assets/demand_loss_distribution.html"
width = '800'
height='600'
frameborder='0'
></iframe>

<iframe
src="assets/outage_duration_distribution.html"
width = '800'
height='600'
frameborder='0'
></iframe>

### Bivariate Analysis
<iframe
src="assets/cause_vs_customers_affected.html"
width = '800'
height='600'
frameborder='0'
></iframe>

<iframe
src="assets/state_customers_affected.html"
width = '80%'
height='800'
frameborder='0'
></iframe>

<div style='display: flex; gap: 20px; flex-wrap: wrap;'>
<iframe
src="assets/anomaly_customers_affected.html"
width = '800'
height='600'
frameborder='0'
></iframe>

<iframe
src="assets/GDP_state_customers_affected.html"
width = '800'
height='600'
frameborder='0'
></iframe>

<iframe
src="assets/util_GSP_customers_affected.html"
width = '800'
height='600'
frameborder='0'
></iframe>

<iframe
src="assets/urban_customers_affected.html"
width = '800'
height='600'
frameborder='0'
></iframe>

<iframe
src="assets/water_customers_affected.html"
width = '800'
height='600'
frameborder='0'
></iframe>
</div>

## Framing a Prediction Problem

## Baseline Model

## Final Model