# Gans-Case
## WBS Data Science Bootcamp third project

## Overview
The aim of this project was to create a Data source from scratch on SQL and having it automatically updated thorugh a cloud function. The main goal was to get updated information regarding weather forecast and also flight information. There was also other data needed like latitude and longitude of each one of the cities as well as population.

## Objective
To create a database that could help Gans to take data-driven decissions regarding their possible demand in relation to weather conditions and passenger on flights every day in order to be able to plan their operations properly.

## Data Source
Web scraping was performed from Wikipedia URLs.
Rapid API was the tool used to get the necessary code for the weather and flights informations.

## Tools Used

- MySQL Workbench
- Python
- Google Cloud

## Folder Structure

- `/src`: Contains all Python scripts.
- `/data`: Exported data from MySQL Workbench.
- `/docs`: Link to medium article.
- `/images`: Images and diagrams used in documentation.

## Analysis Summary

To achieve the first part of this project, we gained insights into web design by inspecting objects on the pages to find patterns that could help us extract the required information. Specifically, we needed to scrape data such as population, latitude, and longitude from different cities. I put a considerable effort into generating stable code, testing it with various cities to pinpoint where corrections were needed. After multiple adjustments, I succeeded in creating a more stable code, adding lines to prevent crashes when information was not found, and taking advantage of the fact that normally Wikipedia City pages contain a similar structure. This way, I obtained a list of 50 European cities and 150 different countries, along with the total number of cars in each one. However, this initial foray into web scraping revealed its difficulty and limitations — it remains unstable and limited at the level we’ve developed it.


After we achieved this, we delved into the world of APIs, allowing us to obtain the needed information in a structured way with less effort than web scraping. Creating the code to get the information in “JSON” format was straightforward, and understanding the data distribution inside dictionaries and lists made extracting the data direct. The difficulties in this step arose from finding the correct links using Rapid API. In our case, we needed to extract data from flights at each airport and the weather forecast in each city. For the airports, we had to locate these using coordinates (latitude and longitude, in order to assure we were getting the information of the city we were looking for and not another one with the same name) obtained in the first step for each city. In a second call, using the “IATA” names for each airport, we obtained the desired information. For the case of weather only using coordinates (in decimals) was enough. Using this structure, functions were created for both flights and weather information. Combined with the web scraping step, and utilizing “for” loops and data frames, the first stage of the project was complete: Extracting Data.


In the second step of the project, we needed to transform this data into a suitable format for SQL tables. Here is where I conducted several tests to understand the correct format needed and considered the structure of the tables and connections required. This involved assigning primary and foreign keys, determining which tables needed merging, and deciding what information needed to be dropped before exporting the tables to SQL. After planning the entire schema, I created three “central” tables from which all others would be detached: “cities,” “counties,” and “airports,” each using a corresponding primary key and related to the others using foreign keys. Additionally, the following tables were created:

“Information”: storing population, latitudes, and longitudes for each city.
“Airport Schedules”: storing information about the flights arriving at each airport in the next 12 hours.
“Weather”: storing information about the weather forecast in the next 5 days.
“Cars”: storing the number of cars currently available in each country; this remained static for simplicity in the project.
To get the primary keys, I exported the three central tables to SQL and then retrieved this data in Python. Once I had the IDs, I merged these tables with the rest, dropped duplicated columns (leaving only the IDs instead of the complete names), changed the format in some cases (like longitude and latitude into decimal), and adjusted flight dates. Also, when merging, I used “inner,” losing some data in that step but ensuring every row had a value for all columns.


Finally, it was time for the last stage: exporting the final tables to SQL. This step was straightforward once I knew how to define all the variables I needed and their types. In my case, the transformation and loading of the data were closely related, as I needed the IDs from the central tables to create the rest. If I were to rewrite my script, I would likely make it more modular at that particular point. Both exporting the information and the transformation part were added to the corresponding “weather” and “flights” functions.

