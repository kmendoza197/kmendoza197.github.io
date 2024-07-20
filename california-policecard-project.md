# California Police Dataset & Hypothesis Testing Project

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

### Exploratory Data Analysis

First, we joined the data in arrests-info and demographic-info to create the shootings_income_level table. We then added all the police shootings columns from 2016 to 2018 to get the total police shootings. We also created a function to categorize the median incomes into High(>$76000), Medium($76000-$52000), and Low(<$52000). The purpose of this table is to compare the number of shootings with median income and to put the police districs data into three income categories rather than 157 distinct median incomes so as to make the statistical analysis easier.

```python
# Use this cell to join two datasets
new_tbl= arrests.join("Agency Name",demographic, "Agency Name")
total_shootings_tbl= new_tbl.with_column("Total Police Shootings 2016-2018", new_tbl.column("2016 Police Shootings")+new_tbl.column("2017 Police Shootings")+ new_tbl.column("2018 Police Shootings")).select("Agency Name", "Total Police Shootings 2016-2018", "Median Income")
def income_level(income):
    """This function seperates the Median incomes
    into three categories High, Medium, and Low"""
    #criteria for Low income
    if income < 52000:
        return "Low"
    #Criteria for High income
    elif income > 76000:
        return "High"
    #Criteria for Medium income
    else:
        return "Medium"
#adds income categories to total_shootings_tbl
shootings_income_level= total_shootings_tbl.with_column("Income category",total_shootings_tbl.apply(income_level,"Median Income"))

average_police_shootings = shootings_income_level.where("Income category", are.not_containing('Medium')).group("Income category", np.mean).select('Income category','Total Police Shootings 2016-2018 mean')
```

We then grouped the previous table based on the income categories and chose to exlude the Medium income category. This is because we will perform an A/B test to answer our hypothesis question, in which we can only perfrom using two income categories. Thefore, to perform this test we must verify that there is a similar average number of police shootings within Low and High income categories. Otherwise, if the results are similar our testing procedure might not be valid. Thus, we generate a group table of summarizing the total police shootings from 2016 to 2018 and income categories of High and Low income categories. The resulting table's values indicate that there a roughly more average shootings from 2016 to 2018 in the Low income category than the total shootings within the High income category. However, we cannot state for certain that the average Police shootings from 2016 to 2018 differ significantly for high and low income categories.

#### Quantitative Plot

```python
income_in_thousands=shootings_income_level.with_column("Median Income (in thousands $)",(shootings_income_level.column("Median Income")/1000)).select("Median Income (in thousands $)","Total Police Shootings 2016-2018")
income_in_thousands.scatter("Median Income (in thousands $)","Total Police Shootings 2016-2018")
plots.title('Relationship between Police Total Shootings \n from 2016 to 2018 and \n Median Income(in thousands $)');
```
<img width="635" alt="Screenshot 2024-07-19 at 8 14 35 PM" src="https://github.com/user-attachments/assets/b63d1c7a-620d-4cf1-a235-ab64c44e9fe4">

In this quantitative scatter plot, we wanted to explore the data related to our prediction problem. We therefore created a scatter plot to illustrate the relationship between the total police shootings from 2016 to 2018 and the median incomes in units of thousands of dollars of the corresponding areas where the shootings took place. Based on this visualization, there doesn't appear to be a strong linear association between the explanatory variable of Median Income (in thousands $) and the predictor of total shootings from 2016 to 2018. Therefore, our linear model is unlikely to perform well.

#### Qualitative Plot

```python
income_category_count = shootings_income_level.group("Income category").select('Income category','count')
income_category_count.barh('Income category')
plots.title("Count of Police Districs Across \n High, Medium, and Low Income Categories");
```
<img width="694" alt="Screenshot 2024-07-19 at 8 16 04 PM" src="https://github.com/user-attachments/assets/52f36057-4b5c-4643-93d8-de615f3c5bc8">

