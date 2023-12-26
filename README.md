# Real_Time_Data_Streaming_using_Apache_Nifi-AWS-Snowpipe-Stream-Task-DATAWAREHOUSE-

# Architecture Diagram


![Architecure diagram](https://github.com/AdityaDevadiga/Real_Time_Data_Streaming_using_Apache_Nifi-AWS-Snowpipe-Stream-Task-DATAWAREHOUSE-/assets/72966036/9c23f74d-55b9-4bfb-a61d-cebc62e073bf)

   

# SCD2 Snowflake Docker Project

This Docker project demonstrates the implementation of Slowly Changing Dimension Type 2 (SCD2) using Snowflake, AWS S3, and Docker.

## Overview

This repository contains code and configurations for a Docker-based project showcasing SCD2 implementation using Snowflake, AWS S3, and various tools.

## Table of Contents

- [Data Generation](#data-generation)
- [Snowflake Database Setup](#snowflake-database-setup)
- [Snowflake Tables and Streams](#snowflake-tables-and-streams)
- [External Stage and File Format](#external-stage-and-file-format)
- [Data Ingestion with Snowflake Pipe](#data-ingestion-with-snowflake-pipe)
- [Data Transformation with Snowflake Merge](#data-transformation-with-snowflake-merge)
- [Snowflake Tasks](#snowflake-tasks)
- [View Creation](#view-creation)
- [AWS EC2 Instance and Security Group](#aws-ec2-instance-and-security-group)
- [Docker Installation](#docker-installation)
- [Accessing Tools](#accessing-tools)
- [Test Data Preparation](#test-data-preparation)

## Data Generation

For data generation, a Python script using the Faker library has been provided. Execute the script to create a CSV file with fake customer data.

```bash
pip install faker
python generate_fake_data.py

## Snowflake Database Setup
Execute the following SQL commands in Snowflake to set up the required database and schema:

sql
create database if not exists scd_demo;
use database scd_demo;
create schema if not exists scd2;
use schema scd2;
show tables;

## Snowflake Tables and Streams
Create tables for the customer, customer_history, and customer_raw in Snowflake. Also, a stream for changes in the customer table.

-- Create tables and stream
-- ...

-- Create or replace stream
-- ...
## External Stage and File Format

Create an external stage in Snowflake for S3 integration and define a file format.
CREATE OR REPLACE STAGE SCD_DEMO.SCD2.customer_ext_stage
    url='s3://your-s3-bucket/'
    credentials=(aws_key_id='' aws_secret_key='');

CREATE OR REPLACE FILE FORMAT SCD_DEMO.SCD2.CSV
    TYPE = CSV,
    FIELD_DELIMITER = ",",
    SKIP_HEADER = 1;
Replace your-s3-bucket with your actual S3 bucket.

## Data Ingestion with Snowflake Pipe
Create a Snowflake pipe to ingest data from the external stage into the customer_raw table.
CREATE OR REPLACE PIPE customer_s3_pipe
    auto_ingest = true
    AS
    COPY INTO customer_raw
    FROM @customer_ext_stage
    FILE_FORMAT = CSV;

-- Monitor and check pipe status
SHOW PIPES;
SELECT SYSTEM$PIPE_STATUS('customer_s3_pipe');
SELECT COUNT(*) FROM customer_raw;


##Data Transformation with Snowflake Merge
Perform data transformation using a Snowflake merge statement to handle SCD2.

-- Merge statement
-- ...

-- Truncate the raw table
TRUNCATE  customer_raw;

-- Execute SCD2 procedure
CALL pdr_scd_demo();

-- Truncate the customer table
TRUNCATE  customer;

##Snowflake Tasks
Set up Snowflake tasks for scheduling data transformations.

-- Task setup
-- ...

##-- Monitoring tasks
SHOW TASKS;
SELECT * FROM TABLE(INFORMATION_SCHEMA.TASK_HISTORY()) WHERE STATE = 'SCHEDULED' ORDER BY COMPLETED_TIME DESC;


##View Creation
Create a view to represent changes in the customer data.
-- View creation
-- ...

## AWS EC2 Instance and Security Group
For deploying the Docker project, create an AWS EC2 instance with a security group allowing traffic on ports 4000 - 38888.

bash

# Commands for connecting to EC2
# ...

# Copy files to EC2
scp -r -i snowflake-project.pem docker-exp ec2-user@<ec2-ip>:/home/ec2-u



##Docker Installation
Install Docker on the EC2 instance.

# Docker installation
sudo yum update -y
sudo yum install docker
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo gpasswd -a $USER docker
newgrp docker
sudo yum install python-pip
sudo pip install docker-compose

# Start and stop Docker
sudo systemctl start docker
sudo systemctl stop docker


##Accessing Tools
Access Docker containers and tools locally.

# Docker commands
docker ps
docker exec -i -t nifi bash

# Tools access
Jupyter Lab: http://ip_address:4888/lab?
NiFi: http://ip_address:2080/nifi/


##Test Data Preparation
Prepare test data within the NiFi container.
docker exec -i -t nifi bash
/opt/workspace/nifi/FakeDataset/customer_(timestamp).csv


This README provides step-by-step instructions for setting up, running, and testing the SCD2 Snowflake Docker project. Make sure to replace placeholder values with your actual configurations.
