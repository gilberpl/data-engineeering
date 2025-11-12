# SpaceX Launch Data Analysis
This project demonstrates how to pull data from the SpaceX API, https://api.spacexdata.com/v4, store it in an SQLite database and perform simple analysis using SQL and Pandas.

This workflow includes:
1. Fetching rocket, launch, and payload data from the SpaceX API
2. Storing the data in a relational SQLite database (spacex.db)
3. Performaing analyses in SQL and Pandas

# Why SpaceX?
My father and I have watched SpaceX launches online for years. We even had the opportunity to watch one of the rockets re-enter orbit and land at Port Canaveral Florida which was incredible to watch. I figured this was a good starter dataset for myself to pull and run a few simple SQLite commands from.

# Data Sources
 * Rockets: https://api.spacexdata.com/v4/rockets
 * Payloads: https://api.spacexdata.com/v4/payloads
 * Launches: https://api.spacexdata.com/v4/launches

Documentation of the SpaceX API can be found @ [SpaceX-API GitHub](https://github.com/r-spacex/SpaceX-API/tree/master/docs)

# Run notebook 
* [analysis.ipynb](https://github.com/gilberpl/data-engineeering/blob/main/analysis.ipynb) 

# Database Schema (SQLite)
<img width="348" height="389" alt="image" src="https://github.com/user-attachments/assets/53fa5d99-8127-4ad3-ad3a-e1fbae81655d" />

# Queries
* Average payload weight per launch type
```
SELECT p.type, avg(p.mass_kg) as avg_weight
FROM payloads p
JOIN launches l ON p.launch_id = l.id
JOIN rockets r ON l.rocket_id = r.id
GROUP BY p.type
HAVING avg_weight IS NOT null
ORDER BY avg_weight DESC
```
<img width="186" height="195" alt="image" src="https://github.com/user-attachments/assets/cd06700f-2363-4ce1-8ed0-af4a65b86b9a" />


* Which rocket has cost spacex the most to launch over its lifetime
```
WITH launch_count AS (
  SELECT count(*) as num_launch, rocket_id
  FROM launches
  GROUP BY rocket_id
)

SELECT r.name, (lc.num_launch*r.cost_per_launch) as total_launch_cost
FROM launch_count lc
LEFT JOIN rockets r
ON lc.rocket_id = r.id
ORDER BY total_launch_cost DESC
```
<img width="228" height="122" alt="image" src="https://github.com/user-attachments/assets/4b97522d-298e-4404-b457-e8e66d135592" />



* Number of launches per year
```
# Load tables
rockets_df = pd.read_sql_query("SELECT * FROM rockets", conn)
launches_df = pd.read_sql_query("SELECT * FROM launches", conn)
payloads_df = pd.read_sql_query("SELECT * FROM payloads", conn)

# Merge payloads, launches, rockets into one df
df = payloads_df.merge(launches_df, left_on='launch_id', right_on='id', suffixes=('_payload', '_launch'))
df = df.merge(rockets_df, left_on='rocket_id', right_on='id', suffixes=('', '_rocket'))

# Show number of launches per year using Pandas
df['date_utc'] = pd.to_datetime(df['date_utc'])
launches_per_year = df.groupby(df['date_utc'].dt.year)['launch_id'].nunique().reset_index()
launches_per_year = launches_per_year.rename(columns={'launch_id': 'num_launches'})
```
<img width="185" height="436" alt="image" src="https://github.com/user-attachments/assets/7f9b71e9-7567-4545-956b-5ea1e08e0a9f" />


# Notes
* Missing payload masses (NULL) are ignored in averages
* SQLite foreign keys were not created for simplicity
* Data is pulled from the live SpaceX API so results may change over time
* This data only goes back to 2022

# Requirements
* Python 3
* requests
* pandas
* sqlite3
* os
