# Snow ForeCasting

In order to forecast if it will be snowing tomorrow 12 years ago (date in 2010) I did the following steps:

1. Retrieving of data from BigQuery
2. Getting a feeling for the data, cleansed it appropriately
3. Split into training, val and test set. I did this at random first and afterwards in
a time-series manner
4. Using the randomly distributed data of the second step to train a classical, linear model (SVM) as baseline
as well as a LSTM with the time-series data 

I will describe for each of the above mentioned points my thoughts in the following:

## Data Cleansing

The data given consists of weather specific measures like the max temperature
for different stations all over the world. For each measure you have a day, month and year
column representing a concrete date where the measure has been taken. Geolocation data
of the stations is not given. Furthermore, several boolean features are present representing 
weather conditions like rain, hail etc. Only snow is relevant for us, so I removed them right away. 

Given the rest of the features I observed (given correlation, column name semantics) that several columns will not have any important information 
for the prediction. Furthermore, just a few NaN values where missing that I replaced with appropriate means. After a manual selection, the following features where used for prediction purposes: 

station_number (encoded using one-hot vector) , mean_temp, mean_dew_point, mean_sealevel_, mean_station_p, mean_visibilit, mean_wind_spee, max_sustained_, max_gust_wind_, max_temperatur, total_precipitation

## 



