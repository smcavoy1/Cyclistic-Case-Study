# Cyclistic Case Study

# Introduction

This is a case study I completed as part of the Google Data Analytics Professional Certificate. The task was to follow the steps of the data analysis process: ask, prepare, process, analyze, share, and act, in order to answer key business questions. This case study is about Cyclistic, a bike-share program in Chicago. While Cyclistic is a fictitious company, the data used in this case study is public data from Chicago’s real bicycle sharing system, Divvy.

Cyclistic was launched in 2016 and since then has grown to a fleet of 5,824 bikes, which are geotracked and locked into a network of 692 stations across the city. The company’s marketing strategy has relied on building awareness of the brand and appealing to broad consumer segments. The company offers three different pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who buy single-ride or full-day passes are referred to as casual riders. Customers who buy annual memberships are Cyclistic members. 

Cyclistic’s finance analysts have determined that annual members are much more profitable than casual riders. Director of marketing Lily Moreno believes believes that maximizing the number of annual members will be key to future growth. To this end, she has set a clear goal: Design marketing strategies aimed at converting casual riders into annual members. To do this, the marketing analyst team needs to better understand how members and casual riders differ in their use of the service, what would persuade casual riders to buy a membership, and how digital media could affect their marketing tactics.

# Ask

I have been assigned the following question: How do annual members and casual riders use Cyclistic bikes differently? By analyzing Cyclistic’s historical bike trip data, I will identify trends to help better understand how these two groups differ in their use of Cyclistic bikes. This will help Cyclistic design marketing strategies to convert casual riders into annual members. 


# Prepare

The data is available for download <a href="https://divvy-tripdata.s3.amazonaws.com/index.html">at this link</a>. 

The data is organized as ZIP files that extract as CSV. I downloaded data from the 12 most recent months of data, from September 2020 to August 2021. Each CSV file contains data on an individual Cyclistic ride on each row, with 13 columns of data. The size of the dataset is 743 MiB. There are 4,913,072 records in the dataset before data cleaning.

The data is reliable and original and has no issues with credibility as it is collected directly from a first-person source, Motivate International Inc. The dataset provides anonymized information on starting and ending times and stations, along with information on the type of bike used. This data is adequate for uncovering broad patterns, however the lack of a user id makes it impossible to analyze individual user profiles. 

One limitation with the data is not being able to determine which casual riders live outside Chicago and may not be part of the target market for Cyclistic memberships (e.g. tourists). Being able to separate data from casual local riders from that of casual out-of-town riders would provide greater insights into how the segment that we are trying to convert into members uses the service. Another limitation is the inability to separate casual users using single-ride passes from those using full-day passes. 

The data was made available by Motivate International Inc. under this <a href="https://www.divvybikes.com/data-license-agreement">data license agreement</a>.

# Process

The tools I used are:

- SQL (BigQuery) for cleaning and formatting the data

- Tableau for making data visualizations

The first step in the data cleaning process was to combine the 12 monthly CSV files into a single table in SQL.

- Create a table called full_year to combine the 12 monthly datasets
- Added ride_length column
- Added columns for month and day_of_week
- Removed redundant start_station_id and end_station_id columns

