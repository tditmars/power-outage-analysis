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
| `POSTAL.CODE` | Represents the postal code of the U.S. states|
| `NERC.REGION` | The North American Electric Reliability Corporation (NERC) regions involved in the outage event|
| `CLIMATE.REGION` | U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.)|
| `ANOMALY.LEVEL` | This represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season. It is estimated as a 3-month running mean of ERSST.v4 SST anomalies in the Niño 3.4 region (5°N to 5°S, 120–170°W) |
| `CLIMATE.CATEGORY` | This represents the climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI) |
| `CAUSE.CATEGORY` | Categories of all the events causing the major power outages |
| `CAUSE.CATEGORY.DETAIL` | Detailed description of the event categories causing the major power outages |
| `OUTAGE.DURATION` |	Duration of outage events (in minutes) |
| `DEMAND.LOSS.MW` | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
| `CUSTOMERS.AFFECTED`| Number of customers affected by the power outage event |
| `TOTAL.PRICE` |	Average monthly electricity price in the U.S. state (cents/kilowatt-hour) |
| `TOTAL.SALES` | Total electricity consumption in the U.S. state (megawatt-hour)|
| `TOTAL.CUSTOMERS` |	Annual number of total customers served in the U.S. state |
| `PC.REALGSP.REL` | Relative per capita real GSP as compared to the total per capita real GDP of the U.S. (expressed as fraction of per capita State real GDP & per capita US real GDP) |
| `UTIL.REALGSP` | Real GSP contributed by Utility industry (measured in 2009 chained U.S. dollars) |
| `PI.UTIL.OFUSA` | State utility sector׳s income (earnings) as a percentage of the total earnings of the U.S. utility sector׳s income (in %) |
| `POPULATION`| Population in the U.S. state in a year |
| `POPPCT_URBAN` | Percentage of the total population of the U.S. state represented by the urban population (in %) |
| `POPDEN_UC` | Population density of the urban clusters (persons per square mile) |
| `POPDEN_RURAL` | Population density of the rural areas (persons per square mile) |
| `PCT_WATER_TOT` | Percentage of water area in the U.S. state as compared to the overall water area in the continental U.S. (in %) |
| `PCT_WATER_INLAND` | Percentage of inland water area in the U.S. state as compared to the overall inland water area in the continental U.S. (in %) |
| `OUTAGE.START` | `pd.Timestamp` that indicates the day and time when the outage event started (as reported by the corresponding Utility in the region) |
| `OUTAGE.RESTORATION` | `pd.Timestamp` that indicates the day and time when power was restored to all the customers (as reported by the corresponding Utility in the region) |

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

We began our exploratory data analysis by examining the distributions of various single columns through visualizations made with `plotly`.

<iframe
src="assets/cause_distribution.html"
width = '800'
height='600'
frameborder='0'
></iframe>

We first created the above histogram of the counts of outages in the dataset labeled with various cause categories. The key takeaway from this visual was that severe weather and intentional attacks were by far the most common causes of the power outages documented in our dataset.

Once we had gotten a sense of the most frequent causes of the outages in our dataset, we wanted to get a sense of the relevant scale and severity of the outages. We identified three potential response variables of interest- '`CUSTOMERS.AFFECTED`', '`DEMAND.LOSS.MW`', and '`OUTAGE.DURATION`'- which we thought characterized the impact of an outage.

<iframe
src="assets/customers_affected_distribution.html"
width = '800'
height='600'
frameborder='0'
></iframe>

The histogram above shows the binned distribution of outages by the number of customers affected in each outage. The median value for '`CUSTOMERS.AFFECTED`' was around 70,000, and the 75th percentile was around 150,000.

<iframe
src="assets/demand_loss_distribution.html"
width = '800'
height='600'
frameborder='0'
></iframe>

Similarly, the above histogram shows the binned distribution of outages by their reported demand loss (in Megawatts). The median value for '`DEMAND.LOSS.MW`' was 168.00 and the 75th percentile was 399.75.

<iframe
src="assets/outage_duration_distribution.html"
width = '800'
height='600'
frameborder='0'
style='margin-left: 10px;'
></iframe>

Lastly, the above histogram shows the binned distribution of outages by their durations (in minutes). The median outage duration was 691 minutes (just over 11.5 hours), and the 75th percentile was 2880 minutes (2 days).

