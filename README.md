## Project description
This project is an analysis of labor market data in the United States. The analysis includes data on unemployment, employment trends, tax collections and other economic indicators for the entire country, as well as for individual states and counties.

## How to start a project
1. Clone the repository to your local computer.
2. Data for analysis is located in the `/data` folder.
3. Open notebooks in the `/notebooks` folder via Google Colab or Jupyter Notebook to view and execute the code.
4. Install the required dependencies using `pip install -r requirements.txt` (assuming you have created a requirements.txt file listing all the libraries).

## Input data
Data taken from (https://catalog.data.gov/) and https://www.ers.usda.gov/.

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
