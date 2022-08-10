# codingChallenge
 codingChallenge notebooks

# Data Science Coding Challenge

This coding challenge consists of two parts:
* sql challenge
* python challenge

You can provide the results to us
* via checking the files into a GitLab or GitHub repository and sending us the link (preferred)
* by sending us the files via e-mail

Some Remarks:
* Provide both the code and the results to us. 
* We're not only interested in the "end result" but also in your thought processes, so make sure to leave some comments in the code explaining how you worked towards the result.
* Put a bit of effort into code structure and formatting, and also your repository structure if you decide to provide your results to us this way - we'll also have a look at that

Deadline to hand in your results is the YYYY-MM-DD.


## Part 1: Sql

For this challenge we make use of BigQuery and it's public "COVID-19 Cases by Country" dataset of the European Centre for Disease Prevention and Control. [See here how to query a public dataset with BigQuery](https://cloud.google.com/bigquery/docs/quickstarts/query-public-dataset-console).

Most of the "Google SQL" on BigQuery follows the SQL standard - if in doubt you can check the [Google Query Syntax](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax).


### Questions

(1) What time frame does the dataset cover? how many countries does it contain?
Link to query: https://console.cloud.google.com/bigquery?sq=90057302534:b37761d45d1a4997a179f3856eae9ca4

SELECT date_diff(max(date), min(date), day) as TimeframeDays, count(DISTINCT countries_and_territories) as TotalCountries
FROM `bigquery-public-data.covid19_ecdc_eu.covid_19_geographic_distribution_worldwide`

(2) Which country had the most deaths due to Covid-19 on a single day, and which day was that?
Link to query: https://console.cloud.google.com/bigquery?sq=114628106456:9f0528a9f29c4932a84a0ccf06b104f6

SELECT countries_and_territories, daily_deaths, date
FROM `bigquery-public-data.covid19_ecdc_eu.covid_19_geographic_distribution_worldwide`
WHERE daily_deaths = (SELECT MAX(daily_deaths) FROM `bigquery-public-data.covid19_ecdc_eu.covid_19_geographic_distribution_worldwide` AS totaldeaths)

 

(3) Which country had the most deaths due to Covid-19 in August 2020, and how many?
Link to query: https://console.cloud.google.com/bigquery?sq=114628106456:6b3dd3fe3efc45848b7742ffec28bacc

SELECT countries_and_territories, deaths, month, year
FROM `bigquery-public-data.covid19_ecdc_eu.covid_19_geographic_distribution_worldwide`
WHERE month = 8 AND year = 2020
ORDER BY deaths DESC
LIMIT 1

(4) How many countries have complete (i.e. one sample per day) data from at least April 2020 to (including) November 2020?
Link to query: https://console.cloud.google.com/bigquery?sq=114628106456:10289803f5894c3e8f7705321aa8249a

######  /* First select all distinct countries between April and November 2020*/
SELECT 
  (SELECT count(DISTINCT countries_and_territories) FROM `bigquery-public-data.covid19_ecdc_eu.covid_19_geographic_distribution_worldwide` WHERE month BETWEEN 4 and 11 AND year = 2020)
###### /* Select all distinct countries with incomplete data (i.e. zero samples per day) */
- (SELECT count(DISTINCT countries_and_territories) FROM `bigquery-public-data.covid19_ecdc_eu.covid_19_geographic_distribution_worldwide` WHERE daily_confirmed_cases= 0 AND month BETWEEN 4 and 11 AND year = 2020 ) AS CountriesCompleteSamples


(5) On which day and in which country was Covid-19 deadliest, defined as ``daily_deaths / sum of daily confirmed cases over last 14 days``. Use only the subset of the countries found in Question 4 to answer this question. What do you observe?
Link to query: https://console.cloud.google.com/bigquery?sq=114628106456:aae802899e7b4ecdafb14206810b3b91

#####  /* First get all distinct countries and max. deaths between month 4 and 11 in year 2020 as countries1 */
#####  /* Second, get all distinct countries where daily_confirmed_cases is 0 between month 4 and 11 in year 2020 as countries0*/
SELECT count(DISTINCT countries_and_territories) AS CountriesCompleteSamples, countries_and_territories as countries0, max(daily_deaths) as MAXDeaths, sum(daily_confirmed_cases) as TotalDailyCases
FROM `bigquery-public-data.covid19_ecdc_eu.covid_19_geographic_distribution_worldwide`
WHERE daily_confirmed_cases= 0 AND month BETWEEN 4 and 11 AND year = 2020
GROUP BY countries_and_territories 
ORDER BY CountriesCompleteSamples, MAXDeaths DESC, TotalDailyCases DESC
#####  /* Next step would be to subtract result of countries1 by countries0 and aggregating daily_deaths / sum of daily confirmed cases over last 14 days*/


## Part 2: Python

Please use Python to work on the following questions. You are free to choose suitable packages, e.g. pyspark, pandas, ...
Ideally, you create a Jupyter Notebook to share your results with us.


### Preparation

Get the hourly air temperature values from a German weather station near Reutlingen: [stundenwerte_TU_03278_20090401_20211231_hist.zip](https://opendata.dwd.de/climate_environment/CDC/observations_germany/climate/hourly/air_temperature/historical/stundenwerte_TU_03278_20090401_20211231_hist.zip)



### Questions

(1) The location of the weather station is contained within the file. Compute the air distance to the Bosch plant located at (48.49524, 9.13782) using a suitable formula.

please find solution in Jupyter Notebook: codingChallenge_MarenLeuthner.ipynb


Now read the file ``produkt_tu_stunde_20090401_20211231_03278.txt`` contained in the archive with Python and answer the following questions. The available columns are:
* ``STATIONS_ID``: not relevant
* ``MESS_DATUM``: date and time of measurement
* ``QN_9``: not relevant
* ``TT_TU`` air temperature in °C
* ``RF_TU`` relative humidity in %
* ````: not relevant

Missing values in ``TT_TU`` and ``RF_TU`` are encoded as ``-999``.

(2) Describe the general properties of the dataset. This could be, among others:
* number of samples, missing values, ...
* measures of central tendency
* measures of variability (or spread)
* correlation measures

... that make sense in the given context.


please find solution in Jupyter Notebook: codingChallenge_MarenLeuthner.ipynb


(3) Compute the minimum, maximum and average temperature in January 2017.

##### creating new dataframe for temperature in January 2017
df_temp = df_new[(df_new.date == '2017-01')]
#####  output minimum, maximumg and average temperature by min, max and mean pandas operations
print(f"Minimum temperature: {df_temp['TT_TU'].min()}, maximum temperature: {df_temp['TT_TU'].max()} and average temperature: {df_temp['TT_TU'].mean()}")


(4) Assess the temperature development in the data. Can you find signs of climate change? Perform aggregations, visualizations to show your findings.

please find solution in Jupyter Notebook: codingChallenge_MarenLeuthner.ipynb