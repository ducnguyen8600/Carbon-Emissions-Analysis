# Carbon-Emissions-Analysis

## 1. Introduction

This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

Carbon emissions play a crucial role in the environment, accounting for over 75% of global emissions and posing a significant environmental challenge. These emissions contribute to the accumulation of greenhouse gases in the atmosphere, leading to climate change, planetary warming, and involvement in various environmental disasters.

Through this analysis, we hope to gain an understanding of the environmental impact of different industries and contribute to making informed decisions in sustainable development.

## 2. Data Overview
###  Data Source
Data Source: Where Our Data Comes From
Our dataset is compiled from publicly available data from nature.com and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent)
### Data Structure
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

https://lms.swisscoding.edu.vn/pluginfile.php/26376/mod_label/intro/Database%20diagram.png<img width="687" height="487" alt="image" src="https://github.com/user-attachments/assets/7c9e769a-4183-4a08-95dc-a148be701f61" />

### Table Preview

#### product_emissions
```sql
SELECT *
FROM product_emissions
LIMIT 5
```
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|

#### industry_groups
```sql
SELECT *
FROM industry_groups
LIMIT 5
```
|id|industry_group|
|--|--------------|
|1|"Consumer Durables, Household and Personal Products"|
|2|"Food, Beverage & Tobacco"|
|3|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|
|4|"Mining - Iron, Aluminum, Other Metals"|
|5|"Pharmaceuticals, Biotechnology & Life Sciences"|

#### companies
```sql
SELECT *
FROM companies
LIMIT 5
```
|id|company_name|
|--|------------|
|1|"Autodesk, Inc."|
|2|"Casio Computer Co., Ltd."|
|3|"Cisco Systems, Inc."|
|4|"CNX Coal Resources, LP"|
|5|"Coca-Cola Enterprises, Inc."|

#### countries
```sql
SELECT *
FROM countries
LIMIT 5
```
|id|country_name|
|--|------------|
|1|Australia|
|2|Belgium|
|3|Brazil|
|4|Canada|
|5|Chile|

