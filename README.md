# Deforestation Project - An SQL Project.
![deforestation](https://github.com/kdm1411/Project-Deforestation./assets/150349346/ae3aadd3-a903-42ef-9c2d-7a538b0589c0)


### Table of Contents.

- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools](#tools)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results](#results)
- [Recomendations](#recomendations)
- [Limitations](#limitations)
- [References](#references)
  
### Project Overview.
---

This data analysis project aims to provide insight into deforestation across the globe between 1990-2016. By analyzing various aspects of deforestatin data, we seek to identify trends, makes data driven decisions, and gain deeper understanding of deforestation across the world.

### Data Source.
---

Deforestation Data: The dataset used for this analysis are 'Forest_Area.csv', 'Land_Area.csv', 'Region.csv' file containing detail information of deforestation across the world between 1990-2016.

### Tools.
---

- SQL Server - Data Cleaning, Data Analysis & Creating Reports

### Data Cleaning.
---

In the initial Data prepartion phase, we performed the following tasks:

1. Data loading and inspections
2. Handling null values
```sql
SELECT DISTINCT COUNTRY_NAME FROM Forest_Area,
WHERE FOREST_AREA_SQKM IS NULL
SELECT FOREST_AREA_SQKM, CASE WHEN  FOREST_AREA_SQKM IS NULL THEN 0 ELSE FOREST_AREA_SQKM END FROM Forest_Area,
UPDATE Forest_Area SET forest_area_sqkm = CASE WHEN  FOREST_AREA_SQKM IS NULL THEN 0 ELSE FOREST_AREA_SQKM END;
```
```sql
SELECT DISTINCT TOTAL_AREA_SQ_MI FROM Land_Area
WHERE TOTAL_AREA_SQ_MI IS NULL,
SELECT TOTAL_AREA_SQ_MI, CASE WHEN TOTAL_AREA_SQ_MI IS NULL THEN 0 ELSE TOTAL_AREA_SQ_MI END FROM Land_Area,
UPDATE Land_Area SET TOTAL_AREA_SQ_MI = CASE WHEN TOTAL_AREA_SQ_MI IS NULL THEN 0 ELSE TOTAL_AREA_SQ_MI END;
```
4. Check outliers
```sql
delete from Forest_Area where COUNTRY_NAME = 'world';
```
```sql
delete from Land_Area where COUNTRY_NAME = 'world';
```

### Exploratory Data Analysis.
---

EDA involves exploring the deforestation data to answer key questuions, such as:

- What are the total number of countries involved in deforestation?
- Show the income groups of countries having total area ranging from 75,000 to 150,000 square meter?
- Calculate average area in square miles for countries in the 'upper middle income region'. Compare the result with the rest of the income categories
- Determine the total forest area in square km for countries in the 'high income' group. Compare result with the rest of the income categories.
- Show countries from each region(continent) having the highest total forest areas.

### Data Analysis.
---

```sql
select count(country_name) TOTAL_NO_OF_COUNTRIES_INVL_DEFORESTATION from (
select country_name, count(year) as year_without_deforestation from
(select country_name, year, forest_area_sqkm,
rank() over(partition by country_name order by forest_area_sqkm) rank from forest_area) u
where rank = 2
group by country_name) B
where year_without_deforestation < 27;
```
![Numbers of Countries involves in deforestation](https://github.com/kdm1411/Project-Deforestation./assets/150349346/ebd4db99-efe0-4818-8b8b-041defd05664)


```sql
SELECT  DISTINCT L.COUNTRY_NAME, R.income_group
FROM Region R join Land_Area L
on r.country_code = L.country_code
where total_area_sq_mi between '75000' and '150000';
```
![income groups of countries having total area ranging from 75,000 to 150,000 square meter](https://github.com/kdm1411/Project-Deforestation./assets/150349346/ae1ed0f8-1ed6-47fb-bf3c-bd2b8f9b6589)

```sql
select income_group, avg(total_area_sq_mi) Average_Area_in_Suqare_Miles from
(select  R.country_name, R.income_group,R.Region, L.total_area_sq_mi from Region R join Land_Area L on R.country_code = L.country_code) as T
group by income_group 
having income_group in ('upper middle income','low income','high income','lower middle income');
```
![Average area in square miles for countries Incomr groups](https://github.com/kdm1411/Project-Deforestation./assets/150349346/2038c009-21e2-4e5b-b402-ec7f62dd2fb1)

```sql
select income_group, sum(forest_area_sqkm) as Total_forest_area_in_sqm from
(select R.country_name, R.income_group, F.forest_area_sqkm from Forest_Area F join Region R on F.country_code = R.country_code) as T
group by income_group
having income_group in ('high income','low income','upper middle income','lower middle income');
```
![total forest area in square km for countries income groups](https://github.com/kdm1411/Project-Deforestation./assets/150349346/df19e618-2c18-4631-959a-2b36de51802d)

```sql
with main_cte as
(select distinct r.country_name, Region, f.forest_area_sqkm,
dense_rank() over(partition by r.region order by f.forest_area_sqkm desc) as rank
from region r join Forest_area f on r.country_code = f.country_code)
select * from main_cte
where rank = 1
```
![countries from each region(continent) having the highest total forest areas](https://github.com/kdm1411/Project-Deforestation./assets/150349346/8f11c697-4518-454d-899a-862ae408e7e9)

### Results.
---

 The analysis results are summarized as follows:
 
 1. The total number of countries invlove in deforestation is 144, some countries do not deforeset for the 27 years e.g Afghanistan.
 2. Finland, Germany, Italy, Japan, New Zealand, Norway, Oman and Poland are categorize as high income groups while Burkina faso, Guinea, Uganda and Zimbambwe are categorize as low income groups having total area ranging from 75,000 to 150,000 square meter.
 3. The Upper middle income groups countries have the highest average total area in square miles, follow by the high income group countries while the low income groups of countries have the lowest.
 4. Upper middle income groups countries have the highest total forest area in square meter follow by the high income group countries while the the low income groups contries have the lowest total forest area in square meters.
 5. Russian Federation in the Europe and central Asia region have the highest forest area in square kilometer.

### Recomendations.

Based on the analysis, we recommend the following actions:

- High income and middle income countries benefited from deforestatation as it contribute to their revenue growth.

### Limitations.

I had to change null values to zero from forest_area_sqm and total_area_sqm column in forest and land table also remove  outliers from country name column in forest area and land area table because they would have affected the accuracy on my my conclusion from the analysis.

### References.

1. [Our world in Data](https://ourworldindata.org/deforestation)
2. [google](https://www.google.com/)





