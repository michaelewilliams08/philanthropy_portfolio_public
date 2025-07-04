# philanthropy_portfolio_public
Independent analytics and mapping project for city-level health, social, and equity indicators—enabling data-driven insights for philanthropic research and community investment


Executive Summary
Understanding Food Deserts
A food desert is a geographic area where residents have limited access to affordable and nutritious food, particularly fresh fruits and vegetables. The United States Department of Agriculture (USDA) typically defines food deserts as low-income census tracts where a significant number or share of residents is far from a supermarket or large grocery store. The Centers for Disease Control and Prevention (CDC) and other organizations have further refined these definitions to capture nuances in urban and rural settings, considering factors such as car ownership and walking distance.
Food deserts matter because inadequate access to healthy food is associated with a higher prevalence of diet-related chronic diseases, such as obesity, diabetes, and cardiovascular conditions. These disparities often overlap with other vulnerabilities, including poverty, racial/ethnic segregation, and social determinants of health. Thus, identifying and characterizing food deserts is crucial for informing equitable policy interventions and resource allocation.
Project Goals
This research project aims to:
Systematically identify and profile food deserts across six diverse U.S. cities (Jacksonville, Ann Arbor, Detroit, Seattle, Spokane, and Weston).
Quantify and visualize the relationship between food desert status, social determinants, and health outcomes at both ZIP code and census tract levels.
Provide actionable, high-resolution data to support public health, city planning, and community advocacy for food access and health equity.
Our research required integrating a broad array of open-source, publicly available datasets, each with its own structure, scale, and documentation. We drew from respected national resources, including:
USDA Food Access Research Atlas (https://www.ers.usda.gov/data-products/food-access-research-atlas): Provides tract-level data on food access, income, and vehicle availability, widely used to define food desert status.
CDC PLACES Project (https://www.cdc.gov/places): Offers ZIP code and tract-level estimates for over 30 chronic disease risk factors, outcomes, and social determinants of health, based on population health surveillance and modeling.
U.S. Census and American Community Survey (ACS): Delivers demographic and socioeconomic detail at the tract and ZIP code levels.
State health department datasets: Supplemented national data with locally-reported health and population indicators.
All of these sources are open to the public, typically requiring only basic registration or acceptance of use terms. Data access, while “open,” was sometimes non-trivial—requiring navigation of multiple portals, data dictionaries, and file formats (CSV, Excel, shapefiles, etc.).
A central challenge was the lack of a standardized “crossbridge” (crosswalk) between ZIP codes and census tracts. ZIP codes are designed for mail delivery and change frequently, while census tracts are stable statistical areas. Many public datasets are reported at only one of these levels, but meaningful local analysis often requires translation between the two. We used the best-available HUD USPS ZIP-TRACT crosswalk as our foundation, but even this required substantial cleaning:
ZIP and tract codes were present in multiple formats (with or without leading zeros, as strings or integers).
Many ZIPs crossed city and county boundaries, creating ambiguity for city-level assignment.
The crosswalk itself changes each quarter, and not all ZIPs in our cities were present in every dataset.
Beyond the crossbridge, another major hurdle was variable complexity: after merging all datasets, we faced over 630 unique variables—ranging from population demographics, detailed income brackets, and insurance status to dozens of risk factors, chronic diseases, and social vulnerability scores.
 Many of these variables used different naming conventions, levels of aggregation, and coding schemes, requiring significant harmonization before analysis was possible.

Data Pipeline and Workflow
Given the scale and complexity of the data, a robust and flexible data pipeline was essential.
 Key steps and challenges included:
Schema inspection and harmonization: We developed automated routines to scan every file, print sample rows, and identify likely join keys. This was critical in early stages, given the diversity of field names (e.g., CensusTract, FIPS, GEOID, LocationName, etc.) and the risk of silent mismatches.
Variable standardization: Each variable was reviewed for type, coding, and completeness. We implemented functions to standardize missing value codes, align variable names, and “zfill” codes to ensure consistent matching across files (e.g., ensuring all ZIPs are five-character strings, all tracts are 11-character FIPS codes).
Batch subsetting and merging: To avoid memory overload and ensure traceability, we built functions to extract and merge city-specific data by both ZIP and tract, with logs of any missing or unmatched records.
Integration of the ZIP-tract crossbridge: This step required repeated iterations—testing merges, checking for data loss, and re-examining the crosswalk as new ZIPs appeared in different datasets.
 For example, certain ZIPs only appeared in health datasets but not in the crosswalk, forcing us to reconcile urban boundary changes or anomalous ZIP definitions.
Dimensionality management: With over 630 variables, exploratory profiling and summary statistics were automated within the pipeline, allowing us to quickly identify variables with excessive missingness or little variation (e.g., columns that were all zeros or only applicable to specific subpopulations).
Use of DuckDB for analytics: Given the volume of data and the need for fast, flexible querying, we stored all merged datasets in DuckDB, an open-source analytic database that runs in-process and supports standard SQL. This enabled rapid development and reproducibility, as we could run complex city-by-city or variable-by-variable queries without the need for external servers or cloud services.
All steps in the pipeline were fully scripted, version-controlled, and documented, supporting repeatability and transparency.
 Outputs—including merged datasets, variable profiles, and hundreds of city-specific heatmap visualizations—were systematically saved to Google Drive for team access and external review.
Absolutely! Building on your previous Executive Summary (through the "Data Pipeline and Workflow" section), here is a comprehensive, data-driven narrative that continues seamlessly from there. This section will:
Anchor the story in the data
Reference actual queries
Tie in each output and the provided bar chart
Interpret the findings in the context of food desert exposure, health, and equity
Conclude with insights and possible next steps

Data Analysis and Key Findings
With the integrated pipeline in place, we conducted a series of analyses to quantify and visualize the landscape of food deserts and their relationship to health disparities across our six cities. All analyses were performed using SQL queries in DuckDB, allowing us to efficiently aggregate, compare, and profile hundreds of variables across millions of records.
Example Query: Calculating Food Desert Prevalence
A typical SQL query used to summarize food desert prevalence and population burden by city was:
SELECT
  City,
  COUNT(*) AS n_tracts,
  SUM(food_desert_flag) AS n_food_desert_tracts,
  ROUND(100.0 * SUM(food_desert_flag) / COUNT(*), 1) AS pct_tracts_fd,
  SUM(total_population) AS total_pop,
  SUM(CASE WHEN food_desert_flag=1 THEN total_population ELSE 0 END) AS pop_in_fd,
  ROUND(100.0 * SUM(CASE WHEN food_desert_flag=1 THEN total_population ELSE 0 END) / SUM(total_population), 1) AS pct_pop_fd
FROM tracts
GROUP BY City
ORDER BY pct_pop_fd DESC;

This query provided the foundation for understanding the extent and impact of food deserts in each city.

Food Desert Exposure: Which Cities Are Most Affected?
The following chart and table summarize the percent of each city’s population living in food desert census tracts:
City
Population in Food Desert Tracts (%)
Jacksonville
22.8
Ann Arbor
11.5
Spokane
8.0
Detroit
7.6
Seattle
2.5
Weston
0.0

Key Insight:
 Jacksonville stands out, with nearly one in four residents (22.8%) living in food desert tracts. Ann Arbor also shows substantial exposure (11.5%), while Detroit and Spokane are in a similar range (8%). Seattle and Weston have much lower proportions, underscoring the diversity in food access challenges across U.S. cities.

Who Lives in Food Deserts? Demographic and Social Profile
By joining health, demographic, and social vulnerability variables to food desert status, we profiled disparities in poverty, race/ethnicity, and social vulnerability index (SVI):
City
Tract Type
Poverty (%)
SVI
Black (%)
Hispanic (%)
White (%)
Asian (%)
Ann Arbor
Non-Desert
17.5
6.17
6.7
4.0
77.4
11.2
Ann Arbor
Food Desert
26.7
7.46
18.4
5.3
52.7
21.1
Detroit
Non-Desert
35.8
-3.91
82.9
6.1
10.8
0.9
Detroit
Food Desert
40.2
9.91
91.5
1.3
5.6
0.2
Jacksonville
Non-Desert
17.1
7.97
30.7
6.8
61.1
3.3
Jacksonville
Food Desert
23.8
9.46
50.2
6.9
42.2
2.0

Key Insights:
Food desert tracts consistently have higher poverty rates and SVI scores compared to non-desert tracts.
In Detroit, food desert tracts are overwhelmingly Black (91.5%) and have the highest poverty rate (40.2%).
In Jacksonville and Ann Arbor, food deserts concentrate more Black and Asian residents, and show a marked drop in White population share.
These patterns underscore that food deserts are not just about geography—they are about social and racial inequity.

Health Disparities Linked to Food Deserts
Aggregating health indicators at the ZIP level, we observed significant health gaps between food desert and non-food desert ZIP codes. Some of the largest gaps include:
Measure
Food Desert ZIP
Non-Desert ZIP
Gap
High blood pressure among adults
37.5
18.6
18.9
Taking medicine to control high blood pressure
80.4
69.1
11.3
Short sleep duration among adults
44.2
36.1
8.1
All teeth lost among adults aged ≥65 years
21.4
13.7
7.7
Food insecurity in the past 12 months
27.1
20.6
6.5
Current lack of health insurance
16.4
10.0
6.4
Cognitive disability among adults
36.1
30.9
5.4
No leisure-time physical activity
32.7
27.5
5.2

Key Insights:
Residents in food desert ZIPs have higher rates of chronic disease, disability, food insecurity, tooth loss, and lack of health insurance.
The most striking gap is in high blood pressure, with food desert ZIPs showing nearly 19 percentage points higher prevalence.
Preventive care (such as dental visits and routine checkups) is lower in food desert ZIPs, compounding long-term health risks.

City-Level Highlights
Jacksonville
Exposure: Jacksonville has the highest share of its population living in food desert tracts (22.8%).
Health Gaps: The city shows the largest disparities in colorectal cancer screening, arthritis, routine checkups, dental visits, and feelings of social isolation.
Demographics: Food desert tracts in Jacksonville have higher poverty (23.8% vs 17.1%), higher SVI (9.46 vs 7.97), and a much higher share of Black residents (50.2% vs 30.7%).
Ann Arbor
Exposure: 11.5% of the city’s residents live in food desert tracts.
Demographics: Food desert tracts have higher poverty (26.7% vs 17.5%) and are more likely to have Black (18.4%) and Asian (21.1%) residents compared to non-desert tracts.
Detroit
Exposure: 7.6% of Detroit’s population is in food desert tracts.
Demographics: Nearly all residents in food desert tracts are Black (91.5%), with a poverty rate of 40.2% and the highest SVI score (9.91).
Health Gaps: Detroit’s largest disparity is in food insecurity and receipt of food stamps.
Seattle, Spokane, and Weston
Exposure: Seattle and Spokane have lower food desert exposure (2.5% and 8% respectively), and Weston has none.
Demographics: Food desert tracts in Seattle, though rare, have twice the percentage of Black and Hispanic residents as non-desert tracts, and higher poverty and SVI.

Urbanicity, Food Deserts, and Health
Further analysis by urban class (urban, suburban, rural) revealed:
In rural areas, food desert ZIPs have especially high rates of disability and tooth loss, but also unique strengths in some preventive care metrics.
Suburban food deserts show higher rates of insurance gaps and food insecurity.
This suggests that interventions must be tailored to local context—what works in urban Detroit may not work in rural Ann Arbor.

Conclusion: Implications for Policy and Practice
This analysis demonstrates:
Food deserts concentrate in—and further disadvantage—communities already facing high poverty and racial inequity.
Residents in food desert areas experience persistently worse health, higher chronic disease burden, and less access to preventive care.
The patterns are starkest in cities like Jacksonville and Detroit, but exist in all cities studied.
Policy Recommendations:
Targeted investments in healthy food retail and transportation in high-burden tracts.
Integrated interventions: address not just food access, but also insurance coverage, preventive care, and social support.
Ongoing data monitoring using the pipeline approach described here, so cities can track progress over time and adapt strategies.

Appendix: Sample Query Used
As referenced, here is one of the SQL queries used to generate summary statistics:
SELECT
  City,
  COUNT(*) AS n_tracts,
  SUM(food_desert_flag) AS n_food_desert_tracts,
  ROUND(100.0 * SUM(food_desert_flag) / COUNT(*), 1) AS pct_tracts_fd,
  SUM(total_population) AS total_pop,
  SUM(CASE WHEN food_desert_flag=1 THEN total_population ELSE 0 END) AS pop_in_fd,
  ROUND(100.0 * SUM(CASE WHEN food_desert_flag=1 THEN total_population ELSE 0 END) / SUM(total_population), 1) AS pct_pop_fd
FROM tracts
GROUP BY City
ORDER BY pct_pop_fd DESC;


All data and code used in this analysis are publicly available and can be updated as new data or cities are added. Our approach allows for rapid, robust profiling of food deserts and their consequences, supporting evidence-based action for healthier, more equitable communities.

If you would like even more detail on a particular city, population group, or health indicator, let me know!

Absolutely! Here’s an expanded section focused on Seattle, Spokane, and Weston, including a data-driven review of their health outcomes in relation to food desert exposure, and a deeper exploration of how urbanicity shapes these patterns.

Seattle, Spokane, and Weston: Low Food Desert Exposure, Distinct Health Profiles
Food Desert Prevalence
As seen in the earlier summary and bar chart, Seattle, Spokane, and Weston stand out for their low or negligible population exposure to food deserts:
Seattle: 2.5% of residents in food desert tracts
Spokane: 8.0%
Weston: 0.0%
This is in stark contrast to the much higher percentages in Jacksonville and Ann Arbor.
Demographics and Social Vulnerability
Despite modest food desert prevalence, we still observe some demographic differences between food desert and non-desert tracts in these cities:
City
Tract Type
Poverty (%)
SVI
Black (%)
Hispanic (%)
White (%)
Asian (%)
Seattle
Non-Desert
11.4
6.75
8.0
6.7
69.1
14.1
Seattle
Food Desert
13.2
8.67
16.0
13.7
49.6
18.3
Spokane
Non-Desert
18.4
7.93
2.4
4.9
87.1
2.2
Spokane
Food Desert
18.9
8.38
3.0
5.5
83.8
2.9
Weston
Non-Desert
6.4
5.57
3.8
39.1
87.2
4.1

While food desert tracts in Seattle have slightly higher poverty and greater racial/ethnic diversity, the absolute differences are much smaller than in cities like Detroit or Jacksonville.

Health Outcomes: Are Lower Food Desert Rates Reflected in Better Health?
To test whether lower food desert exposure is associated with better health outcomes, we examine the city-level averages for key health measures:
Seattle
Measure
Non-Desert ZIP
Food Desert ZIP
Gap
All teeth lost among adults aged >=65 years
8.00
10.23
2.23
Any disability among adults
19.50
22.14
2.64
Diagnosed diabetes among adults
6.11
7.45
1.34
High blood pressure among adults
23.41
26.33
2.92
Short sleep duration among adults
30.60
33.21
2.61
Food insecurity in the past 12 months
7.10
9.41
2.31
Visited dentist in the past year
72.66
71.32
-1.34

Spokane
Measure
Non-Desert ZIP
Food Desert ZIP
Gap
All teeth lost among adults aged >=65 years
12.23
13.76
1.53
Any disability among adults
25.60
27.17
1.57
Diagnosed diabetes among adults
8.31
9.24
0.93
High blood pressure among adults
29.04
33.01
3.97
Short sleep duration among adults
34.75
36.28
1.53
Food insecurity in the past 12 months
11.13
11.90
0.77
Visited dentist in the past year
67.18
66.01
-1.17

Weston
Weston, with zero food desert tracts, provides a unique point of comparison. Across all health measures, mean values are generally more favorable than in other cities:
Measure
Non-Desert ZIP
City Average
All teeth lost among adults aged >=65 years
4.53
4.53
Any disability among adults
17.10
17.10
Diagnosed diabetes among adults
4.93
4.93
High blood pressure among adults
19.08
19.08
Food insecurity in the past 12 months
5.13
5.13
Visited dentist in the past year
69.15
69.15

Interpretation
Seattle and Spokane: The absolute differences between food desert and non-desert ZIPs are consistently smaller than in high-exposure cities. For most health outcomes, the gap is only 1–3 percentage points, and the citywide averages are generally better than in Jacksonville or Detroit.
Weston: With no food deserts, health outcomes are uniformly better—lower rates of disability, tooth loss, and chronic disease, and high rates of preventive care (e.g., dental visits).

Urbanicity, Food Deserts, and Health Outcomes
The relationship between food deserts, urbanicity, and health is complex and context-dependent:
Urban Areas: In large, dense cities, food deserts often concentrate in historically marginalized neighborhoods. Here, the gap in health outcomes can be large (as in Detroit and Jacksonville).
Suburban and Rural Areas: Food deserts are less common, and when present, affect a smaller population. The differences in health outcomes between food desert and non-desert areas are smaller, as seen in Seattle, Spokane, and Weston.
Weston: As a predominantly suburban community with strong infrastructure and higher average income, Weston has no food deserts and the most favorable health and preventive care statistics.
Key Takeaways:
Low food desert exposure is associated with smaller health disparities and better overall population health.
In cities where food deserts are rare (Seattle, Spokane, Weston), there is less concentration of poverty and racial segregation in food desert tracts, and gaps in health outcomes are minimized.
Urbanicity matters: The impact of a food desert is context-specific. In cities with robust economic resources and well-distributed amenities, food deserts may be less of a health risk, but in large or historically segregated cities, the same designation signals much greater risk and inequity.

Conclusion
Seattle, Spokane, and especially Weston exemplify how urban planning, infrastructure, and socioeconomic status can mitigate the risks associated with food deserts. In these cities, food access is more equitably distributed, and health disparities by food desert status are narrow. This contrasts with cities like Jacksonville and Detroit, where food deserts overlap with entrenched social vulnerability and produce significant, measurable health inequities.
Policy Implication:
 Efforts in high-performing cities should focus on maintaining universal access and continuing to monitor for emerging gaps, while lessons from their urban planning, transportation, and economic investment might inform interventions in more at-risk communities elsewhere.

This nuanced view emphasizes that food deserts are not just a rural or urban issue, but a reflection of broader patterns in city development, equity, and social policy. Ongoing, granular surveillance—like that enabled by our pipeline—remains essential for identifying and addressing both persistent and emerging food access challenges.


Certainly! Here’s a formal bibliography for your report, including all key data sources, pipeline technologies, and foundational definitions referenced throughout your analysis. Entries follow a modified APA style. Please add, remove, or adjust entries as needed for your specific documentation or institutional requirements.

Bibliography
Centers for Disease Control and Prevention (CDC). (2023). PLACES: Local Data for Better Health. U.S. Department of Health & Human Services.
 https://www.cdc.gov/places
Centers for Disease Control and Prevention (CDC). (2023). Defining Adult Overweight & Obesity: Food Deserts.
 https://www.cdc.gov/obesity/data/food-deserts.html
DuckDB Developers. (2024). DuckDB: An Embeddable Analytical Database.
 https://duckdb.org/
HUD Office of Policy Development and Research. (2024). USPS ZIP Code Crosswalk Files.
 https://www.huduser.gov/portal/datasets/usps_crosswalk.html
OpenStreetMap Contributors. (2024). OpenStreetMap Data.
 https://www.openstreetmap.org
United States Census Bureau. (2023). American Community Survey (ACS) Data.
 https://www.census.gov/programs-surveys/acs
United States Census Bureau. (2023). TIGER/Line Shapefiles.
 https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-line-file.html
United States Department of Agriculture Economic Research Service (USDA ERS). (2023). Food Access Research Atlas.
 https://www.ers.usda.gov/data-products/food-access-research-atlas
United States Department of Agriculture Economic Research Service (USDA ERS). (2023). Food Desert Locator.
 https://www.ers.usda.gov/data-products/food-desert-locator
Washtenaw County Health Department (Michigan). (2024). Health Data and Reports.
 https://www.washtenaw.org/857/Health-Data-Reports
Washington State Department of Health. (2024). Health Statistics.
 https://www.doh.wa.gov/DataandStatisticalReports/HealthStatistics
Florida Health CHARTS. (2024). Community Health Assessment Resource Tool Set.
 http://www.flhealthcharts.gov/
United States Department of Health and Human Services (HHS) Office of Minority Health. (2023). Social Vulnerability Index (SVI).
 https://www.atsdr.cdc.gov/placeandhealth/svi/index.html
United States Department of Agriculture Economic Research Service (USDA ERS). (2023). Documentation: Food Access Research Atlas.
 https://www.ers.usda.gov/data-products/food-access-research-atlas/documentation/
GeoPandas Developers. (2024). GeoPandas: Python tools for geographic data.
 https://geopandas.org/
Pandas Development Team. (2024). pandas-dev/pandas: Pandas. Zenodo.
 https://doi.org/10.5281/zenodo.3509134

Note: For local or state datasets, URLs and agency names should be updated to the specific file or data portal used. For city-specific reports, cite local government or health agencies as appropriate.
If you have additional proprietary or unpublished datasets, or wish to expand on technology/tool references (e.g., Google Colab, Matplotlib), let me know and I can further augment the bibliography.

