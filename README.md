# LCD-Daily-Max-Avg-Downloader-and-Converter
Automates hourly weather data retrieval from NOAA’s Local Climatological Database (LCD) via API. Processes raw data into daily metrics: max/avg temperature, heat index, wet-bulb temp, and relative humidity. Features built-in visualization for generating probability distribution plots of daily values. The program imports hourly readings of temperature and relative humidity, makes corrections for or ignores erroneous entries, and outputs daily maximum/average readings for Temperature, Heat index, Wet-bulb temperature, and Relative Humidity for every city given to it. It also creates probability distribution plots for those values for each city, and consolidates statistical information for each one of those cities into one large database, allowing for easy comparisons of extreme heat behavior for cities across the United States or even the world.

# Technical Information:

Language: Python 3.11+

Libraries: Pandas, SQLalchemy (Dataframes), NumPy (Matrix Math), MetPy (Atmospheric calcs), Scipy (Stats), Matplotlib (Visualization), Requests (API HTTP Integration)

Infrastructure: Logging (System monitoring)

# Pipeline Architecture 

Ingest: requests fetches raw CSV from NOAA.

Clean: Pandas and NumPy handle the ETL process.

Compute: MetPy and SciPy derive advanced metrics like Wet Bulb and heat Index.

Visualize: Matplotlib generates the final probability distributions.

# Project Architecture

