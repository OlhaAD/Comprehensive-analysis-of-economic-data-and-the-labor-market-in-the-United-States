## Project description
This project is an analysis of labor market data in the United States. The analysis includes data on unemployment, employment trends, tax collections and other economic indicators for the entire country, as well as for individual states and counties.

## How to start a project
1. Clone the repository to your local computer.
2. Data for analysis is located in the `/data` folder.
3. Open notebooks in the `/notebooks` folder via Google Colab or Jupyter Notebook to view and execute the code.
4. Install the required dependencies using `pip install -r requirements.txt` (assuming you have created a requirements.txt file listing all the libraries).

## Input data
Data taken from https://catalog.data.gov/ and https://www.ers.usda.gov/.

## Objectives and basic data of the project
### Goals and objectives
The first database is utilized for the following analyses:
- Analysis of Total Computed Taxes Collected Across All Counties Over All Years Presented: This analysis aggregates the total taxes collected by each county over the available years and visualizes the results, ranking counties from highest to lowest tax amounts collected, identifying the counties with the highest tax contributions.
- Analysis of the Top 10 Counties by the Amount of Taxes Collected, Broken Down by Year: Focuses on counties consistently showing the highest tax collections, breaking down tax collection data year by year to provide insights into the tax trends and fluctuations.
Required columns for these analyses: "Fiscal Year", "FIPS County Code", "County", and "Computed Tax".

The second database supports a variety of analyses focusing on employment metrics and economic conditions in the United States:
- Analysis of the Civilian Labor Force, Employed, and Unemployed in the U.S. by Year: Tracks changes in employment statistics over the years.
- U.S. Unemployment Rate by Year: Reviews the unemployment rates annually across the country.
- Linear Graphs for the Top 10 States by Highest Employment by Year: Visualizes employment trends in states with the highest employment rates.
- Linear Graphs for the Top 10 States by Highest Unemployment by Year: Charts unemployment trends in states with the highest unemployment rates.
- Additional analyses include detailed breakdowns of employment and unemployment for 2021, the top 20 counties by median household income, and correlation studies between unemployment rates and median household income percentages.
Essential columns from the database for these analyses: "FIPS_Code", "State", "Area_Name", "Attribute", and "Value". Specific lines used will be those related to "Civilian labor force", "Employed", "Unemployed", "Unemployment rate", "Median Household Income", "Med HH Income Percent of State Total"

#### Additional Analyses Using Merged Databases:

After merging the two databases, several complex analyses were performed to further explore the relationship between tax collections and employment metrics, particularly focusing on Iowa (IA). These analyses include:
- Correlation Calculation Between Tax Collections and Employment Levels in Iowa (IA): This analysis aimed to identify any statistical correlations between the amount of taxes collected and the employment levels, providing insights into how economic activity and employment might impact fiscal revenues.
- Quartile Calculations for Tax Collections: Tax data was divided into quartiles to facilitate more detailed analysis, allowing us to observe the distribution and disparities in tax collection amounts across different regions.
- Visualization of Unemployment Levels by Tax Collection Quartiles: This visualization compares the levels of unemployment within different quartiles of tax collections, illustrating how economic hardship or prosperity in terms of tax contributions correlates with employment rates.
- Visualization of Employment Numbers by Tax Collection Quartiles: Similarly, this analysis visualizes the number of employed individuals within the quartiles of tax collections, offering a visual representation of employment distribution relative to economic contributions.
- Computed Tax by Top 20 Counties in Iowa (IA) State and Fiscal Year: This detailed analysis focuses on the top 20 counties in Iowa by computed tax, broken down by fiscal year, to track how top-contributing counties have performed over time in terms of tax collection.

### Database structures
Two source databases were used for analysis.

