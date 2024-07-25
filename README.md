To solve the problem of finding the nearest stores for a given customer based on their geographic coordinates (latitude and longitude), you can follow these steps:

1. **Calculate Distance Using the Haversine Formula:**
   - The Haversine formula calculates the great-circle distance between two points on the Earth's surface. It's useful for finding the distance between customer locations and store locations.

2. **Store Distance Calculation in SQL:**
   - Use SQL functions to implement the Haversine formula and calculate the distance between each customer and each store.

3. **Optimization with Indexing:**
   - Index the latitude and longitude columns in your database to optimize the distance calculations and queries.

4. **Limit the Results:**
   - Use `ORDER BY` and `LIMIT` clauses to find the nearest stores for each customer.

Here is an SQL query that implements the Haversine formula and finds the nearest retail stores for a given customer:

```sql
-- Assuming two tables: customers (id, latitude, longitude) and stores (id, latitude, longitude)

SELECT 
    s.id AS store_id,
    s.latitude AS store_latitude,
    s.longitude AS store_longitude,
    c.id AS customer_id,
    c.latitude AS customer_latitude,
    c.longitude AS customer_longitude,
    (
        6371 * acos(
            cos(radians(c.latitude)) * cos(radians(s.latitude)) * cos(radians(s.longitude) - radians(c.longitude)) + 
            sin(radians(c.latitude)) * sin(radians(s.latitude))
        )
    ) AS distance
FROM
    customers c
JOIN
    stores s
ON
    1=1 -- Cross join to calculate distance for all pairs
WHERE
    c.id = ? -- Replace with the specific customer ID
ORDER BY
    distance
LIMIT 3; -- Change to 4 if you need the nearest four stores
```

### Explanation:
1. **Cross Join (Cartesian Product):**
   - `JOIN stores s ON 1=1` generates a Cartesian product, meaning each customer is paired with each store.

2. **Distance Calculation:**
   - The formula `6371 * acos(...)` computes the distance between the customer's location and each store using the Haversine formula. The constant `6371` is the Earth's radius in kilometers.

3. **Filtering by Customer:**
   - The `WHERE c.id = ?` clause filters the results to calculate distances for the specific customer ID provided.

4. **Ordering and Limiting Results:**
   - `ORDER BY distance` sorts the results by the calculated distance in ascending order, and `LIMIT 3` returns the three nearest stores.

This approach provides a reliable and efficient way to find the nearest stores for a given customer using SQL.
