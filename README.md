# DE_Cloud_Data_Warehouse_Snowflake_POC

The project conducted a Proof of concept on Snowflake and compare their performance, security, integration 

## Performace comparison from synapse and snowflake
- large warehouse:  the data in Azure Synapse are 3 to ten times as those in Snowflake
- big table: The migration for the big table from SY to SF (600K GB to 300KGB)
- ETL Load Difference: The transfer time between them is almost the same.  The writing_to_sink value in Synapse is smaller than Snowflake's
- use ADF to ingest or drop files on Lake to Snowflake:
- Test parallel run and check limit