The first database contains information about quarterly retail sales tax data by county and city, including the following:
Number of records: 40820
Columns:
- Fiscal Year: int64 (The year to which the financial statement relates.)
- Quarter Ending: object (The end date of the quarter to which 	the report relates.)
- County_Number: int64 (County number.)
- County: object (County name.)
- City: object (City name.)
- Number of Returns: int64 (The number of tax refunds or returns for a given county/city.)
- Taxable Sales: float64 (The amount of taxable sales in a given county/city.)
- Computed Tax: float64 (Computed tax payment based on taxable sales.)
- Percent of Tax: int64 (The percentage tax rate of total taxable sales.)
- FIPS County Code: int64 (A unique code for a county in the FIPS (Federal Information Processing Standards) standard.)
- Primary Lat Dec: float64 (Latitude (geographic coordinate) of the main population center in a given county/city.)
- Primary Long Dec: float64 (Longitude (geographic coordinate) of the main population center in a given county/city.)

The second database contains information about unemployment and median household income for the U.S., States, and counties, 2000–22, including the following:
Number of records: 31663
Columns:
- FIPS_Code: int64 (Unique code for the County in the FIPS (Federal Information Processing Standards) standard/State+county Federal Information Processing Standard (FIPS) code)
- State: object (State abbreviation)
- Area_Name: object (State or county name)
- Attribute: object (Name of attribute, which consists of attribute and year/name of attribute with year, for example, Unemployment_rate_2022, Civilian_labor_force_2022, Employed_2022,...)
- Value: float64 (Corresponding attribute value)

In the Attribute were found all years from 2000 to 2022 inclusive and 8 unique attribute names:
- Civilian_labor_force (Average annual civilian labor force)
- Employed Number of employees on average per year
- Unemployed Number of unemployed people on average per year
- Unemployment_rate Average annual unemployment rate
- Median_Household_Income Estimated median household income for the year
- Med_HH_Income_Percent_of_State_Total County median household income as a percentage of total state median household income
- Rural_Urban_Continuum_Code Rural-Urban Continuum Code
- Urban_Influence_Code Urban Influence Code
- Metro Subway dummy without metro 0 = no metro 1 = metro (based on 2013 OMB metropolitan area delimitation)

## Analysis methodology
### Data Preprocessing

During the preprocessing of the first database, it was discovered that all column values were non-null, indicating the absence of missing values. An analysis of the uniqueness of FIPS County Code and corresponding County names revealed 99 unique values for each. A verification was conducted, confirming a one-to-one correspondence between FIPS County Code and County, allowing any of these columns to be used as a key. 
```
print(df.groupby('FIPS County Code')['County'].nunique().max()) 
print(df.groupby('County')['FIPS County Code'].nunique().max())
1
1
```
However, considering that there are counties with identical names in different states in the USA, it was decided to use additional data from the second database, which includes extra attributes like the state name for precise identification.

The second database also showed no missing values. However, the Attribute column, which contained attribute names and years, required additional processing to separate data by years and attributes. Out of eight unique attributes, six (Civilian_labor_force, Employed, Unemployed, Unemployment_rate, Median_Household_Income, Med_HH_Income_Percent_of_State_Total) were selected for relevance to the analysis. The data were transformed into a pivot table pivot_df3, where each attribute became a column, and corresponding values were aggregated.

#### Handling Missing Values

The pivot table pivot_df3 exhibited a significant number of missing values in the columns Median_Household_Income and Med_HH_Income_Percent_of_State_Total. These missing values were replaced with zeros to ensure data integrity for further analysis.
```
pivot_df3.fillna(0, inplace=True)
```

#### Database Merging

To enable a comprehensive analysis, it was necessary to merge data from two distinct sources. The merging process involved the following steps:

