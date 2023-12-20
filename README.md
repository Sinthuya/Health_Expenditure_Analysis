# Health_Expenditure_In_Australia 1997-2011

### Table of contents

- [Project overview](#project-overview)
- [Data sources](-data-sources)
- [Tools](#tools)
- [Data cleaning/preparation](#data-cleaning-/-preparation)
- [Explanatory data analysis (EDA)](#explanatory-data-analysis-(EDA))
- [Results/Findings](#results/findings)
- [Recomendations](#recomendations)
- [Limitations](#limitations)

### Project overview
This data analysis project aims to provide insights into the financial landscape of the Australian health sector by analysing health expenditure data. We examined the variations between States and Territories, funding source contributions, and trends over time. we used PostgreSQL for data management and Tableau for captivating visualisations. This project aims to equip policymakers, researchers, and healthcare professionals with practical insights, enabling well-informed decision-making within the dynamic realm of public health funding.

### Data sources
Data.gov.au link: https://data.gov.au/data/dataset/f84b9baf-c1c1-437c-8c1e-654b2829848c/resource/88399d53-d55c-466c-8f4a-6cb965d24d6d/download/healthexpenditurebyareaandsource.csv

### Tools
- PostgreSQL - data cleaning and analysis
- Tableau - data visualisation

### Data cleaning / preparation
In the initial data preparation phase I performed the following tasks:
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
ADD COLUMN finacial_years INTEGER;

UPDATE health_expenditure
SET finacial_years = CAST(SPLIT_PART(financial_year, '-', 1) AS INTEGER);

-- Deleted the original financial_year column

ALTER TABLE health_expenditure
DROP COLUMN financial_year;

```

### Explanatory data analysis (EDA)
EDA involved exploring the health expenditure data to answer key questions such as:
- What are the major contributors to health expenditure in different states and territories?
```Sql
SELECT area_of_expenditure, SUM(real_expenditure_millions) AS total_expenditure, (SUM(real_expenditure_millions) / SUM(SUM(real_expenditure_millions)) OVER ()) * 100 AS percentage_contribution
FROM health_expenditure
GROUP BY area_of_expenditure
ORDER BY total_expenditure DESC;
```

Utilised Tableau to create visualisations such as a bar chart to show the distribution of expenditure across States and Territories.

- How does the distribution of funding sources vary across regions?
```Sql

SELECT  broad_source_of_funding, SUM(real_expenditure_millions), SUM(real_expenditure_millions) * 100.0 / SUM(SUM(real_expenditure_millions)) OVER() AS percentage
FROM health_expenditure
GROUP BY broad_source_of_funding;
```

```Sql
-- detailed sources of funding within broad categories
SELECT broad_source_of_funding,
       detailed_source_of_funding,
       SUM(real_expenditure_millions) AS total_expenditure,
	   (SUM(real_expenditure_millions) / SUM(SUM(real_expenditure_millions)) OVER ()) * 100 AS percentage_contribution
FROM health_expenditure
WHERE broad_source_of_funding = 'Government'
GROUP BY broad_source_of_funding, detailed_source_of_funding
ORDER BY total_expenditure DESC;

SELECT broad_source_of_funding,
       detailed_source_of_funding,
       SUM(real_expenditure_millions) AS total_expenditure,
	   (SUM(real_expenditure_millions) / SUM(SUM(real_expenditure_millions)) OVER ()) * 100 AS percentage_contribution
FROM health_expenditure
WHERE broad_source_of_funding = 'Non-government'
GROUP BY broad_source_of_funding, detailed_source_of_funding
ORDER BY total_expenditure DESC;
```

Utilised Tableau to create visualisations such as a column chart to show the borad and detailed sources of funding.

- What trends emerge when analysing health expenditure over consecutive financial years?
```Sql

SELECT financial_year, SUM(real_expenditure_millions) AS total_expenditure
FROM health_expenditure
GROUP BY financial_year
ORDER BY financial_year DESC;
```
Utilised Tableau to create visualisations such as line chart to showcase trends in health expenditure over consecutive financial years.

  
- Are there notable differences in health spending between states and territories?
```Sql
SELECT state, SUM(real_expenditure_millions) AS total_expenditure, (SUM(real_expenditure_millions) / SUM(SUM(real_expenditure_millions)) OVER ()) * 100 AS percentage_contribution
FROM health_expenditure
GROUP BY state
ORDER BY total_expenditure DESC;
```
Utilized Tableau maps to visually represent the geographic distribution of health expenditure, highlighting any notable disparities.

### Results/Findings

The analysis results are summarised as follows:

1. Major Contributors to Health Expenditure:
The analysis identified public hospital as the primary contributor to health expenditure in Australia, accounting for 30% of the total expenditure. Notably, Medical Services also played significant roles with 18% contributions.

2. Distribution of Funding Sources:
Broad sources of funding were diversified across states and territories. Government Funding constituted 69% of the total funding, while non-government played significant roles with 31% of the total funding. Largest detailed source of funding for the Government was the Government itself (63%) followed by State and Local (37%). Largest detailed source of funding for the non-government was the individuals (54%) followed by Private health insurance funds (26%) and Other non-government (21%).

3. Trends in Health Expenditure Over Year
Time-series analysis revealed an upward trend in health expenditure over consecutive financial years.It reflects the dynamic changes in the overall expenditure landscape.

4. Disparities in Health Spending:
Geographic analysis exposed disparities in health expenditure between states and territories. New South Wales (33%) exhibited higher percentage of health expenditure followed by Victoria (24%). In comparison Norther Territory (1%) had the lowest percentage of health expenditure, highlighting regional discrepancies in resource allocation.

### Recomendations
Based on the analysis I recomend the following:
- Addressing regional disparities in health expenditure to ensure equitable access to healthcare services. Initiatives and policies should be aimed at reducing the disparities and to promote balanced resource allocation, especially in regions with lower percentages of health expenditure like Northern Territory.
- Since public Hospitals are the major contributor to health expenditure, policymakers might consider targeted investments in public healthcare infrastructure, staffing, and resources to ensure the sustainability and productivity of this health sector.
- The fact that health spending is going up suggests we need to plan ahead for the future. policymakers can forecast for future health expenditure (area of expenditure and sources of funding to different regions). This helps them take proactive steps to manage the budget.
- Given the significant role of individual contributions in the non-government sector, promoting awareness and education around health insurance benefits could be beneficial. Encouraging individuals to explore and utilize private health insurance funds might contribute to more sustainable and diversified funding sources.

### Limitations
- The accuracy and reliability of the analysis heavily depend on the quality of the provided data. If the dataset contains inaccuracies, missing values, or inconsistencies, it can affect the validity of the findings.
- The dataset may not cover all relevant factors influencing health expenditure. Factors such as socio-economic indicators, demographic changes, or specific healthcare policies could play a role but might not be included in the dataset.
- External factors such as economic fluctuations, global events, or public health crises (like pandemics) can significantly impact health expenditure. These factors are challenging to predict and may not be adequately addressed in the analysis.


