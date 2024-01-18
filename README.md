# Space Mission Analysis

## Table of Contents

- [Project Overview](#project-overview)
- [Tools](#tools)
- [Data Cleaning](#data-cleaning)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Insights](#insights)
### Project Overview

This data analysis project aims to provide inisghts into the space mission over the past year. By analyzing various parts of the space mission dataset, i seek to identify trends, gain a deeper understanding of the dataset and make data-driven insights.

### Tools 
- Microsoft Excel - Data Validation
- Microsoft SQL Server Management Studio (SSMS) - Data Cleaning and Data Analysis
- Microsoft Power BI - Creating Visualizations

### Data Cleaning
In the data preparation phase, i performed the following tasks;
1. Data loading
2. Data Validation
3. Handling missing Values
4. Data Cleaning

### Exploratory Data Analysis
EDA involved exploring the space mission data to answer key questions such as;
- What is the mission start time frequency
- What is the rocket reliability of each rocket
- What is each country's launch frequency
- What is the success rate of each company
- What is the launch frequency as well as the success rate for each rocket
- What is the total number of rocket status for each rocket
- What is the total number of missions per year
- What is the total number rocket status for each rocket
- What is the total number of successful and unsuccessful missions
- What is the total price each company spent

### Data Analysis
```sql
SELECT  *
  FROM space;

-- To check for duplicates 
  WITH RowNumberCTE AS(
	SELECT *, ROW_NUMBER() OVER (PARTITION BY company, location, year, rocket, missionstatus, rocketstatus, price, mission, time
		ORDER BY (select 0)) as rownum
		FROM space)
			 SELECT *
			 FROM RowNumberCTE
			 WHERE rownum > 1;

  --  mission start time frequency
select top 5 time, count(*) as occurences
from space
group by time
order by occurences desc;

-- Rocket Reliability
SELECT Rocket,
	sum(case when MissionStatus = 1 then 1 else 0 end) as total_successful_launches,
	count(MissionStatus) as total_number_missions,
	ROUND(CASE WHEN count(Rocket) > 0 THEN
	(CAST(SUM(CASE WHEN Missionstatus = 1 THEN 1 ELSE 0 END) AS FLOAT) /COUNT(Rocket)) * 100 ELSE 0 END, 2) as rocket_reliability
	from space
	group by Rocket
	order by rocket_reliability desc, total_successful_launches desc;

-- To change the Data type of a column
	Alter table space
	alter column missionstatus int;


-- Countries and their launch frequencies	
SELECT  REVERSE(SUBSTRING(REVERSE(Location), 1, CHARINDEX(',', REVERSE(location)) -1)) AS Country,
COUNT(*) AS frequency
FROM space
GROUP BY REVERSE(SUBSTRING(REVERSE(Location), 1, CHARINDEX(',', REVERSE(location)) -1))
ORDER BY frequency DESC;


-- Company with their respective success rates
SELECT company,
	COUNT(*) AS Total_missions,
	SUM(CASE WHEN missionstatus = 1 THEN 1 ELSE 0 END) AS Successful_missions,
	ROUND(CASE WHEN count(*) > 0 THEN
	(CAST(SUM(CASE WHEN Missionstatus =1 THEN 1 ELSE 0 END) AS FLOAT) /COUNT(*)) * 100 ELSE 0 END, 2) AS Success_rate
	FROM space
	GROUP BY company
	ORDER BY Success_rate desc, Total_missions desc;

-- Frequently used Rocket Type
SELECT rocket, count(*) as rocket_count,
	SUM(CASE WHEN missionstatus = 1 THEN 1 ELSE 0 END) AS Success
	From space
	GROUP BY rocket
	ORDER BY rocket_count DESC;


SELECT * FROM space;

-- Total number of retired and Active status for each rocket
SELECT Rocket,
	COUNT(*) AS rocket_count,
	SUM(CASE WHEN Rocketstatus = 'retired' then 1 else 0 end) as retired_rockets,
	SUM(CASE WHEN Rocketstatus = 'active' then 1 else 0 end) as active_rockets
	FROM space
	GROUP BY Rocket
	ORDER BY rocket_count DESC;

-- Total number of retired and Active rocket
SELECT RocketStatus,
	COUNT(*) AS rocket_count
		FROM space
		GROUP BY  RocketStatus
		ORDER BY rocket_count DESC;

-- Total Number of missions per Year
SELECT Year, count(*) as number_of_missions
FROM space
Group by Year
ORDER BY count(*) desc;

-- Total number of successful and unssucessful missions
SELECT 
	CASE 
		WHEN missionstatus = 1 THEN 'Successful' 
		 WHEN Missionstatus = 0 Then 'Unsuccessful' 
		  ELSE 'others'
		   END AS Mission_status ,
			count(*) AS mission_count
FROM space
GROUP BY 
	CASE 
		WHEN missionstatus = 1 THEN 'Successful' 
		 WHEN Missionstatus = 0 Then 'Unsuccessful' 
		  ELSE 'others'
		   END
ORDER by mission_count;

-- To the Total price each company spent
SELECT company,SUM(Price) as total_amount
from space
group by company
order by sum(price) desc;
```

### Insights
The analsysis results are summarized as follows;
1. Falcon 9 Block 5 rocket type has 111 total missions and 111 total successful missions, giving the highest 100% rocket reliability
2. Most mission took place at midnight
3. USA has the highest number of total missions of 1467
4. Blue Origin company has the highest success rate with total of 21 missions and 21 successful missions with 100% success rate
5. Cosmos-3M (11K65M) is the most frequently used rocket with a total of 446 times
6. Most retired rocket type is Cosmos-3M (11K65M) while the most active rocket type is Falcon 9 Block 5
7. There are 3618 retired rockets and 1008 active rockets
8. 2021 has the highest numer of mission while 1957 has the least number of missions
9. There are 4160 successful missions and 466 unsuccessful missions
10. NASA company has the highest total amount spent on missions











