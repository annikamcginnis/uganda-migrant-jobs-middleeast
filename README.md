# Developing an Auto-Updating Database and Map of Job Openings for Ugandans in the Middle East and the Companies Recruiting

In this repo, I scrape, analyze and visualize data from Uganda's Ministry of Gender, Labour & Social Development External Employment Management System (EEMIS). I develop a running database of all jobs and companies that auto-updates daily, noting which jobs and companies are currently active on the recruiting site. I also develop an interactive map that shows the recruiting companies and currently open jobs.

## Step 1: Scraping the Companies Data

In the Jupyter notebook ("1. Scraping EEMIS Companies.ipynb"), I scrape the companies information on the EEMIS system. 

1. First, I scrape the list of companies on the EEMIS system (https://eemis.mglsd.go.ug/companies). In the Jupyter notebook, I use Playwright to press the ‘load more’ button until the list of companies is finished. 
2. I scrape the list of companies with BeautifulSoup. 
3. Through a loop, I generate a list of dictionaries with each element representing a single company, with the links to each individual company page included within each list element.
4. Then, I conduct a multipage scrape to simultaneously scrape all the information on the individual company pages. I use the page links, saved in the companies list dictionary, the requests library, and BeautifulSoup to go to each company’s page and extract information.
5. I add the information from each company page to the same list of dictionaries containing the information from the overall companies page. This is especially important since different addresses for the same companies are often included on the individual company pages as compared to the overall companies list. I save repeated variables along with their
source (company page, companies page, etc) to remember where the information came from.
6. I save the companies information to "companies.csv".

## Step 2: Scraping the Jobs Data

In the Jupyter notebook ("2. Scraping EEMIS Jobs.ipynb"), I scrape the jobs information on the EEMIS system.

1. First, I scrape the list of jobs (https://eemis.mglsd.go.ug/jobs). In the Jupyter notebook, I use Playwright to press the 'load more' button until the list of jobs is finished.
2. I scrape the list of jobs with BeautifulSoup. 
3. Through a loop, I generate a list of dictionaries with each element representing a single job, with the links to each individual job page included within each list element.
4. Then, I conduct a multipage scrape to simultaneously scrape all the information on the individual job pages. Since the jobs listings only
embedded the last part of the URLs, I added it to the first part to create the whole URLs. I then use these links, the requests library, and BeautifulSoup to go to each job page and extract information.
5. I add the information from each job page to the same list of dictionaries containing the information from the jobs list.
6. I save the jobs information to "jobs.csv".

## Step 3: Combining Companies and Jobs Data

In the Jupyter notebook ("3. Combining Companies and Jobs Data.ipynb"), I combine the companies and jobs data and add it to a running database of all companies/jobs information scraped daily since January 2025.

1. First, I import the pandas library and read in companies.csv. I create a boolean column with values 'True' to show that all these companies are currently active on the EEMIS system.
2. Then, I read in "companies_last.csv," which contains yesterday's scrape of companies on the site. I remove the active_company column from this dataframe. I merge it with today's scrape of companies, and give companies that no longer appear on the database a 'False' value in the active_company column.
3. I go through the same process with the jobs data ("jobs.csv" and "jobs_last.csv"), creating a single dataframe with the active_job boolean column designating currently open jobs.
4. I then merge the companies and jobs dataframes to create a single dataframe. I do some cleaning on column names and save the dataframe to "companies_and_jobs.csv".

## Step 4: Data Cleaning

In the Jupyter notebook ("4. Data Cleaning and Adding Columns.ipynb"), I clean the merged data and add columns for further analysis.

1. I read in the file ("companies_and_jobs.csv") to Pandas. 
2. I do a lot of cleaning in Pandas, including renaming and rearranging columns, splitting text to columns and using regular expressions to clean the values, changing formats of dates, etc.
3. Because salaries are listed in the currencies of the countries where jobs were located, I use each currency code and currency amount to search xe.com to find out how much each salary would be in USD, then save it to the same list of dictionaries. I do this by transforming the dataframe to a list of dictionaries and, in a loop, using the requests library and BeautifulSoup to search xe.com and extract the amount in USD. 
4. Using the Google Maps Geocode API, I looped through the list of dictionaries and found the latitude, longitude, city name and country name for all the company locations listed on the companies list (usually more detailed addresses than those listed on the individual company pages). 
5. I then used regular expressions to clean the addresses listed on the company pages as well, and Geocoded these addresses too. 
6. Bringing the list back to Pandas, I created a new column for latitude and longitude to be shown on the map, taking the values from the companies list Geocoded locations (if not NaN) and if NaN, taking the values from the individual company pages' Geocoded locations.
7. I create some new columns: for the length of jobs, through calculating the difference between the job start and end dates, after converting them to dates formats, and for company registration years through regexing the registration dates (only available for companies with open jobs).
8. I save the revised dataframe to "cleaned_data_eemis.csv".




