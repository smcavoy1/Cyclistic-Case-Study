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

<br>

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

<br>

Preview Dataset

![Screen Shot 2021-09-24 at 11 27 43 AM](https://user-images.githubusercontent.com/91289713/134718980-ba55e6ed-9340-44a1-b53e-52d9a48424ff.png)

<br>

Then we check for null values.

<br>

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
Result

![Screen Shot 2021-09-24 at 2 02 26 PM](https://user-images.githubusercontent.com/91289713/134720754-5936e101-9fe1-4679-9242-7a7e520a3b11.png)

<br>

Next we check for duplicate data.

```
SELECT ride_id, count (ride_id)
FROM `cyclistic-case-study-326019.cyclistic_data.full_year`
GROUP BY ride_id
HAVING count(ride_id) > 1
```

This shows that there are 209 duplicates in the ride_id field

<br>

Let's now check for trips with a ride length of less than 1 minute

```
SELECT COUNT(ride_length)
FROM `cyclistic-case-study-326019.cyclistic_data.full_year`
WHERE ride_length < 1
```

We find 80,515 records where the ride length is less than one minute. 

<br>

Check for trips with a ride length of greater than 24 hours (1440 minutes).

```
SELECT COUNT(ride_length)
FROM `cyclistic-case-study-326019.cyclistic_data.full_year`
WHERE ride_length >1440
```

This shows 3482 records where the ride length is greater than 24 hours. 

<br>

Let’s clean the data by creating a new table that removes duplicate records and rows with null values. I will also remove rides under 1 minute long and rides over 24 hours (1440 minutes), as these outliers do not represent typical usage. We’ll also add columns for starting date and starting time.

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

<br>

Now let’s double check to make sure there are no null values. 

```
SELECT
    SUM(CASE WHEN ride_id IS NULL THEN 1 ELSE 0 END) AS ride_id_null,
    SUM(CASE WHEN rideable_type IS NULL THEN 1 ELSE 0 END) AS rideable_type_null,
    SUM(CASE WHEN started_date IS NULL THEN 1 ELSE 0 END) AS started_date_null,
    SUM(CASE WHEN started_time IS NULL THEN 1 ELSE 0 END) AS started_time_null,
    SUM(CASE WHEN start_station_name IS NULL THEN 1 ELSE 0 END) AS start_station_null,
    SUM(CASE WHEN end_station_name IS NULL THEN 1 ELSE 0 END) AS end_station_null,
    SUM(CASE WHEN start_lat IS NULL THEN 1 ELSE 0 END) AS start_lat_null,
    SUM(CASE WHEN start_lng IS NULL THEN 1 ELSE 0 END) AS start_lng_null,
    SUM(CASE WHEN end_lat IS NULL THEN 1 ELSE 0 END) AS end_lat_null,
    SUM(CASE WHEN end_lng IS NULL THEN 1 ELSE 0 END) AS end_lng_null,
    SUM(CASE WHEN member_casual IS NULL THEN 1 ELSE 0 END) AS start_station_null,
    SUM(CASE WHEN ride_length IS NULL THEN 1 ELSE 0 END) AS ride_length_null,
    SUM(CASE WHEN month IS NULL THEN 1 ELSE 0 END) AS month_null,
    SUM(CASE WHEN day_of_week IS NULL THEN 1 ELSE 0 END) AS day_of_week_null,
FROM  cyclistic_data.full_year_clean
```
Result

![Screen Shot 2021-09-29 at 11 00 59 AM](https://user-images.githubusercontent.com/91289713/135295561-7a07b377-0f29-4bf8-b7f2-ec363fdee68c.png)

No null were values found.

<br>

Check the new table for duplicate records.

```
SELECT ride_id, count (ride_id)
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
GROUP BY ride_id
HAVING count(ride_id) > 1
```

This query returned no results, confirming that there are no duplicates in the ride_id column.


# Analyze

Let's start the analysis by finding total rides, ride percentage, and average ride length for members and casual riders. 


```
SELECT member_casual,
    COUNT(ride_id) AS total_rides, 
    ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER (), 2) AS percentage,
    ROUND(AVG(ride_length),2) AS avg_ride_length
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
GROUP BY member_casual
```
Result


![Screen Shot 2021-09-25 at 10 54 05 AM](https://user-images.githubusercontent.com/91289713/134775800-516431a4-1366-4e45-a8b2-3438124479cb.png)

We can see that the average ride length for casual riders is nearly 30 minutes. This is more than twice the average ride length for members.
 
<br>


Since ride patterns can vary by the day of the week, let's look at the total rides and average ride length by day of the week.

```
SELECT member_casual,
    COUNT(ride_id) AS total_rides, 
    ROUND(AVG(ride_length),2) AS avg_ride_length,
    day_of_week
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
GROUP BY member_casual, day_of_week
ORDER BY day_of_week, member_casual
```

Result


![Screen Shot 2021-09-29 at 11 10 54 AM](https://user-images.githubusercontent.com/91289713/135297457-e90b074c-a11b-4eb2-9574-9f173128f1f7.png)

For the day_of_week column, 1 = Sunday, 7 = Saturday


<br>          


We can also break this down by weekday and weekend. We'll include percentage columns to make analysis easier.

```
SELECT member_casual,
    CASE WHEN day_of_week BETWEEN 2 AND 6 THEN "weekday" ELSE "weekend" END day_type,
    COUNT(ride_id) AS total_rides, 
    ROUND(AVG(ride_length),2) AS avg_ride_length,
     ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER 
			 		(PARTITION BY member_casual),2) 
					           AS percentage_type,
	   ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER (), 2)
	   				           AS percentage_total
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
GROUP BY member_casual, day_type
ORDER BY member_casual, day_type
```

Result 

![Screen Shot 2021-09-27 at 11 03 19 AM](https://user-images.githubusercontent.com/91289713/134934677-0e77f03d-d257-42ec-aa06-5be43123e858.png)


<br>           


Find the most popular starting hours for members

```
SELECT member_casual,
EXTRACT(HOUR FROM started_time) AS start_hour,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
WHERE member_casual = 'member'
GROUP BY member_casual, start_hour
ORDER BY num_of_rides DESC 
```

Result 

![Screen Shot 2021-09-27 at 1 50 33 PM](https://user-images.githubusercontent.com/91289713/134959868-73908f0f-0586-4809-99c3-e1990dcdceaf.png)

<br>   


Find the most popular starting hours for casual riders


```
SELECT member_casual,
EXTRACT(HOUR FROM started_time) AS start_hour,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
WHERE member_casual = 'casual'
GROUP BY member_casual, start_hour
ORDER BY num_of_rides DESC 
```

Result

![Screen Shot 2021-09-27 at 1 55 55 PM](https://user-images.githubusercontent.com/91289713/134960350-6e83dd6f-a23c-43c4-bea5-463f88209d2f.png)


<br>                

Now we'll look at the number of rides and average ride length by month for members and casual riders.

```
SELECT member_casual,
month,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
GROUP BY member_casual, month
```

Result

![Screen Shot 2021-09-28 at 10 04 02 AM](https://user-images.githubusercontent.com/91289713/135102896-516c5694-1bfd-42c3-84eb-c3655cd2907c.png)

<br>

We can also break rides down by meteorological season to better view the seasonal effects.

```
SELECT member_casual,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length,
CASE WHEN month = 12 or month = 1 or month = 2 THEN "winter" 
WHEN month BETWEEN 3 AND 5 THEN "spring"
WHEN month BETWEEN 6 AND 8 THEN "summer"
ELSE "fall" END season
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
GROUP BY member_casual, season
```

Result

![Screen Shot 2021-09-28 at 11 16 51 AM](https://user-images.githubusercontent.com/91289713/135116003-c676cc99-8e77-41a0-8716-be0bbf55607f.png)


<br>  

Let's look at bike selection for members and casual riders.

```
SELECT member_casual,
rideable_type,
COUNT(*) num_of_rides,
ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER 
	(PARTITION BY member_casual),2) AS percentage_type,
ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER (), 2)AS percentage_total,
ROUND(AVG(ride_length),2) AS avg_ride_length
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
GROUP BY member_casual, rideable_type
ORDER BY member_casual, num_of_rides DESC 
```

Result

![Screen Shot 2021-09-28 at 11 56 47 AM](https://user-images.githubusercontent.com/91289713/135122891-44d61924-5cac-49ee-8420-665de84003bb.png)


<br>


Now we'll run queries to find the top starting stations for members and casual riders. 

For members: 

```
SELECT start_station_name,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length,
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
WHERE member_casual = 'member'
GROUP BY start_station_name
ORDER BY num_of_rides DESC 
```

Result

![Screen Shot 2021-09-29 at 2 27 33 PM](https://user-images.githubusercontent.com/91289713/135327566-3010b040-2d78-409e-941f-ed772ac61272.png)

<br>

For casual riders:

```
SELECT start_station_name,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length,
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
WHERE member_casual = 'casual'
GROUP BY start_station_name
ORDER BY num_of_rides DESC 
```

Result

![Screen Shot 2021-09-29 at 2 36 26 PM](https://user-images.githubusercontent.com/91289713/135328739-9715c860-3729-4747-ab22-c88fd3ba932c.png)


<br>


We can use the start_lat & start_lng columns to create maps in Tableau showing the most popular starting stations for both members and casual riders.

The following SQL query gives us a list of the top 30 starting stations for members grouped by start_lat and start_lng

```
SELECT start_lat,
start_lng,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length,
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
WHERE member_casual = 'member'
GROUP BY start_lat, start_lng
ORDER BY num_of_rides DESC 
LIMIT 30
```

We can tweak the query slightly to get a list of the top 30 starting stations for casual riders.

```
SELECT start_lat,
start_lng,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length,
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
WHERE member_casual = 'casual'
GROUP BY start_lat, start_lng
ORDER BY num_of_rides DESC 
LIMIT 30
```

<br>
             

Let's look at the top ending stations for members.

```
SELECT end_station_name,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length,
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
WHERE member_casual = 'member'
GROUP BY end_station_name
ORDER BY num_of_rides DESC 
```

Result

![Screen Shot 2021-09-29 at 3 46 33 PM](https://user-images.githubusercontent.com/91289713/135338186-30228cfa-9c75-4d17-a5b5-3aea0b416767.png)


As it turns out, the top starting stations and ending stations for members are largely the same. 27 of 30 stations on the top starting stations for members list is also on the list of the top 30 ending stations for members.

<br>

Let's check popular ending stations for casual riders. 

```
SELECT end_station_name,
COUNT(*) num_of_rides,
ROUND(AVG(ride_length),2) AS avg_ride_length,
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
WHERE member_casual = 'casual'
GROUP BY end_station_name
ORDER BY num_of_rides DESC 
```

Result

![Screen Shot 2021-09-29 at 3 55 30 PM](https://user-images.githubusercontent.com/91289713/135339344-75d90139-72ab-43fe-9c10-2eb156ab0c8e.png)


Again, it's similar to the list of popular starting stations. 28 of 30 stations are found on both lists.

<br>

We can run a SQL query to calculate one-way and round-trip rides for members and casual riders. When a bike is returned to the same station where the ride was initiated, we will cousider it a round-trip. When a bike is retured to a different station, then it is a one-way trip.

```
SELECT member_casual,
	   CASE WHEN start_station_name = end_station_name 
	   		THEN 'round'
	   		ELSE 'one way'
			END AS trip_type,
	   COUNT(*) AS rides,
	   ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER 
			 		(PARTITION BY member_casual),2) 
					           AS percentage_type,
	   ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER (), 2)
	   				           AS percentage_total
FROM `cyclistic-case-study-326019.cyclistic_data.full_year_clean`
GROUP BY member_casual, trip_type
ORDER BY member_casual, trip_type
```

Result

![Screen Shot 2021-09-28 at 4 08 33 PM](https://user-images.githubusercontent.com/91289713/135158313-e3690f9e-d0f2-48cb-8fff-effef9c3d732.png)

               

There are relatively few round trips made, however casual riders are 349% more likely than members to complete a round trip.    ]

# Share

<div class='tableauPlaceholder' id='viz1632764927851' style='position: relative'><noscript><a href='#'><img alt='Average Ride Length ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Av&#47;AverageRideLength&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='AverageRideLength&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Av&#47;AverageRideLength&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div> 


<div class='tableauPlaceholder' id='viz1632754144248' style='position: relative'><noscript><a href='#'><img alt='Rides by Day of the Week ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ri&#47;RidesbyDayoftheWeek&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='RidesbyDayoftheWeek&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ri&#47;RidesbyDayoftheWeek&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>                


<div class='tableauPlaceholder' id='viz1632754883557' style='position: relative'><noscript><a href='#'><img alt='Average Ride Length by Day of the Week ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Av&#47;AverageRideLengthbyDayoftheWeek&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='AverageRideLengthbyDayoftheWeek&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Av&#47;AverageRideLengthbyDayoftheWeek&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>   


<div class='tableauPlaceholder' id='viz1632757278118' style='position: relative'><noscript><a href='#'><img alt='Total Rides &amp; Avg Ride Length by Weekday&#47;Weekend ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;To&#47;TotalRidesAvgRideLengthbyWeekdayWeekend&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='TotalRidesAvgRideLengthbyWeekdayWeekend&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;To&#47;TotalRidesAvgRideLengthbyWeekdayWeekend&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>  


<div class='tableauPlaceholder' id='viz1632770071941' style='position: relative'><noscript><a href='#'><img alt='Starting Hours for Members &amp; Casual Riders ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;St&#47;StartingHoursforMembersCasualRiders&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='StartingHoursforMembersCasualRiders&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;St&#47;StartingHoursforMembersCasualRiders&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>      


Let's break the starting hours down by weekdays and weekends


<div class='tableauPlaceholder' id='viz1632775809113' style='position: relative'><noscript><a href='#'><img alt='Starting Hours by Weekday&#47;Weekend ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;St&#47;StartingHoursbyWeekdayWeekend&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='StartingHoursbyWeekdayWeekend&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;St&#47;StartingHoursbyWeekdayWeekend&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>   


<div class='tableauPlaceholder' id='viz1632839096622' style='position: relative'><noscript><a href='#'><img alt='Number of Rides and Average Ride Length by Month ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Nu&#47;NumberofRidesandAverageRideLengthbyMonth&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='NumberofRidesandAverageRideLengthbyMonth&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Nu&#47;NumberofRidesandAverageRideLengthbyMonth&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>                


<div class='tableauPlaceholder' id='viz1632843095975' style='position: relative'><noscript><a href='#'><img alt='Rides and Average Ride Length by Season ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ri&#47;RidesandAverageRideLengthbySeason&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='RidesandAverageRideLengthbySeason&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ri&#47;RidesandAverageRideLengthbySeason&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>  


<div class='tableauPlaceholder' id='viz1632850627747' style='position: relative'><noscript><a href='#'><img alt='Bike Type for Members &amp; Casual Riders ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Bi&#47;BikeTypeforMembersCasualRiders&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='BikeTypeforMembersCasualRiders&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Bi&#47;BikeTypeforMembersCasualRiders&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>                


<div class='tableauPlaceholder' id='viz1632856109214' style='position: relative'><noscript><a href='#'><img alt='Top 50 Starting Stations for Members ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;To&#47;Top50StartingStationsforMembers&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='Top50StartingStationsforMembers&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;To&#47;Top50StartingStationsforMembers&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>                


<div class='tableauPlaceholder' id='viz1632855925353' style='position: relative'><noscript><a href='#'><img alt='Top 50 Starting Stations for Casual Riders ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;To&#47;Top50StartingStationsforCasualRiders&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='Top50StartingStationsforCasualRiders&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;To&#47;Top50StartingStationsforCasualRiders&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>   


<div class='tableauPlaceholder' id='viz1632860162455' style='position: relative'><noscript><a href='#'><img alt='One-Way vs Round Trips ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;On&#47;One-WayvsRoundTrips&#47;Sheet1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='One-WayvsRoundTrips&#47;Sheet1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;On&#47;One-WayvsRoundTrips&#47;Sheet1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div> 



