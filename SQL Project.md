# Table of Content


## Entity Relationship Diagram (ERD)
![ERD](https://user-images.githubusercontent.com/121811651/211201186-68c02ff0-7b6f-445e-80e5-d97f58bbd79b.png)

## Information Schema
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
## SELECT DISTINCT 
Select unique records from the **state** column
````sql
SELECT DISTINCT state
FROM general_hospital.patients
ORDER BY state
````
![distinct on column](https://user-images.githubusercontent.com/121811651/211205030-68548fa8-2a7d-4786-96fe-eb542b214b38.png)

### SELECT DISTINCT COUNT
Counting the number of unique records and renaming the output column
````sql
SELECT DISTINCT COUNT(city) AS distinct_city_count
FROM general_hospital.patients
````
![SELECT DISTINCT COUNT](https://user-images.githubusercontent.com/121811651/211205177-62028e8e-3c3d-408e-82a1-bd57beaf0fcf.png)