Each of these visualizations helped us to contextualize the data we were provided by gaining an understanding of the distribution of outage severities. 

### Bivariate Analysis

We then conducted a variety of bivariate analyses in an attempt to identify possible associations between features in our dataset, especially with our primary response variable '`CUSTOMERS.AFFECTED`' to help us eventually motivate the features on which our predictive models would be trained.

<iframe
    src="assets/new_cause_customers_affected.html"
    width="100%"
    height="600"
    frameborder="0"
></iframe>

The above stacked box plot shows the distributions of the number of customers affected among outages with various causes. Each cause category had a slightly different distribution of customers affected, with severe weather outages having the highest median number of customers affected at roughly 110,433.

<iframe
src="assets/state_customers_affected.html"
width = '800'
height='600'
frameborder='0'
></iframe>

We then wanted to see if the geographical location of an outage had any impact on the number of customers affected, so we created the above choropleth chart which displays the mean '`CUSTOMERS.AFFECTED`' of all outages in the dataset for each U.S. state. It appeared that Florida, South Carolina, Texas, Illinois, and California had particularly high reach (number of customers affected) on average.

<div style="max-width: 1200px; margin: auto;">
<div style='display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px;'>
<iframe
src="assets/anomaly_customers_affected.html"
width = '100%'
height='400'
frameborder='0'
></iframe>

<iframe
src="assets/GDP_state_customers_affected.html"
width = '100%'
height='400'
frameborder='0'
></iframe>

<iframe
src="assets/util_GSP_customers_affected.html"
width = '100%'
height='400'
frameborder='0'
></iframe>

<iframe
src="assets/urban_customers_affected.html"
width = '100%'
height='400'
frameborder='0'
></iframe>

<iframe
src="assets/water_customers_affected.html"
width = '100%'
height='400'
frameborder='0'
></iframe>
</div>
</div>

We created numerous scatter plots to try and identify potential linear relationships between various numerical features (namely the '`ANOMALY.LEVEL`', '`PC.REALGSP.REL`', '`UTIL.REALGSP`', '`POPPCT_URBAN`', and '`PCT_WATER_TOT`') and the number of customers affected. Although '`POPPCT_URBAN`' and '`PCT_WATER_TOT`' showed slight signs of a correlation (positive and negative, respectively) with '`CUSTOMERS.AFFECTED`', the majority of these plots revealed heavily skewed and clustered data which did not show strong signs of linear relationships between these features and our response variable.

### Interesting Aggregates

We were interested in further examining the trends in overall outage severity (which we characterized through the three variables of '`CUSTOMERS.AFFECTED`', '`OUTAGE.DURATION`', and '`DEMAND.LOSS.MW`') across different states, so we created the following grouped table which reports the mean of each of those three variables for each U.S. state in the dataset, sorted in descending order by mean '`CUSTOMERS.AFFECTED`'. The top 10 rows are shown below:

| POSTAL.CODE   |   CUSTOMERS.AFFECTED |   OUTAGE.DURATION |   DEMAND.LOSS.MW |
|:--------------|---------------------:|------------------:|-----------------:|
| FL            |               303149 |          4084.19  |          846.921 |
| SC            |               251913 |          3135     |         1699.71  |
| TX            |               223232 |          2560.56  |          552.083 |
| IL            |               207027 |          1602.45  |          214.222 |
| CA            |               201366 |          1674.56  |          668.522 |
| DC            |               194709 |          4303.6   |         1280     |
| NY            |               190676 |          6034.96  |         1283.15  |
| WV            |               179794 |          6979     |          362     |
| PA            |               168537 |          3811.7   |          225.263 |
| NM            |               166667 |           140.375 |          346.667 |

The results of the first group table largely mirrored what was displayed earlier in the choropleth chart, so we wanted to dive deeper by grouping on both the U.S. state and cause category together and finding the average number of customers affected for each combination, as shown by the slice of the pivot table below. We were surprised to find that zero of the top 5 combinations came from states which were in the top 5 by overall average customers affected. Furthermore, it was interesting to see that 6 of the top 10 combinations were related to system operability disruptions in particular, which could suggest that utility companies in those states are not well equipped to handle and contain the impact of these sorts of disruptions. 

