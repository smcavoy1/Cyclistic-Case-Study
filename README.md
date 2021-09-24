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

The data was made available by Motivate International Inc. under this data license agreement https://www.divvybikes.com/data-license-agreement


# Process

The tools I used are:

SQL (BigQuery) for cleaning and formatting the data

Tableau for making data visualizations

The first step in the data cleaning process was to combine the 12 monthly CSV files into a single table in SQL
