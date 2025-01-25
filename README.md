# EV-Charging-Data-Analysis-SQL (DataCamp)

As electronic vehicles (EVs) become more popular, there is an increasing need for access to charging stations, also known as ports. To that end, many modern apartment buildings have begun retrofitting their parking garages to include shared charging stations. A charging station is shared if it is accessible by anyone in the building.

But with increasing demand comes competition for these ports — nothing is more frustrating than coming home to find no charging stations available! In this project, you will use a dataset to help apartment building managers better understand their tenants’ EV charging habits.

The data has been loaded into a PostgreSQL database with a table named `charging_sessions` with the following columns:

## charging_sessions

| Column | Definition | Data type |
|-|-|-|
|`garage_id`| Identifier for the garage/building|`VARCHAR`|
|`user_id` | Identifier for the individual user|`VARCHAR`|
|`user_type`|Indicating whether the station is `Shared` or `Private`| `VARCHAR` |
|`start_plugin`|The date and time the session started |`DATETIME`|
|`start_plugin_hour`|The hour (in military time) that the session started | `NUMERIC`|
|`end_plugout`|The date and time the session ended | `DATETIME` |
|`end_plugout_hour`|The hour (in military time) that the session ended | `NUMERIC`|
|`duration_hours`| The length of the session, in hours|`NUMERIC`|
|`el_kwh`| Amount of electricity used (in Kilowatt hours)|`NUMERIC`|
|`month_plugin`| The month that the session started |`VARCHAR`|

Project Instructions

Write three SQL queries to answer the following questions:

-Find the number of unique individuals that use each garage’s shared charging stations. 
-The output should contain two columns: garage_id and num_unique_users. Sort your results by the number of unique users from highest to lowest. Save the result as unique_users_per_garage.
-Find the top 10 most popular charging start times (by weekday and start hour) for sessions that use shared charging stations. Your result should contain three columns: weekdays_plugin, start_plugin_hour, and a column named num_charging_sessions containing the number of plugins on that weekday at that hour. Sort your results from the most to the least number of sessions. Save the result as most_popular_shared_start_times.
-Find the users whose average charging duration last longer than 10 hours when using shared charging stations. Your result should contain two columns: user_id and avg_charging_duration. Sort your result from highest to lowest average charging duration. Save the result as long_duration_shared_users.

Goal of the Project
────────────────────────────────────────────────────────────────
• The main purpose was to help apartment building managers understand how their shared EV charging stations are used.
• By analyzing session data stored in a PostgreSQL table (charging_sessions), managers can identify usage patterns—such as the number of unique users, the most popular charging hours, and which tenants charge for unusually long durations.
────────────────────────────────────────────────────────────────
2) Core Objectives
────────────────────────────────────────────────────────────────

Identify how many unique individuals use each garage’s shared charging stations.
Determine when (weekday + hour) most shared charging sessions start.
Find users whose average charging duration exceeds 10 hours when using shared stations.
────────────────────────────────────────────────────────────────
3) How We Solved It
────────────────────────────────────────────────────────────────
Below is a concise explanation of the technical steps we took for each part. (Short code snippets are included in code blocks for clarity.)

Used SQL for:
Data Aggregation and Filtering
Complex grouping operations (GROUP BY garage_id, weekdays_plugin, start_plugin_hour)
Filtering specific conditions (WHERE user_type = 'Shared')
Calculating aggregates (COUNT DISTINCT, AVG)

────────────────────────────────────────────────────────────────
A) Unique Users per Garage (Shared Stations)
────────────────────────────────────────────────────────────────
Goal: “How many distinct users use each garage’s shared charging stations?”

We filtered the rows in charging_sessions by user_type = 'Shared' to consider only shared-station usage.
We grouped by garage_id to view each garage’s usage separately.
We counted DISTINCT user_id to ensure that each user is only counted once per garage.
We sorted in descending order of unique users to see which garages are most heavily used.
Query:
```
SELECT
    garage_id,
    COUNT(DISTINCT user_id) AS num_unique_users
FROM charging_sessions
WHERE user_type = 'Shared'
GROUP BY garage_id
ORDER BY num_unique_users DESC;
```
B) Top 10 Most Popular Charging Start Times (Shared Stations)
────────────────────────────────────────────────────────────────
Goal: “On which weekday-hour combinations do most users plug in?”

We again filtered by user_type = 'Shared' to include only sessions on shared stations.
We grouped the data by weekdays_plugin (the day of the week) and start_plugin_hour.
We counted all sessions (COUNT(*)) within each weekday-hour group.
We ordered the results from most to least sessions, then retrieved the top 10.
Query:
```
SELECT
    weekdays_plugin,
    start_plugin_hour,
    COUNT(*) AS num_charging_sessions
FROM charging_sessions
WHERE user_type = 'Shared'
GROUP BY weekdays_plugin, start_plugin_hour
ORDER BY num_charging_sessions DESC
LIMIT 10;
```
C) Long-Duration Users (Over 10 Hours) at Shared Stations
────────────────────────────────────────────────────────────────
Goal: “Which users tend to occupy the chargers for more than 10 hours on average?”

We filtered by user_type = 'Shared' since we only care about shared-station usage.
We grouped results by user_id.
We calculated the average session duration with AVG(duration_hours).
We used the HAVING clause (rather than WHERE) to keep only those users whose average charging duration is greater than 10 hours.
We sorted these users by the longest average session first.
Query:
```
SELECT
    user_id,
    AVG(duration_hours) AS avg_charging_duration
FROM charging_sessions
WHERE user_type = 'Shared'
GROUP BY user_id
HAVING AVG(duration_hours) > 10
ORDER BY avg_charging_duration DESC;
```


