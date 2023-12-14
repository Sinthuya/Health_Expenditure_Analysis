# Health_Expenditure_Analysis 1997-2011


### Project overview
This data analysis project aims to provide insights into the financial landscape of the Australian health sector by analysing health expenditure data. We examined the variations between States and Territories, funding source contributions, and trends over time. we used PostgreSQL for data management and Tableau for captivating visualisations. This project aims to  equip policymakers, researchers, and healthcare professionals with practical insights, enabling well-informed decision-making within the dynamic realm of public health funding.

### Data sources
Data.gov.au link: https://data.gov.au/data/dataset/f84b9baf-c1c1-437c-8c1e-654b2829848c/resource/88399d53-d55c-466c-8f4a-6cb965d24d6d/download/healthexpenditurebyareaandsource.csv

### Tools
- PostgreSQL - data cleaning and analysis
- Tableau - data visualisation

### Data cleaning/preparation
In the initial data prepartion phase I performed the following tasks:
1. data loading and inspection
2. finding/Handling missing values
```Sql
SELECT
    COUNT(*) AS total_rows,
    COUNT(financial_year) AS non_null_financial_year,
    COUNT(state) AS non_null_state,
    COUNT(area_of_expenditure) AS non_null_area,
    COUNT(broad_source_of_funding) AS non_null_broad_source,
    COUNT(detailed_source_of_funding) AS non_null_detailed_source,
    COUNT(real_expenditure_millions) AS non_null_real_expenditure
FROM health_expenditure;
```
4. Data cleaning and formatting

```Sql
--data transformation added a new column to store the extracted year information

ALTER TABLE health_expenditure
ADD COLUMN year INTEGER;

UPDATE health_expenditure
SET year = CAST(SPLIT_PART(financial_year, '-', 1) AS INTEGER);

-- renamed the column 'year' to 'finalcial_year'

ALTER TABLE health_expenditure
RENAME COLUMN year TO finalcial_year;

-- Deleted the original financial_year column

ALTER TABLE health_expenditure
DROP COLUMN financial_year;

-- Renmed the column finalcial_year to financial_year

ALTER TABLE health_expenditure
RENAME COLUMN finalcial_year TO financial_year;
```

### Explanatory data analysis (EDA)
EDA involved exploring the health expenditure data to answer key questions such as:
- What are the major contributors to health expenditure in different states and territories?
```Sql
--Retrieve total health expenditure for each state and territory:

SELECT state, SUM(real_expenditure_millions) AS total_expenditure
FROM health_expenditure
GROUP BY state
ORDER BY total_expenditure DESC;
```
Utilised Tableau to create visualisations such as a line chart to showcase the distribution of expenditure across the States and Territories.

- How does the distribution of funding sources vary across regions?
```Sql

SELECT  broad_source_of_funding, SUM(real_expenditure_millions), SUM(real_expenditure_millions) * 100.0 / SUM(SUM(real_expenditure_millions)) OVER() AS percentage
FROM health_expenditure
GROUP BY broad_source_of_funding;
```
Utilised Tableau to create visualisations such as a pie chart to showcase the distribution of funding sources across the regions.

- What trends emerge when analysing health expenditure over consecutive financial years?
```Sql

SELECT financial_year, SUM(real_expenditure_millions) AS total_expenditure
FROM health_expenditure
GROUP BY financial_year
ORDER BY financial_year DESC;
```
Utilised Tableau to create visualisations such as line chart to shocase  trends in health expenditure over consecutive financial years.

- Which specific areas of expenditure demonstrate the highest impact on the overall healthcare budget?
```Sql

SELECT area_of_expenditure, SUM(real_expenditure_millions) AS total_expenditure
FROM health_expenditure
GROUP BY area_of_expenditure
ORDER BY total_expenditure DESC;
```

Utilised Tableau to create visualisations such as... to show the top areas of health expenditure 
  
- Are there notable differences in health spending between states and territories?
```Sql
SELECT state, SUM(real_expenditure_millions) AS total_expenditure
FROM health_expenditure
GROUP BY state
ORDER BY total_expenditure DESC;
```
Utilize Tableau maps to visually represent the geographic distribution of health expenditure, highlighting any notable disparities.

### Data analysis

