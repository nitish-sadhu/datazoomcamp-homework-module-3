# datazoomcamp-homework-module-3
module-3 homework
---loading the data into GCS
Ran the script 'load_yellow_taxi_data.py', by using the following command

        `python3 $(pwd)/load_yellow_taxi_data.py`

To create a GCS bucket and upload data into it, have used key from my service account.


---creating an external table on the data in GCS

        CREATE OR REPLACE EXTERNAL TABLE datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips_ext
        OPTIONS
        (
        format = "parquet",
        uris = ['gs://dezc-nits-module3-hw/*.parquet']
        )
        ;


---creating regular table from the external table

        CREATE OR REPLACE TABLE datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips
        AS
        SELECT * FROM datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips_ext
        ;


---Question 1
    count of records for the 2024 Yellow Taxi Data: 20332093

        select count(*) from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips
        ;


---Question 2:
    Estimated amount of data that will be read when the following query is executed on the External Table and the Table is:
        0 MB for the External Table and 155.12 MB for the Materialized Table.

        select distinct PULocationID from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips_ext
        ;

        select distinct PULocationID from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips
        ;


---Question 3:
    BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns (PULocationID, DOLocationID) requires reading more data than querying one column (PULocationID), leading to a higher estimated number of bytes processed.

        select PULocationID from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips
        ;

        select PULocationID, DOLocationID from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips
        ;


---Question 4:
    records have a fare_amount of 0
    8333

        select count(*) from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips
        where fare_amount = 0
        ; 


---Question 5:
    Best strategy to make an optimized table in Big Query if your query will always filter based on tpep_dropoff_datetime and order the results by VendorID
        Partition by tpep_dropoff_datetime and Cluster on VendorID

        CREATE OR REPLACE TABLE datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips_part
        PARTITION BY DATE(tpep_dropoff_datetime)
        CLUSTER BY VendorID
        AS
        SELECT * FROM datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips_ext
        ;


---Question 6:
    310.24 MB for non-partitioned table and 26.84 MB for the partitioned table

        select distinct VendorID from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips_part
        where cast(tpep_dropoff_datetime as date) >= '2024-03-01' and cast(tpep_dropoff_datetime as date) <= '2024-03-15'
        ;

        select distinct VendorID from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips
        where cast(tpep_dropoff_datetime as date) >= '2024-03-01' and cast(tpep_dropoff_datetime as date) <= '2024-03-15'
        ;

---Question 7: 
    Data in the external table is stored in a "GCP Bucket".


---Question 8: 
It is best practice in Big Query to always cluster your data: "True"

    select count(*) from datazoomcamp-484706.nyc_yellow_taxi_data_hw_nits.yellow_taxi_trips
    ;


---Question 9: 
    Estimated bytes to read are 0B, because the data is not present in Big Query but in GCS.