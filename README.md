# Assignment-
Uploaded SQL script on GitHub. Analyzed food prices, categorized cities, and calculated averages. Explored joins with commodities data.
set sql_safe_updates=0;
use assignment;
select * from wfp_food_prices_pakistan;
select * from commodity;
# Converting date into date time format
UPDATE wfp_food_prices_pakistan SET date = STR_TO_DATE(date, '%m/%d/%y');
alter table wfp_food_prices_pakistan modify date datetime;
/*
●	Select dates and commodities for cities Quetta, Karachi, and Peshawar where price 
was less than or equal 50 PKR
*/
SELECT date, cmname, price, mktname FROM wfp_food_prices_pakistan
WHERE mktname IN ('Quetta', 'Karachi', 'Peshawar') 
      AND price <= 50 
ORDER BY date, mktname, cmname;

# ●	Query to check number of observations against each market/city in PK
SELECT country, mktname, COUNT(*) AS observation_count FROM wfp_food_prices_pakistan
GROUP BY country, mktname
ORDER BY observation_count asc;

#Show number of distinct cities
select distinct mktname from wfp_food_prices_pakistan;

#List down/show the names of commodities in the table
select distinct cmname from commodity;


# Just to check spelling of Wheat Flour REtail
SELECT DISTINCT cmname FROM wfp_food_prices_pakistan;
# List Average Prices for Wheat flour - Retail in EACH city separately over the entire period.
select cmname , avg(price) as Average_Price_Wheat_Flour, mktname from wfp_food_prices_pakistan
where cmname="Wheat Flour - Retail"
group by mktname
order by Average_Price_Wheat_Flour desc;
/*●	Calculate summary stats (avg price, max price) for each city separately for all 
cities except Karachi and sort alphabetically the city names, commodity names where 
commodity is Wheat (does not matter which one) with separate rows for each commodity
*/
SELECT mktname AS City,cmname AS Commodity,AVG(price) AS Average_Price,MAX(price) AS Max_Price FROM 
wfp_food_prices_pakistan
WHERE mktname != 'Karachi' AND cmname LIKE 'Wheat%'
GROUP BY City, Commodity
ORDER BY City ASC, Commodity ASC;

/*Calculate Avg_prices for each city for Wheat Retail and show only those avg_prices which 
are less than 30
*/
SELECT mktname AS City,AVG(price) AS Average_Price_Wheat_Retail FROM wfp_food_prices_pakistan
WHERE cmname = 'Wheat flour - Retail'
GROUP BY City HAVING AVG(price) < 30
ORDER BY Average_Price_Wheat_Retail ASC;
/*Prepare a table where you categorize prices based on a logic (price < 30 
is LOW, price > 250 is HIGH, in between are FAIR)
*/
SELECT date, cmname, mktname AS city, price,
CASE WHEN price < 30 THEN 'LOW' WHEN price > 250 THEN 'HIGH' ELSE 'FAIR' END AS price_category
FROM wfp_food_prices_pakistan;

/*●	Create a query showing date, cmname, category, city, price, city_category where Logic for 
city category is:Karachi and Lahore are 'Big City', Multan and Peshawar are 'Medium-sized 
city', Quetta is 'Small City'
*/
SELECT date, cmname, category, mktname AS city, price,
CASE WHEN mktname IN ('Karachi', 'Lahore') THEN 'Big City'
WHEN mktname IN ('Multan', 'Peshawar') THEN 'Medium-sized City'
WHEN mktname = 'Quetta' THEN 'Small City'
ELSE 'Unknown' END AS city_category
FROM wfp_food_prices_pakistan;
/*Create a query to show date, cmname, city, price. Create new column price_fairness \
through CASE showing price is fair if less than 100, unfair if more than or equal 
to 100, if > 300 then 'Speculative'
*/
SELECT date, cmname, mktname AS city, price,
CASE WHEN price < 100 THEN 'Fair'WHEN price >= 100 AND price <= 300 THEN 'Unfair'
WHEN price > 300 THEN 'Speculative' ELSE 'Unknown' END AS price_fairness
FROM wfp_food_prices_pakistan;

#Join the food prices and commodities table with a left join. 
SELECT wfp.date, wfp.cmname, wfp.mktname AS city, wfp.price, c.category
FROM wfp_food_prices_pakistan wfp
LEFT JOIN commodity c ON wfp.cmname = c.cmname;
#Join the food prices and commodities table with an inner join
SELECT wfp.date, wfp.cmname, wfp.mktname AS city, wfp.price, c.category
FROM wfp_food_prices_pakistan wfp
INNER JOIN commodity c ON wfp.cmname = c.cmname;
