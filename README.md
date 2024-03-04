# Deforestation Analysis.

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
2. Data filtering
3. Handling null values
4. Check outliers

### Exploratory Data Analysis.
---

EDA involves exploring the sales data to answer key questuions, such as:

- What are the total number of countries involved in deforestation?
- Show the income groups of countries having total area ranging from 75,000 to 150,000 square meter?
- Calculate average area in square miles for countries in the 'upper middle income region'. Compare the result with the rest of the income categories
- Determine the total forest area in square km for countries in the 'high income' group. Compare result with the rest of the income categories.
- Show countries from each region(continent) having the highest total forest areas.

### Data Analysis.
---

```sql
SELECT COUNT(distinct COUNTRY_NAME) AS TOTAL_NO_OF_COUNTRIES_INVL_DEFORESTATION FROM Forest_Area
where forest_area_sqkm != 0;
```
```sql
SELECT  DISTINCT L.COUNTRY_NAME, R.income_group
FROM Region R join Land_Area L
on r.country_code = L.country_code
where total_area_sq_mi between '75000' and '150000';
```
```sql
select income_group, avg(total_area_sq_mi) Average_Area_in_Suqare_Miles from
(select  R.country_name, R.income_group,R.Region, L.total_area_sq_mi from Region R join Land_Area L on R.country_code = L.country_code) as T
group by income_group 
having income_group in ('upper middle income','low income','high income','lower middle income');
```
```sql
select income_group, sum(forest_area_sqkm) as Total_forest_area_in_sqm from
(select R.country_name, R.income_group, F.forest_area_sqkm from Forest_Area F join Region R on F.country_code = R.country_code) as T
group by income_group
having income_group in ('high income','low income','upper middle income','lower middle income');
```
```sql
with main_cte as
(select distinct r.country_name, Region, f.forest_area_sqkm,
dense_rank() over(partition by r.region order by f.forest_area_sqkm desc) as rank
from region r join Forest_area f on r.country_code = f.country_code)
select * from main_cte
where rank = 1
```
### Results.
---

 The analysis results are summarized as follows:
 
 1. The total number of countries invlove in deforestation is 207
 2. Finland, Germany, Italy, Japan, New Zealand, Norway, Oman and Poland are categorize as high income groups while Burkina faso, Guinea, Uganda and Zimbambwe are categorize as low income groups having total area ranging from 75,000 to 150,000 square meter.
 3. The Upper middle income groups countries have the highest average total area in square miles, follow by the high income group countries while the low income groups of countries have the lowest.
 4. Upper middle income groups countries have the highest total forest area in square meter follow by the high income group countries while the the low income groups contries have the lowest total forest area in square meters.
 5. Russian Federation in the Europe and central Asia region have the highest forest area in square kilometer.

### Recomendations.

Based on the analysis, we recommend the following actions:

- High income and middle income countries have more forest area which contribute to their revenue generation, job availability etc other income groups like the low income should increase their forest area.

### Limitations.

I had to remove null values from forest_area_sqm and total_area_sqm column in forest and land table and outlier from country name column in forest area and land area table because they would have affected the accuracy on my my conclusion from the analysis.

### References.

1. [Our world in Data](https://ourworldindata.org/deforestation)
2. [google](https://www.google.com/)





