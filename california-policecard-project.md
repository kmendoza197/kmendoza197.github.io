```python
# modules for research report
!pip install datascience
from datascience import *
import numpy as np
import random
%matplotlib inline
import matplotlib.pyplot as plots
plots.style.use('fivethirtyeight')
```

# California Police Scorecard Data

This dataset combines data from 8 Can't Wait and Campaign Zero on Californian police departments. A brief descriptive summary of the dataset is provided below.

## Summary

The California Police Scorecard features data from 2016-2018 regarding police department performance and policies in all 58 California counties (with information about multiple police departments per county). We split the data into three datasets: an arrests dataset, an accountability dataset, and a demographic dataset.

The arrests data (157 rows, 26 columns) quantifies police shootings, uses of force, arrests (with information about race) and homicides, budget, and more. The accountability data (157 rows, 17 columns) marks what policies are in place (1: yes, 0: no) for police departments to hold their police officers accountable. The demographic data (157 rows, 16 columns) provides race and economic information about the citizens in a police district to contextualize police behavior in their respective communities.

The dataset has also recently expanded to include information about Sheriffs’ Departments, the operator of county jails, which has the same metrics used to evaluate police departments.

# Research Report

## Introduction

In this research report, we aim to explore the relationship between the number of police shootings from 2016-18 across low and high-income categories within various both police and sheriff agencies within California. We will begin using data collected by Campaign Zero whose mission is to end police brutality in America and report on police violence, police accountability, and approaches to policing. Their data was collected based on the police and sheriff departments within California. From Campaign Zero’s data, we will concentrate on total police shootings from 2016-2018 from the police-arrests data table and the median incomes of each police and sheriff district from the police-demographic data table. The police-arrests and police-demographic data tables consisted of several different columns, therefore, we only selected the Agency name column, where each row corresponds to different police and sheriff departments in California, and the police shootings from 2016 to 2018 columns, where each row contains the number of police shootings, from the police-arrests data table. As for the police-demographic table, we only selected the Agency name, where each row corresponds to different police and sheriff departments in California, and the median income, where each row recorded the number of dollars earned in each area of various police and sheriff departments. We chose these columns as these variables, police shootings from 2016 to 2018, median incomes, and police and sheriff departments, assist in examining the variation in police shootings across high and low-income neighborhoods.

## Hypothesis Testing and Prediction Questions

To analyze the policing situation in low-income and high-income neighborhoods, we will combine the demographics' data of each district with the police arrests data in California. We are interested in seeing how violence-related instances, specifically police shootings, differ in low and high-income districts. Therefore, we will conduct an A/B test with a test statistic of the difference between average police shootings in high and low-income neighborhoods. This will allow us to examine if there is a significant difference between police shootings in high and low-income neighborhoods. As for our hypothesis testing, we will use a p-value cut-off of 0.05, meaning that if our p-value is less than 0.05, we will reject the null hypothesis. For our experiment, the null hypothesis is that in the population, the distributions of police shootings in low-income neighborhoods and high-income neighborhoods are the same. They are different in the sample just due to chance. On the other hand, the alternative hypothesis is that in the population, the number of police shootings in low-income areas is greater, on average, than the number in high-income neighborhoods.

For the predictive question, we want to determine if we can use median income to predict the number of police shootings in the area. To test this question, we will employ a linear regression model to accomplish this task, and determine whether there is a linear association between police shootings in high-income and low-income neighborhoods.


