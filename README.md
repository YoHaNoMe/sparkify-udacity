# Project Overview
Sparkify is a startup wants to analyze the songs they have been collecting and keep track of the users activity on their music Application. As my role (Data Engineer) I will help them building their Databases.

# Project Description

## Project Structure

```
├── data  (folder containing all the json data that we will extract)
│   ├── log_data (user logs folder)
│   │   └── 2018
│   │       └── 11
│   │           ├── 2018-11-01-events.json
│   │           └── .....
│   └── song_data (folder containing songs and artists data)
│       └── A
│           ├── A
│           │   ├── A
│           │   │   ├── TRAAAAW128F429D538.json
│           │   │   ├── ....
│           │   ├── B
│           │   │   ├── TRAABCL128F4286650.json
│           │   │   ├── ....
│           │   └── C
│           │       ├── TRAACCG128F92E8A55.json
│           │       ├── ....
│           └── B
│               ├── A
│               │   ├── TRABACN128F425B784.json
│               │   ├── ....
│               ├── B
│               │   ├── TRABBAM128F429D223.json
│               │   ├── ....
│               └── C
│                   ├── TRABCAJ12903CDFCC2.json
│                   ├── ....
│
├── etl.ipynb (Jupyter Notebook same as 'etl.py')
│
├── etl.py
│
├── create_tables.py
│
├── README.md
│
├── requirements.txt
│
├── sql_queries.py (all the queries that we will run)
│
├── test.ipynb (Jupyter Notebook to test the database and the tables are created 
│			    and to test that the data is inserted successfully)
│
└── tree.txt (a file containing this tree diagram with all the data)

15 directories, 110 files
```

## Tables

