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

# Getting Started

## Clone the repo
git clone https://github.com/AustinBozgoz/Novel-Metrics-for-Analyzing-Extreme-Heat-Patterns-Across-US-Cities-

## Install dependencies
pip install -r requirements.txt

## Run the pipeline
python src/main.py

## Operation
This program operates in a 3 step sequence as directed by user input:

1) Downloads the hourly LCD information for a given station
   * To find the correct station, navigate to https://www.ncei.noaa.gov/cdo-web/datatools/lcd and find the station you want to use. Record the 5 digit WBAN for the station (for example, Atlanta Hartsfield Airport is 13874). Then navigate to https://infosys.ars.usda.gov/svn/code/windgen/doc/USAF-WBAN.txt and, using ctrl-F, input the WBAN to locate the full station ID. The 11 digit station identifier is the combination of the first two columns listed beside your station (for example, Atlanta Hartsfield airport would be 72219013874)
   * The LCD data will be saved in a directory labelled NOAA_LCD_CSVs within the same folder the python scripts are located in
   * Input the name of the city, state exactly as its listed on the NOAA website
   * Input a 3 letter designator for the particular station (for example, Atlanta Hartsfield Airport might be ATL)
   * The CSVs are downloaded in batches of ten years to reduce the chances of the server request timing out
2) Converts the hourly weather data into daily maximum/average values
  * Can convert one city at a time, in which case input the city, state exactly as the directory is written inside the NOAA_LCD_CSVs folder and give the same 3 letter designator as the station
  * Can also convert all cities located within the NOAA_LCD_CSVs folder, in which case an extra file titled checkpoint.csv is saved to prevent redoing completed cities if the process is interrupted
3) Analyzes daily maximums/averages to create probability distribution plots for each city and record statistical features of the plots in a seperate file/database
   * Probabilty distribution plots are saved within their respective cities, in a folder called PDF plots
   * The statistical features (average, skewness, etc.) for the plots are saved within a seperate folder within NOAA_LCD_CSVs called city logs. The distributions of all cities are saved within csvs and sql databases for easy comparison to one another
   * Specific ranges for analysis can be defined, like only examining information between june and august, or only for a specific year range

# Project Architecture

User Interface: Operates in a 3 step sequence based on user input: (1) 

Data Input: Reads raw csv's from NOAA's LCD database (https://www.ncdc.noaa.gov/cdo-web/datatools/lcd). The database only permits data downloading one decade at a time, so you must download the information each decade starting at January 1st at the beginning of the decade (or whenever the sation first started collecting information) to the last day in the decade (as in December 31st, the 9th year of the decade). E.g. for the 1980s of a particular station, the csv should be dated from January 1st, 1980 to December 31st, 1989. If the station started in 1985, it is also acceptable to use a csv of [Any month] [Any day], 1985 to December 31st, 1989. Manual Adjustments to the code will need to be made in order to include csv's of more recent years (i.e. past 2019).
