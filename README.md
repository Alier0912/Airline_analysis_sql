## Airline Analysis

![](https://github.com/Alier0912/Airline_analysis_sql/blob/main/Airplane%20In%20Flight%2C%20Airplane%2C%20Flight%20PNG%20Transparent%20Image%20and%20Clipart%20for%20Free%20Download.jpeg)
* This project contains an analysis of airline data, exploring flight routes, airport performance, and other key metrics.
* Dive in to explore the insights!


```sql
DROP TABLE IF EXISTS business_flights;
CREATE TABLE business_flights
(
    date DATE,
    airline VARCHAR(255),
    ch_code VARCHAR(255),
    num_code VARCHAR(255),
    dep_time VARCHAR(255),
    from_location VARCHAR(255),
    time_taken VARCHAR(255),
    stop VARCHAR(255),
    arr_time VARCHAR(255),
    to_location VARCHAR(255),
    price INT
);

DROP TABLE IF EXISTS general_flights;
CREATE TABLE general_flights
(
    flight_id INT ,
     airline VARCHAR(255),
     flight VARCHAR(255),
     source_city VARCHAR(255),
      departure_time VARCHAR(255),
      stops VARCHAR(255),
      arrival_time VARCHAR(255),
      destination_city VARCHAR(255),
      class VARCHAR(255),
      duration FLOAT,
      days_left INT,
      price INT
);

DROP TABLE IF EXISTS economy_flights;
CREATE TABLE economy_flights
(date DATE,
airline VARCHAR(255),
ch_code VARCHAR(255),
num_code VARCHAR(255),
dep_time VARCHAR(255),
from_location VARCHAR(255),
time_taken VARCHAR(255),
stop VARCHAR(255),
arr_time VARCHAR(255),
to_location VARCHAR(255),
price INT
);
```

```sql
SELECT 
     * 
FROM general_flights
```

```sql
SELECT 
    * 
FROM 
business_flights
```

```sql
SELECT 
    * 
FROM economy_flights
```



-- Data Cleaning

```SQL
SELECT * FROM general_flights
``` 

```SQL
SELECT * FROM general_flights
WHERE flight_id IS NULL
OR airline IS NULL
OR flight IS NULL
OR source_city IS NULL
OR departure_time IS NULL
OR stops IS NULL
OR arrival_time IS NULL
OR destination_city IS NULL
OR class IS NULL
OR duration IS NULL
OR days_left IS NULL
OR price IS NULL
``` 
*our data is clean and there are no missing values*

```SQL
SELECT * FROM business_flights
WHERE date IS NULL
OR airline IS NULL
OR ch_code IS NULL
OR num_code IS NULL
OR dep_time IS NULL
OR from_location IS NULL
OR time_taken IS NULL
OR stop IS NULL
OR arr_time IS NULL
OR to_location IS NULL
OR price IS NULL
```
*our data is clean and there are no missing values*

```SQL
SELECT * FROM economy_flights
WHERE date IS NULL
OR airline IS NULL
OR ch_code IS NULL
OR num_code IS NULL
OR dep_time IS NULL
OR from_location IS NULL
OR time_taken IS NULL
OR stop IS NULL
OR arr_time IS NULL
OR to_location IS NULL
OR price IS NULL
```

 **our data is clean and there are no missing values*

 -------------------

#### Benchmark Questions

**The aim of our study is to answer the below research questions:**

1. Does price vary with Airlines?
3. How is the price affected when tickets are bought in just 1 or 2 days before departure?
4. Does ticket price change based on the departure time and arrival time?
5. How the price changes with change in Source and Destination?
6. How does the ticket price vary between Economy and Business class?

------------------
 **Data Analysis**
1. Does price vary with Airlines?
```SQL
SELECT 
    airline,
    ROUND(AVG(price)::integer,2) AS avg_price 
FROM general_flights
GROUP BY 1 
ORDER BY 2 DESC
```
2. How is the price affected when tickets are bought in just 1 or 2 days before departure?
```SQL
SELECT 
    days_left,
    SUM(price) AS avg_price,
    COUNT(flight_id) AS total_flights
FROM general_flights
WHERE days_left IN (1,2)
GROUP BY 1
ORDER BY 2 DESC
```

3. Does ticket price change based on the departure time and arrival time?

```SQL
SELECT 
    departure_time,
    arrival_time,
    ROUND(AVG(price)::integer,2) AS avg_price,
    stops
FROM general_flights
GROUP BY 1,2,4
ORDER BY 3 DESC
```

4. How the price changes with change in Source and Destination?

```SQL
SELECT 
    source_city,
    destination_city,
    ROUND(AVG(price)::integer,2) AS avg_price
FROM general_flights
GROUP BY 1,2
ORDER BY 3 DESC
```

5. How does the ticket price vary between Economy and Business class?

```SQL
SELECT 
    class,
    ROUND(AVG(price)::integer,2) AS avg_price
FROM general_flights
GROUP BY 1
ORDER BY 2 DESC
```
---------------
**Further Analysis**
```SQL
SELECT 
        *
FROM business_flights
```

1. Classifying the flights based on time taken
```SQL

WITH flight_class
AS
(
SELECT 
     airline,
     COUNT(*) AS total_flights,
     CASE 
         WHEN time_taken BETWEEN '00h 00m' AND '03h 45m' THEN 'Short'
         WHEN time_taken BETWEEN '03h 46m' AND '06h 59m' THEN 'Medium'
         ELSE 'Long'
        END AS time_category,
        time_taken,
        price
FROM business_flights
GROUP BY 1,4,5
ORDER BY 2 DESC
) 
SELECT 
    time_category,
    COUNT(*) AS total_flights,
    ROUND(AVG(price),2) AS avg_price
FROM flight_class
GROUP BY 1
ORDER BY 2 DESC
```

2. Segmenting flights by departure time
   
```SQL
WITH flight_time
AS
(
SELECT 
     airline,
     dep_time,
        COUNT(*) AS total_flights,
    CASE 
       WHEN dep_time BETWEEN '00:00' AND '06:00' THEN 'Early Morning'
       WHEN dep_time BETWEEN '06:01' AND '12:00' THEN 'Morning'
       WHEN dep_time BETWEEN '12:01' AND '18:00' THEN 'Afternoon'
       ELSE 'Evening'
    END AS dep_time_category,
    price
FROM business_flights
GROUP BY 1,2,5
ORDER BY 3 DESC
)
SELECT 
    dep_time_category,
    COUNT(*) AS total_flights,
    ROUND(AVG(price),2) AS avg_price
FROM flight_time
GROUP BY 1
ORDER BY 2 DESC
```

3. Most profitable airlines
```SQL
SELECT 
    airline,
    COUNT(*) AS total_flights,
    ROUND(SUM(price),2) AS total_revenue
FROM business_flights
GROUP BY 1
ORDER BY 2 DESC
```

4. Most profitable routes
```SQL
SELECT 
    from_location,
    to_location,
    COUNT(*) AS total_flights,
    ROUND(SUM(price),2) AS total_revenue
FROM business_flights
GROUP BY 1,2
ORDER BY 3 DESC
```

5. Analyzing stops in flights
```SQL
SELECT 
      from_location,
        to_location,
      stop,
      SUM(price) AS total_revenue
FROM business_flights
GROUP BY 1,2,3
ORDER BY 4 DESC
```
------------------------------------
#### ANALYSIS OF ECONOMY FLIGHTS
```SQL
SELECT * FROM economy_flights
```

1. Classifying the flights based on time taken
```SQL
WITH flight_class
AS
(
SELECT 
        airline,
        COUNT(*) AS total_flights,
        CASE 
            WHEN time_taken BETWEEN '00h 00m' AND '03h 45m' THEN 'Short'
            WHEN time_taken BETWEEN '03h 46m' AND '06h 59m' THEN 'Medium'
            ELSE 'Long'
            END AS time_category,
            time_taken,
            price
FROM economy_flights
GROUP BY 1,4,5
ORDER BY 2 DESC
)
SELECT 
    time_category,
    COUNT(*) AS total_flights,
    ROUND(AVG(price),2) AS avg_price
FROM flight_class
GROUP BY 1
ORDER BY 2 DESC
```

2. Most profitable airlines
```SQL
SELECT 
    airline,
    COUNT(*) AS total_flights,
    SUM(price) AS total_revenue
FROM economy_flights
GROUP BY 1
ORDER BY 2 DESC
```

3. Most profitable routes
```SQL
SELECT 
    from_location,
    to_location,
    COUNT(*) AS total_flights,
    SUM(price) AS total_revenue
FROM economy_flights
GROUP BY 1,2
ORDER BY 3 DESC
```

4. Analyzing stops in flights
```SQL
SELECT 
    from_location,
    to_location,
    stop,
    SUM(price) AS total_revenue
FROM economy_flights
GROUP BY 1,2,3
ORDER BY 4 DESC
```

5. Segmenting flights by departure time
```SQL
WITH flight_time
AS
(
SELECT 
    airline,
    dep_time,
    COUNT(*) AS total_flights,
    CASE 
        WHEN dep_time BETWEEN '00:00' AND '06:00' THEN 'Early Morning'
        WHEN dep_time BETWEEN '06:01' AND '12:00' THEN 'Morning'
        WHEN dep_time BETWEEN '12:01' AND '18:00' THEN 'Afternoon'
        ELSE 'Evening'
        END AS dep_time_category,
        price
FROM economy_flights
GROUP BY 1,2,5
ORDER BY 3 DESC
)
SELECT 
    dep_time_category,
    COUNT(*) AS total_flights,
    ROUND(AVG(price),2) AS avg_price
FROM flight_time
GROUP BY 1
ORDER BY 2 DESC
```
--------
### Findings

1. **Price Variation with Airlines**
   - The average price varies significantly across different airlines. Some airlines have higher average prices compared to others.

2. **Price Impact When Tickets are Bought 1 or 2 Days Before Departure**
   - Tickets bought 1 or 2 days before departure tend to have higher prices. This indicates a premium for last-minute bookings.

3. **Ticket Price Based on Departure and Arrival Time**
   - Ticket prices vary based on departure and arrival times. Flights with certain departure and arrival times may be more expensive.

4. **Price Changes with Source and Destination**
   - The price of tickets changes with different source and destination cities. Some routes are more expensive than others.

5. **Price Variation Between Economy and Business Class**
   - Business class tickets are generally more expensive than economy class tickets.

6. **Classifying Flights Based on Time Taken**
   - Flights are categorized into short, medium, and long based on the time taken. The average price varies across these categories.

7. **Segmenting Flights by Departure Time**
   - Flights are segmented into early morning, morning, afternoon, and evening based on departure time. The average price varies across these segments.

8. **Most Profitable Airlines**
   - Certain airlines have higher total revenue, indicating they are more profitable.

9. **Most Profitable Routes**
   - Some routes generate higher total revenue, making them more profitable.

10. **Analyzing Stops in Flights**
    - The number of stops in flights affects the total revenue. Flights with more stops may have different pricing dynamics.

### Recommendations

1. **Airline Pricing Strategies**
   - Airlines with higher average prices should consider competitive pricing strategies to attract more customers.
   - Airlines with lower prices can leverage their cost advantage to increase market share.

2. **Last-Minute Booking Premium**
   - Airlines can continue to charge a premium for last-minute bookings. However, offering occasional discounts for last-minute bookings could attract more spontaneous travelers.

3. **Optimal Flight Times**
   - Airlines should analyze the demand for flights at different times and adjust prices accordingly. Offering discounts for less popular times could help fill seats.

4. **Route-Based Pricing**
   - Airlines should consider dynamic pricing based on the popularity and demand of specific routes. Offering promotions on less popular routes could increase occupancy rates.

5. **Class-Based Pricing**
   - Airlines should maintain a clear distinction in pricing between economy and business classes. Offering additional perks for business class could justify higher prices and attract more premium customers.

6. **Flight Duration-Based Pricing**
   - Airlines should consider pricing strategies based on flight duration. Short, medium, and long flights can have different pricing models to optimize revenue.

7. **Departure Time-Based Pricing**
   - Airlines should adjust prices based on departure times. Offering discounts for less popular departure times could help increase bookings.

8. **Maximizing Profitable Routes**
   - Airlines should focus on maximizing revenue from the most profitable routes by offering premium services and targeted promotions.

9. **Optimizing Stops in Flights**
   - Airlines should analyze the impact of stops on pricing and revenue. Offering direct flights or optimizing stop durations could improve customer satisfaction and profitability.

*By implementing these recommendations, airlines can optimize their pricing strategies, increase revenue, and improve customer satisfaction.*

----------------
*Author : Awal Alier*

*Reading between the data!*

------