The design of the database schema will be based on [***Star Schema***](https://en.wikipedia.org/wiki/Star_schema), So we will have, in this project, **one** fact table and **four** dimension tables.

### Fact Tables

##### Songplays

the table `songplays` will have these columns:
- songplay_id
- start_time
- user_id
- level
- song_id
- artist_id
- session_id
- location
- user_agent

### Dimension Tables

##### 1- Users

the table `users` will have these columns:
- user_id
- first_name
- last_name
- gender
- level

##### 2- Songs

the table `songs` will have these columns:
- song_id
- title
- artist_id
- year
- duration

##### 3- Artists

the table `artists` will have these columns:
- artist_id
- name
- location
- latitude
- longitude

##### 4- Time

the table `time` will have these columns:
- id
- start_time
- hour
- day
- week
- month
- year
- weekday

## Getting Started

### Installing Dependencies

#### PostgreSQL

Make sure you have `PostgreSQL` Installed. [Download PostgreSQL](https://www.postgresql.org/download/)

#### Python
Follow instructions to install the latest version of python for your platform in the [Python Docs](https://docs.python.org/3/using/unix.html#getting-and-installing-the-latest-version-of-python)

#### Virtual Environment
It's recommend to work within a virtual environment whenever using Python for projects. This keeps your dependencies for each project separate and organized. Instructions for setting up a virtual environment for your platform can be found in the [Python Docs](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/)

#### PIP Dependencies
Once you have your virtual environment setup and running. Make sure you are in the right folder then install dependencies:
```
pip install -r requirements.txt
```
This will install all of the required packages within the `requirements.txt` file.

### Running Application

1.  First you have to create database `testdb` 

2. Run `create_tables` file to  the tables mentioned [above](https://github.com/YoHaNoMe/sparkify-udacity#tables). You have to pass your **username** and **password** that you have created in installing [PostgreSQL Step](https://github.com/YoHaNoMe/sparkify-udacity#postgresql).

```
python create_tables.py username password
```

3. Run `etl.py` this file will extract data from json files and insert it into the tables. Again you have to pass your **username** and **password** that you have created in installing [PostgreSQL Step](https://github.com/YoHaNoMe/sparkify-udacity#postgresql).

```
python etl.py username password
```

***Important Notes***: 

- If you want to reset the tables (clear all the data), re-run the second step.
- To run any of `etl.ipynb` or `test.ipynb` you have to run **JupyterLab**. Please visit this [link](https://jupyter.org/install#run-jupyterlab)
- If you run any of `etl.ipynb` or `test.ipynb` you have to **Reset The Kernel**. Please visit this [link](https://support.labs.cognitiveclass.ai/knowledgebase/articles/857388-how-to-restart-the-jupyter-kernel#:~:text=You%20can%20restart%20your%20Jupyter,occurs%20try%20refreshing%20your%20browser.) if you don't know how to reset it.

### Finally 

When you finish remove `sparkifydb` and `testdb`  by:

```
DROP DATABASE IF EXISTS sparkifydb
DROP DATABASE IF EXISTS testdb
```

## Tables In Details

### Fact Tables

##### Songplays

**Note:** We didn't use foreign_key with any of `song_id` or `artist_id` because the data is not *UNIQUE*. So we can't make any of `songs.song_id` nor `artists.artist_id` *unique*.

Create Query

```
CREATE TABLE songplays (
	songplay_id serial PRIMARY KEY,
	start_time float NOT NULL,
	user_id int,
	level varchar,
	song_id varchar,
	artist_id varchar,
	session_id int,
	location varchar,
	user_agent varchar
)
```

Insert Query

```
INSERT INTO songplays (
start_time,
user_id,
level,
song_id,
artist_id,
session_id,
location,
user_agent) VALUES (%s, %s, %s, %s, %s, %s, %s, %s)
```

### Dimension Tables

##### 1- Users

Create Query
```
CREATE TABLE users (
user_id int PRIMARY KEY,
first_name varchar,
last_name varchar,
gender varchar,
level varchar
)
```

**Note:** We add `INSERT ... ON CONFLICT (user_id) DO NOTHING;` because we want a *UNIQUE* users.

Insert Query

```
INSERT INTO users (
user_id,
first_name,
last_name,
gender,
level) VALUES (%s, %s, %s, %s, %s)
ON CONFLICT (user_id)
DO NOTHING
```

##### 2- Songs

**Note:** We didn't use primary_key with  `song_id` because the data is not *UNIQUE*. 
We could use `INSERT ... ON CONFLICT target action;` but this will eliminate fields that we will use to join it with ***artists*** Table to build ***songplays*** table. As the requirements specify that there will be **one row** in ***songplays*** table that will have a value for `artist_id` and `song_id` and if we make `song_id` a primary_key that row will be eliminated **as we don't know which row it is**.

Create Query

```
CREATE TABLE songs (
song_id varchar NOT NULL,
title varchar,
artist_id varchar,
year int,
duration float
)
```

Insert Query

```
INSERT INTO songs (
song_id,
title,
artist_id,
year,
duration) VALUES (%s, %s, %s, %s, %s)
```

##### 3- Artists

**Note:** We didn't use primary_key with  `artist_id` because the data is not *UNIQUE*. 
We could use `INSERT ... ON CONFLICT target action;` but this will eliminate fields that we will use to join it with ***songs*** Table to build ***songplays*** table. As the requirements specify that there will be **one row** in ***songplays*** table that will have a value for `artist_id` and `song_id` and if we make `artist_id` a primary_key that row will be eliminated **as we don't know which row it is**.

Create Query

```
CREATE TABLE artists (
artist_id varchar NOT NULL,
name varchar,
location varchar,
latitude float,
longitude float
)
```

Insert Query

```
INSERT INTO artists (
artist_id,
name,
location,
latitude,
longitude) VALUES (%s, %s, %s, %s, %s)
```

##### 4- Time

Create Query

```
CREATE TABLE time (
id serial PRIMARY KEY,
start_time timestamp NOT NULL,
hour int NOT NULL,
day int NOT NULL,
week int NOT NULL,
month int NOT NULL,
year int NOT NULL,
weekday int NOT NULL)
```

Insert Query

```
INSERT INTO time (
start_time,
hour,
day,
week,
month,
year,
weekday) VALUES (%s, %s, %s, %s, %s, %s, %s)
```

## Error Convention

- This error arise if you forget to pass *username* and *password* in `create_tables.py` and `etl.py` scripts. Please refer to [This Section](https://github.com/YoHaNoMe/sparkify-udacity#running-application).

>You have to pass username and password. please refer to the documentation
