# Table of Content


### Entity Relationship Diagram (ERD)
![ERD](https://user-images.githubusercontent.com/121811651/211201186-68c02ff0-7b6f-445e-80e5-d97f58bbd79b.png)

### Information Schema
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
### SELECT DISTINCT 
Select unique records from the **state** column
````sql
SELECT DISTINCT state
FROM general_hospital.patients
ORDER BY state
````
![distinct on column](https://user-images.githubusercontent.com/121811651/211205030-68548fa8-2a7d-4786-96fe-eb542b214b38.png)

#### SELECT DISTINCT COUNT
Counting the number of unique records and renaming the output column
````sql
SELECT DISTINCT COUNT(city) AS distinct_city_count
FROM general_hospital.patients
````
![SELECT DISTINCT COUNT](https://user-images.githubusercontent.com/121811651/211205177-62028e8e-3c3d-408e-82a1-bd57beaf0fcf.png)

#### LIKE Operator
Filtering data which contains specific strings and numbers
````sql
SELECT *
FROM general_hospital.hospitals
WHERE zip_cd LIKE '%111%'
AND city = 'Philadelphia'
AND address_number <=1000
````
![like where](https://user-images.githubusercontent.com/121811651/211206075-ac8e63bd-c7c6-4311-b606-1ce7310d1e2f.png)
##### _Alternative way to write the above query_
````sql
SELECT *
FROM general_hospital.hospitals
WHERE zip_cd LIKE '%111%'
AND city = 'Philadelphia'
AND address_number BETWEEN 0 and 1000
````
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











