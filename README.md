# Cyclistic-Case-Study
Capstone Project for Google Data Analytics Certificate
# Introduction
Cyclistic is a fictional company based on divvy a bike sharing company based in Chicago,The company is a subscription-based service, The company executives believe that converting the casual users to members is the way for the company's future growth.
# Scenario
You are a junior data analyst working on the marketing analyst team at Cyclistic, a bike-share company in Chicago. The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members. But ůrst, Cyclistic executives must approve your recommendations, so they must be backed up with compelling data insights and professional data visualizations.
The project follows the six step data analysis process: ask, prepare, process, analyze, share, and act.
# Ask
Three questions will guide the future marketing program:
1. How do annual members and casual riders use Cyclistic bikes differently?
2. Why would casual riders buy Cyclistic annual memberships?
3. How can Cyclistic use digital media to influence casual riders to become members?
### A clear statement of the business task:
Convert Casual users to Cyclistic members, by understanding how casuals use Cyclistic differently, and create a marketing campaign that targets them.
### Stakeholders:
1-Cyclistic executive team<br/>
2-Lily Moreno: The director of marketing and your manager. 
# Prepare
The data used is Cyclistic historical data from Jan 2023 to Dec 2023. The data has been made available by Motivate International Inc on this [link](https://divvy-tripdata.s3.amazonaws.com/index.html) under this [license](https://divvybikes.com/data-license-agreement).<br/>
containing 13 columns and 5,719,877 rows.<br/>
The data from the 12 months were merged into one table, with only the follwing 7 columns chosen:<br/>
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual.

```
-- Prepare phase --

-- Merging the tables --
SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
FROM
	dbo.[202301]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
FROM
	dbo.[202302]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual	
FROM
	dbo.[202303]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual	
FROM
	dbo.[202304]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
FROM
	dbo.[202305]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
FROM
	dbo.[202306]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
FROM
	dbo.[202307]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
FROM
	dbo.[202308]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual	
FROM
	dbo.[202309]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual	
FROM
	dbo.[202310]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
FROM
	dbo.[202311]
UNION ALL

SELECT 
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
FROM
	dbo.[202312]
```
## Data limitations
data-privacy issues prohibit you from using riders’ personally identiůable information. This means that you won’t be able to connect pass purchases to credit card numbers to determine if casual riders live in the Cyclistic service area or if they have purchased multiple single passes.<br/>
There were 88,816 observations were the started_at/ended at column had null value.<br/>
# Process
Intially I wanted to use excel for the process and cleaning phase but due to the data size I opted to use SQL.<br/>
The data was collected by the marketing analytics team which makes it a first source insuring the data integrity and reliability.<br/>
The following steps were taken to make sure the data is clean:<br/>
1.Identify null values
```
-- Identify null values --
SELECT *
FROM
	dbo.raw_data
WHERE
	ride_id IS NULL
	OR rideable_type IS NULL
	OR started_at IS NULL
	OR ended_at IS NULL
	OR start_station_name IS NULL
	OR end_station_name IS NULL
	OR member_casual IS NULL
	OR ride_length IS NULL
	OR ride_month IS NULL
	OR ride_day_started IS NULL
	OR ride_day_ended IS NULL
	OR ride_hour_started IS NULL
	OR ride_hour_ended IS NULL;
```
2.Identify duplicated data
```
-- Identify duplicated data --
SELECT	
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual,
	COUNT(*) AS duplicate_count
FROM
	dbo.raw_data
GROUP BY
	ride_id,
	rideable_type,
	started_at,
	ended_at,
	start_station_name,
	end_station_name,
	member_casual
	HAVING COUNT(*) > 1;
```
Then add additional were created
```
-- Creating new columns --
-- Ride duration --
ALTER TABLE dbo.raw_data
ADD ride_length INT;

UPDATE dbo.raw_data
SET ride_length = DATEDIFF(MINUTE,started_at,ended_at)
```
Ride length was checked for negative and more than 24 hours rides
```
-- Cheking for negative time rides --
SELECT
	*
From
	dbo.raw_data
WHERE
	ride_length < 0;

-- Cheking for more than a day rides --
SELECT
	*
From
	dbo.raw_data
WHERE
	ride_length > 1440;

-- Handling wrong data: negative time is fixed --
UPDATE dbo.raw_data
SET ride_length = CASE
	WHEN ride_length > 0 THEN DATEDIFF(MINUTE,started_at,ended_at)
	WHEN ride_length < 0 THEN DATEDIFF(minute,ended_at,started_at)
	END ;
```
There were 143 observations were the ride length was negative, affter more inspection it turned to be that the statred_at and ended_at columns for the specific rides were swapped.<br/>
There were 6,417 rides that lasted for more than 24 hours.<br/>
Five more columns were created for analysis purposes
```
-- ride month column --
ALTER TABLE dbo.raw_data
ADD ride_month INT;

UPDATE dbo.raw_data
SET ride_month = DATEPART(MM,started_at);
-- ride day column --
ALTER TABLE dbo.raw_data
ADD ride_day_started INT;

UPDATE dbo.raw_data
SET ride_day_started = DATEPART(WEEKDAY,started_at);

ALTER TABLE dbo.raw_data
ADD ride_day_ended INT;

UPDATE dbo.raw_data
SET ride_day_ended = DATEPART(WEEKDAY,ended_at);

-- ride hour column --
ALTER TABLE dbo.raw_data
ADD ride_hour_started INT;

UPDATE dbo.raw_data
SET ride_hour_started = DATEPART(HOUR,started_at);

ALTER TABLE dbo.raw_data
ADD ride_hour_ended INT;

UPDATE dbo.raw_data
SET ride_hour_ended = DATEPART(HOUR,ended_at);
```
# Analysis
Key tasks
- [x] Aggregate data so it’s useful and accessible.
- [x] Organize and format your data.
- [x] Perform calculations.
- [x] Identify trends and relationships.<br/>
-- Number of rides comparsion --
```
SELECT 
	COUNT(ride_id) AS number_of_rides,
	member_casual
FROM 
	dbo.raw_data
GROUP BY
	member_casual
ORDER BY
	COUNT(ride_id) DESC

-- Type of bike comparsion --
SELECT 
	COUNT(ride_id) AS number_of_rides,
	rideable_type,
	member_casual
FROM
	dbo.raw_data
GROUP BY
	rideable_type,
	member_casual
ORDER BY
	COUNT(ride_id) DESC
```
-- Ride length comparsion --
```
SELECT
	COUNT(ride_id) AS number_of_rides,
	AVG(ride_length) AS avg_ride_length,
	member_casual
FROM
	dbo.raw_data
Group BY
	member_casual
ORDER BY
	AVG(ride_length),
	COUNT(ride_id)
-- AVG ride by bike type --
SELECT
	COUNT(ride_id) AS number_of_rides,
	AVG(ride_length) AS avg_ride_length,
	rideable_type,
	member_casual
FROM
	dbo.raw_data
Group BY
	rideable_type,
	member_casual
ORDER BY
	AVG(ride_length),
	COUNT(ride_id) DESC
-- Max and MIN ride length --
SELECT
	MAX(ride_Length) AS max_ride_length,
	Min(ride_length) AS min_ride_length,
	member_casual
FROM
	dbo.raw_data
GROUP BY
	member_casual
```
--Time Analysis --
```
-- BY Month --
SELECT
	COUNT(ride_id) AS number_of_rides,
	DATEPART(month,started_at) AS month,
	member_casual
FROM
	dbo.raw_data
WHERE
	started_at IS NOT NULL
GROUP BY
	member_casual,
	DATEPART(month,started_at)
ORDER BY
	COUNT(ride_id) DESC
-- BY Weekday--
SELECT
	COUNT(ride_id) AS number_of_rides,
	DATEPART(WEEKDAY,started_at) AS weekday,
	member_casual
FROM
	dbo.raw_data
WHERE
	started_at IS NOT NULL
GROUP BY
	member_casual,
	DATEPART(WEEKDAY,started_at)
ORDER BY
	COUNT(ride_id) DESC
--BY Hour--
SELECT
	COUNT(ride_id) AS number_of_rides,
	DATEPART(HOUR,started_at) AS hour,
	member_casual
FROM
	dbo.raw_data
WHERE
	started_at IS NOT NULL
GROUP BY
	member_casual,
	DATEPART(HOUR,started_at)
ORDER BY
	COUNT(ride_id) DESC
```
-- Stations--
```
--start Stations--
SELECT TOP 12
	COUNT(ride_id) AS number_of_rides,
	start_station_name,
	member_casual
FROM
	dbo.raw_data
WHERE
	start_station_name IS NOT NULL
GROUP BY
	member_casual,
	start_station_name
ORDER BY
	COUNT(ride_id) DESC

--END Stations--
SELECT TOP 12
	COUNT(ride_id) AS number_of_rides,
	end_station_name,
	member_casual
FROM
	dbo.raw_data
GROUP BY
	member_casual,
	end_station_name
ORDER BY
	COUNT(ride_id) DESC
```
# Share
Tableau was used to create the visualization.<br/>
You can check them [here](https://public.tableau.com/app/profile/asim.mohammed6745/vizzes)
# Act
Based on my analysis I gave this three recommendations:<br/>
1. Increase the time limit for the annual membership to meet the casual users need.
2. Create new offers for weekend and holidays.
3. Promote the E-bikes more effectively.

