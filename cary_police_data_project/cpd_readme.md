## Project Description
In 2022, Cary was ranked as America’s “safest” small city by the [background check company Good Hire](https://www.goodhire.com/resources/articles/safest-and-least-safe-cities-in-america/). They reviewed *FBI Crime Data — property, violent, and society crime — and ranked each city by its rate of offenses per 1,000 people for each of the three offense types. GoodHire then assigned a total rank that weighted crimes against persons and crimes against property at 40% and crimes against society at 20%.*

As a Cary resident, I was interested in answering the following questions: 

* [What is the overall trend over the past six years (three pre-COVID + three during-COVID)?](#what-is-the-overall-trend-from-the-past-six-years) 
* [What types of police incidents are most commonly reported in Cary?](#what-types-of-incidents-are-most-commonly-reported)
* [How do the top incident types compare in frequency?](#how-do-the-top-incidents-types-compare)

# Summary of Findings
1. Cary police incidents have declined 21% between 2017 and 2022. The largest decline occurred from 2021 to 2022 (-12% YoY).   
2. The most common category of incident was 'all other,' which consistently accounted for roughly 60% of incidents across all six years. 
3. Although motor vehicle thefts composed the smallest absolute proportion of incidents, they had the largest percentage of increase (~300%) among crime categories.
4. The most common crime types were larceny and fraud. Combined, they accounted for roughly 40% of all incidents, with larceny roughly doubling different types of fraud.  

## About the Data 
The analysis was based on the [Town of Cary Police Incident dataset](https://data.townofcary.org/explore/dataset/cpd-incidents/export/?disjunctive.crime_category&disjunctive.crime_type&disjunctive.crimeday&disjunctive.district&disjunctive.offensecategory&disjunctive.violentproperty&disjunctive.total_incidents&disjunctive.year&dataChart=eyJxdWVyaWVzIjpbeyJjaGFydHMiOlt7InR5cGUiOiJjb2x1bW4iLCJmdW5jIjoiQ09VTlQiLCJ5QXhpcyI6InRvdGFsX2luY2lkZW50cyIsImNvbG9yIjoiIzJCM0Y1NiIsInNjaWVudGlmaWNEaXNwbGF5Ijp0cnVlfV0sInhBeGlzIjoieWVhciIsIm1heHBvaW50cyI6IiIsInRpbWVzY2FsZSI6IiIsInNvcnQiOiIiLCJzZXJpZXNCcmVha2Rvd25UaW1lc2NhbGUiOiIiLCJjb25maWciOnsiZGF0YXNldCI6ImNwZC1pbmNpZGVudHMiLCJvcHRpb25zIjp7ImRpc2p1bmN0aXZlLmNyaW1lX2NhdGVnb3J5Ijp0cnVlLCJkaXNqdW5jdGl2ZS5jcmltZV90eXBlIjp0cnVlLCJkaXNqdW5jdGl2ZS5jcmltZWRheSI6dHJ1ZSwiZGlzanVuY3RpdmUuZGlzdHJpY3QiOnRydWUsImRpc2p1bmN0aXZlLm9mZmVuc2VjYXRlZ29yeSI6dHJ1ZSwiZGlzanVuY3RpdmUudmlvbGVudHByb3BlcnR5Ijp0cnVlLCJkaXNqdW5jdGl2ZS50b3RhbF9pbmNpZGVudHMiOnRydWUsImRpc2p1bmN0aXZlLnllYXIiOnRydWV9fX1dLCJ0aW1lc2NhbGUiOiIiLCJkaXNwbGF5TGVnZW5kIjp0cnVlLCJhbGlnbk1vbnRoIjp0cnVlfQ%3D%3D). Some of the key attributes that the data encompasses are: 

* Beginning/end dates and times of each incident 
* Crime category and type (more or less a sub-category) 
* Geographical coordinates. 
* A column devoted to the UCR (uniform crime reporting) code.   
<sub>*The FBI’s Uniform Crime Reporting Program is intended to standardize crime reporting across jurisdictions to aid in the statistical analysis of crime nationwide.</sub>    

Prior to data cleaning, I created a new table composed of only the columns pertinent to analysis, which was restricted to the three “pre-COVID” years (2017-2019) and three COVID years (2020-2022).   

This narrowed set included data for **28,395 unique police incidents**.

```sql
/*Select subset of columns for analysis and add them to a new table, recategorize crime types 
within the 'all other' category to assist aggregation*/
SELECT
	ucr
	,record
	,lower(crime_category) as category
	,lower(crime_type) as crime_type
	,begin_date_of_occurrence 
	,end_date_of_occurrence
	,CAST(begin_time_of_occurrence as time)
	,CAST(end_time_of_occurrence as time) 
	,lower(crime_day) AS day_of_week
	,location
	,lat
	,lon
INTO
	cpd_data2
FROM
	cpd_data
WHERE
	EXTRACT(year FROM end_date_of_occurrence) in (2017, 2018, 2019, 2020, 2021, 2022);
```
## Data Cleaning
After uploading the data table to a local PostgreSQL server, I reviewed for the following potentially problematic data characteristics: null values and missing values. Mixed and invalid data types were addressed when the CSV was initially loaded into Postgres.  
```sql
/*Identify NULL values from each column by subtracting column counts from all counts.*/ 
SELECT
	COUNT(lon) - COUNT(*) AS lon_null_count
	,COUNT(record) - COUNT(*) AS record_null_count
	,COUNT(begin_date_of_occurrence) - COUNT(*) AS begin_date_of_occurrence_null_count
	,COUNT(end_date_of_occurrence) - COUNT(*) AS end_date_of_occurrence_null_count
	,COUNT(begin_time_of_occurrence) - COUNT(*) AS begin_time_of_occurrence_null_count
	,COUNT(end_time_of_occurrence) - COUNT(*) AS end_time_of_occurrence_null_count
	,COUNT(lat) - COUNT(*) AS lat_null_count
	,COUNT(category) - COUNT(*) AS category_null_count
	,COUNT(new_crime_type) - COUNT(*) AS new_crime_type_null_count
	,COUNT(ucr) - COUNT(*) AS ucr_null_count
	,COUNT(day_of_week) - COUNT(*) AS day_of_week_null_count
	,COUNT(location) - COUNT(*) AS location_null_count
FROM
  cpd_data2;
```
```sql
/*Select counts by year of NULL values in rows: latitude, longitude, location, 
or begin time of occurrence values per previous query*/
SELECT 
	,EXTRACT(year FROM end_date_of_occurrence), 
	,COUNT(CASE WHEN begin_time_of_occurrence IS NULL 
	,OR lat IS NULL OR lon IS NULL or location IS NULL THEN record END) as null_count
FROM
	cpd_data2
GROUP BY
	EXTRACT(year FROM end_date_of_occurrence)
```

```sql
/*Rule out blank values in all text/varchar columns*/
SELECT
	*
FROM
	cpd_data2
WHERE
	category = '' 
	OR new_crime_type = '' 
	OR ucr = '' 
	OR day_of_week = '' 
	OR location = '';
```

I discovered **147 rows with null values for the geographic variables latitude, longitiude, and location and one row where the begin time of occurrence was null**. Because other valuable categorical data was still present for these rows, I retained them but noted that they would need to be excluded for any future geographical analysis. No columns had missing values. 

Once null and blank values were addressed, I reviewed the record numbers column for duplicate values, which I’d established as the ID column for each unique incident.  

# Analysis
## What Is the Overall Trend from the Past Six Years? 
**Overall recorded police incidents saw a gradual decline between from 2017 to 2022, with a decline of -21%**. 

![image4](https://github.com/rp2323/cary_nc_crime_data/assets/126728422/53676e40-c71a-4213-be97-caf04173b76c)
<sub>*graph created in Excel</sub>  

![image5](https://github.com/rp2323/cary_nc_crime_data/assets/126728422/9081bfb0-b066-49c9-9e44-506214283dbf)
<sub>*graph created in Excel</sub>  

## What Types of Incidents Are Most Commonly Reported?
When broken down by category, **the incident category “all other” accounted for 58-62%** of police incidents while **larceny accounted for 26-29%**: 

![image2](https://github.com/rp2323/cary_nc_crime_data/assets/126728422/b0f4c41d-a704-473f-baf4-3ed7561b2ed2)
<sub>*graph created in Excel</sub>  

**Incidents involving motor vehicle theft increased almost 300%** over the six-year period: 

![motor_v_theft_by_year](https://github.com/rp2323/cary_nc_crime_data/assets/126728422/68ddff54-ea47-4171-9987-25c806bcff81)
<sub>*graph created in Excel</sub>  

Otherwise, the trends amongst the major crime categories remained stable. 

## What Crime Types Composed ‘All other’ Incidents?
Identifying trends among the sub-types within the 'all other' category required further standardization/cleaning of the data. 

The distinct UCR codes helped differentiate some of the incidents contained within the category. 

Example: 
* 13B - *simple assault*
* 13C - *intimidation*

However, some incidents with the same UCR (26A) code were logged with distinct crime_type descriptions: 
* *fraud - confidence games/larceny by trick*
* *fraud - failure to return hired property*
* *fraud - obtaining property by false pretense*
* *fraud - pharmaceutical fraud*

To facilitate the analysis, I used XLOOKUP in Excel to standardize the crime type entries so that similar incident types would be grouped together. (For the example above, the four crime types were standardized as simply ‘fraud.’) Once the recategorization was complete I uploaded the cpd_data2 table data to Postgres as 'cpd_data3' with an added column of standardized incident types.

Those adjustments helped draw insights from the 'all other' category, with fraud consistently composing the majority of the category's incidents, followed by destruction/damage/vandalism of property.  

```sql
/*Select record counts grouped by new crime type and year*/
SELECT 
	new_crime_type
	,EXTRACT(year FROM end_date_of_occurrence) as year
	,COUNT(record)
FROM 
	cpd_data3
GROUP BY 
	new_crime_type, year;
```

![all_other_heatmap](https://github.com/rp2323/cary_nc_crime_data/assets/126728422/5ecf0bc1-a2b8-488b-b9f5-900ef6afe819)

## How Do the Top Incidents Types Compare?
Once I was able to establish that different types of fraud were the most common 'all other' incident type, I was curious how they compared with the most common non-'all other' type, larceny. *While larceny maintained a steady percentage of 26-29%, fraud incidents accounted for 11-15%, or roughly half of the lareceny incidents.*

![fraud_vs_larceny](https://github.com/rp2323/data_analysis_portfolio/assets/126728422/0987d3b4-857f-4f7b-93c2-d0feb92fe559)
