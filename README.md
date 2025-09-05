# Carbon-Emissions-Analysis

## I. Introduction

This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

Carbon emissions play a crucial role in the environment, accounting for over 75% of global emissions and posing a significant environmental challenge. These emissions contribute to the accumulation of greenhouse gases in the atmosphere, leading to climate change, planetary warming, and involvement in various environmental disasters.

Through this analysis, we hope to gain an understanding of the environmental impact of different industries and contribute to making informed decisions in sustainable development.

## II. Data Overview
###  Data Source
Our dataset is compiled from publicly available data from nature.com and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent)
### Data Structure
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.
https://github.com/ducnguyen8600/Carbon-Emissions-Analysis/blob/main/Database%20diagram.png?raw=true<img width="687" height="487" alt="image" src="https://github.com/user-attachments/assets/478bbf06-0101-48dd-8571-dc167c6a6988" />


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

## III. Pre-processing
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
HAVING COUNT(id) > 1
LIMIT 10;
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

## IV. Analyze
### 1. Which products contribute the most to carbon emissions?
### 2. What are the industry groups of these products?
```sql
WITH pe_no_duplicate as 
(SELECT *
FROM 
	(SELECT
		*,
		ROW_NUMBER() OVER(PARTITION BY id) as rn
	FROM product_emissions) t
WHERE rn = 1)

SELECT 
	pe.product_name, ig.industry_group,
	ROUND(AVG(pe.carbon_footprint_pcf),2) as avg_carbon
FROM pe_no_duplicate as pe
LEFT JOIN industry_groups as ig 
ON pe.industry_group_id = ig.id 
GROUP BY pe.product_name, ig.industry_group
ORDER BY avg_carbon DESC 
LIMIT 10
```
|product_name|industry_group|avg_carbon|
|------------|--------------|----------|
|Wind Turbine G128 5 Megawats|Electrical Equipment and Machinery|3718044.00|
|Wind Turbine G132 5 Megawats|Electrical Equipment and Machinery|3276187.00|
|Wind Turbine G114 2 Megawats|Electrical Equipment and Machinery|1532608.00|
|Wind Turbine G90 2 Megawats|Electrical Equipment and Machinery|1251625.00|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|Automobiles & Components|191687.00|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|Materials|167000.00|
|TCDE|Materials|99075.00|
|Mercedes-Benz GLE (GLE 500 4MATIC)|Automobiles & Components|91000.00|
|Mercedes-Benz S-Class (S 500)|Automobiles & Components|85000.00|
|Mercedes-Benz SL (SL 350)|Automobiles & Components|72000.00|

### 3. What are the industries with the highest contribution to carbon emissions?
```sql
WITH pe_no_duplicate as 
(SELECT *
FROM 
	(SELECT
		*,
		ROW_NUMBER() OVER(PARTITION BY id) as rn
	FROM product_emissions) t
WHERE rn = 1)

SELECT 
	ig.industry_group,
	ROUND(SUM(pe.carbon_footprint_pcf),2) as sum_carbon
FROM pe_no_duplicate as pe
LEFT JOIN industry_groups ig 
ON pe.industry_group_id = ig.id 
GROUP BY ig.industry_group
ORDER BY sum_carbon DESC 
LIMIT 10
```
|industry_group|sum_carbon|
|--------------|----------|
|Electrical Equipment and Machinery|9801558.00|
|Automobiles & Components|2582264.00|
|Materials|430199.00|
|Technology Hardware & Equipment|278650.00|
|Capital Goods|258633.00|
|"Food, Beverage & Tobacco"|109132.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|72486.00|
|Software & Services|46533.00|
|Chemicals|44939.00|
|Media|23017.00|

