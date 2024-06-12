#athletic_sales_analysis
# Sales analysis of atheletic sales
> Analyzing sales data to gain insights into which cities in the U.S. have sold the most athletic wear over two years.

## Description
This Jupyter notebook details analyzes sales data of various retail stores which are selling athletic athletic products, using mostly groupby and pivot table commands

### Contents
* combine two dataframes
* Show sales in quantitt sold by region, state and city  
* Show sales in dollars by region, state and city
* Show sales in dollars by retailer, region, state and city
* Show sales in dollars by retailer, region, state and city for Women's Athletic Footwear category
    * Day with most sales
    * Week with most sales

### Preparing dataframe

First the data is imported into a dataframe create a dataset that can be used for analysis

`import pandas as pd`

`path = 'Resources'`

`as_2020_df = pd.read_csv(path+'/athletic_sales_2020.csv')`

`as_2021_df = pd.read_csv(path+'/athletic_sales_2021.csv')`

Since the data for year 2020 and 2021 are in different dataframes, CONCAT is used to combine the frames together

`as_df = pd.concat([as_2020_df,as_2021_df], ignore_index = True)`

To avoid any errors in data, the code below checks for null values in any of the columns

`as_df.isna().sum()`

To use dates in calculations, invoice_date column is converted from string to date format

`as_df['invoice_date'] = as_df['invoice_date'].apply(pd.to_datetime)`

Now the data is ready to be used for analysis

## Sales in quantity sold by region, state and city 
There are two ways the data is analyzed to demonstrate the results can be achieved in multiple ways

### Using Groupby

The code below is used to performing 

Calculates number of products sold by region, state, and city, rename the sum to "Total_Products_Sold" and show top 5 results

`as_grouped_df = as_grouped_df.rename(columns={'units_sold' : 'Total_Products_Sold'})`

`as_grouped_df.sort_values(by='Total_Products_Sold', ascending = False).head()`

### Using Pivot Table

`as_df_pivoted = pd.pivot_table(as_df, index=['region','state','city'] ,values = 'units_sold', aggfunc ='sum')`

`as_df_pivoted = as_df_pivoted.rename(columns = {'units_sold' : 'Total_Products_Sold'})`

`as_df_pivoted = as_df_pivoted.sort_values(by = 'Total_Products_Sold', ascending = False)`

Results from both code snippets is shown below

![Most product sales](/images/Top5_most_products_sold.png)

## Sales in dollars by region, state and city 

Calculates sales in dollars by region, state, and city, rename the sum to "Total Sales" and show top 5 results. Here I have tried a slightly different approach. I am passing the values of columns in a variable - **rsc**. This variable is called in the code for both groupby and pivot table codes. Additionally, instead of writing separate line of code for each action, I have daisy-chained the code

### Using Groupby

`rsc = ['region', 'state','city']`

`as_df_total_sales_grouped = as_df.groupby(
    rsc)[['total_sales']].sum().rename(
    columns = {'total_sales' : 'Total Sales'}).sort_values(
    by='Total Sales', ascending = False).head()`

### Using Pivot Table

`as_df_total_sales_pivoted = pd.pivot_table(
    as_df, index= rsc,
    values = 'total_sales', aggfunc = 'sum').rename(
    columns = {'total_sales' : 'Total Sales'}).sort_values(
    by = 'Total Sales', ascending = False)`

Results from both code snippets is shown below

![Top 5 most sales](/images/top_5_most_sales.png)

## Sales in dollars by retailer, region, state and city

Here one more parameter - *retailer* - is added to the code. Similar to the code snippet above, column retailer in addition to above columns is passed to a new variable - **rrsc** - and its called in the code snippets

### Using Groupby

`rrsc = ['retailer','region', 'state','city']`

`as_df2_total_sales_grouped = as_df.groupby(
    rrsc)[['total_sales']].sum().rename(
    columns = {'total_sales' : 'Total Sales'}).sort_values(
    by='Total Sales', ascending = False)`

### Using Pivot Table

`as_df_total_sales_pivoted2 = pd.pivot_table(
    as_df, index = rrsc,
    values = 'total_sales', aggfunc = 'sum').rename(
    columns = {'total_sales' : 'Total Sales'}).sort_values(
    by = 'Total Sales', ascending = False)`

Results from both code snippets is shown below

![Top 5 retailers](/images/top_5_most_sales_retailer.png)

## Most Women's Athletic Footware Sales by Retailer

A new dataframe is created to only have data for products named "Women's Athletic Footwear"

`most_womens_athletic_footwear_df = as_df[as_df['product'] == "Women's Athletic Footwear"]`

This dataframe is now analyzed by both methods - groupby and pivot_table which results in identical result, with resulting numberic columns renamed to Womens_Footwear_Units_Sold

### Using Groupby

`wm_fw_total_sales_grouped = most_womens_athletic_footwear_df.groupby(
    rrsc)[['units_sold']].sum().rename(
    columns = {'units_sold' : 'Womens_Footwear_Units_Sold'}).sort_values(
    by='Womens_Footwear_Units_Sold', ascending = False)`

### Using Pivot Table

`wm_fw_total_sales_pt_df = pd.pivot_table(
    most_womens_athletic_footwear_df, index = rrsc,
    values = 'units_sold', aggfunc = 'sum').rename(
    columns = {'units_sold' : 'Womens_Footwear_Units_Sold'}
    ).sort_values(by = 'Womens_Footwear_Units_Sold', ascending = False)`

Result
![Top 5 Women's Footwear](/images/Top5_most_women_footwear_sold.png)

## Day and week with most sales

First, a new pivot table is created with invoice_date column as index and value column as sum of 'total_sales' column

`clean_as_df = pd.pivot_table(most_womens_athletic_footwear_df,index='invoice_date', values = 'total_sales', aggfunc = 'sum')`

total_sales column is renamed as 'Total Sales'

`clean_as_df = clean_as_df.rename(columns = {'total_sales' : 'Total Sales'})`

Using resample command, sales by day of the year and week of the year is calculated

### Top 10 days with most sales in dollars

`resample_daily_df = clean_as_df.resample('D').sum()`

`resample_daily_df.sort_values(by = 'Total Sales', ascending = False).head(10)`

![](/images/Top5_most_women_footwear_sold_by_day.png)


### Top 10 weeks with most sales in dollars
`resample_weekly = clean_as_df.resample('W').sum().sort_values(by = 'Total Sales', ascending = False)`

![](/images/Top5_most_women_footwear_sold_by_week.png)