Here, I would like to share a personal thought about working with functions. In my case, it helped me structure the entire project more efficiently, but it presented a significant challenge to understand the proper way to limit these functions and assign parameters to them. For example, initially, I created a single function called “update()” that would scrape the list of cities directly from Wikipedia and perform all the other operations in one function. This would have been extremely inefficient in the long term, as all static tables don’t need to be updated every time I want the newest flights or weather information. I was already taking this list from a static list on Wikipedia, so repeating these steps would only cause the function to be inefficient. I also needed functions that, without any parameters, would retrieve static data from SQL (list of cities) and perform the necessary updates. Therefore, I rewrote this script four times until I arrived at the final versions of my functions. All of this back and forth could be spared with good planning of the SQL tables and their relationships before starting coding. Understanding the final objective of the function you are writing is the key to avoiding wasting time. In our case, knowing the end goal was to automate this pipeline, I needed this function to work in a certain determined way, which could vary depending on the premises of your project.

Now that the three stages were finished, it was time for me to upload these two functions to the cloud to get the pipeline updated automatically. The first step was to create the GCP SQL Instance and understand how to connect it with MySQL Workbench. Using the guidance of the WBS study platform made this quite straightforward. Secondly, I had to create the cloud function, and for this, it was important to run some tests to understand the correct format of the Python functions for cloud functions. Here is where I started to struggle quite a lot, as the connection between the cloud function and SQL instance is built in a completely different way than doing it locally. Forgetting to add the corresponding modules could lead to errors, and updating the functions takes several minutes. After understanding the correct structure, and adding the corresponding modules to requirement.txt, the function finally worked. Using Cloud-Run to understand what the actual error was helped me solve many 500 type errors as debugging directly from Google Cloud could be a nightmare. Assigning a scheduled trigger once per day and hiding the API-Key as well as the SQL-Instance password in Google Secrets rounded up the whole project.


I would like to emphasize the importance of the usage of large language models while coding to solve certain issues and save considerable amounts of time. Personally, I defined a limit, always understanding every single change and line of code I received back; otherwise, the code might run, but inserting this codes without fully understanding it could lead to further errors in the rest of the code. It has become an incredibly powerful tool, and as it is here to stay, it will be important to know how to use it and its limitations. It is important to mention that large language models will often not provide the exact answer you are looking for, but it will certainly provide an orientation on how to solve the issue.

In conclusion, this project presented many challenges to me. We were given a real case and had to apply all the knowledge acquired up to this point. Achieving the final goal of automating the pipeline was extremely satisfactory and motivating for me, but I acknowledge that there were aspects that could have been done better. I couldn’t have imagined being able to accomplish such a challenging project at this stage of the course. Additionally, this first approach to the cloud helped me understand the difficulties, especially for beginners, and how vast and comprehensive the topic can be. I now see the practical, real-world application of what we have been learning, and, of course, the challenges of dealing with raw data.

## Authors

- [Dante Lertora](https://github.com/Dantelertora)
  
## License

### Types of licenses

1. MIT License
   - Why Use It: It's simple and permissive. This license allows users to do almost anything they want with your project, like using, copying, modifying, merging, publishing, distributing, sublicensing, and/or selling copies.
   - Ideal For: Projects where you want to allow maximum freedom for others to use or build upon.
2. GNU General Public License (GPL)
   - Why Use It: It's a copyleft license, meaning any derivative work must be open-sourced under the GPL as well.
   - Ideal For: Projects where you want to ensure that all modifications and extensions remain open source.
3. Apache License 2.0
   - Why Use It: Similar to the MIT License but also provides an express grant of patent rights from contributors to users.
   - Ideal For: Projects where you want to provide some protection against patent litigation.
4. Creative Commons Licenses
   - Why Use It: If your project is more documentation or content-focused (like datasets or educational content).
   - Variations: Ranging from very permissive (CC BY) to more restrictive (CC BY-NC-ND).
   - Ideal For: Projects where you want to specify conditions for attribution, commercial use, or derivative works.
5. No License
   - Implication: Others cannot reproduce, distribute, or create derivative works from your work.
   - Ideal For: If you want to retain all rights but still share your work publicly for viewing.
