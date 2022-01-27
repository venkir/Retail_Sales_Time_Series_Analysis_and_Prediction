# Retail_Sales_Time_Series_Analysis_and_Prediction

**Time series analysis, and forecasting using VAR,VARIMAX and SARIMAX**
For Rossmann Retail Sales
By : Venki Ramachandran
Dated : 17-Jan-2022
Rossmann is a European drug distributor which operates over 3,000 drug stores across seven European countries. Since a lot of drugs come with a short shelf life, that is, they do not have a long expiry date, it becomes imperative for Rossmann to accurately forecast sales at their individual stores. Currently, the forecasting is taken care of by the store managers who are tasked with forecasting daily sales for the next six weeks.

As expected, store sales are influenced by many factors, including promotional campaigns, competition, state holidays, seasonality, and locality.

With thousands of individual managers predicting sales based on their unique circumstances and intuitions, the accuracy of the forecasts is quite varied. To overcome this problem, the company has hired you as a data scientist to work on the forecasting problem. As part of your job role, you are tasked with building a forecasting model to forecast the daily sales for the next six weeks. To help you with the same, you have been provided with historical sales data for 1,115 Rossmann stores

Process Followed:
Load the two (2) CSV files - store and train csv files, do some initial analysis, and then merge. Initial Count of records is: 1017209
Filter for the nine(9) Stores: Count reduces to 8110
Remove the rows where Open=0 (Closed on Sunday) and Sales = 0. I went back and forth on this, whether to remove the rows or leave it in. After all, time series analysis should be able to detect that all Sundays, the shops are closed and there are no Sales. I decided to remove these rows as ZERO (0) valuesi in the Sales column was creating a lot of issues. Such as:
MAPE coud not get calculaed for simple time series
Box Cox transformation does not work out of the box. Had to use boxcox1p a special lib
In the end, decided to remove the rows and let the time series handle the trend.
Count of records dropped to 6681
On this set, EDA was performed. The results of the EDA are listed in next section
The stores were then split into nine (9) seperate data frames, one for each
A combined analysis was done on all nine stores as one large time series, but because of non-aligned dates and gaps in the data, the RMSE and MAPE accurayc was very bad. And so this process, was abandoned and individual stores analysis was undertaken
Store 13 and 46 has about 320 rows missing which were added using the date_range method, filled with no.nan values and then interpolated (Sales and Customers column). The rest were filled using ffil() and bfill() methods.

Time Series Analysis - Find Gaps, Impute Values, Smooth using Linear Interpolation + Seasonality.
Auto Regression Models Used for all nine (9) stores:
* VAR
* VARMAX
* SARIMAX

Data Preparation Steps:
Assigned the individual data for each store to a generic var called 'data'.
Find outliers at the 99th percentile and update them to NaN (not remove them).
Since we were supposed to predict for 6 weeks and compare results, I used 36 rows from the end of each data frame and reserved the rest for training. 6 weeks to 42 days and since I had removed Sundays, deleted 6. 42-6 = 36 days
I did not Standardize the sales and number of customers variables before modelling. This is because it did not have a lot of improvement in the accuracy. These two series are cointegrated and all documentation explains that the values are not used to predict, rather the trend is.
For Stores = 13, 46, missing rows were added using the date_range method as too many rows were missing
Linear interpolate was used to Determine the values of Sales and Customers col where they were NaN.
ADF and KPSS tests were done to check and see if the series was stationary, and if not for some box cox and Differencing was used to make them stationary.
Once they were, stationary the the ACF and PACF graphs were drawn to detect the p and q values. These were used in the VAR Vector Autoregression Model.
The endogenous variables were Sales and Customers and the exogenous variables were Promo, DayOfWeek and SchoolHoliday
Most of the store time series proved that Sales and Customers were tightly cointegrated and this was done by Check for cointegration using the Johansen test. The rank came oout to be 1 in most cases and hence concluded that they were cointegrated.
Predict sales for the last 6 weeks and compared with the test data to determine the rmse and mape values for all the nine stores.
EDA Analysis:
For all stores Customers and Sales are linearly related with an upward tread as seen above
No Sales on Sundays (Day == 7) probably because they are closed and then there is a spike on Monday
Promo has a definite effect on Sales all the nine stores have a greater Sales when the Promo was on
No affect on Sales and Customers due to 'Promo2'
Definite Increase in Sales on School Holidays. All nine stores show an increased Sales numbers on School Holiday probably because the stores are open and customers have the day off to shop
No effect on Sales on State Holidays, probably because the store is also Closed on State Holidays
We see that Sales & Customers are ZERO(0) on Sundays probably because they are closed on Sundays. Also, because of that Sales on Monday is highest.
Also, Note that Christmas & New Year Days lead to increase in sales. As these Stores sells drug or medicines, it may be that during these holiday season, they have long days and night parties and need these medicines or maybe it is COLD in Europe and so need warm medicine which might explain the increase in sales.
Time series analysis: Store 13 and Store 46 have a lot of missing rows. We might have to fill them in with empty rows and interpolate. Store 25 also shows a gap. Rest of them are all ok, seem like regular time series. Store 25 has sone missing rows
Almost all stores have significant outliers and for all stores on Sundays Sales was ZERO because they were closed.
Final Results Analysis and Observations:
The p and q values are all over the place. Documenttaion says that q should not be more than 0,1,2, but if you see the ACF and PACF graphs, the p and q values come up to be very high.
I used the AIC table to determine the optimum p value based on the lowers AIC value (denoted by *)
The VAR model performed the least well, although as you increase the p value its performance improved.
VARMAX gave much better results than the VAR model because it used the effect of the exogenous varibale
Finally, SARIMAX performed the BEST because it used the seasonality into its predictions. All the individual time series have NO Trend but display a lot of Seasonality and so it is not surprising that the SARIMAX was the most effective.
Further Work can be done:
Further explorations can be done with the values of p and q to get better results for VAR and VARMAX models.
Read further on the affect of leaving ZERO (0) values in the predictor columns and how to handle them. Removing them should not be the answer as these are valid entries. Zero Sales on days when stores are closed is valid value and they should be used in the analysis.
Study further on the how to interpret the ACF and PACF graphs and what is the optimum method to detect the perfect p and q values for your models.
Specific Questions:
Is the sales data non-stationary? If so, how do you find it and correct it?
While analyzing the nine(9) stores's data, some of the time series was stationary and for some not stationary, base on the ADF and KPSS tests. In some cases they gave contradictory answers. I used Box Cox and Differencing to make them stationaty and proceed.
Is the data cointegrated? Which variables are cointegrated and how do you find them?
If you look at the two specific columns - Sales and Customers, Yes they are cointegrated. Based on the rank provided by the output of Johansen test, and checking to see the value = 1, we concluded that the two series were cointegrated.
What is the impact of the number of customers on sales?
They are linearly corelated. As Customers increase so does the sales.
What is the impact of promo and promo2 variables on sales? How do you measure it?
Only Promo has had visible impact on the Sales. Promo2 had no affect on the Sales at these nine(9) stores
Forecast sales for the next 6 weeks? Report the accuracy of the model using MAPE.
The last 36 rows (6 weeks) of data were used for prediction and then comparedf with the test data to calculate the RMSE and MAPE errors.
