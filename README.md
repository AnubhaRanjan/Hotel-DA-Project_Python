# Hotel data analysis project

****Problem Statement:****<br />
AtliQ Grand is a hotel chain management system which has hotels in different cities in India. They are in **Mumbai, Delhi, Bangalore and Hyderabad**. They are running a variety of hotels,
**AtliQ Seasons, AtliQ Exotica, AtliQ Bay and AtliQ Palace**. 
Each holds different room types, **Standard,Elite, Premium and Presidential**. 
They have different booking platforms also
**Atliqgrands.com, Tripster, Logtrip, Makeyourtrip and Offline booking**.
Atliq Grand is losing their revenue generation in their booking and they want to analyze insights of their booking.<br/>
<br/>
**Objective:**<br/>
The objective of this project is to go step by step and analysis the insights of booking.<br/>
<br/>
**Performed tasks:**<br/>
## **Understand Business problem**<br/>
As stated in the Objective section along with more specific queries from business.<br/>
## **Data Collection and understanding**<br/>
We have different CSV files, **fact_bookings.csv, dim_hotels.csv, dim_rooms.csv, dim_date.csv and fact_aggregated_bookings.csv**
The above files give booking information, different hotels, rooms and corresponding dates knowledge.<br/>
## **Data Cleaning and exploration**<br/>
**Perform initial EDA (Exploratory data analysis)**<br/>
Installing pandas lib<br/>
   **Pip install pandas**<br/>
Import pandas lib on kernel<br/>
		**Import pandas as pd**<br/>
All the booking information is given on the file fact_bookings.csv so starting to analyse this file first. Loading this file.<br/>
	**df_booking=pd.read_csv("fact_bookings.csv")**<br/>
Checking the first few rows from the file.<br/>
	**df_booking.head(5)**<br/>
Checking few basic parameters from the file like, Shape of the file, detail, datatypes, null values and unique values.<br/>
	**df_booking.shape**<br/>
  **df_booking.describe()<br/>
    df_booking.info()<br/>
		df_booking.isna().sum()<br/>
		df_booking.booking_platform.unique()<br/>
		df_booking.room_category.value_counts()<br/>
		df_hotels.category.value_counts()<br/>
    df_agg_booking.isna().sum()**<br/>
  Similar EDA activities performed on other required files.<br/>
  <br/>
**After basic exploration we got to know**<br/>
   - ‘no_guests’ column has min value of -17 and 3 null values.<br/>
   - ‘ratings_given’ has 77907 null values.<br/>
   - ‘revenue_generated’ few values are exponentially high for ‘room_category’=’RT4’ which is   extremely high from max value.<br/>
   -‘capacity’ column has 2 null values.<br/>
   -Where 'successful_bookings'  is greater than ‘capacity’   (df_agg_booking.where(df_agg_booking['successful_bookings'] >  df_agg_booking['capacity'])).value_counts()<br/>
   
## **Data transformation**  
-	Removing negative values and filling null values with 0.<br/>
  **df_booking.where(df_booking['no_guests']<0).value_counts()<br/>
    df_booking['no_guests'].fillna(0.0,inplace=True)**<br/>
-	Finding correct possible range for ‘revenue_generated’ column. Values of this column is exceeding the max value of the column.<br/>
-	To fix ‘revenue_generated’ column value need to find out higher possible values according to 3 standard deviation.
  **avg,std=df_booking.revenue_realized.mean(),df_booking.revenue_realized.std()
  higher_limit_rr=avg+3*std
  df_booking[df_booking.revenue_realized>higher_limit_rr]**
-	Filling capacity column with its mean value df_agg_booking.capacity.fillna(value=df_agg_booking.capacity.mean(),inplace=True)
-	Adding new column ‘occ_pct’
  **df_agg_booking['occ_pct']=(df_agg_booking['successful_bookings']/df_agg_booking['capacity'])*100**

## Collect Insights
**df_hotels.city.value_counts().plot(kind="bar")**
<AxesSubplot:>
 
<img width="383" alt="1" src="https://github.com/AnubhaRanjan/Hotel-DA-Project_Python/assets/110692265/3ed7d6dd-761c-41aa-a21c-0c0b1338d0d2">

-	What is the avg occupancy rate in each room category?<br/>
     Merging two files fact_aggregated_bookings.csv and dim_hotels.csv  on key field ‘property_id’<br/>
         **df=pd.merge(df_agg_booking,df_hotels,on="property_id")**<br/>
	   Finding avg occupancy rate for each room_type.<br/>
         **df.groupby("room_category")["occ_pct"].mean().round(2)**<br/>
                room_category<br/>
                RT1    58.22<br/>
                RT2    58.04<br/>
                RT3    58.03<br/>
                RT4    59.30<br/>
             Name: occ_pct, dtype: float64<br/>
-	What is the avg occupancy rate in each day type?<br/>
     Merging two files fact_aggregated_bookings.csv and dim_date.csv  on key field ‘check_in_date’ and ‘date <br/>
     **df=pd.merge(df,df_date,left_on="check_in_date",right_on="date")**<br/>
- Finding avg occupancy rate for each day_type.<br/>
     **df.groupby("day_type")["occ_pct"].mean().round(2)**<br/>
               day_type<br/>
               weekeday    50.90<br/>
               weekend     72.39<br/>
               Name: occ_pct, dtype: float64<br/>

      **df.groupby("day_type")["occ_pct"].mean().plot(kind="bar")**<br/>
         <AxesSubplot:xlabel='day_type'>
 <img width="393" alt="2" src="https://github.com/AnubhaRanjan/Hotel-DA-Project_Python/assets/110692265/1cdda4a8-6bc1-4cd6-a446-497fee0dd797">

Finding avg occupancy rate for each citywise. df.groupby("city")["occ_pct"].mean().plot(kind="bar")
<AxesSubplot:xlabel='city'>
 
Analyzing on June 22 month<br/>
Occupational percentage for each city in the month of June.<br/>
	**df_jun_22=df[df["mmm yy"]=="Jun 22"]<br/>
	df_jun_22.groupby("city")["occ_pct"].mean().plot(kind="bar")**<br/>
<AxesSubplot:xlabel='city'>
 

<img width="383" alt="3" src="https://github.com/AnubhaRanjan/Hotel-DA-Project_Python/assets/110692265/09e95f6c-fbae-42d0-bc99-7545d9d88a1d">

**df_jun_22.groupby("city")["occ_pct"].mean().round(2).sort_values(ascending=False)**<br/>
city<br/>
Delhi        62.47<br/>
Hyderabad    58.46<br/>
Mumbai       58.38<br/>
Bangalore    56.58<br/>
Name: occ_pct, dtype: float64






