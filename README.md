# Analyzing Power Outages

## Project for EECS 398 at University of Michigan  
**By Jackson Gertner**

---

## Introduction

In this project, I examined a dataset of major power outages in the U.S. from January 2000 to July 2016. These major outages are defined by the Department of Energy to have impacted at least 50,000 customers, or caused an unplanned energy demand loss of at least 300 MegaWatts. This dataset was accessed from Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure, available at [Purdue LASCI](https://engineering.purdue.edu/LASCI/research-data/outages).

The dataset includes information about the major outages, as well as geographical, climatic, land-use characteristics, electricity consumption patterns, and economic characteristics of the states affected by the outages.

I aim to answer the question: **What are the characteristics of major power outages with higher severity and how can we use these to predict the Duration of severe Outage?**  
By building a predictive model, I hope to help energy companies enact preventative measures.

---

## Data Overview

The original raw DataFrame contains 1,534 rows (corresponding to 1,534 outages) and 57 columns. I focus on the following key columns for my analysis:

| Column             | Description                                                                                      |
|--------------------|--------------------------------------------------------------------------------------------------|
| `YEAR`             | Indicates the year when the outage event occurred.                                              |
| `MONTH`            | Indicates the month when the outage event occurred.                                             |
| `U.S._STATE`       | Represents all the states in the continental U.S.                                               |
| `NERC.REGION`      | The North American Electric Reliability Corporation (NERC) regions involved in the outage event.|
| `CLIMATE.REGION`   | U.S. Climate regions as specified by National Centers for Environmental Information (9 regions). |
| `ANOMALY.LEVEL`    | The oceanic El Niño/La Niña (ONI) index, referring to cold and warm episodes by season.         |
| `CAUSE.CATEGORY`   | Categories of all the events causing the major power outages.                                   |
| `OUTAGE.DURATION`  | Duration of outage events (in minutes).                                                         |
| `TOTAL.PRICE`      | Average monthly electricity price in the U.S. state (cents/kilowatt-hour).                      |
| `TOTAL.SALES`      | Total electricity consumption in the U.S. state (megawatt-hour).                                |
| `TOTAL.CUSTOMERS`  | Annual number of total customers served in the U.S. state.                                      |
| `POPPCT_URBAN`     | Percentage of the total population of the U.S. state represented by the urban population (in %). |
| `POPDEN_URBAN`     | Population density of the urban areas (persons per square mile).                                |
| `AREAPCT_URBAN`    | Percentage of the land area of the U.S. state represented by the land area of the urban areas (in %). |
| `UTIL.CONTRI`      | Utility industry’s contribution to the total real GDP of the state (in %).                      |
| `PC.REALGSP.STATE` | Per capita real gross state product (GSP) in the U.S. state (measured in 2009 chained U.S. dollars). |

## Data Cleaning and EDA

### Cleaning:
Steps I took in cleaning my data for analysis:

- **Selected Relevant Columns**: Filtered to only focus on columns listed above.
- **Replaced 0 Values**: Replaced `0` values in our target columns `OUTAGE.DURATION`,  with `NaN`, as `0` likely represents missing data.
- **Deleted Duplicates**: There were 63 duplicates in our cleaned Data Frame with limited columns, and even though they are not duplicates in our larger data set, we will drop them here for our analysis.

|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CAUSE.CATEGORY     |   OUTAGE.DURATION |   TOTAL.PRICE |   TOTAL.SALES |   TOTAL.CUSTOMERS |   POPPCT_URBAN |   POPDEN_URBAN |   AREAPCT_URBAN |   UTIL.CONTRI |   PC.REALGSP.STATE |
|-------:|--------:|:-------------|:--------------|:-------------------|----------------:|:-------------------|------------------:|--------------:|--------------:|------------------:|---------------:|---------------:|----------------:|--------------:|-------------------:|
|   2011 |       7 | Minnesota    | MRO           | East North Central |            -0.3 | severe weather     |              3060 |          9.28 |   6.56252e+06 |           2595696 |          73.27 |           2279 |            2.14 |       1.75139 |              51268 |
|   2014 |       5 | Minnesota    | MRO           | East North Central |            -0.1 | intentional attack |                 1 |          9.28 |   5.28423e+06 |           2640737 |          73.27 |           2279 |            2.14 |       1.79    |              53499 |
|   2010 |      10 | Minnesota    | MRO           | East North Central |            -1.5 | severe weather     |              3000 |          8.15 |   5.22212e+06 |           2586905 |          73.27 |           2279 |            2.14 |       1.70627 |              50447 |
|   2012 |       6 | Minnesota    | MRO           | East North Central |            -0.1 | severe weather     |              2550 |          9.19 |   5.78706e+06 |           2606813 |          73.27 |           2279 |            2.14 |       1.93209 |              51598 |
|   2015 |       7 | Minnesota    | MRO           | East North Central |             1.2 | severe weather     |              1740 |         10.43 |   5.97034e+06 |           2673531 |          73.27 |           2279 |            2.14 |       1.6687  |              54431 

### EDA: 

#### Univariate Analysis:

In my first Univariate Analysis I wanted to look at the distribution of our target variable `OUTAGE.DURATION` just to see the ranges of the values we are predicting. Initially I took the distribution of the entire variable but the outliers were so great that it was hard to see the distribution of the entire variable. Therefore for this plot, I filtered to only include `OUTAGE.DURATION`'s in the IQR of the variable:

<iframe
  src="assets/duration_distribution_univariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Bivariate Analysis: 

I wanted to see where what kinds of outages were taking places in each `CLIMATE.REGION` in order to inspect whether they may be trends or prominence of certain causes in certain regions: 

<iframe
  src="assets/climate_region_cause_category_multivariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Aggregated Tables: 

Given this theme of looking at outage causes in different climate regions, mainly in hopes of finding some sort of geographical that makes practical sense, I grouped by `CLIMATE.REGION` and `CAUSE.CATEGORY` to look at the mean `OUTAGE.DURATION` in each of these pairings: 

| Climate Region   | Cause Category        |   Average Outage Duration (minutes) |   Count of Cases |
|:-----------------|:----------------------|------------------------------------:|-----------------:|
| Central          | equipment failure     |                             322     |                7 |
| Central          | fuel supply emergency |                           10035.2   |                4 |
| Central          | intentional attack    |                             346.059 |               38 |
| Central          | islanding             |                             125.333 |                3 |
| Central          | public appeal         |                            1410     |                2 |

#### Imputation: 

We have a very insignificant amount of missing values in our data. The column with the largest missing value count is `OUTAGE.DURATION` with 58 null values and 78 0's (Basically Null values as far as we are concerned). Because this only amounts to under 9% of our total dataset, I took the liberty of just dropping these null values for simplicity. Therefore, we did impute any values in this dataset.

## My Prediction Problem

My goal is to predict the duration of an outage event, `OUTAGE.DURATION` which is measured in minutes. This is a regression problem, as the `OUTAGE.DURATION` is a continuous variable. 

I chose this problem mainly because outage duration is crucial for utility companies to allocate resources effectively, improve restoration times, and minimize customer dissatisfaction during outages. A model that predicts outage duration could help operators better prepare for prolonged outages caused by severe weather, technical failures, or other factors.

The metric I am going to use to test my model is mean absolute error (MAE) instead of the standard, mean squared error. The main reason for this is that in my EDA, I found `OUTAGE.DURATION` to have some extremely large outliers, and MAE will avoid emphasizing these large unlike a value like MSE or RMSE.

In order to create this model, we need to only focus on variables that are going to be available to us at the time of prediction. Fortunately, we filtered out from our Data Frame columns that would be realized later on, so all variables in our Cleaned DataFrame are fair game to be used in our model.

## Baseline Model

For my Baseline model, I used a pretty simple linear regression model to predict the `OUTAGE.DURATION`. I used 4 different variables one of which is Categorical(`CAUSE.CATEGORY`) and the other 3 Numerical(`ANOMALY.LEVEL`,`MONTH`,`YEAR`).

I chose this mix of variables as I believed that in a concise group, these 4 could have a strong and wholistic predictive outcome. They cover an aspect of climate as they both include time of year(`MONTH`) and `ANOMALY.LEVEL`. On top of this they have the general year which in our EDA we say to have an effect on the amount of outages, as well as the `CAUSE.CATEGORY` to cover what type of outage this was. 

I encoded `CAUSE.CATEGORY` using a one hot encoder. As for our numerical columns, I used a Standard scaler transformation, just in case we decided later down the line to inspect our coefficients for any sort of reason.

I used an 80-20 train-test split in order to evaluate the models performance and ultimately used Mean Absolute Error (MAE) to understand its performance for reasons stated above. MAE is straightforward as it tells me how many minutes off my predictions are on average. 

For our initial model, which I did not expect to perform very will, we saw a MAE of 2522.05 minutes, which seemed very high. We did see a lot of variance in our initial distribution plot of `OUTAGE.DURATION`, and we can therefore justify having such a high error, but we will do our best to improve this.


## Final Model

For my final model, I aimed to improve on the baseline by adding more features and conduction a Quantile Transformation with the numerical columns in the dataset. I also needed to impute some values as there were 12 missing values in the `TOTAL.SALES` and `TOTAL.PRICE` columns that were interfering with the model. 

For this model I used all the columns in our cleaned dataset, scaling the numerical columns with the Quantile Transformer and all the categorical ones with One Hot Encoder again. 

I experimented with a few different regression models, including Lasso, but was unable to lower the MAE until I arrived at the Random Forest Regressor. I then used GridSearchCV to tune the hyper parameters n_estimators, max_depth and min_samples_split. 

This time the model performed better, lowering the Mean Absolute Error almost 300 minutes, to 2240.52 minutes. This is definitely a result of using a better regressor, as well as the use of Grid Search CV.
