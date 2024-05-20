
Event Tracker Script Documentation

Overview
This script is designed to track activity on GitHub by leveraging the GitHub Events API. The script monitors up to five configurable repositories and generates statistics based on a rolling window of either the last 7 days or the most recent 500 events, whichever occurs first. These statistics are made accessible to end-users via a REST API via  http://localhost:5001/statistics or http://localhost:5001/statistics?limit=3 (1 to 5)

There are following files in a folder:
sql_part.py
event_tracker.py
repositories.json
.env - it is necessary to create

sql_part.py 
Creating a database connection - get_db_connection()
Creating tables - _create_tables()
Create indexes if doesn’t exist - _create_indexes()
Cleaning old events - clean_old_events()

event_tracker.py - contains the rest of the application logic:
logging - Set up logging 
requests_cache - Configure cache
app = Flask(__name__) - Set up Flask
_fetch_events() - Making GitHub API request
_process_and_store_events() - Process and store the events
 _store_in_database() - Save events into database
_calculate_and_store_statistics() - Calculate and store the statistics
get_statistics() - Get the statistics
_load_repositories_config() - Load a config with repositories
_process_events_periodically() - Retrieve the configuration
_start_scheduled_tasks() - Manage scheduled tasks
main() - launching the app


Database schema:
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int          | NO   | PRI | NULL    | auto_increment |
| repo_name  | varchar(255) | YES  |     | NULL    |                |
| event_time | datetime     | YES  | MUL | NULL    |                |
| event_type | varchar(255) | YES  |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+

+-------------------------+--------------+------+-----+---------+----------------+
| Field                   | Type         | Null | Key | Default | Extra          |
+-------------------------+--------------+------+-----+---------+----------------+
| id                      | int          | NO   | PRI | NULL    | auto_increment |
| repo_name               | varchar(255) | YES  | MUL | NULL    |                |
| event_type              | varchar(255) | YES  |     | NULL    |                |
| avg_time_between_events | float        | YES  |     | NULL    |                |
+-------------------------+--------------+------+-----+---------+----------------+

Indexes
* idx_event_time on event_time column in events table.
* idx_statistics_repo_event on repo_name and event_type columns in statistics table.

Flask Endpoints
* GET /statistics: Returns statistics for the repositories being monitored. Supports a limit query parameter to limit the number of results.

Setup Environment Variables
Ensure the .env file is created for usage in sql_part.py file and the following environment variables are:
* DB_HOST: Database host
* DB_USER: Database username
* DB_PASSWORD: Database password
* DB_NAME: Database name

Set variables as:

DB_HOST=localhost
DB_USER=username
DB_PASSWORD=password
DB_NAME=github_events_db

Dependencies
* aiohttp
* asyncio
* requests
* requests_cache
* certifi
* ssl
* mysql.connector
* flask
* apscheduler
* dotenv
* pytest

Install the required Python modules using pip3:
pip3 install aiohttp requests requests_cache certifi mysql-connector-python flask apscheduler python-dotenv pytest

Setup Configuration File
A repositories.json file in the same directory have the following structure:

    "repositories": [
        "user1/repo1",
        "user2/repo2",
        "user3/repo3",
        "user4/repo4",
        "user5/repo5"
    ]
Fill the file with the number of users e.g. as:

    "repositories": [
        "Rydzaki/Final_project_teatAPI",
        "jtorralbaflores/Javier-Torralba-portfolio"
]

Install mysql on you mac in a new terminal window:
brew install mysql
Check your version optionally:
mysql --version

Type to login to MySQL:
mysql -u username -p
Input your password

After that type:
CREATE DATABASE github_events_db;
USE github_events_db; 

Now we are done with all settings for running the script sql_part.py.
Run it in a new terminal window inside the package where all files are placed:
python sql_part.py

Type in the side terminal with mysql to check new state:
SHOW TABLES;
DESCRIBE events;
DESCRIBE statistics;


Check previous steps:
1. Ensure the MySQL server is running and accessible.
2. Set up the environment variables in the .env file.
3. Ensure the repositories.json file is present and correctly formatted.

Then run the app: python event_tracker.py


TODO
1. Tests and possible refactoring
2. Opportunity to add an authentication (GitHub restriction of 60 no auth to 5000 auth approach)
3. More modular way to arrange the code
4. Better automatization
5. Using Docker and Kubernetes to deploy and manage the app
6. Thinking of scalability
# event_tracker
This script to track activity on GitHub
