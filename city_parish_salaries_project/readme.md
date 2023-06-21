## Project Description
This project is intended to address the following key questions using the [City-Parish Salary Data supplied by the East Baton Rouge city-parish website](https://data.brla.gov/Government/City-Parish-Employee-Annual-Salaries/g9vh-zeiw):   

* What departments lead in average base pay (annual salary not including additional earning allowances)?
* What departments lead in average gross pay (base pay plus overtime plus any extra earnings)?
* What departments lead in total overtime hours?
* What departments lead in total employee headcount?
* What positions pay the highest average base pay?
* What positions pay the highest average gross pay?
* How have base pay and gross pay trended over the past five years?

## Summary of Findings
1. The Council Budget Office has the highest average salary ($62k) when not accounting for number of employees. When limited to departments with at least 10 employees, Information Services has the highest average base pay ($55k). When limited to at least 100 employees, the fire department has the highest average base pay ($52k).
2. The Council Budget Office ($64k) and fire department ($63k) also lead for average gross pay.
3. The police department led all departments in total OT hours from 2018 to 2020 before falling behind the fire department for 2021 and 2022.
4. The police department led all departments in total employees, accounting for 18-20% of all government employees across all five years.
5. The mayor led all individual gross/base salaries with $175k, followed by the police chief ($149k).

## About the Data
The dataset encompasses salary for all departments of the East Baton Rouge City-Parish from 2018 to 2022 (24,346 unique rows). The dataset included a data dictionary which defined all fields. The data was last updated 1/19/23 at the time of export. 

## Data Cleaning
After uploading hte dataset to a local PostgreSQL server, I reviewed for problematic data characteristics: 

* Obtained NULL counts for all columns
```sql
/*Obtain null counts for all columns*/
SELECT 'last_name_null_count', COUNT(CASE WHEN last_name IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'first_name_null_count', COUNT(CASE WHEN first_name IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'middle_init_null_count', COUNT(CASE WHEN middle_init IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'department_number_null_count', COUNT(CASE WHEN department_number IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'department_name_null_count', COUNT(CASE WHEN department_name IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'pay_location_code_null_count', COUNT(CASE WHEN pay_location_code IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'pay_location_description_null_count', COUNT(CASE WHEN pay_location_description IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'job_code_null_count', COUNT(CASE WHEN job_code IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'job_title_null_count', COUNT(CASE WHEN job_title IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'current_hire_date_null_count', COUNT(CASE WHEN current_hire_date IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'employment_end_date_null_count', COUNT(CASE WHEN employment_end_date IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'employment_status_null_count', COUNT(CASE WHEN employment_status IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'base_pay_null_count', COUNT(CASE WHEN base_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'gross_pay_null_count', COUNT(CASE WHEN gross_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'total_ot_hours_null_count', COUNT(CASE WHEN total_ot_hours IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'total_ot_pay_null_count', COUNT(CASE WHEN total_ot_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'severance_pay_null_count', COUNT(CASE WHEN severance_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'shift_differential_pay_null_count', COUNT(CASE WHEN shift_differential_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'pay_adjustments_null_count', COUNT(CASE WHEN pay_adjustments IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'car_allowance_null_count', COUNT(CASE WHEN car_allowance IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'meal_allowance_null_count', COUNT(CASE WHEN meal_allowance IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'state_supplemental_jp_null_count', COUNT(CASE WHEN state_supplemental_jp IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'education_pay_null_count', COUNT(CASE WHEN education_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'transfer_compensation_null_count', COUNT(CASE WHEN transfer_compensation IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'special_assignment_null_count', COUNT(CASE WHEN special_assignment IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'engineer_license_null_count', COUNT(CASE WHEN engineer_license IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'police_suit_allowance_null_count', COUNT(CASE WHEN police_suit_allowance IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'workers_comp_null_count', COUNT(CASE WHEN workers_comp IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'employee_reimbursement_null_count', COUNT(CASE WHEN employee_reimbursement IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'state_2_percent_null_count', COUNT(CASE WHEN state_2_percent IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'prison_assignment_null_count', COUNT(CASE WHEN prison_assignment IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'hazardous_pay_null_count', COUNT(CASE WHEN hazardous_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'employee_refund_null_count', COUNT(CASE WHEN employee_refund IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'longevity_null_count', COUNT(CASE WHEN longevity IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'commander_pay_null_count', COUNT(CASE WHEN commander_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'field_training_pay_null_count', COUNT(CASE WHEN field_training_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'aviation_pay_null_count', COUNT(CASE WHEN aviation_pay IS NULL THEN uniqueid END) FROM cps_data
UNION ALL
SELECT 'uniqueid_null_count', COUNT(CASE WHEN uniqueid IS NULL THEN uniqueid END) FROM cps_data
```

* Ruled out active employees with populated end of employment dates/inactive employees without termination dates (19 rows were discovered and removed from the dataset prior to analysis

```sql
/*Rule out faulty begin date/end date entries*/
SELECT 
	*
FROM 
	cps_data
WHERE 
	employment_end_date - current_hire_date < 0;

/*Identify instances where employees are inactive but termination dates weren't supplied*/
with t1 AS (SELECT 
	uniqueid
	,year
	,current_hire_date as hire_date
	,employment_end_date
	,employment_status
	,DENSE_RANK() OVER(PARTITION BY uniqueid ORDER BY year) as years_worked
	,job_title
	,department_name
	,base_pay
	,gross_pay
FROM
	cps_data)
	
SELECT 
	* 
FROM 
	t1
WHERE
	employment_end_date IS NULL 
	AND employment_status = 'I';

/*Identify instances where employee was active but employment end date was entered*/
with t1 AS (SELECT 
	uniqueid
	,year
	,current_hire_date as hire_date
	,employment_end_date
	,employment_status
	,DENSE_RANK() OVER(PARTITION BY uniqueid ORDER BY year) as years_worked
	,job_title
	,department_name
	,base_pay
	,gross_pay
FROM
	cps_data)
	
SELECT 
	* 
FROM 
	t1
WHERE
	employment_end_date IS NOT NULL 
	AND employment_status = 'A';
```
* Ruled out mismatched job title/code combinations

```sql
/*Find instances where job titles match but job codes do not*/
SELECT 
	DISTINCT(cps.job_title)
	,cps.job_code
FROM 
	cps_data cps JOIN 
	cps_data cps1 ON cps.job_title = cps1.job_title
WHERE cps.job_code != cps1.job_code;

/*Obtain counts of employees with mismatched job_codes and titles*/
SELECT
	COUNT(
		CASE WHEN job_code = 1474 THEN uniqueid END) as crime_stat1
	,COUNT(
		CASE WHEN job_code = 1484 THEN uniqueid END) as crime_stat2
	,COUNT(
		CASE WHEN job_code = 5227 THEN uniqueid END) as prog_coordinator1
	,COUNT(
		CASE WHEN job_code = 6040 THEN uniqueid END) as prog_coordinator2
FROM
	cps_data;
```
* Ensured department names and codes are consistent
