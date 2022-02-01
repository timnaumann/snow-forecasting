# Snow ForeCasting

In order to forecast if it will be snowing tomorrow 12 years ago (date in 2010) I did the following steps:

1. Retrieving of data from BigQuery
2. Getting a feeling for the data, cleansed it appropriately
3. Split into training, val and test set. I did this at random first and afterwards in
a time-series manner
4. Using the randomly distributed data of the second step to train a classical, linear model (SVM) as baseline
as well as a LSTM with the time-series data 

I will describe several of the above-mentioned points in more detail in the following:

## Data Cleansing

The data given consists of weather specific measures like the max temperature
for different stations all over the world. For each measure you have a day, month and year
column representing a concrete date where the measure has been taken. Geolocation data
of the stations is not given. Furthermore, several boolean features are present representing 
weather conditions like rain, hail etc. Only snow is relevant for us, so I removed them right away. The data that is 
important for the prediction should only contain values between the years 2006 and 2009. However, I enhanced the window
to 2010 in order to have the day 12 years ago part of the test set.

Given the rest of the features I observed (given correlation, column name semantics) that several columns will not have any important information 
for the prediction. Furthermore, just a few NaN values where missing that I replaced with appropriate means. After a manual selection, the following features where used for prediction purposes: 

station_number (encoded using one-hot vector) , mean_temp, mean_dew_point, mean_sealevel_, mean_station_p, mean_visibilit, mean_wind_spee, max_sustained_, max_gust_wind_, max_temperatur, total_precipitation,
month, day_of_year

## Data Splitting

I employed two approaches to for data splitting:

1. uniformly without any temporal splitting. I used it train, val, test set for a linear classifier
2. in a time-series manner:
   1. For each given station in the data set, I take n-timestaps of each month as sample window whereas the n+1-timestamp represents
   the target. With a "stride" of m, I shift the window to the ride and repeat this process
   2. In order to keep a clean separation between training and validation, the stride = number of timestamps for the sample
    dedicated to the validation set
   3. All of the samples of the year 2010 are dedicated to the test-set only. Therefore, it 
   includes the day that should be predicted at the end

## Prediction

First, I tried a Support Vector Machine to do predictions based on the randomly distributed data set. As expected,
the approach performed poorly due to the missing context information given the days before (ca. 60% precision, ca. 30% recall on validation set).
The data is at least not easily separable in the embedding space.

Afterwards, a LSTM have been applied in order to capture the temporal dependencies between samples and targets. However, 
I was not able to train it properly on the given dataset due to extreme overfitting (validation set only ca. 40% precision and ca. 20% recall).
Despite my efforts of changing the underlying data samples (increasing time-series window width) and the model itself (experimented with different unit
counts) it seems that I may still have a bug during the data splitting or that the feature selection, the given
data or the model is not sufficient. Due to the experienced overfitting, I didn't start improving hyperparams because there is no benefit at the moment.
Nevertheless, I created a small method that allows to predict the occurrence of snow for all stations given the day tomorrow 12 ago.
