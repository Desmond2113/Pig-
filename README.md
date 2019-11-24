# Pig-



-- QUESTION 2: List the countries together with the number of cities in each country.

--Loading up data columns

CountryData = LOAD 'unsd-citypopulation-year-fm.csv' USING PigStorage (',')AS (Country:chararray, year:int ,Sex: chararray,   City: chararray, CityType:chararray, RecordType: chararray, Reliability: chararray, SourceYear:int, Value:int, ValueFootnotes:int);


--grouping the countrydata by country
countriesg= GROUP CountryData BY Country;


-- forEach of the country data grouped by the Country, This code generates that group with a count(number of cities for each country)

ForCountriesCity = FOREACH countriesg  GENERATE group,COUNT(CountryData.City);


SORTING = ORDER ForCountriesCity BY year DESC;


-- displaying of final answer

dump SORTING;









