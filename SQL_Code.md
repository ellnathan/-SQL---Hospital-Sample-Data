
### Information Schema
````sql
SELECT *
FROM information_schema.tables --replace tables with columns to view columns
````
### SELECT DISTINCT 
````sql
SELECT DISTINCT state
FROM general_hospital.patients
ORDER BY state
````
#### SELECT DISTINCT COUNT
````sql
SELECT DISTINCT COUNT(city) AS distinct_city_count
FROM general_hospital.patients
````
#### LIKE Operator
````sql
SELECT *
FROM general_hospital.hospitals
WHERE zip_cd LIKE '%111%'
AND city = 'Philadelphia'
AND address_number <=1000 -- AND address_number BETWEEN 0 and 1000
````
#### SUBQUERY Operator
````sql
SELECT COUNT (*) AS patient_count
FROM (
	SELECT * 
	FROM general_hospital.patients 
	WHERE date_of_birth >= '1995-01-01' 
	) p 
WHERE p.primary_language != 'English' 
````
#### IN Operator
````sql
SELECT * 
FROM general_hospital.patients
WHERE master_patient_id in 
(100821,100822,100823,100824,100825,100826,100827,100828,100829,100830,100831,100832,100833,100834,100835,100836)
````
##### NOT IN
````sql
SELECT * 
FROM general_hospital.patients
WHERE county NOT IN ('Los Angeles', 'Dayton', 'Lafayette', 'Columbus') 
````
#### CTE (Common Table Expression)
````sql
WITH tb1 AS (
		SELECT resource_name, resource_type, ROUND (resource_cost, 2) AS rounded_number
		FROM general_hospital.surgical_costs
		WHERE lower(resource_name) = 'spec100'),
tb2 AS (
    		SELECT resource_name, resource_type, ROUND (resource_cost, 2) AS rounded_number
		FROM general_hospital.surgical_costs
		WHERE lower(resource_name) = 'paracetamol')
			
SELECT * FROM tb1
UNION 
SELECT * from tb2
ORDER BY rounded_number 
````
##### CTE (Common Table Expression) - Additional Example #2
````sql
WITH top_counties_count AS (
				SELECT
					county, count (*) as num_patients
				FROM general_hospital.patients
				GROUP BY county
				HAVING count(*) >1700),			
	county_patients AS (
				SELECT 
					pa.master_patient_id, pa.county
				FROM general_hospital.patients pa
				INNER JOIN top_counties_count tc 
				ON pa.county = tc.county)			
SELECT 
	pa.county, count(se.surgery_id) AS num_surgeries
FROM general_hospital.surgical_encounters se
INNER JOIN county_patients pa ON 
	se.master_patient_id = pa.master_patient_id
GROUP BY pa.county
````
##### CTE (Common Table Expression) - Additional Example #3
````sql
WITH adult_patients AS (
			SELECT * 
			FROM general_hospital.patients
			WHERE date_of_birth <='2004-01-01'
			)
SELECT *
FROM adult_patients
WHERE lower(city) = 'bristol'
````
##### CTE (Common Table Expression) - Additional Example #4
````sql
SELECT * 
FROM general_hospital.surgical_encounters se
JOIN general_hospital.patients pa
ON se.master_patient_id = pa.master_patient_id
WHERE surgical_admission_date 
BETWEEN '2016-11-01' AND '2016-11-30'
AND date_of_birth >= '1990-01-01'
````
##### CTE (Common Table Expression) - Additional Example #5
````sql
WITH tb1 AS (
		SELECT pa.master_patient_id, surgical_type, round(total_cost,2) AS total_cost_rounded, round(total_profit,2) AS total_profit_rounded
		FROM general_hospital.patients pa
		JOIN general_hospital.surgical_encounters se
		ON pa.master_patient_id = se.master_patient_id
		WHERE surgical_type = 'NonElective') 
			
SELECT *, total_cost_rounded+total_profit_rounded as revenue
FROM tb1
WHERE total_cost_rounded >=1000 
AND total_profit_rounded >= 1000
````
##### Window Function (RANK) 
````sql
SELECT
	account_id,
	primary_icd 
	total_account_balance,
	RANK()
	      OVER (partition by primary_icd 
		   ORDER BY total_account_balance desc) 
	as account_rank_by_icd
FROM general_hospital.accounts
````