```
CREATE TABLE cyclistic_data.full_year AS
SELECT *,
    date_diff(ended_at, started_at, minute) AS ride_length, EXTRACT(MONTH FROM started_at) month,
EXTRACT(DAYOFWEEK FROM started_at) day_of_week,
FROM (
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Sep2020` UNION ALL 
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Oct2020` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Nov2020` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Dec2020` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Jan2021` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Feb2021` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Mar2021` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Apr2021` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.May2021` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Jun2021` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Jul2021` UNION ALL
    SELECT * EXCEPT (start_station_id, end_station_id) FROM `cyclistic-case-study-326019.cyclistic_data.Aug2021` )
 ```
 
The size of the dataset is 743 MiB. There are 4,913,072 records in the dataset.

#### Preview Dataset

![Screen Shot 2021-09-24 at 11 27 43 AM](https://user-images.githubusercontent.com/91289713/134718980-ba55e6ed-9340-44a1-b53e-52d9a48424ff.png)

Then we check for null values.

```
SELECT
    SUM(CASE WHEN ride_id IS NULL THEN 1 ELSE 0 END) AS ride_id_null,
    SUM(CASE WHEN rideable_type IS NULL THEN 1 ELSE 0 END) AS rideable_type_null,
    SUM(CASE WHEN started_at IS NULL THEN 1 ELSE 0 END) AS started_at_null,
    SUM(CASE WHEN ended_at IS NULL THEN 1 ELSE 0 END) AS ended_at_null,
    SUM(CASE WHEN start_station_name IS NULL THEN 1 ELSE 0 END) AS start_station_null,
    SUM(CASE WHEN end_station_name IS NULL THEN 1 ELSE 0 END) AS end_station_null,
    SUM(CASE WHEN start_lat IS NULL THEN 1 ELSE 0 END) AS start_lat_null, 
    SUM(CASE WHEN start_lng IS NULL THEN 1 ELSE 0 END) AS start_lng_null, 
    SUM(CASE WHEN end_lat IS NULL THEN 1 ELSE 0 END) AS end_lat_null, 
    SUM(CASE WHEN end_lng IS NULL THEN 1 ELSE 0 END) AS end_lng_null, 
    SUM(CASE WHEN member_casual IS NULL THEN 1 ELSE 0 END) AS member_casual_null, 
    SUM(CASE WHEN ride_length IS NULL THEN 1 ELSE 0 END) AS ride_length_null, 
    SUM(CASE WHEN month IS NULL THEN 1 ELSE 0 END) AS month_null, 
    SUM(CASE WHEN day_of_week IS NULL THEN 1 ELSE 0 END) AS day_of_week_null
FROM cyclistic_data.full_year
```
![Screen Shot 2021-09-24 at 2 02 26 PM](https://user-images.githubusercontent.com/91289713/134720754-5936e101-9fe1-4679-9242-7a7e520a3b11.png)

Next we check for duplicate data.

```
SELECT ride_id, count (ride_id)
FROM `cyclistic-case-study-326019.cyclistic_data.full_year`
GROUP BY ride_id
HAVING count(ride_id) > 1
```
This shows that there are 209 duplicates in the ride_id field

Let's now check for trips with a ride length of less than 1 minute

```
SELECT COUNT(ride_length)
FROM `cyclistic-case-study-326019.cyclistic_data.full_year`
WHERE ride_length < 1
```

We find 80,515 records where the ride length is less than one minute. 

Check for trips with a ride length of greater than 24 hours (1440 minutes).

```
SELECT COUNT(ride_length)
FROM `cyclistic-case-study-326019.cyclistic_data.full_year`
WHERE ride_length >1440
```

This shows 3482 records where the ride length is greater than 24 hours. 

Let’s clean the data by creating a new table that removes duplicate records and rows with null values. I will also remove rides under 1 minute long and rides over 24 hours (1440 minutes), as these outliers do not represent normal usage. We’ll also add columns for starting date and starting time.

```
CREATE TABLE cyclistic_data.full_year_clean AS 
    SELECT 
        ride_id,
        rideable_type,
        CAST(started_at AS DATE) started_date, 
        CAST(started_at AS TIME) started_time,
        start_station_name,
        end_station_name,
	   start_lat,
        start_lng,
	   end_lat,
        end_lng,
        member_casual,
        ride_length,
	   month,
        day_of_week
    FROM 
    (
        SELECT 
            *,
            ROW_NUMBER()
            OVER (PARTITION BY ride_id) AS row_number 
        FROM `cyclistic-case-study-326019.cyclistic_data.full_year`  )
    WHERE 
        row_number = 1 AND
        ride_length >= 1 AND
        ride_length < 1440 AND
        start_station_name IS NOT NULL AND 
        end_station_name IS NOT NULL AND
        end_lat IS NOT NULL AND 
        end_lng IS NOT NULL
```
        
This new table, full_year_clean, removes 741,120 records, leaving us with 4,171,952.


        