Within this qualtitative bar plot, we wanted to make sure number of police districts were equally distributed across the three income categories of High, Medium, and Low. Based on the bar plot we saw that medium has the highest count and that Low and High vary minimaly (by around 11 samples of police districs) compared to High and Medium (by around 25 samples of police districs) and Low and Medium (by around 13 samples of police districs). Using the information presented in this bar graph we concluded that comparing the High and Low income categories would be best for the hypothesis testing since they have the closest number of samples of police districs. Therefore, the small sample size differnces would not effect the standard deviations and averages to much if we only compare the Low and High rather than the Medium and High income catagories for the A/B testing.

## Hypothesis Testing

Our goal is to examine policing patterns in low-income and high-income neighborhoods in California. We will merge demographic information from each district with data on police arrests to identify any differences in violent incidents, specifically police shootings. To test whether there is a significant variation in police shootings between high and low-income areas, we will carry out an A/B test using a test statistic based on the average number of police shootings in each neighborhood. This analysis will provide us with insights into the prevalence of police shootings in low and high-income neighborhoods. As for the cut-off for a significance level, we will use a p-value cut-off of 0.05, meaning that if our p-value is less than 0.05, we will reject the null hypothesis. For our experiment, the null hypothesis is that in the population, the distributions of police shootings in low-income neighborhoods and high-income neighborhoods are the same. They are different in the sample just due to chance. On the other hand, the alternative hypothesis is that in the population, the number of police shootings in low-income areas is greater, on average, than the number in high-income neighborhoods.

```python
random.seed(1231)
# set the random seed so that results are reproducible
def test_stat(tbl, label_col, val_col):
    """This function defines our test statistic, which is the differnce between the average 
    police shootings from 2016 to 2018 of the Low income category with the High Income category"""
    
    #tbl without medium
    new_tbl=tbl.where("Income category", are.not_containing('Medium'))
    #Groups table by categories and gets the average of each catgory
    average_per_category = new_tbl.group(label_col,np.mean)
    
    #Gets difference between average shootings for the Low and High income categories
    test_stat = average_per_category.column(val_col + " mean").item(1)-average_per_category.column(val_col + " mean").item(0)
    
    return test_stat

#Observed test statistic
observed_test_stat= test_stat(shootings_income_level, "Income category", "Total Police Shootings 2016-2018")

random.seed(1231)
def simulate_and_test_statistic(tbl, labels_col, values_col):
    """This function defines our simulated test statistic, which shuffles low and 
    high income category lables. It then gets the differnce between the average 
    police shootings from 2016 to 2018 of the Low income category with the High Income category"""
    
    #tbl without medium
    no_med_tbl=tbl.where("Income category", are.not_containing('Medium'))
    #Shuffles total police shootings assinging random number of shootings per income category
    shuffled = no_med_tbl.with_column('shuffled Income categories', no_med_tbl.sample(with_replacement = False).column(labels_col)).select("shuffled Income categories",'Total Police Shootings 2016-2018')
    
    #Gets the average of each income category
    average_per_category = shuffled.group('shuffled Income categories', np.mean)
    
    #Gets difference between average shootings for the Low and High income categories
    simulated_test_stat = average_per_category.column(values_col + ' mean').item(1)-average_per_category.column(values_col + ' mean').item(0)
    
    return simulated_test_stat

random.seed(1231)
#Perform A/B testing by simulating the test statistic's distribution under the null
simulated_differences = make_array()
repititons = 1500
for i in np.arange(repititons):
    simulated_differences = np.append(simulated_differences, simulate_and_test_statistic(shootings_income_level, "Income category", "Total Police Shootings 2016-2018"))                    

#visulization of 1500 simulated statistics
Table().with_column('Difference Between Low and High', simulated_differences).hist()
plots.title('Distribution Difference of Mean total Police Shootings \n for Low and High Income Categories');
```
<img width="769" alt="Screenshot 2024-07-19 at 8 18 21 PM" src="https://github.com/user-attachments/assets/66a1bf15-f6d2-4cb8-b5a1-18ac1527029a">