1. **Data Merging**: Data from the second database (`pivot_df3`), which contains extended information for each county and year, was merged with the primary dataset (`df2`) using 'FIPS County Code' and 'Fiscal Year' from the first database, and 'FIPS_Code' and 'Year' from the second database. The merge was performed with an `inner` join to ensure that only records present in both tables were included:
```python
df2 = df2.merge(pivot_df3, left_on=['FIPS County Code', 'Fiscal Year'], right_on=['FIPS_Code', 'Year'], how='inner')
```
2. **Cleaning the Final DataFrame**: After merging, columns that duplicated the merging keys were removed to simplify the data structure and reduce the memory footprint of the DataFrame:
```python
df2.drop(['FIPS_Code', 'Year'], axis=1, inplace=True)
```
#### Characteristics of the Merged DataFrame:
- Class: pandas.core.frame.DataFrame
- Total Entries: 1089 entries
- Number of Columns: 12 non-null columns
The DataFrame structure after merging is outlined below:


| Index | Column Name                          | Non-Null Count | Data Type |
|-------|--------------------------------------|----------------|-----------|
| 0     | Fiscal Year                          | 1089 non-null  | int64     |
| 1     | County                               | 1089 non-null  | object    |
| 2     | FIPS County Code                     | 1089 non-null  | int64     |
| 3     | Computed Tax                         | 1089 non-null  | float64   |
| 4     | State                                | 1089 non-null  | object    |
| 5     | Area_Name                            | 1089 non-null  | object    |
| 6     | Civilian_labor_force                 | 1089 non-null  | float64   |
| 7     | Employed                             | 1089 non-null  | float64   |
| 8     | Med_HH_Income_Percent_of_State_Total | 1089 non-null  | float64   |
| 9     | Median_Household_Income              | 1089 non-null  | float64   |
| 10    | Unemployed                           | 1089 non-null  | float64   |
| 11    | Unemployment_rate                    | 1089 non-null  | float64   |

#### Unique Values:
- Fiscal Years Represented: 2012 to 2022
- Counties: A diverse range of counties from 'Adair' to 'Wright'.
- State: All records pertain to Iowa (IA), highlighting the data’s geographic specificity.

### Data Analysis
This section outlines the analytical techniques employed to explore relationships and distributions within the dataset. The analysis focuses on uncovering patterns and insights that can inform decision-making processes.
***************************************************************************************************************************
### Analysis of Unemployment Rate Distribution
**Normality Testing of the Distribution**
To test the assumption of normal distribution for the unemployment rate, the following statistical tests were conducted:

- **Shapiro-Wilk Test:** This test helps determine whether the data distribution follows a normal distribution. The results showed a p-value of 0.000, which is significantly below the accepted significance level of 0.05. This indicates that the data do not follow a normal distribution.
- **D'Agostino's K^2 Test:** Additionally, D'Agostino's K^2 test was conducted, which also confirmed significant deviations from normality (p-value = 0.000). The high test statistic value (24597.513) underscores a significant deviation from the normal distribution.
**Distribution Visualization**
To visually corroborate these findings, a histogram of the unemployment rate distribution was plotted:

