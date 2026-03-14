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

Data Acquisition: Downloads Hourly CSV data from NOAAs LCD database (https://www.ncdc.noaa.gov/cdo-web/datatools/lcd) using their RESTful API. The user provides an 11-digit station identifier (see Operation section for instructions on how to find the station identifier), the city of the station, and a 3-letter station ID. Hourly data is stored within csvs covering 10-year periods, and is saved in the same folder as the scripts within a directory called NOAA_LCD_CSVs.

Data Input: Reads raw csv's of hourly station data. Checks entries for missing entries or nonnumerical characters, and then finds the average or maximum of daily values. Calculates heat index and wetbulb temperature using drybulb temperature and humidity readings. Stores the calculated values of each day in a csv labelled daily max within the respective LCD folder.

Data Analysis: Plots values (daily max/avg temperature, heat index, etc.) of each city in a probability distribution plot within the respective LCD folder. Can be specified for a specific month range or year range. Saves statistical descriptors (avg, skewness, etc.) of all cities within a csv and sql database for easy comparison. The statistical descriptors are as follows:

    * avg -  average value of the PDF plot
    * std - standard deviation of the PDF plot
    * skew - skewness of the PDF plot
    * n - number of datapoints
    * min - minimum value
    * True max- absolute max value of the PDF plot (is sensitive to outliers or erroneous data)
    * median - median of the PDF
    * mode - mode of the PDF
    * AAOMM - deprectated value from a previous project, should be ignored
    * AAO90 - Annual average over 90 degrees (is 80 degrees for wet-bulb temperature) the average number of days per year where the respective weather value reaches over 90 degrees 
    * 95Q - Value of the 95th quartile (chosen because it is not sensitive to outliers)
    * ACDO90 - Average consecutive days over 90 degrees (is 80 degrees for wet-bulb temperature) the average length of "high heat periods exhibited in the pdf" defined by consecutive days above 90 degrees
    * ACDOMM - deprecated value from  previous project, should be ignored
    * Urange - difference between the average and true maximum of a plot (is sensitive to outliers or erroneous data)
    * Delta - difference between the average of the pdf and the average of all values above the 99th quartile of the PDF. Has a demonstrable relationship with the longitude and distance from coast of the station. For more information (https://github.com/AustinBozgoz/Novel-Metrics-for-Analyzing-Extreme-Heat-Patterns-Across-US-Cities-)
    * RHavgoverall - Average relative humidity a station experiences
    * Pcau - (heat index only) proportion of all days within the PDF that are WITHIN the "caution" category of heat index values (defined by being between 80 and 90 degrees F in heat index)
    * POcau - (heat index only) proportion of all days within the PDF that are ABOVE the "caution" category of heat index values (defined by being above 80 degrees F in heat index)
