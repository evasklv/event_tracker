# Event Tracker Script Documentation

## Overview
This script is designed to track activity on GitHub by leveraging the GitHub Events API. The script monitors up to five configurable repositories and generates statistics based on a rolling window of either the last 7 days or the most recent 500 events, whichever occurs first. These statistics are made accessible to end-users via a REST API.

### REST API Endpoints
- `http://localhost:5001/statistics`
- `http://localhost:5001/statistics?limit=3` (limit can be set from 1 to 5)

## Files in the Folder
- `sql_part.py`
- `event_tracker.py`
- `repositories.json`
- `.env` (needs to be created)

## sql_part.py
- **Functions:**
  - `get_db_connection()`: Creating a database connection
  - `_create_tables()`: Creating tables
  - `_create_indexes()`: Create indexes if they don’t exist
  - `clean_old_events()`: Cleaning old events

## event_tracker.py
Contains the rest of the application logic:
- **Modules:**
  - `logging`: Set up logging
  - `requests_cache`: Configure cache
  - `app = Flask(__name__)`: Set up Flask
- **Functions:**
  - `_fetch_events()`: Making GitHub API request
  - `_process_and_store_events()`: Process and store the events
  - `_store_in_database()`: Save events into database
  - `_calculate_and_store_statistics()`: Calculate and store the statistics
  - `get_statistics()`: Get the statistics
  - `_load_repositories_config()`: Load a config with repositories
  - `_process_events_periodically()`: Retrieve the configuration
  - `_start_scheduled_tasks()`: Manage scheduled tasks
  - `main()`: Launching the app

## Database Schema
### `events` Table
| Field      | Type         | Null | Key | Default | Extra          |
|------------|--------------|------|-----|---------|----------------|
| id         | int          | NO   | PRI | NULL    | auto_increment |
| repo_name  | varchar(255) | YES  |     | NULL    |                |
| event_time | datetime     | YES  | MUL | NULL    |                |
| event_type | varchar(255) | YES  |     | NULL    |                |

### `statistics` Table
| Field                   | Type         | Null | Key | Default | Extra          |
|-------------------------|--------------|------|-----|---------|----------------|
| id                      | int          | NO   | PRI | NULL    | auto_increment |
| repo_name               | varchar(255) | YES  | MUL | NULL    |                |
| event_type              | varchar(255) | YES  |     | NULL    |                |
| avg_time_between_events | float        | YES  |     | NULL    |                |

## Indexes
- `idx_event_time` on `event_time` column in `events` table.
- `idx_statistics_repo_event` on `repo_name` and `event_type` columns in `statistics` table.

## Flask Endpoints
- **GET `/statistics`**: Returns statistics for the repositories being monitored. Supports a `limit` query parameter to limit the number of results.

## Setup Environment Variables
Ensure the `.env` file is created for usage in `sql_part.py` and the following environment variables are set:

```plaintext
DB_HOST=localhost
DB_USER=username
DB_PASSWORD=password
DB_NAME=github_events_db

## Dependencies
- `aiohttp`
- `asyncio`
- `requests`
- `requests_cache`
- `certifi`
- `ssl`
- `mysql.connector`
- `flask`
- `apscheduler`
- `dotenv`
- `pytest`

Install the required Python modules using pip3:

```sh
pip3 install aiohttp requests requests_cache certifi mysql-connector-python flask apscheduler python-dotenv pytest

A repositories.json file in the same directory should have the following structure:

{
    "repositories": [
        "user1/repo1",
        "user2/repo2",
        "user3/repo3",
        "user4/repo4",
        "user5/repo5"
    ]
}

Example:

{
    "repositories": [
        "Rydzaki/Final_project_teatAPI",
        "jtorralbaflores/Javier-Torralba-portfolio"
    ]
}

Setup MySQL on Mac
Install MySQL:

brew install mysql
Check your version (optional):

mysql --version
Login to MySQL:

mysql -u username -p
Create the database:

CREATE DATABASE github_events_db;
USE github_events_db;
Running the Scripts
Run sql_part.py to set up the database:

python sql_part.py
Verify the tables:

SHOW TABLES;
DESCRIBE events;
DESCRIBE statistics;
Running the App
Ensure MySQL is running and accessible. Then run the app:

python event_tracker.py


##TODO
 - Tests and possible refactoring
 = Opportunity to add authentication (GitHub restriction of 60 no auth to 5000 auth approach)
 = More modular code arrangement
 = Better automation
 = Using Docker and Kubernetes to deploy and manage the app
 = Thinking of scalability


