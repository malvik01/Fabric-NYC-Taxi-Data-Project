# pl_stg_processing_nyctaxi

![Screenshot 2024-07-27 at 13.05.30.png](pl_stg_processing_nyctaxi%20eb4ad33c3bb54d92bafeee33a57360bc/Screenshot_2024-07-27_at_13.05.30.png)

For the Script Activity **“Latest Processed Date”**

```sql
select top 1 
latest_processed_pickup 
from metadata.processing_log 
where table_processed = 'staging_nyctaxi_yellow'
order by latest_processed_pickup desc;
```

---

Pipeline expression for **v_date** Set Variable activity

```sql
@formatDateTime(addToTime(activity('Latest Processed Date').output.resultSets[0].rows[0].latest_processed_pickup, 1, 'Month'), 'yyyy-MM')
```

---

**Copy to Staging**

Pre Copy Script

![Screenshot 2024-07-27 at 13.13.47.png](pl_stg_processing_nyctaxi%20eb4ad33c3bb54d92bafeee33a57360bc/Screenshot_2024-07-27_at_13.13.47.png)

---

For the Stored Procedure Activity **“SP Removing Outlier Dates”.**

Created the Stored Procedure **stg.data_cleaning_stg** in the Data Warehouse using the code below.

```sql
create procedure stg.data_cleaning_stg
@end_date datetime2,
@start_date datetime2
as
delete from stg.nyc_taxi_yellow where tpep_pickup_datetime < @start_date or tpep_pickup_datetime > @end_date;
```

![Screenshot 2024-07-27 at 13.12.29.png](pl_stg_processing_nyctaxi%20eb4ad33c3bb54d92bafeee33a57360bc/Screenshot_2024-07-27_at_13.12.29.png)

---

For the Stored Procedure Activity **“SP Loading Staging Metadata”.**

Code to create the **metadata.processing_log** table.

```sql
create table metadata.processing_log
(
	pipeline_run_id varchar(255), 
	table_processed varchar(255), 
	rows_processed INT, 
	latest_processed_pickup datetime2(6),
	processed_datetime datetime2(6)
);
```

Created the Stored Procedure **metadata.insert_staging_metadata** in the Data Warehouse using the code below.

```sql
CREATE PROCEDURE metadata.insert_staging_metadata
    @pipeline_run_id VARCHAR(255),
    @table_name VARCHAR(255),
    @processed_date DATETIME
AS
    INSERT INTO metadata.processing_log (pipeline_run_id, table_processed, rows_processed, latest_processed_pickup, processed_datetime)
    SELECT
        @pipeline_run_id AS pipeline_id,
        @table_name AS table_processed,
        COUNT(*) AS rows_processed,
        MAX(tpep_pickup_datetime) AS latest_processed_pickup,
        @processed_date AS processed_datetime
    FROM stg.nyc_taxi_yellow;
```

![Screenshot 2024-07-27 at 13.12.55.png](pl_stg_processing_nyctaxi%20eb4ad33c3bb54d92bafeee33a57360bc/Screenshot_2024-07-27_at_13.12.55.png)