```python
#Calculates p-value 
p_value = np.count_nonzero(simulated_differences >= observed_test_stat) / repititons
p_value
```
Through the A/B testing we found that the observed test statistic of around 3.44, which based on the 1500 simulated differences is statistically significantly differnt because we got a p-value of less than 0.05. Therefore, we can reject our null hypothesis and state with 95% confidence that there is a statisitcally signifantly larger amount of average police shootings within the Low income category compared with the high income category.

## Prediction

To further analyze our data, we seek to see if there is a linear relationship between the median income of a neighborhood and the total number of shootings in the area. To do this, we will conduct a linear regression by plotting the data in a scatter plot and observing its best-fit line. We will also calculate the slope and intercept of such line. To determine if the best predictive model for the data is a linear regression model, we will calculate the correlation coefficient. By examining the plots in the exploratory data analysis section, we do not expect for there to be a strong correlation coefficient (-1 or 1) because the scatter plot doesn’t indicate a linear relationship between the number of shootings and income. However, we do expect for there to be a negative correlation.

```python
#set the random seed so that results are reproducible
random.seed(1231)
#Scatter plot of Total police Shootings 2016-18 and median income (in thousands $) with line of best fit
income_in_thousands.scatter(0,1,fit_line=True)
```
<img width="519" alt="Screenshot 2024-07-19 at 8 20 56 PM" src="https://github.com/user-attachments/assets/d551d348-d1c5-4423-a57f-2281d0d824be">

```python
#Converts data into standard units
def standard_units(data):
    """This function converts the data into Standard units"""
    return (data - np.mean(data))/np.std(data)
#Finds R value between two varaibles
def correlation(tbl, x_col, y_col):
    """This function finds the R value between two variables"""
    return np.mean(standard_units(tbl.column(x_col)) * standard_units(tbl.column(y_col)))
#R value between Median Income (in thousands $) and Total Police Shootings 2016-2018
correlation(income_in_thousands, "Median Income (in thousands $)", "Total Police Shootings 2016-2018")

#Calculates slope and intercept
def best_fit_line(tbl,x_col, y_col):
    """This function caculates and slope and intercept of 
    line of best fit and then puts them into an array"""
    r = correlation(tbl,x_col, y_col)
    slope = r * (np.std(tbl.column(y_col))/ np.std(tbl.column(x_col)))
    intercept = np.mean(tbl.column(y_col)) - (slope * np.mean(tbl.column(x_col)))
    return make_array(slope,intercept)

#slope and intercept of line of best fit
best_fit_line = best_fit_line(income_in_thousands,"Median Income (in thousands $)", "Total Police Shootings 2016-2018") 
print('slope:', best_fit_line.item(0), '; intercept:',best_fit_line.item(1))
```
Based on our results, the data does not fit a linear model since the r value is close to 0. However, as expected there is a negative correlation between the variables (r=-0.118). This means that there is no linear association between median income and police shootings in California, thus we are not able to use this linear model to predict the number of police shootings based on the median income of a respective police jurisdiction area. Potential changes that could be made to the data would be to get rid of any outlier that may skew the data. To get a working linear model, we could also take into consideration other variables that may affect the number of police shootings such as what police departments require to exhaust all other means before shooting.

## Conclusion
Overall, we wanted to see if there was a statisticaly signifantly greater average police shootings within police districs classified as Low income compared to the High income category. We tested this by using A/B testing in which we were able to reject our null hypothsis as we receieved a p-value that was less than 0.05. We therefore were able to conclude with 95% confidence that there is a statisitcally signifantly larger amount of average police shootings within the Low income category compared with the high income category. In addition, we also wanted to see if there was a linear correlation between median income and the number of shootings, such that as median income increased the number of ploice shootings decreased. Unfortunately, we found that the leaner model did not work very well and that there was a very weak negative correlation of aroud -0.1177. Based on our observations of the data set we also found that not all the there was unequal amount of police district samples per income category, which could have resulted in errors throughout our analysis. Therefore, for future projects we would like to observe a new data set with an equal number of police district samples for each income category to reduce any fluctations caused by the enequal sample sizes.