### 4. What are the companies with the highest contribution to carbon emissions?
```sql
WITH pe_no_duplicate as 
(SELECT *
FROM 
	(SELECT
		*,
		ROW_NUMBER() OVER(PARTITION BY id) as rn
	FROM product_emissions) t
WHERE rn = 1)

SELECT 
	c.company_name,
	ROUND(SUM(pe.carbon_footprint_pcf),2) as sum_carbon
FROM pe_no_duplicate as pe
LEFT JOIN companies c 
ON pe.company_id = c.id 
GROUP BY c.company_name
ORDER BY sum_carbon DESC 
LIMIT 10
```
|company_name|sum_carbon|
|------------|----------|
|"Gamesa Corporación Tecnológica, S.A."|9778464.00|
|Daimler AG|1594300.00|
|Volkswagen AG|655960.00|
|"Hino Motors, Ltd."|191687.00|
|Arcelor Mittal|167007.00|
|Weg S/A|160655.00|
|General Motors Company|137007.00|
|"Mitsubishi Gas Chemical Company, Inc."|106008.00|
|"Daikin Industries, Ltd."|105600.00|
|CJ Cheiljedang|94817.00|

### 5. What are the countries with the highest contribution to carbon emissions?
```sql
WITH pe_no_duplicate as 
(SELECT *
FROM 
	(SELECT
		*,
		ROW_NUMBER() OVER(PARTITION BY id) as rn
	FROM product_emissions) t
WHERE rn = 1)

SELECT 
	c.country_name,
	ROUND(SUM(pe.carbon_footprint_pcf),2) as sum_carbon
FROM pe_no_duplicate as pe
LEFT JOIN countries c 
ON pe.country_id = c.id 
GROUP BY c.country_name
ORDER BY sum_carbon DESC 
LIMIT 10
```
|country_name|sum_carbon|
|------------|----------|
|Spain|9786127.00|
|Germany|2251225.00|
|Japan|519348.00|
|USA|451867.00|
|Brazil|167587.00|
|Luxembourg|167007.00|
|South Korea|140995.00|
|Netherlands|70417.00|
|Taiwan|61511.00|
|India|24574.00|

### 6. What is the trend of carbon footprints (PCFs) over the years?
```sql
WITH pe_no_duplicate as 
(SELECT *
FROM 
	(SELECT
		*,
		ROW_NUMBER() OVER(PARTITION BY id) as rn
	FROM product_emissions) t
WHERE rn = 1)

SELECT
	year ,
	ROUND(SUM(pe.carbon_footprint_pcf),2) as sum_carbon
FROM pe_no_duplicate pe
GROUP BY `year`
ORDER BY year ASC
```
|year|sum_carbon|
|----|----------|
|2013|496076.00|
|2014|548229.00|
|2015|10810407.00|
|2016|1612760.00|
|2017|228531.00|

https://github.com/ducnguyen8600/Carbon-Emissions-Analysis/blob/main/Total%20PCFs%20by%20Year.png?raw=true<img width="600" height="371" alt="image" src="https://github.com/user-attachments/assets/d15b6f0d-7291-456f-b7c7-cac626139a6c" />

#### PCFs reached their peak in 2015, marking a significant point in the dataset. To provide further clarity, this section examines the top 10 products by PCF over the year, highlighting the key contributors to the overall impact.
```sql
WITH pe_no_duplicate as 
(SELECT *
FROM 
	(SELECT
		*,
		ROW_NUMBER() OVER(PARTITION BY id) as rn
	FROM product_emissions) t
WHERE rn = 1)

SELECT
	year , product_name, carbon_footprint_pcf
FROM pe_no_duplicate pe
ORDER BY carbon_footprint_pcf DESC
LIMIT 10
```
|year|product_name|carbon_footprint_pcf|
|----|------------|--------------------|
|2015|Wind Turbine G128 5 Megawats|3718044|
|2015|Wind Turbine G132 5 Megawats|3276187|
|2015|Wind Turbine G114 2 Megawats|1532608|
|2015|Wind Turbine G90 2 Megawats|1251625|
|2016|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687|
|2013|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|167000|
|2017|TCDE|99075|
|2016|Mercedes-Benz GLE (GLE 500 4MATIC)|91000|
|2014|Electric Motor|87589|
|2016|Mercedes-Benz S-Class (S 500)|85000|

Insights:
The Wind Turbine products account for approximately 90% of the total PCF, making it the dominant contributor among the top 10 products
