## 3. Pre-processing
### Check Duplicate
#### Check total row
```sql
SELECT COUNT(*)
FROM product_emissions
```
|COUNT(*)|
|--------|
|1037|
#### Find duplicate id
```sql
SELECT id, COUNT(id)
FROM product_emissions
GROUP BY id
HAVING COUNT(id) > 1;
```
|id|COUNT(id)|
|--|---------|
|10056-1-2014|2|
|10056-1-2015|2|
|10222-1-2013|2|
|10261-1-2017|2|
|10261-2-2017|2|
|10261-3-2017|2|
|10324-1-2016|2|
|10418-1-2013|2|
|10661-1-2014|2|
|10661-1-2015|2|
|10661-1-2016|2|
|10661-10-2014|2|
|10661-10-2015|2|
|10661-10-2016|2|
|10661-11-2014|2|
|10661-11-2015|2|
|10661-2-2014|2|
|10661-2-2015|2|
|10661-2-2016|2|
|10661-3-2014|2|
|10661-3-2015|2|
|10661-3-2016|2|
|10661-4-2014|2|
|10661-4-2015|2|
|10661-5-2014|2|
|10661-5-2015|2|
|10661-6-2014|2|
|10661-6-2015|2|
|10661-7-2014|2|
|10661-7-2015|2|
|10661-8-2014|2|
|10661-8-2015|2|
|10661-9-2014|2|
|10661-9-2015|2|
|10666-1-2014|2|
|10666-10-2014|2|
|10666-11-2014|2|
|10666-12-2014|2|
|10666-13-2014|2|
|10666-14-2014|2|
|10666-15-2014|2|
|10666-16-2014|2|
|10666-17-2014|2|
|10666-18-2014|2|
|10666-19-2014|2|
|10666-2-2014|2|
|10666-20-2014|2|
|10666-21-2014|2|
|10666-22-2014|2|
|10666-23-2014|2|
|10666-24-2014|2|
|10666-25-2014|2|
|10666-26-2014|2|
|10666-27-2014|2|
|10666-28-2014|2|
|10666-29-2014|2|
|10666-3-2014|2|
|10666-4-2014|2|
|10666-5-2014|2|
|10666-6-2014|2|
|10666-7-2014|2|
|10666-8-2014|2|
|10666-9-2014|2|
|10667-1-2014|2|
|10667-1-2015|2|
|10667-2-2013|2|
|10667-2-2014|2|
|10667-2-2015|2|
|10667-2-2016|2|
|10667-3-2015|2|
|10667-3-2016|2|
|10667-4-2016|2|
|10667-5-2016|2|
|10670-1-2013|2|
|10834-1-2015|2|
|1085-1-2013|2|
|1085-1-2014|2|
|1085-1-2016|2|
|11152-1-2013|2|
|11632-1-2016|2|
|1198-1-2013|2|
|1198-1-2014|2|
|1198-1-2015|2|
|1198-2-2013|2|
|1198-2-2014|2|
|1198-2-2015|2|
|1198-4-2015|2|
|12084-1-2016|2|
|12117-1-2013|2|
|12117-2-2013|2|
|12134-1-2017|2|
|12134-2-2017|2|
|12134-3-2017|2|
|12134-4-2017|2|
|12134-5-2017|2|
|12134-6-2017|2|
|12134-7-2017|2|
|12134-8-2017|2|
|12289-1-2017|2|
|12348-1-2013|2|
|12348-2-2013|2|
|12348-2-2014|2|
|12348-3-2013|2|
|12348-3-2014|2|
|12348-4-2014|2|
|12903-1-2015|2|
|12903-2-2015|2|
|12903-3-2015|2|
|12942-1-2013|2|
|12942-1-2014|2|
|12942-1-2015|2|
|12942-1-2016|2|
|12942-2-2013|2|
|12942-2-2014|2|
|12942-2-2015|2|
|12942-2-2016|2|
|12942-3-2013|2|
|12942-4-2013|2|
|12942-5-2013|2|
|13360-1-2013|2|
|13360-1-2014|2|
|13360-2-2013|2|
|13360-3-2013|2|
|13889-1-2014|2|
|13889-10-2017|2|
|13889-11-2017|2|
|13889-12-2017|2|
|13889-13-2017|2|
|13889-14-2017|2|
|13889-2-2014|2|
|13889-4-2014|2|
|13889-5-2017|2|
|13889-6-2017|2|
|13889-7-2017|2|
|13889-8-2017|2|
|13889-9-2017|2|
|14021-1-2013|2|
|14021-1-2015|2|
|14021-1-2016|2|
|14605-1-2013|2|
|14605-1-2014|2|
|14605-1-2015|2|
|14605-2-2013|2|
|14605-2-2014|2|
|14605-2-2015|2|
|14605-3-2013|2|
|14605-3-2014|2|
|14605-3-2015|2|
|14605-4-2013|2|
|14605-4-2014|2|
|14605-4-2015|2|
|14697-1-2013|2|
|149-1-2013|2|
|15391-1-2013|2|
|15391-1-2014|2|
|15398-1-2013|2|
|15398-10-2014|2|
|15398-11-2014|2|
|15398-12-2014|2|
|15398-13-2015|2|
|15398-13-2016|2|
|15398-14-2015|2|
|15398-14-2016|2|
|15398-15-2015|2|
|15398-15-2016|2|
|15398-16-2015|2|
|15398-16-2016|2|
|15398-17-2015|2|
|15398-18-2016|2|
|15398-2-2013|2|
|17659-2-2014|2|

####  product_emissions no duplicate CTE
```sql
WITH pe_no_duplicate as 
(SELECT *
FROM 
	(SELECT
		*,
		ROW_NUMBER() OVER(PARTITION BY id) as rn
	FROM product_emissions) t
WHERE rn = 1)

SELECT * FROM pe_no_duplicate
LIMT 10
```
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|rn|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|--|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|1|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|1|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|1|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|1|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|1|
|10261-3-2017|14|16|25|2017|Multifunction Printers|110.0|2274|20.05|3.61|76.34|1|
|10324-1-2016|15|16|19|2016|KURALON  fiber|1500.0|10000|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|1|
|10418-1-2013|84|9|19|2013|Portland Cement|1000.0|1102|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|1|
|10661-1-2014|85|28|11|2014|501® Original Jeans – Dark Stonewash|0.997|16|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|1|
|10661-1-2015|85|28|6|2015|501® Original Jeans – Dark Stonewash|0.997|16|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|1|

## 4. Analyze






