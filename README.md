# Module-Challenge-11: Forecasting Net Prophet
## Step 1. Find Unusual Patterns in Hourly Google Search Traffic
### 1. First, from import files from google.colab followed by reading the google_hourly_search_trends.csv file into a Pandas dataframe, with the date as the index. Once we review it with the .head() and .tail() functions, we use the .info() function to review the dataframe data types. Using hv.extension('bokeh'), which is used A LOT, we slice the May data from the dataframe using the .loc() function into a new dataframe, followed by plotting the May dataframe using hvplot().

### 2. We create a new variable for the sum of the May traffic, and get it by using the May 2020 dataframe and getting "Search Trends", followed by applying .sum(). For the median monthly traffic, we used the following code:

groupby = [df_mercado_trends.index.year, df_mercado_trends.index.month]

median_monthly_traffic = df_mercado_trends.groupby(by = groupby).sum().median()

### where we and to groupby the years and months to determine the median traffic flow. To compare the May 2020 traffic flow to the monthly median, we simply subtract the May 2020 data from the median data, then divide by the median data, as shown in the code. 

## Step 2. Mine the Search Traffic Data for Seasonality
### 1. For the average traffic by weekday, we use the hv.extension('bokeh') again, and then use the df_mercado_trends dataframe incombination with .groupby(), .mean(), and .hvplot(), where groupby is sorted by dayofweek.

### 2. Using the hv.extension again, we use hvplot and heatmap() to plot df_mercado_trends with x = "index.hour", y = "index.dayofweek", and C = "Search Trends".

### 3. Using hv.extension again, we use df_mercado_trends in combination with groupby(), mean(), and hvplot() to plot the 'weekofyear', which is contained within groupby.

## Step 3. Relate the Search Traffic to Stock Price Patterns
### 1. This time, we will read the mercado_stock_price.csv into a Pandas Dataframe, with the index column set to "date" again, and review using the head() and tail() functions. With the hv.extension again, we use hvplot to plot the dataframe with the mercado_stock_price.csv info. We now want to concatenate the df_mercado_trends and df_mercado_stock Dataframes, which can be done through the following:

mercado_stock_trends_df = pd.concat([df_mercado_stock, df_mercado_trends], axis=1).dropna()

display(mercado_stock_trends_df.head())

display(mercado_stock_trends_df.tail())

### 2. For the first half of 2020 stock trends data, we use the .loc() function from 2020-01:2020-06, followed by using head() and tail(). With hv.extension again, we plot the first half of 2020 data with hvplot and parameters shared_axes=False and subplots=True, with .cols(1) added to plot each column on separate axes.

### 3. To create a Lagged Search Trends column that shifts the search trend info by one hour, we produce the following code using .shift():

mercado_stock_trends_df['Lagged Search Trends'] = mercado_stock_trends_df['Search Trends'].shift(1)

### For a stock volatility column, we need close prices, percent change, a 4-period rollin g window, and the standard deviation. This can be expressed through:

mercado_stock_trends_df['Stock Volatility'] = mercado_stock_trends_df['close'].pct_change().rolling(window=4).std()

### Now again with hv.extension, we use hvplot to plot the Stock Volatility column that was created previously. For hourly stock return, we create a new column and apply the .pct_change() function to the 'close' prices, followed by reviewing with the head and tail functions.

### 4. to make a correlation table of the mercado_stock_trends_df dataframe, we apply .corr() as shown:

mercado_stock_trends_df[["Stock Volatility", "Lagged Search Trends", "Hourly Stock Return"]].corr()

## Step 4. Create a Time Series Model with Prophet
### 1. To set up the Google search data, we need to reset the index, create 'ds' and 'y' columns, and drop null values, which can be expressed through:

mercado_prophet_df = df_mercado_trends.reset_index()

mercado_prophet_df.columns = ['ds','y']

mercado_prophet_df = mercado_prophet_df.dropna()

### After displaying with the head() and tail() function, we call the Prophet() function and store it as an object. With the stored object and .fit(mercado_prophet_df), we fit the time-series model. To project out 2000 hours, we use our object and apply make_future_dataframe with parameters of periods=2000 and freq='H', followed by the tail() function. Next, we use the .predict() fucntion on the object using the newly-created future dataframe as the parameter, followed by the head() function. 

### 2. To plot model_mercado_trends, we use the .plot() function with the newly-created forecast_mercado_trends as the parameter. 

### 3. We first set the forecast index to 'ds', and view the yhat with upper and lower bouinds as columns. Next with hv.extension again, we use forecast_mercado_trends with yhat, yhat_lower, and yhat_upper columns with iloc[-2000:,:] for the last 2000 hours with .hvplot() to see the Mercado Forecast. To visualize the broken-down forecast reports, the index is reset again, and plot_components() is applied to model_mercado_trends with forecast_mercado_trends as the parameter.

## Step 5. (Option) 
### Please note that I have completed the optional Step 5, however, due to time contraints, I will not explain it out in the readme file.  
