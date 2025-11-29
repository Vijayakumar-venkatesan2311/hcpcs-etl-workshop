# HCPCS ETL Workshop — Data Engineering Project

This repository contains my completed Data Engineering workshop project, implemented in Python and PostgreSQL.
The pipeline extracts, transforms, loads, and validates HCPCS medical codes using a clean ETL workflow.

## Project Overview

## The goal of the workshop:

- Load HCPCS data (locally provided CSV)
- Clean & transform the dataset
- Load the processed dataset into PostgreSQL
- Run validation checks
- Verify the final data in the database

Due to system limitations (low memory), Airflow orchestration was not used.
Instead, the pipeline is run manually through a single script: run_pipeline.py.

The ETL logic is identical to the Airflow DAG steps, only executed without orchestration.

## Project Structure
- workshop_etl

- scripts
  - extract_hcpcs.py       # Loads raw HCPCS CSV
  - transform_hcpcs.py     # Cleans and transforms data
  - load_hcpcs.py          # Loads data into PostgreSQL
  - validate_hcpcs.py      # Validation rules
    
- data
  - raw
       - hcpcs.csv          # Raw dataset used for extract step
  - processed/
       - hcpcs_clean.csv    # Processed dataset after transform

- run_pipeline.py             # Runs the entire ETL end-to-end
- requirements.txt
- README.md                   # Project documentation

## How the Pipeline Works
1. Extract
Loads data from data/raw/hcpcs.csv
Logs number of rows extracted

2. Transform
Removes invalid/missing values
Standardizes columns
Saves clean output to data/processed/hcpcs_clean.csv

3. Load
Connects to PostgreSQL
Creates table hcpcs_codes if not exists
Performs UPSERT (update/insert) into the table

4. Validate
Confirms total rows = expected count

## Ensures:
no missing descriptions
no missing applications
valid categories only

## PostgreSQL Setup
Create database:
CREATE DATABASE workshop;

Create table (auto-created by script, but included for reference):
CREATE TABLE IF NOT EXISTS hcpcs_codes (
  code VARCHAR PRIMARY KEY,
  description TEXT,
  application TEXT,
  category VARCHAR(5)
);

## How to Run the Pipeline
1. Install dependencies:
pip install -r requirements.txt

2. Set PostgreSQL credentials in load_hcpcs.py and validate_hcpcs.py:
host="localhost",
port=5432,
database="workshop",
user="postgres",
password="postgres"

3. Run the entire pipeline:
python run_pipeline.py

## Expected console output:

Loading local HCPCS data...
Extracted 50 rows...
Transformed → hcpcs_clean.csv
Loading into PostgreSQL...
Load completed successfully.
Running validations...
Total HCPCS records in DB: 50
Validation complete.

## PostgreSQL Output Screenshot

<img width="1479" height="929" alt="image" src="https://github.com/user-attachments/assets/ec2d1ff0-7250-4fc8-88cf-fcc8d67491b4" />

## Validation Summary
50 records successfully loaded
0 missing descriptions
0 missing applications
0 invalid categories

<img width="1404" height="968" alt="image" src="https://github.com/user-attachments/assets/d40ccc89-9674-4480-a8f0-d8bdda5d74b9" />

