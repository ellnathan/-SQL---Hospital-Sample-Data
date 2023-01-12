# Table of Content

### Entity Relationship Diagram (ERD)
The ERD, also known as Entity Relationship Diagram, provides a holistic visual representation of the table structure and relationships, along with the different data types for columns. Before working on a set of data, it can be a useful diagram to view to provide a better understand of where the location of different columns, and in which tables they reside in.

![ERD](https://user-images.githubusercontent.com/121811651/211201186-68c02ff0-7b6f-445e-80e5-d97f58bbd79b.png)
___________________
### Information Schema
Like the ERD, the information schema provides metadata about the databases but in the format of a table.  

![tables](https://user-images.githubusercontent.com/121811651/211203753-60c9bb38-7630-4a76-9d46-58bc6159fcda.png)
````sql
SELECT *
FROM information_schema.tables
````
![columns](https://user-images.githubusercontent.com/121811651/211203760-5a72909d-4e04-4b4e-b6af-c062b4108e94.png)
````sql
SELECT *
FROM information_schema.columns
````
___________________
### SELECT DISTINCT 
The DISTINCT statement returns unique records. In this query, the distinct operator filters on the **state** column and will eliminate any duplicate values in the list. 
Select unique records from the **state** column
````sql
SELECT DISTINCT state
FROM general_hospital.patients
ORDER BY state
````
![distinct on column](https://user-images.githubusercontent.com/121811651/211205030-68548fa8-2a7d-4786-96fe-eb542b214b38.png)
__________________
#### SELECT DISTINCT COUNT
The DISTINCT count will perform a similar action as above, but as an aggregate function is mentioned, it will count all the unique records, and output the results in a table called distinct_city_count since the AS command has been used to rename the table, but this alias will only exist for the duration of the query.
````sql
SELECT DISTINCT COUNT(city) AS distinct_city_count
FROM general_hospital.patients
````
![SELECT DISTINCT COUNT](https://user-images.githubusercontent.com/121811651/211205177-62028e8e-3c3d-408e-82a1-bd57beaf0fcf.png)
__________________
#### LIKE Operator
The LIKE operator has been used to search for a specific pattern within the zip_cd column. Here in the query, the wildcard characters have been places at the beginning and ending of the numbers and this will only return zip codes which contain '111'. In additional, the output of the query returns everything from the hospital table where the city is Philadelphia, and the address number is equalled to or less than 1000. Everything else will be disregarded. Alternatively, the query can be written using BETWEEN for the address_number instead of the <=. This will also return the same output.
````sql
SELECT *
FROM general_hospital.hospitals
WHERE zip_cd LIKE '%111%'
AND city = 'Philadelphia'
AND address_number <=1000 -- AND address_number BETWEEN 0 and 1000
````
![like where](https://user-images.githubusercontent.com/121811651/211206075-ac8e63bd-c7c6-4311-b606-1ce7310d1e2f.png)

#### SUBQUERY Operator
Retrieving the count for records based on filters applied in the nested query, and outer query
````sql
SELECT COUNT (*) AS patient_count
FROM (
	SELECT * 
	FROM general_hospital.patients 
	WHERE date_of_birth >= '1995-01-01' 
	ORDER BY master_patient_id
	) p 
WHERE p.primary_language != 'English' 
````
![Screenshot 2022-12-27 000100](https://user-images.githubusercontent.com/121811651/211206479-954e6ae4-0f54-4064-9bdc-984b417ea5d0.png)

#### IN Operator
Filtering on specific records within the patients table
````sql
SELECT * 
FROM general_hospital.patients
WHERE master_patient_id in 
(100821,100822,100823,100824,100825,100826,100827,100828,100829,100830,100831,100832,100833,100834,100835,100836)
````
![IN](https://user-images.githubusercontent.com/121811651/211208684-310fbaaf-e9b8-4740-b3c5-fae0331c1cde.png)

##### NOT IN
````sql
SELECT * 
FROM general_hospital.patients
WHERE county NOT IN ('Los Angeles', 'Dayton', 'Lafayette', 'Columbus') 
````
![NOT IN](https://user-images.githubusercontent.com/121811651/211208716-b9c84861-fb5e-488d-acc7-f81dccaccf6a.png)

#### CTE (Common Table Expression)
Creating temporary tables and combining both into one output table 
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
![UNION  And not UNION ALL](https://user-images.githubusercontent.com/121811651/211220814-c63845e0-ab20-463f-a145-f1d733cbe6b0.png)

##### CTE (Common Table Expression) - Additional Example
Creating 2 temporary tables, joins and count 
````sql
WITH top_countries_count AS (
				SELECT
				county, count (*) as num_patients
				FROM general_hospital.patients
				GROUP BY county
				HAVING count(*) >1700),			
	county_patients AS (
				SELECT 
				pa.master_patient_id, pa.county
				FROM general_hospital.patients pa
				INNER JOIN top_countries_count tc 
				ON pa.county = tc.county)			
SELECT 
pa.county, count(se.surgery_id) AS num_surgeries
FROM general_hospital.surgical_encounters se
INNER JOIN county_patients pa 
ON se.master_patient_id = pa.master_patient_id
GROUP BY pa.county
````
![Screenshot 2022-12-28 002714](https://user-images.githubusercontent.com/121811651/211221503-788d8c1b-1d82-4908-b4e3-b4b68e27b9e2.png)


##### CTE (Common Table Expression) - Additional Example
Temporary table with filters applied
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
![Screenshot 2022-12-27 233250](https://user-images.githubusercontent.com/121811651/211221102-b93113f6-2aa4-4be6-9fa4-329362ae6672.png)

##### CTE (Common Table Expression) - Additional Example
JOINs
````sql
SELECT * 
FROM general_hospital.surgical_encounters se
JOIN general_hospital.patients pa
ON se.master_patient_id = pa.master_patient_id
WHERE surgical_admission_date 
BETWEEN '2016-11-01' AND '2016-11-30'
AND date_of_birth >= '1990-01-01'
````
![Screenshot 2022-12-27 154937](https://user-images.githubusercontent.com/121811651/211221914-41f60449-5728-42c2-831c-93a29b9088f6.png)

##### CTE (Common Table Expression) - Additional Example
CTE and JOINs
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
![Screenshot 2022-12-27 154937](https://user-images.githubusercontent.com/121811651/211221810-b1d81420-70b4-414c-9e11-1aef20aeb604.png)

#### JOINs
Inner joins to pull data from different tables
````sql
SELECT se.master_patient_id, surgery_id, diagnosis_description
FROM general_hospital.surgical_encounters se
JOIN general_hospital.patients pa
ON se.master_patient_id = pa.master_patient_id
WHERE surgical_admission_date BETWEEN '2016-11-01' AND '2016-11-30'
AND date_of_birth >= '1990-01-01'
````
![image](https://user-images.githubusercontent.com/121811651/211222597-61600dba-5ca4-4255-aadd-7c01ab6afef1.png)


