# DE_Cloud_Data_Warehouse_Snowflake_POC

The project conducted a Proof of concept on Snowflake and compare their performance, security, integration 



## Scalability
Manipulate the Warehouse using Script
NO interruption for reading and writing w WH up/down
Scale in/out and up/down
Test Resource Monitoring Notification - Warehouse Usage


## Performace comparison from synapse and snowflake

---ETL
- large warehouse:  the data in Azure Synapse are 3 to ten times as those in Snowflake
- big table: The migration for the big table from SY to SF (600K GB to 300KGB)
- ETL Load Difference: The transfer time between them is almost the same.  The writing_to_sink value in Synapse is smaller than Snowflake's
- use ADF to ingest or drop files on Lake to Snowflake: The time of texting from Synapse is more than writing to ADF ( 25min - 15min)
- Test parallel run and check limit, both of them have great performances
  
---query
- Complex logic: snowflake has higher speed (1/2), especially when the logic runs the second time, the duration time is 1/100
- Multi-Join:  snowflake:1/3 synapse
- Query Auditing Support: snowflake: easily to get the query history

Snowflake Performance Features Summary:

Micro-Partitions: this is good for ETL load, especially for the multi files loading in/out snowflake which utilized Micro-Partitions

Search Optimization - this is used to get one ID from Terabytes of data, which needs to be paid as you go, the normal search is already faster than Synapse

Caching - Snowflake utilized Metadata cache, statistic cache, and part of the data result cache, with the very noticeable difference   

Query Profiling - Query profiling and pruning could help developers to figure out where went wrong in terms of partition scan

Warehouse Performance, Concurrency - auto-resume and auto-suspend safely is a big advantage

## security
Security Role Structure Design Considerations
The Query to get the whole security hierarchy, from a high level, the sysadmin would be recommended to

1> use role ACCOUNTADMIN;
2> Grant imported privileges on database SNOWFLAKE to role SYSADMIN;
3> Use role SYSADMIN; -- Remember to always get out of the ACCOUNTADMIN role when done using it.

4> In real practice, try not using an ADMIN account but using 
4-1 DBA (all databases with Grant privilege)
4-2 DB owners (individual database owners to create objects, such as RISK_DEV, TPM_DEV), riskdba, tpmdba
4-3 Individual DB developers, consumers

Snowflake and Synapse include RBAC (role-based access control) for managing users and privileges, and both solutions automatically encrypt data both- in transit and at rest. 
Both platforms offer strong MFA (multi-factor authentication) security and connection via VPNs (virtual private networks). Azure Private Link is supported by each tool (Snowflake also supports AWS PrivateLink).
except for what snowflake has, Azure Synapse has a Microsoft Defender feature that keeps an eye on resources and offers integrated threat detection to identify loopholes and stop unwanted actions.
Native integration with AAD (Azure Active Directory), Azure Key Vault, and by extension SSO (Single Sign On) is as simple and straightforward as it gets.

## Integration
From a strict data warehousing perspective Snowflake definitely has the edge, but from a data lake and overall data platform perspective, Synapse usually wins.

Synapse is built to run as an analytics layer on top of Azure Data Lake and also act as a data warehouse for analytics workloads.

Synapse integrates natively with other specific Azure services like Github, Azure DevOps, Azure Data Factory, Power BI, etc.

With its native Spark and Delta Lake integrations, Azure Synapse is also extremely robust at handling ML, AI, and streaming workloads

## Development

- ETL:  snowflake: Snowflake Python worksheet and Snowflake Stored Procedure + ADF or Synapse. synapes: ADF - Built-in integration engine: Synapse Pipelines offers an SSIS-like experience, with a GUI-based development experience of ETL processes.
- Programming: Snowflake just recently rolled out a new feature called Snowpark, which is a new developer tool that supports Java, Scala, and Python programming languages for unstructured data use cases. While, for synapse, spark pool and Notebook integration: Synapse offers ad-hoc spark notebooks to be run, as well as Pipeline-based notebook calls.
Allows for a Hybrid Transactional-Analytical Processing (HTAP) paradigm using Synapse Link.
- ML: Spark-Snowflake connector vs Spark notebook
- Query History & profile: very good SQL history and profile auditing for developers' performance troubleshooting for snowflake
- DevOps: Terraform + Python vs Source control integration: Synapse natively integrates with Github and ADO as source control systems. CI/CD for Synapse is supported via Azure DevOps, while Snowflake requires more manual steps.
  
## Data Sharing

snowflake vendors, allows users to securely share their data with other snowflake accounts, without requiring data movement or additional infrastructure
Robust Web Interface for compute/virtual warehouse & database components.
However, for Snapyse, built in Power BI integration allows fast and easy data exploration and visualization.

## Cost

Since Snowflake's pricing is based on the usage of specific warehouses, it is relatively simple. The sizes of the compute resources in Snowflake are X-Small, Small, Medium, Large, X-Large, etc. Costs and servers/clusters for each of these sizes vary greatly. To begin with, an X-small Snowflake warehouse costs about 0.0003 credits per second or one credit per hour. Based on consumption, Snowflake offers both reserved (pre-purchased) plans and on-demand (pay-as-you-go) pricing (this is calculated on a per-second basis). Rates for upfront payments and credit purchases are often less expensive than those for on-demand solutions. However, credit fees inside Snowflake vary greatly depending on the company tier.

This doubles with each increase in warehouse size. The number of virtual machines also doubles with each increase in size. Credit costs within Snowflake are calculated based on the business tier that is chosen. The on-demand version of Snowflake Standard Edition begins at $2 per credit. The largest warehouse (6X-Large burns approximately 512 credits per hour or .1422 credits per second.

Snowflake offers on-demand (pay-as-you-go) pricing and also reserved (pre-purchased) plans based on usage (this is calculated on a per-second basis). Paying upfront and purchasing credits usually provides lower rates compared to the on-demand options. However, credit costs within Snowflake vary substantially depending on the business tier. Storage within Snowflake is charged at a flat rate of $40 per month for each TB for on-demand customers and $23 per TB for upfront customers.

Compared to Snowflake, Synapse pricing is a little more complex because there are more alternatives. In Azure, computing resources, or DWUs, range in price from $1.51 per hour for the smallest size to $453 for the highest level. But by acquiring reserved capacity, these expenses can be significantly decreased, like with Snowflake. In contrast, Serverless SQL pools impose a flat fee of $5.65 for each TB of processed data. Azure's storage charges are about $26 per TB per month, which is slightly more expensive than Snowflake's.

Pricing with Synapse is a bit more complex as there are more options compared to Snowflake. Computing resources or DWUs in Azure start at $1.51 per hour for the smallest size and can go all the way up to $453 for the largest size. Like Snowflake though, these costs can be drastically reduced by purchasing reserved capacity. On the other hand, Serverless SQL pools charge a flat rate of $5.65 for each TB of data that is processed. Storage costs within Azure are slightly more expensive than Snowflake, costing around $26 per TB per month.

One important thing to note is that Snowflake charges for compute on a per-minute basis and Azure Synapse charges based on an hourly basis. This means that if query execution in Snowflake takes 3 minutes users pay for 3 minutes of computing. Conversely, if Azure Synapse is active for 30 minutes, users would still be forced to pay for an entire hour. This is why there appears to be such a large discrepancy in the pricing model of both solutions.

Preview unavailable
Azure synapse shines with constant usage pattern while Snowflake is a better fit variable usage patterns.

## lesson learned
1.  for their sight, weight
2.  security

