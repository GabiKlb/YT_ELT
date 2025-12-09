YT_ELT is a Docker‑based ELT pipeline orchestrated with Apache Airflow. It extracts data from the YouTube Data API, loads it into PostgreSQL, transforms it into curated tables, and validates data quality using Soda Core.

This project is designed as a clean, modular, and production‑ready data engineering workflow.

Architecture

YouTube API
↓
produce_json (DAG #1)
↓ raw data (JSON)
update_db (DAG #2)
↓ staging → core
↓
data_quality (DAG #3 via Soda)


Components

Airflow (webserver, scheduler, worker) with Celery + Redis

PostgreSQL for metadata & ELT schemas

Docker Compose for full orchestration

Soda Core for programmatic data quality checks


Main DAGs
1. produce_json

Retrieves playlist ID

Extracts video metadata from YouTube API

Stores raw JSON in /data

Triggers the next DAG

2. update_db

Loads raw data into staging (incremental: insert/update/delete)

Transforms and loads into core schema

Triggers data quality checks

3. data_quality

Executes Soda Core scans:

soda scan -d pg_datasource -c configuration.yml -v SCHEMA=<schema> checks.yml

Validates nulls, duplicates, row counts, and schema expectations.


Technology Stack

Apache Airflow 2.9+

CeleryExecutor + Redis

PostgreSQL

Docker / Docker Compose

Soda Core

YouTube Data API