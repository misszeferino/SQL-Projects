# Battery Alert System for Electric Vehicles

A client wants to create an alert system for users whose vehicles have insufficient battery to return to the starting point of their trip. This alert is particularly important for users traveling to remote areas with few or no charging stations, such as mountainous regions.

**Original Table (`vehicle_position`)**

This table captures vehicle data every 2 minutes, including the timestamp, order ID, vehicle autonomy, and its geographical coordinates (latitude and longitude).

| timestamp           | order_id | autonomy (km) | latitude  | longitude |
|---------------------|----------|---------------|-----------|-----------|
| 2024-08-05 08:00:00 | 1        | 100           | 40.7128   | -74.0060  |
| 2024-08-05 08:02:00 | 1        | 98            | 40.7138   | -74.0070  |
| 2024-08-05 08:04:00 | 1        | 95            | 40.7148   | -74.0080  |
| 2024-08-05 08:06:00 | 1        | 93            | 40.7158   | -74.0090  |
| 2024-08-05 08:00:00 | 2        | 150           | 34.0522   | -118.2437 |
| 2024-08-05 08:02:00 | 2        | 148           | 34.0525   | -118.2440 |
| 2024-08-05 08:04:00 | 2        | 145           | 34.0528   | -118.2443 |
| 2024-08-05 08:06:00 | 2        | 143           | 34.0531   | -118.2446 |

To determine whether a vehicle has enough battery to return to its starting point, we need to calculate the Haversine distance (straight-line distance) between the vehicle's current location and its start point. If this distance exceeds 80% of the vehicle's autonomy, an alert will be triggered for the client.

**Final Table (After Query)**

| timestamp           | order_id | autonomy (meters) | latitude  | longitude | distance_from_start (meters) |
|---------------------|----------|---------------|-----------|-----------|------------------------------|
| 2024-08-05 08:04:00 | 1        | 95000            | 40.7148   | -74.0080  | 75000 (example)               |
| 2024-08-05 08:06:00 | 1        | 93000            | 40.7158   | -74.0090  | 78000 (example)               |
| 2024-08-05 08:04:00 | 2        | 145000           | 34.0528   | -118.2443 | 115000 (example)              |
| 2024-08-05 08:06:00 | 2        | 143000           | 34.0531   | -118.2446 | 117000 (example)              |

This table filters records to show only those where the vehicle's distance from the start point exceeds 80% of its autonomy, indicating insufficient battery to return to the start point.

**Note**: A more accurate solution under development will use the driving distance instead of the Haversine distance to determine the vehicle's ability to return to its starting point.

```sql
WITH cte AS (
  SELECT
    *,
    ST_DISTANCE(ST_GEOGPOINT(longitude, latitude), 
                FIRST_VALUE(ST_GEOGPOINT(longitude, latitude)) 
                OVER (PARTITION BY order_id ORDER BY timestamp ASC)) AS distance_from_start
  FROM `data-analyst-326910.vehicle_km.vehicle_position`
  WHERE order_id IS NOT NULL
)

SELECT
  timestamp,
  order_id,
  autonomy * 1000,  -- autonomy is in km, so convert to meters
  latitude,
  longitude,
  distance_from_start
FROM cte
WHERE distance_from_start > (autonomy * 0.80) 
ORDER BY order_id, timestamp ASC;
```

### SQL Query Explanation

1. CTE (Common Table Expression)

- **Create a geographic point** for each record using latitude and longitude: `ST_GEOGPOINT(longitude, latitude)`.
- The `FIRST_VALUE` function retrieves the first geographical point for each `order_id`, which represents the start location of each trip.
- `ST_DISTANCE` computes the straight-line distance between the current location and the start location in meters.

2. Main Query

- **Filter records** where the distance from the start location is greater than 80% of the vehicle's autonomy. The condition `(cte.distance_from_start > (autonomy * 0.80 * 1000))` ensures that an alert is triggered if the vehicle's distance from the start exceeds 80% of its autonomy, leaving 20% battery for finding a charging station.
- **Order the results** by `order_id` and `timestamp` to maintain chronological order.