```python
sns.histplot(pivot_df3['Unemployment_rate'], kde=True)
plt.title('Distribution of Unemployment Rate')
plt.xlabel('Unemployment Rate')
plt.ylabel('Frequency')
plt.show()
```
![Distribution of Unemployment Rate](https://raw.githubusercontent.com/OlhaAD/Comprehensive-analysis-of-economic-data-and-the-labor-market-in-the-United-States/main/visualizations/DistributionOfUnemploymentRate.png)

This histogram shows right-skewed asymmetry, multiple peaks, and a long right tail, which also indicates the presence of outliers and confirms the distribution does not conform to a classic normal distribution.

Based on the analysis conducted, it was decided to use the median instead of the mean for further data analysis of the unemployment rate. This is because the median is less sensitive to outliers and the asymmetry of the distribution. This approach not only enhances the accuracy of the analysis but also provides a more reliable basis for data-driven decision-making.
*************************************************************************************************************************
### Analysis of Relationship between Employment and Computed Tax by County in IA State

**Correlation Analysis**

In this analysis, we explored the relationship between the number of employed individuals and computed tax in counties across Iowa. We computed a correlation coefficient to quantify the strength of this relationship.

#### Findings:
- **Correlation Coefficient:** The computed correlation coefficient between the 'Computed Tax' and 'Number of Employed' is `0.9611`, indicating a very strong positive correlation.
- **Interpretation:** This strong correlation suggests that counties with higher employment levels typically collect higher amounts of tax. This is expected as more employed individuals likely mean more economic activity and, consequently, higher taxable amounts.

#### Visualization

The scatter plot below visualizes this relationship, clearly showing that as the number of employed increases, the computed tax also tends to increase.

![Relationship between Employment and Computed Tax by County in IA State](/visualizations/RelationshipEmloymentComputedTaxIA.png)

This pattern underscores the importance of employment levels as a significant factor in fiscal health at the county level.
The analysis was conducted using merged data from our main employment and taxation databases.
****************************************************************************************************************************
### Quartile Analysis of Tax Collections and Employment Levels

This section discusses the relationship between tax collections, employment levels, and unemployment rates across different tax quartiles in Iowa. Tax collections were divided into quartiles to analyze the socio-economic patterns across the state.

**Methodology**
The dataset was divided into quartiles based on the computed tax to explore variations in employment and unemployment metrics. Each entry was classified into one of four quartiles:

- **1st Quartile:** Lowest tax collections.
- **2nd Quartile:** Moderate tax collections.
- **3rd Quartile:** Higher than average tax collections.
- **4th Quartile:** Highest tax collections.

#### Visualizations
**Employment Distribution by Tax Quartile**
This bar chart represents the average number of employed individuals categorized by tax quartile. It clearly shows an increase in employment with higher tax brackets.

![Employed by Tax Quartile](/visualizations/EmployedbyTaxQuartile.png)

**Unemployment Rate by Tax Quartile:**
This visualization demonstrates the median unemployment rate within each tax quartile. Despite higher employment in the higher quartiles, these also feature marginally higher unemployment rates.

![Unemployment Rate by Tax Quartile](/visualizations/URbyTaxQuartile.png)

#### Findings:
The data suggests that while higher tax quartiles, indicative of higher economic activity, correlate with higher employment, they also slightly elevate unemployment rates. This could reflect more dynamic job markets in economically active counties where higher turnovers and seasonal jobs might inflate unemployment statistics temporarily.
***************************************************************************************************************************
### Computed Tax Contributions by Top Counties in IA State
This section of the analysis focuses on understanding the distribution of tax contributions across counties over several years. Here’s how the data was processed and visualized:

- **Data Aggregation:** For each year, we aggregated the computed tax for each county to get a yearly total per county.
```python
heatmap_county_tax = df2.pivot_table(index='Fiscal Year', columns='County', values='Computed Tax', aggfunc='sum')
total_tax_per_year = heatmap_county_tax.sum(axis=1)
```
- **Percentage Calculation:** We then calculated each county's tax contributions as a percentage of the total tax collected each year. This helps in understanding the relative contribution of each county compared to the total.
```python
tax_percentage = heatmap_county_tax.divide(total_tax_per_year, axis=0) * 100
tax_percentage_rounded = tax_percentage.round(2)
```
- **Averaging Percentages:** To get a consistent measure across years, we calculated the average percentage contribution for each county over the observed years.
```python
average_tax_percentage_by_county = tax_percentage_rounded.mean(axis=0)
```
- **Top Counties Selection:** From this dataset, we identified the top 20 counties with the highest average percentage contributions to computed tax.
```python
top_20_counties = average_tax_percentage_by_county.nlargest(20)
```

A heatmap visualization was created to display these top 20 counties computed tax contributions across different years. This allows for a quick visual assessment of which counties have consistently been the largest contributors to the tax base.
![Computed Tax by 20 TOP County in IA State and Fiscal Yea](/visualizations/PercentTaxByCountyAndYearTop20.png)
This analysis is crucial for policymakers and economic planners to identify key areas contributing to the fiscal health of the region and to make informed decisions about resource allocation and development planning.
***************************************************************************************************************************
### Analysis of Median Household Income as a Percentage of State Total Across Counties for 2021

To assess the relationship between unemployment rates and household median income across various US counties in 2021, data on the percentage of median household income relative to the state median were used.

#### Visualizations
**Histogram of the Distribution of Median Household Income Percent of State Total**
The data show that most counties have a median income around the state average, with the majority of values concentrated around 75% to 100% of the state median. The distribution is normal with some outliers, particularly in counties where the median income significantly exceeds the state average.

![Distribution of Median HH Income Percent of State Total across Counties for 2021](/visualizations/DistributionMHICounties2021.png)

**Boxplot of Median Household Incomes Across Counties:**
The visualization confirms the presence of outliers where the median income in counties is significantly higher than the state average. The majority of the data is concentrated between 75% and 100% of the state median income.

![Boxplot of Median HH Income Percent of State Total across Counties for 2021](/visualizations/BoxplotMHICounties2021.png)

**Scatter Plot of Median Household Income Relative to Unemployment Rate:**
There is a downward trend, confirming that as the unemployment rate increases, the median household income decreases.
The data show significant variability, especially at low levels of unemployment, indicating differences in economic conditions across counties.

![Scatter Plot of Unemployment Rate vs. Median Household Income Percent of State Total](/visualizations/ScatterPlotURvsMHI2021.png)

**Statistical Data:**
Mean: 87.35%
Standard Deviation: 23.82%
Minimum Value: 0%
25th Percentile: 75.7%
Median (50th Percentile): 86.7%
75th Percentile: 99.6%
Maximum Value: 246.9%

These results provide deeper insights into the economic status of various counties in relation to the overall economic state of the state, as well as the impact of unemployment levels on household incomes.
**************************************************************************************************************************
### Analysis of Top 20 Counties by Median Household Income Relative to State Total

In 2021, a detailed analysis was conducted on the top 20 counties with the highest median household income as a percentage of their state's total. This analysis aimed to identify outliers and understand the economic disparities within these regions.

**Methodology**
The data for the median household income was filtered to focus only on the top 20 counties. These counties were identified as potential outliers from previous scatter plots and boxplots, which suggested significant variations in income levels.

#### Visualizations
**Bar Chart of Top 20 Counties by Median Household Income as a Percent of State Total for 2021**
A bar chart was created to visually represent these counties, ranking them by their median household income relative to the state total. This visualization helps in quickly identifying which counties have exceptionally high income levels compared to the rest of their state.

![Top 20 Counties by Median Household Income as a Percent of State Total for 2021](/visualizations/Top20CountiesByMHI2021.png)

**Correlation Analysis of Relationship between Unemployment Rate and Median Household Income Percent of State Total**
```python
correlation = top_20_counties['Unemployment_rate'].corr(top_20_counties['Med_HH_Income_Percent_of_State_Total'])
```
We calculated the correlation between unemployment rates and median household income within these counties. The result was a correlation coefficient of -0.2137, indicating a slight negative relationship. This suggests that higher unemployment rates in these counties slightly correlate with lower median household incomes as a percentage of the state total.

**Scatter Plot with Regression Line Relationship of Unemployment Rate vs Median Household Income Percent of State Total**
To further explore this relationship, a scatter plot with a regression line was created. This plot shows the relationship between unemployment rates and median household income percentages. The regression line helps illustrate the overall trend across these counties, confirming the mild negative correlation observed.

![Relationship between Unemployment Rate and Median Household Income Percent of State Total](/visualizations/RelationshipURandMHIinTOP20Counties.png)

#### Findings and Conclusions
This analysis highlights the significant disparities in income across different counties and the subtle impact of unemployment on income levels. These findings could be crucial for policymakers focusing on economic balance and development in regions with disparate incomes.
In addition to comparison with unemployment rates, it is important to conduct a contextual analysis: the economic, social and geographical characteristics of these counties can be examined. This may include industrial and economic profile, income distribution, educational attainment, access and quality of healthcare and housing. For example, **Los Alamos,  New Mexico**, ranked first for its high median household income, which can be attributed to several key factors:

- **Highly Paid Workforce:** Los Alamos is home to the Los Alamos National Laboratory, which specializes in advanced scientific and engineering research, particularly in nuclear physics. Most jobs in the area offer high salaries and are linked to federal government or government contracts.
- **Educational Level:** The educational level in Los Alamos is among the highest in the country. A large proportion of the population has higher education, which correlates with higher wages and a higher standard of living.
-**Economic Specialization:** The economy of the county is focused on science and technology sectors, which also contributes to the high income level. Scientific and technical industries generally offer high-paying jobs.
-**Demographic and Social Factors:** The small population size and high standard of living create favorable conditions for maintaining high incomes. Los Alamos also features high living standards and a low poverty rate.
These factors collectively contribute to Los Alamos maintaining one of the highest median household incomes at the national level.
****************************************************************************************************************************
### US Labor Force Dynamics by Year

This graph depicts the changes in the US labor force dynamics over the years. The data includes three key statistics:

- **Civilian Labor Force**: Represents the total number of people in the United States available for work, including those in jobs and those seeking employment.
- **Employed**: The segment of the labor force that is currently employed.
- **Unemployed**: The segment of the labor force that is not employed but actively seeking work.

Here's the visual representation of these trends from 2000 to 2022:

![US Labor Force Dynamics](/visualizations/USLaborForceDynamicsByYear.png)

#### Findings and Key Conclusions
- The civilian labor force and the number of employed individuals have shown a general increase over the years, reflecting population growth and economic expansion.
- The number of unemployed shows significant peaks, particularly around the years corresponding to economic recessions, such as the 2008 financial crisis and the 2020 pandemic.
- The relative stability of employment compared to the fluctuations in unemployment highlights the resilience and recovery capability of the US labor market.

This graph provides a clear overview of how economic policies and market forces have influenced employment trends in the United States over two decades.
****************************************************************************************************************************
### US Unemployment Rate by Year

This line graph illustrates the annual changes in the US unemployment rate from 2000 to 2022. The data points clearly delineate the fluctuations in unemployment, highlighting economic downturns and recoveries.

![US Unemployment Rate by Year](/visualizations/USUnempoloymentRateByYear.png)

#### Findings and Key Conclusions
- A noticeable peak in unemployment occurred around 2010, coinciding with the aftermath of the 2008 financial crisis, reflecting its profound impact on the labor market.
- The unemployment rate gradually decreased from 2010 onwards as the economy recovered, reaching historic lows just before 2020.
- The sharp spike in 2020 represents the dramatic effect of the COVID-19 pandemic on the US labor market, showcasing how external shocks can lead to rapid increases in unemployment.

The graph provides a visual representation of economic resilience and the challenges faced by the labor market over two decades. Understanding these patterns helps in evaluating the effectiveness of employment policies and economic interventions.
**************************************************************************************************************************
### Employment and Unemployment Trends in the Top 10 States
#### Visualizations
**Employment Trends by Year in the Top 10 States**
This visualization illustrates the employment trends from 2000 to 2022 in the top 10 states with the highest employment figures. Each line represents one state, showing the change in the number of employed individuals over the years. The states like California and Texas show a consistent increase in employment, highlighting robust economic growth and job creation.

![Employment Trends by Year in the Top 10 States](/visualizations/EmploymentTrendsByYear.png)

**Unemployment Trends by Year in the Top 10 States**
Following the employment analysis, we explore the unemployment trends in the same period for the states with the highest numbers of unemployed individuals. The graph details how the unemployment rates have fluctuated, with notable peaks during economic downturns. This visualization helps in understanding the impact of economic changes on job markets in these populous states.

![Unemployment Trends by Year in the Top 10 States](/visualizations/UnemploymentTrendsByYear10Stats.png)

**Unemployment Rate Trends in the Top 10 States**
Additionally, we analyze the unemployment rate trends specifically in the states with the highest rates. This graph provides a clear picture of how unemployment rates have evolved, peaking particularly during major economic crises.

![Unemployment Rate Trends by Year in the Top 10 States](/visualizations/UnemploymentRateByYear10States.png)

These visual analyses not only highlight the states with significant employment and unemployment figures but also serve as an essential tool for comparing economic health and labor market dynamics across different regions. These insights are crucial for policymakers, economists, and researchers interested in the labor market's responses to economic changes over the past two decades.
****************************************************************************************************************************
### 2021 U.S. Employment and Unemployment Distribution by State
#### Visualizations
**Employment Distribution**
The pie chart illustrates the distribution of employment across different states in 2021, highlighting the share of employment in each major state and grouping smaller contributions under the "Other" category. California and Texas hold significant portions of the nation’s employment, emphasizing their major roles in the U.S. economy. This distribution helps in understanding the economic activity concentration across the states.

![Employed by State in 2021 (with "Other" category)](/visualizations/EmploymentByState2021.png)

**Unemployment Distribution**
Similarly, the unemployment distribution pie chart for 2021 shows how unemployment rates are spread across major states, with a significant portion again categorized under "Other" to include states with lesser shares. This chart is particularly useful for identifying regions with higher unemployment challenges and could guide policy focus areas.

![Unemployed by State in 2021 (with "Other" category)](/visualizations/UnemploymentByState2021.png)

****************************************************************************************************************************
### Heatmap of Unemployment Rates by State and Year
This heatmap provides a visual representation of unemployment rates across various U.S. states from the year 2000 to 2022. Each cell in the heatmap corresponds to a specific state and year, with colors varying according to the unemployment rate—warmer colors indicate higher rates, while cooler colors represent lower rates.
 ![Unemployment Rates by State and Year](/visualizations/HMUnemploymentRateByStateAndYear.png)
The visualization clearly shows temporal trends and fluctuations in unemployment rates across different regions, highlighting years of economic downturn or recovery. For instance, high unemployment rates across most states during economic crises can be easily identified.

This comprehensive view aids in understanding how different states have been affected over time and can help policymakers and analysts pinpoint patterns or anomalies in employment trends.

### Conclusion
This comprehensive analysis showcased significant insights into the dynamics of the U.S. labor market over several years, emphasizing changes in employment, unemployment rates, and computed tax by county. Key findings include:

- **Labor Market Trends:** There has been a steady increase in the civilian labor force and employment rates over the years, with a notable resilience or recovery in employment post-economic downturns and after pandemics.
- **Unemployment Dynamics:** The unemployment rate shows clear cyclical patterns, spiking during economic recessions and pandemics and gradually decreasing during recovery phases.
- **Tax Analysis:** Computed tax analysis revealed that higher employment levels typically correspond to higher tax collections, indicating more robust economic activity in these areas.
- **Geographical Variations:** Certain states and counties displayed distinct economic characteristics, with places like Los Alamos showing unusually high median household income levels, likely due to specific local economic conditions such as the concentration of high-tech jobs and government facilities.

These findings are crucial for policymakers, local government officials, and economic planners. Understanding these patterns can aid in crafting targeted economic policies, especially in regions showing higher volatility in employment and economic stability.

### Future Directions
Further research could explore:

- **Predictive Analysis:** Employing models to predict future labor market trends based on historical data, aiding in proactive policy making.
- **Sector-Specific Studies:** Diving deeper into which sectors most significantly impact employment trends across different regions could yield insights for focused economic development.
- **Impact of External Factors:** Investigating the impact of global economic shifts, such as trade policies or international economic crises, wars, on local labor markets.
- **Educational Impact Analysis:** Studying how the educational levels of populations correlate with unemployment rates, median income, and other economic indicators can provide deeper insights into the socio-economic dynamics that shape regional economies. This analysis could help tailor educational programs and initiatives to better meet the needs of the labor market.
  
### Contact

For any further inquiries or to discuss this analysis, please feel free to contact:
Olha Danylchenko
dolga334320@gmail.com
