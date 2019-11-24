# Pig-

QUESTION 1
CountryData = LOAD 'unsd-citypopulation-year-fm.csv' USING PigStorage (',')AS (Country:chararray, year:int ,Area:chararray, Sex: chararray,   City: chararray, CityType:chararray, RecordType: chararray, Reliability: chararray, SourceYear:int, Value:int, ValueFootnotes:int);


ForCountries   =   FOREACH    CountryData   GENERATE   Country;


ForCount   =   DISTINCT ForCountries ;



GroupingCountries = GROUP  ForCount ALL;
 


CountingCountries  = FOREACH  GroupingCountries GENERATE   COUNT (ForCount);


dump CountingCountries





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



--Question 3: List countries in ascending order of female-to-male ratio, throughout the years. 

CountryData = LOAD 'unsd-citypopulation-year-fm.csv' USING PigStorage (',')AS (Country:chararray, Year:int, Area:chararray, Sex: chararray,   City: chararray, CityType:chararray, RecordType: chararray, Reliability: chararray, SourceYear:int, Value:int, ValueFootnotes:int);




-- Grouping data (Country,Year,Sex) from the loaded Countrydata
Grouping
CountryyearSex= GROUP CountryData BY (Country,Year,Sex);



-- This line of code sums all the Value column

totalValue = FOREACH GroupingCountryyearSex GENERATE group, SUM(CountryData.Value) AS ValueSum;



--This splits the Sex column as seen

SPLIT totalValue INTO  M if group.Sex == 'Male', W if group.Sex == 'Female';



--This line of code combines the Country year by Male and Female for each country and year

joining = JOIN M BY (group.Country,group.Year) , W BY (group.Country,group.Year);




-- This line of code generates and removes the bags or brackets.

Generation = FOREACH joining GENERATE FLATTEN(M::group.Country) AS Country, FLATTEN(M::group.Year) AS Year, FLATTEN(M::ValueSum) AS MalePopulation, FLATTEN(W::ValueSum) AS FemalePopulation;


-- Using the nested foreach function

NESTING = FOREACH Generation  { ratio = (float)FemalePopulation / (float)MalePopulation;
	
                                GENERATE Country AS Country, Year AS Year, ratio AS Ratio;
};

-- This line of code arranges the Nested foreach variable in ascending order

ord = ORDER NESTING BY Ratio ASC;

dump ord


















