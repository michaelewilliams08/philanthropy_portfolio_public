Understanding Food Deserts in America 

A food desert is a geographic area where residents face barriers to accessing affordable and nutritious food—especially fresh produce and staple groceries. Traditionally, the United States Department of Agriculture (USDA) has defined these areas as low-income census tracts with limited proximity to supermarkets or large grocery stores. More recent research, including data from the Centers for Disease Control and Prevention (CDC), highlights additional challenges in both urban and rural settings, factoring in transportation access, car ownership, and walkability.
My data and spatial analysis provide new evidence that food deserts are closely linked to a range of health and social outcomes at the ZIP code and city level. Areas with high levels of food insecurity often coincide with elevated rates of chronic health conditions—such as obesity, diabetes, and frequent mental distress—as well as other vulnerabilities like poverty, disability, and reduced access to healthcare.
These overlaps underscore the importance of using fine-grained, place-based data to identify and address food deserts. By mapping and quantifying these relationships, we can better inform equitable policy, direct philanthropic resources, and support interventions that target the communities most affected by food access barriers.
Project Mission:
Systematically identify and profile food deserts across six diverse U.S. cities (Jacksonville, Ann Arbor, Detroit, Seattle, Spokane, and Weston).
Quantify and visualize the relationship between food desert status, social determinants, and health outcomes at both ZIP code and census tract levels.
Provide actionable, high-resolution data to support public health, city planning, and community advocacy for food access and health equity.
My research required integrating a broad array of open-source, publicly available datasets, each with its own structure, scale, and documentation. I drew from respected national resources, including:
USDA Food Access Research Atlas (https://www.ers.usda.gov/data-products/food-access-research-atlas): Provides tract-level data on food access, income, and vehicle availability, widely used to define food desert status.
CDC PLACES Project (https://www.cdc.gov/places): Offers ZIP code and tract-level estimates for over 30 chronic disease risk factors, outcomes, and social determinants of health, based on population health surveillance and modeling.
U.S. Census and American Community Survey (ACS): Delivers demographic and socioeconomic detail at the tract and ZIP code levels.
State health department datasets: Supplemented national data with locally-reported health and population indicators.
All of these sources are open to the public, typically requiring only basic registration or acceptance of use terms. Data access, while “open,” was sometimes non-trivial—requiring navigation of multiple portals, data dictionaries, and file formats (CSV, Excel, shapefiles, etc.).
A central challenge was the lack of a standardized “crossbridge” (crosswalk) between ZIP codes and census tracts. ZIP codes are designed for mail delivery and change frequently, while census tracts are stable statistical areas. Many public datasets are reported at only one of these levels, but meaningful local analysis often requires translation between the two. We used the best-available HUD USPS ZIP-TRACT crosswalk as our foundation, but even this required substantial cleaning. Many of the ZIP and tract codes were present in multiple formats (with or without leading zeros, as strings or integers). Many ZIPs crossed city and county boundaries, creating ambiguity for city-level assignment. The crosswalk itself changes each quarter, and not all ZIPs in our cities were present in every dataset. 
Beyond the crossbridge, my biggest hurdle by far in this project was variable complexity. Variable complexity was overwhelming. After merging all datasets, I faced over 630 unique variables—ranging from population demographics, detailed income brackets, and insurance status to dozens of risk factors, chronic diseases, and social vulnerability scores.
 Many of these variables used different naming conventions, levels of aggregation, and coding schemes, requiring significant harmonization before analysis was possible. 

Data Pipeline and Workflow
Given the scale and complexity of the data, a robust and flexible data pipeline was essential. I developed automated routines to scan every file, print sample rows, and identify likely join keys. This was critical in early stages, given the diversity of field names (e.g., CensusTract, FIPS, GEOID, LocationName, etc.) and the risk of silent mismatches, which were everywhere. Each variable was reviewed for type, coding, and completeness. I implemented functions to standardize missing value codes, align variable names, and “zfill” codes. After much problem solving I was able to ensure consistent matching across files (e.g., ensuring all ZIPs are five-character strings, all tracts are 11-character FIPS codes). Zip codes were also manually validated. 
Batch testing and subsetting was used to avoid memory overload and ensure traceability, I built functions to extract and merge city-specific data by both ZIP and tract, with logs of any missing or unmatched records. Integrating the ZIP-tract crossbridge required days of repeated iterations—testing merges, checking for data loss, and re-examining the crosswalk as new ZIPs appeared in different datasets.For example, certain ZIPs only appeared in health datasets but not in the crosswalk, forcing us to reconcile urban boundary changes or anomalous ZIP definitions. With over 630 variables, exploratory profiling and summary statistics were automated within the pipeline, allowing me to quickly identify variables with excessive missingness or little variation (e.g., columns that were all zeros or only applicable to specific subpopulations).
Given the volume of data and the need for fast, flexible querying, all merged datasets were stored in DuckDB, an open-source analytic database that runs in-process and supports standard SQL. This enabled rapid development and reproducibility, as we could run complex city-by-city or variable-by-variable queries without the need for external servers or cloud services.
All steps in the pipeline were fully scripted, version-controlled, and documented, supporting repeatability and transparency. Outputs—including merged datasets, variable profiles, and hundreds of city-specific heatmap visualizations—were systematically saved to Google Drive for team access and external review.
Data Analysis and Key Findings
With the integrated pipeline in place, I conducted a series of analyses to quantify and visualize the landscape of food deserts and their relationship to health disparities across our six cities. All analyses were performed using SQL queries in DuckDB, eventually helping me to “efficiently” aggregate, compare, and profile hundreds of variables across millions of records.
Limitations & Data Challenges
While this project leverages the best available public data, several challenges continue to remain above my abilities. Not all ZIP codes or census tracts align perfectly, especially as boundaries change or new ZIPs are created. Some health and social indicators are only available in modeled form at small geographies, which introduces some uncertainty. The crosswalk process—matching ZIPs to tracts—remains imperfect. Finally, these analyses capture risk, not causality: food deserts are a powerful marker for inequity, but not its sole cause.

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

 Jacksonville stands out, with nearly one in four residents (22.8%) living in food desert tracts. Ann Arbor also shows substantial exposure (11.5%), while Detroit and Spokane are in a similar range (8%). Seattle and Weston have much lower proportions, underscoring the diversity in food access challenges across U.S. cities.

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

Food desert tracts consistently have higher poverty rates and SVI scores compared to non-desert tracts.In Detroit, food desert tracts are overwhelmingly Black (91.5%) and have the highest poverty rate (40.2%).In Jacksonville and Ann Arbor, food deserts concentrate more Black and Asian residents, and show a marked drop in White population share.These patterns underscore that food deserts are not just about geography—they are about social and racial inequity.


Health Disparities Linked to Food Deserts
Aggregating health indicators at the ZIP level, significant health gaps are observed between food desert and non-food desert ZIP codes. Some of the largest gaps include:
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

Residents in food desert ZIPs have higher rates of chronic disease, disability, food insecurity, tooth loss, and lack of health insurance. The most striking gap is in high blood pressure, with food desert ZIPs showing nearly 19 percentage points higher prevalence. Preventive care (such as dental visits and routine checkups) is lower in food desert ZIPs, compounding long-term health risks.

City-Level Highlights
Jacksonville
Jacksonville emerges as the most food-insecure city in this analysis, with nearly one in four residents (22.8%) living in food desert tracts—a proportion that stands out even among large U.S. cities. The heatmaps make this reality impossible to ignore: food desert zones are not scattered randomly but form visible, contiguous clusters, especially across north and west Jacksonville. These tracts have a poverty rate of 23.8% (vs. 17.1% in non-deserts), and are home to a majority Black population (50.2%)—a striking jump from the city’s non-desert tracts (30.7% Black). The SVI is also elevated (9.46 vs. 7.97), signaling layered vulnerability. Health disparities are just as pronounced: food desert ZIPs in Jacksonville show the city’s widest gaps for chronic disease indicators, preventive care (like dental visits and cancer screening), and social isolation. The visualizations show how food access barriers, economic disadvantage, and health risks reinforce each other, often in the same neighborhoods. In Jacksonville, food deserts are not a marginal issue—they are a central driver of health and social inequity.

Ann Arbor
Ann Arbor’s reputation as an affluent, healthy college town hides sharp internal contrasts that become clear through spatial analysis. Although 11.5% of residents live in food desert tracts, these areas are disproportionately impacted on nearly every dimension. The affected tracts have a poverty rate of 26.7%—a full 9 percentage points higher than the rest of the city. The demographic shift is equally significant: food desert tracts are both more Black (18.4%) and Asian (21.1%) than non-deserts, with the White population share dropping by 25 points. SVI scores are higher too (7.46 vs. 6.17), reflecting layered disadvantage. The city’s heatmaps highlight how food insecurity and vulnerability are not spread evenly, but instead cluster in well-defined pockets—sometimes just blocks away from high-resource neighborhoods and the university campus. Ann Arbor’s case demonstrates that even in wealthy, well-served cities, food access divides can be sharp and consequential, underscoring the need for targeted, neighborhood-level interventions.

Detroit
Detroit’s heatmaps reveal a city where food deserts are both spatially and socially entrenched. Although only 7.6% of Detroiters live in food desert tracts, those neighborhoods are profoundly isolated in terms of both resources and outcomes. The data show that food desert tracts are 91.5% Black—even higher than the city’s already large Black population—while the share of White residents drops below 6%. Poverty is severe, with a 40.2% rate in food deserts, compared to 35.8% elsewhere in the city, and the Social Vulnerability Index (SVI) soars to 9.91 (versus a negative SVI in non-desert tracts, suggesting relative advantage). Detroit’s food desert residents also experience the city’s highest rates of food insecurity and dependency on food stamps, compounding the impact of limited food access. The heatmaps make these patterns visually unavoidable: large swaths of the city light up simultaneously for chronic disease, poverty, and food access risk, confirming that Detroit’s food deserts are not isolated pockets, but core zones of concentrated disadvantage.


Seattle
Seattle’s data and heatmaps tell a story of relative abundance—yet they also spotlight the edges where risk remains. Just 2.5% of Seattle’s population lives in food desert tracts, and citywide averages for poverty and chronic disease are among the lowest in this study. However, the few tracts identified as food deserts reveal subtle but important disparities: poverty rises to 13.2% (from 11.4% in non-deserts), and the SVI jumps to 8.67 from 6.75. Racial and ethnic diversity is also more pronounced in these areas, with 16% Black and 13.7% Hispanic residents—roughly double their representation in the rest of the city. Health gaps, while narrower than in Detroit or Jacksonville, still show up on the map: rates of diabetes, high blood pressure, and disability are consistently higher in food desert ZIPs. Seattle’s heatmaps highlight that even in cities with strong infrastructure, geography and inequity persist at the margins.
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
Spokane sits somewhere between the “big city” and “suburban” patterns seen elsewhere. About 8% of its residents live in food desert tracts, and the heatmaps show these areas are less starkly defined than in Detroit or Jacksonville, but still distinct. In food desert tracts, poverty is about the same (18.9% versus 18.4%), and SVI is elevated (8.38 compared to 7.93). There’s a modest increase in racial diversity, with slightly higher percentages of Black and Hispanic residents. Health gaps are present but less dramatic: rates of high blood pressure, disability, and food insecurity all tick up in these tracts, but the differences are measured in single percentage points. Spokane’s case shows that even in smaller cities, food access and vulnerability cluster together, but the gaps may be less visible without careful mapping and local context.
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



Conclusion
Across these six diverse cities beautiful cities, This analysis shows that the geographic distribution of food deserts and related health disparities is neither random nor uniform across American cities. In each city, the intersection of food access, poverty, race, and health outcomes plays out in distinct but persistent patterns. Jacksonville and Detroit illustrate how concentrated disadvantage—historically shaped by segregation and disinvestment—continues to drive both food insecurity and poor health at the neighborhood level. Ann Arbor, despite its relative affluence, contains clearly defined areas where economic and social vulnerability concentrate, often out of sight of the city’s broader prosperity. Seattle and Spokane, while less affected overall, still contain neighborhoods where barriers to healthy food and higher disease risks are apparent, especially for communities of color. Weston stands as an example of what is possible when economic opportunity and infrastructure are more evenly distributed.
The consistency of these findings across diverse cities reinforces the importance of local context in understanding and addressing food insecurity. The heatmaps and data do not simply highlight need; they point to the neighborhoods where intervention will matter most. Addressing food deserts requires more than expanding grocery store footprints—it calls for policies that recognize and respond to the layered realities of each community.


Policy Opportunities:
Seattle, Spokane, and especially Weston exemplify how urban planning, infrastructure, and socioeconomic status can mitigate the risks associated with food deserts. In these cities, food access is more equitably distributed, and health disparities by food desert status are narrow. This contrasts with cities like Jacksonville and Detroit, where food deserts overlap with entrenched social vulnerability and produce significant, measurable health inequities. This nuanced view emphasizes that food deserts are not just a rural or urban issue, but a reflection of broader patterns in city development, equity, and social policy. Ongoing, granular surveillance—like that enabled by our pipeline—remains essential for identifying and addressing both persistent and emerging food access challenges.






































Bibliography

Centers for Disease Control and Prevention (CDC). (2024).
 PLACES: Local Data for Better Health.
 https://www.cdc.gov/places


Centers for Disease Control and Prevention/Agency for Toxic Substances and Disease Registry (CDC/ATSDR). (2024).
 Social Vulnerability Index (SVI) Data & Documentation.
 https://www.atsdr.cdc.gov/place-health/php/svi/svi-data-documentation-download.html


DuckDB Developers. (2024).
 DuckDB: An In-process SQL OLAP Database Management System.
 https://duckdb.org/


HUD Office of Policy Development and Research. (2024).
 USPS ZIP Code Crosswalk Files.
 https://www.huduser.gov/portal/datasets/usps_crosswalk.html


ProximityOne. (2024).
 ZIP Code to Census Tract Equivalence Table.
 https://proximityone.com/ziptractequiv.htm


Capitol Impact, Inc. (2024).
 CI Gateway Zip Code List: Ann Arbor, Michigan.
 https://ciclt.net/sn/clt/capitolimpact/gw_ziplist.aspx?ClientCode=capitolimpact&State=mi&StName=Michigan&StFIPS=26&CityKey=2603000


United States Census Bureau. (2021).
 TIGER/Line Shapefiles: 2020 ZCTA5.
 https://www2.census.gov/geo/tiger/TIGER2020/ZCTA5/


United States Census Bureau. (2023).
 American Community Survey (ACS) Data.
 https://www.census.gov/programs-surveys/acs


United States Department of Agriculture Economic Research Service (USDA ERS). (2023).
 Food Access Research Atlas – Download the Data.
 https://www.ers.usda.gov/data-products/food-access-research-atlas/download-the-data


United States Department of Agriculture Economic Research Service (USDA ERS). (2023).
 Food Desert Locator (archived).
 https://www.ers.usda.gov/data-products/food-desert-locator


GeoPandas Developers. (2024).
 GeoPandas: Python tools for geographic data.
 https://geopandas.org/


Pandas Development Team. (2024).
 pandas-dev/pandas: Pandas. Zenodo.
 https://doi.org/10.5281/zenodo.3509134


OpenStreetMap Contributors. (2024).
 OpenStreetMap Data.
 https://www.openstreetmap.org


APPENDIX   

DuckDB Database Schema
tracts
Column Name
Data Type
Description
TractID
BIGINT
Census tract GEOID
State
VARCHAR
State name
County
VARCHAR
County name
Urban
INTEGER
Urban/rural classification
Pop2010
INTEGER
2010 population
OHU2010
INTEGER
2010 occupied housing units
... (demographics etc)
...
...
city
VARCHAR
City name (if available)


zips
Column Name
Data Type
Description
ZIP
BIGINT
5-digit ZIP code
Year
BIGINT
Data year
DataSource
VARCHAR
Source (e.g., BRFSS)
Category
VARCHAR
Indicator category
Measure
VARCHAR
Indicator
Data_Value_Unit
VARCHAR
Value units
Data_Value_Type
VARCHAR
Value type
Data_Value
DOUBLE
Value
... (demographics etc)
...
...
city
VARCHAR
City name (if available)


food_access_atlas_tracts
Column Name
Data Type
Description
CensusTract
BIGINT
Census tract GEOID
State
VARCHAR
State
County
VARCHAR
County
Urban
INTEGER
Urban/rural code
Pop2010
INTEGER
2010 pop
... (multiple vars)
...
Full list: up to 147 columns


PLACES_zcta
Column Name
Data Type
Description
Year
BIGINT
Year
LocationName
VARCHAR
ZIP code
DataSource
VARCHAR
Source (e.g., BRFSS)
Category
VARCHAR
Indicator category
Measure
VARCHAR
Indicator
Data_Value_Unit
VARCHAR
Value units
Data_Value_Type
VARCHAR
Value type
Data_Value
DOUBLE
Value
TotalPopulation
INTEGER
Population
Geolocation
VARCHAR
Geopoint string
...
...
...


ZIP_TRACT_CROSSWALK
Column Name
Data Type
Description
ZIP
BIGINT
5-digit ZIP
TRACT
BIGINT
11-digit tract GEOID
USPS_ZIP_PREF_CITY
VARCHAR
USPS preferred city name
USPS_ZIP_PREF_STATE
VARCHAR
USPS state abbreviation
RES_RATIO
DOUBLE
Ratio of residential addresses
BUS_RATIO
DOUBLE
Ratio of business addresses
OTH_RATIO
DOUBLE
Ratio of other addresses
TOT_RATIO
DOUBLE
Total ratio


food_desert_zips
Column Name
Data Type
Description
ZIP
BIGINT
5-digit ZIP
USPS_ZIP_PREF_CITY
VARCHAR
USPS preferred city name
USPS_ZIP_PREF_STATE
VARCHAR
USPS state abbreviation
total_ratio
DOUBLE
Proportion of addresses in the ZIP
fd_ratio
DOUBLE
Proportion of addresses in food desert tracts
fd_pct
DOUBLE
% of ZIP's addresses in food desert tracts


food_desert_tracts
Column Name
Data Type
Description
TractID
BIGINT
Census tract GEOID
State
VARCHAR
State
County
VARCHAR
County
Urban
INTEGER
Urban/rural code
Pop2010
INTEGER
2010 pop
... (demographics etc)
...
...


VariableLookup
Column Name
Data Type
Description
Field
VARCHAR
Short field name
LongName
VARCHAR
Descriptive field name
Description
VARCHAR
Description


SupplementalDataCounty
Column Name
Data Type
Description
FIPS
INTEGER
County FIPS
State
VARCHAR
State
County
VARCHAR
County name
Variable_Code
VARCHAR
Code for variable
Value
DOUBLE
Value


SupplementalDataState
Column Name
Data Type
Description
State_FIPS
INTEGER
State FIPS
State
VARCHAR
State
Variable_Code
VARCHAR
Code for variable
Value
DOUBLE
Value




Example: SQL CREATE TABLE Statements
CREATE TABLE tracts (
    TractID BIGINT,
    State VARCHAR,
    County VARCHAR,
    Urban INTEGER,
    Pop2010 INTEGER,
    OHU2010 INTEGER,
    -- ... other columns ...
    city VARCHAR
);

CREATE TABLE zips (
    ZIP BIGINT,
    Year BIGINT,
    DataSource VARCHAR,
    Category VARCHAR,
    Measure VARCHAR,
    Data_Value_Unit VARCHAR,
    Data_Value_Type VARCHAR,
    Data_Value DOUBLE,
    -- ... other columns ...
    city VARCHAR
);

CREATE TABLE ZIP_TRACT_CROSSWALK (
    ZIP BIGINT,
    TRACT BIGINT,
    USPS_ZIP_PREF_CITY VARCHAR,
    USPS_ZIP_PREF_STATE VARCHAR,
    RES_RATIO DOUBLE,
    BUS_RATIO DOUBLE,
    OTH_RATIO DOUBLE,
    TOT_RATIO DOUBLE
);








Link to Colab Notebook where research was conducted:

6_city_food_deserts_research_project.ipynb

Link to HeatMaps:

6_city_food_deserts_heatmaps

Link to All research data used:

research_data