| POSTAL.CODE   | CAUSE.CATEGORY                |   AVG.CUSTOMERS.AFFECTED |
|:--------------|:------------------------------|-------------------------:|
| NY            | system operability disruption |                1.081e+06 |
| MI            | system operability disruption |           759738         |
| OH            | system operability disruption |           613000         |
| VA            | system operability disruption |           600000         |
| NM            | system operability disruption |           500000         |
| FL            | severe weather                |           444907         |
| CA            | severe weather                |           361041         |
| TX            | system operability disruption |           289696         |
| TX            | severe weather                |           258094         |
| SC            | severe weather                |           251913         |

### Imputation

The table below shows the percentage of null values in each of the 24 columns we kept in our cleaned dataset. Since the only columns with proportions of missing data that were non-negligible were columns that would either be unavailable at the time of prediction (and hence, better suited as response variables) or we had no intention of using to train our machine learning models, we did not feel it was necessary to impute any missing values.

| Column Name           |   Percent of Data Missing |
|:----------------------|--------------------------:|
| `POSTAL.CODE `          |                  0        |
| `NERC.REGION`           |                  0        |
| `CLIMATE.REGION`        |                  0.393185 |
| `ANOMALY.LEVEL`         |                  0.589777 |
| `CLIMATE.CATEGORY`      |                  0.589777 |
| `CAUSE.CATEGORY`        |                  0        |
| `CAUSE.CATEGORY.DETAIL` |                 30.6029   |
| `OUTAGE.DURATION`       |                  3.80079  |
| `DEMAND.LOSS.MW`        |                 45.8716   |
| `CUSTOMERS.AFFECTED`    |                 28.637    |
| `TOTAL.PRICE`           |                  1.44168  |
| `TOTAL.SALES`          |                  1.44168  |
| `TOTAL.CUSTOMERS`       |                  0        |
| `PC.REALGSP.REL`        |                  0        |
| `UTIL.REALGSP`          |                  0        |
| `PI.UTIL.OFUSA`         |                  0        |
| `POPULATION`            |                  0        |
| `POPPCT_URBAN`          |                  0        |
| `POPDEN_UC`             |                  0.655308 |
| `POPDEN_RURAL`          |                  0.655308 |
| `PCT_WATER_TOT`         |                  0        |
| `PCT_WATER_INLAND`      |                  0        |
| `OUTAGE.START`          |                  0.589777 |
| `OUTAGE.RESTORATION`    |                  3.80079  |

## Framing a Prediction Problem

### Prediction Problem

The goal of our analysis is to predict the number of customers affected ('`CUSTOMERS.AFFECTED`') by a given power outage. This is a **regression** problem, since the response variable we are trying to predict– the number of customers affected– is a continuous numerical variable.

### Response Variable

**Response Variable:** `CUSTOMERS.AFFECTED` – The number of customers affected by the power outage event

**Units:** People

The number of customers affected by a power outage event can be used as a measure of the scale or reach of the outage, which may also provide insight into the degree of economic impact caused by the event. Being able to accurately predict this value might help utility providers to allocate resources towards service recovery or further provide realistic estimates to local communities regarding how many households and/or businesses should anticipate being affected by a given outage.

### Feature Justification

When selecting the features that we would use to train both our baseline and final predictive models, we ensured that the features we selected would all be known at the **"time of prediction"**. Specifically, this meant that we could not use features such as '`CUSTOMERS.AFFECTED`', '`OUTAGE.DURATION`', or '`DEMAND.LOSS.MW`' which all represent some sort of numerical outcome that could only be measured after the outage itself had concluded, as this sort of information would not be readily available when predicting the impact of an ongoing or future outage. 

Collectively, our models were trained on the following features, each of which can be documented/measured in their entirety prior to an outage actually occuring:
- `ANOMALY.LEVEL`
- `CAUSE.CATEGORY`
- `PCT_WATER_TOT`
- `POPPCT_URBAN`
- `POSTAL.CODE`
- `TOTAL.CUSTOMERS`

### Model Evaluation Metric

**Evaluation Metric:** Mean Absolute Error (MAE)

During our EDA, we noticed the presence of many outlier outages which affected millions of customers, despite the 75th percentile of our response variable '`CUSTOMERS.AFFECTED`' being around 150,000. As such, we felt that the more common choice of Mean Squared Error (MSE) may penalize these outliers too heavily, so we decided to evaluate our models comparatively using MAE since it is more robust to outliers. Additionally, MAE is slightly easier to interpret and contextualize since the average error values it reports are in the same units (number of people) as our response variable.

## Baseline Model

## Final Model