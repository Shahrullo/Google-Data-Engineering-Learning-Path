## Overview

[BigQuery](http://bigquery.cloud.google.com/) is Google's fully managed, NoOps, low cost analytics database. With BigQuery you can query terabytes and terabytes of data without having any infrastructure to manage or needing a database administrator. BigQuery uses SQL and can take advantage of the pay-as-you-go model. BigQuery allows you to focus on analyzing data to find meaningful insights.

In this lab you will ingest subsets of the NYC taxi trips data into tables inside of BigQuery.

What you'll learn
* Loading data into BigQuery from various sources
* Loading data into BigQuery using the CLI and Console
* Using DDL to create tables

## Setup

1. Create GCP project
2. Click Open Google Console.

### Open BigQuery Console
1. In the Google Cloud Console, select **Navigation menu** > **BigQuery**.

The **Welcome to BigQuery in the Cloud Console** message box opens. This message box provides a link to the quickstart guide and lists UI updates.

2. Click **Done**.


## Task 1. Create a new dataset to store tables

1. To create a dataset, click on the **View actions** icon (the three vertical dots) next to your project ID and select **Create dataset**.

2. Next, name your Dataset ID **nyctaxi** and leave all other options at their default values, and then click **Create dataset**.

You'll now see the nyctaxi dataset under your project name.

## Task 2. Ingest a new dataset from a CSV

In this section, you will load a local CSV into a BigQuery table.

1. Download a subset of the NYC taxi 2018 trips data locally onto your computer from [this link](https://storage.googleapis.com/cloud-training/OCBL013/nyc_tlc_yellow_trips_2018_subset_1.csv).

2. In the BigQuery Console, Select the nyctaxi dataset then click **Create Table**

**Specify the below table options:**

**Source:**

* Create table from: **Upload**
* Choose File: **select the file you downloaded locally earlier**
* File format: **CSV**

**Destination:**

* Table name: **2018trips** Leave all other settings at default.

**Schema:**

* Check **Auto Detect** (tip: Not seeing the checkbox? Ensure the file format is CSV and not Avro)

**Advanced Options**

* Leave at default values

Click **Create Table**.

3. You should now see the **2018trips** table below the nyctaxi dataset.

Select the 2018trips table and view details:

4. Select **Preview** and confirm all columns have been loaded (sampled below):

You have successfully loaded a CSV file into a new BigQuery table.

### Running SQL Queries

Next, practice with a basic query on the 2018trips table.

1. In the Query Editor, write a query to list the top 5 most expensive trips of the year:
```
#standardSQL
SELECT
  *
FROM
  nyctaxi.2018trips
ORDER BY
  fare_amount DESC
LIMIT  5
```


## Task 3. Ingest a new dataset from Google Cloud Storage

Now, let's try to load another subset of the same 2018 trip data that is available on Cloud Storage. And this time, let's use the CLI tool to do it.

1. In your Cloud Shell, run the following command :
```
bq load \
--source_format=CSV \
--autodetect \
--noreplace  \
nyctaxi.2018trips \
gs://cloud-training/OCBL013/nyc_tlc_yellow_trips_2018_subset_2.csv
```
`Note: With the above load job, you are specifying that this subset is to be appended to the existing 2018trips table that you created above.`

2. When the load job is complete, you will get a confirmation on the screen.

3. Back on your BigQuery console, select the 2018trips table and view **details**. Confirm that the row count has now almost doubled.

4. You may want to run the same query like earlier to see if the top 5 most expensive trips have changed.


## Task 4. Create tables from other tables with DDL

The 2018trips table now has trips from throughout the year. What if you were only interested in January trips? For the purpose of this lab, we will keep it simple and focus only on pickup date and time. Let's use DDL to extract this data and store it in another table

1. In the Query Editor, run the following CREATE TABLE command :
```
#standardSQL
CREATE TABLE
  nyctaxi.january_trips AS
SELECT
  *
FROM
  nyctaxi.2018trips
WHERE
  EXTRACT(Month
  FROM
    pickup_datetime)=1;
```

2. Now run the below query in your Query Editor find the longest distance traveled in the month of January:
```
#standardSQL
SELECT
  *
FROM
  nyctaxi.january_trips
ORDER BY
  trip_distance DESC
LIMIT
  1
```