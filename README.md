# SpaceX Launch Data Analysis
This project demonstrates how to pull data from the SpaceX API, https://api.spacexdata.com/v4, store it in an SQLite database and perform simple analysis using SQL and Pandas.

This workflow includes:
1. Fetching rocket, launch, and payload data from the SpaceX API
2. Storing the data in a relational SQLite database (spacex.db)
3. Performaing analyses in SQL and Pandas

# Data Sources
 * Rockets: https://api.spacexdata.com/v4/rockets
 * Payloads: https://api.spacexdata.com/v4/payloads
 * Launches: https://api.spacexdata.com/v4/launches
Documentation of the SpaceX API can be found @ https://github.com/r-spacex/SpaceX-API/tree/master/docs

# Database Schema (SQLite)
<img width="348" height="389" alt="image" src="https://github.com/user-attachments/assets/53fa5d99-8127-4ad3-ad3a-e1fbae81655d" />

# Run notebook [analysis.ipynb](https://github.com/gilberpl/data-engineeering/blob/main/analysis.ipynb) 

# Notes
* Missing payload masses (NULL) are ignored in averages
* SQLite foreign keys were not created for simplicity
* Data is pulled from the live SpaceZ API so results may change over time

# Requirements
* Python 3
* requests
* pandas
* sqlite3
* os
