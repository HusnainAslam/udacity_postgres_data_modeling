# Data Modeling with Postgres

## Project Overview
A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analytics team is particularly interested in understanding what songs users are listening to. Currently, they don't have an easy way to query their data, which resides in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.

They'd like a data engineer to create a Postgres database with tables designed to optimize queries on song play analysis, and bring you on the project. Your role is to create a database schema and ETL pipeline for this analysis. You'll be able to test your database and ETL pipeline by running queries given to you by the analytics team from Sparkify and compare your results with their expected results.

## Project Description
In this project, you'll apply what you've learned on data modeling with Postgres and build an ETL pipeline using Python. To complete the project, you will need to define fact and dimension tables for a star schema for a particular analytic focus, and write an ETL pipeline that transfers data from files in two local directories into these tables in Postgres using Python and SQL.

## Schema
### Fact Table
**songplays** - Records in log data associated with song plays i.e. records with page NextSong)
- songplay_id BIGSERIAL PRIMARY KEY ,
- start_time TIMESTAMP,
- user_id BIGINT ,
- level TEXT NOT NULL, _(User level)_
- song_id TEXT ,
- artist_id TEXT ,
- session_id INT NOT NULL,
- location TEXT NOT NULL,
- user_agent TEXT

_songplay_id_ is the primary key. And it is only column which identifies songplay uniquely.

### Dimension Tables
**users**  - users in the app
- user_id BIGSERIAL PRIMARY KEY ,
- first_name TEXT NOT NULL ,
- last_name TEXT NOT NULL ,
- gender TEXT NOT NULL ,
- level TEXT NOT NULL 
_user_id_ is primary key and due to bigserial data type, database add it automatically. So there is no need to pass user_id while inserting user.
        
**songs**  - songs in music database
- song_id TEXT PRIMARY KEY ,
- title TEXT NOT NULL ,
- artist_id TEXT ,
- year INT NOT NULL ,
- duration NUMERIC, _(milliseconds)_

_song_id_ is primary key. It's datatype is text because it contains Alpha numeric.

**artists**  - artists in music database
- artist_id TEXT PRIMARY KEY ,
- name TEXT NOT NULL ,
- location TEXT NOT NULL ,
- latitude NUMERIC ,
- longitude NUMERIC
_artist_id_ is primary key. It's datatype is text because it contains Alpha numeric.

**time**  - timestamps of records in  **songplays**  broken down into specific units
- start_time TIMESTAMP PRIMARY KEY ,
- hour INT ,
- day INT ,
- week INT ,
- month INT ,
- year INT ,
- weekday INT

_start_time_ is primary key because a unique time on a certain date should be stored only once.
_weekday_ is day number starting from Monday=0 to Sunday=6

## Description of Queries
### Create Table Queries
Create Table Queries are same as schema described above.

### Insert Queries
Insert queries are basically upsert queries. The queries will insert new records in the corresponding table but if there is conflict on constraint i.e primary key value checked by clause `ON CONFLICT`, it will simply ignore that insert due to clause `Do nothing`. However, for the users table insert, it will update the level of the user if there is a conflict. `DO UPDATE` part of the `UPSERT` statement to  update value and new value passed can get by `EXCLUDED` clause.

### Find Songs Query
This is a little complicated query as compared to other ones in the project. It selects `song_id` and `artist_id` by Joining the `songs` and `artists` tables ON `artist_id`. Both of these tables have `artist_id` as a common attribute which makes it easy to JOIN these tables. The problem this query is solving is that there is no information about `artist_id` and `song_id` in the log file so we had to JOIN these two tables to get the required information (to insert in songplays table) based on `title`, `artist_name` and `duration`.


## About Project Files
- `sql_queries.py` contains all sql queries, and is imported into the last three files above.<br />
- `create_tables.py` drops and creates tables. Run this file to reset tables before running ETL scripts.<br />
- `test.ipynb` displays the first few rows of each table.<br />
- `data` contains data about songs and logs of song plays.<br />
- `etl.py` reads and processes files from `song_data` and `log_data` and loads them into tables.<br />
- `etl.ipynb` reads and processes a single file from `song_data` and `log_data` and loads the data into tables.
- `README.md` contains Project Overview, DataBase Schema, SQL Queries Overview.
