### Using Polybase in Azure Synapse to Ingest from an external source(Data Lake), Transform and Write to External location(Data Lake)

#### Create master key
```
CREATE MASTER KEY;
GO
```

#### Create credential to access Data Lake
```
CREATE DATABASE SCOPED CREDENTIAL PSDataLakeCredential
WITH 
	IDENTITY = 'user', 
	Secret = '***Access Key***';
GO
```

#### Create external data source, pointing to Data Lake
```
CREATE EXTERNAL DATA SOURCE PSDataLake
with (  
      TYPE = HADOOP,
      LOCATION ='abfss://***ContainerName***@***DataLakeName***.dfs.core.windows.net',  
      CREDENTIAL = PSDataLakeCredential  
);  
GO
```

#### Create external file format
```
CREATE EXTERNAL FILE FORMAT CSVFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  
	(   
		FIELD_TERMINATOR   = ','
		, STRING_DELIMITER = '"'
        , DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss'
        , USE_TYPE_DEFAULT = FALSE
        , FIRST_ROW  = 2
    )
);
GO
```

#### Create schema for external resources
```
CREATE SCHEMA ext
GO
```

#### Create external table for Taxi Zones  
```
CREATE EXTERNAL TABLE ext.TaxiZones
(
	LocationId INT,
	Borough NVARCHAR(100),
	Zone NVARCHAR(100),
    ServiceZone NVARCHAR(100)
)
WITH
(
    DATA_SOURCE = PSDataLake
  , FILE_FORMAT = CSVFileFormat  
  -- , LOCATION='/Raw/TaxiZones/TaxiZones1.csv'  (Just 1 file)
  , LOCATION='/Raw/TaxiZones'  (All files in a folder)
)
GO
```

```
#### Select from external table
SELECT * FROM ext.TaxiZones
GO
```

#### Create schema 'main' for Dedicated SQL Pool tables
```
CREATE SCHEMA main
GO
```

#### Create table main.TaxiRides, using CTAS (via Polybase) - (This is a physical table created in Dedicated SQL Pool)
```
CREATE TABLE main.TaxiRides
WITH 
(
	DISTRIBUTION = ROUND_ROBIN,
	CLUSTERED COLUMNSTORE INDEX
) 
AS 
SELECT * 
FROM ext.TaxiRides
```

#### Select from Dedicated SQL Pool table
```
SELECT * FROM main.TaxiRides
```

#### Perform Transformation(Enrichment using JOINs and filtering using WHERE clause)
#### Use 'CETAS' to write data to Data Lake (using PolyBase)
```
CREATE EXTERNAL TABLE ext.ProcessedTaxiRides
WITH 
(  
    DATA_SOURCE = PSDataLake,
    FILE_FORMAT = SynapseParquetFormat,
    LOCATION='/Facts/TaxiRides.parquet'    
) 
AS
SELECT 
      *
FROM main.TaxiRides y
    INNER JOIN ext.TaxiZones z ON y.PickupLocationId = z.LocationID
WHERE PickupTime >= '2023-01-01' 
    AND PickupTime < '2023-02-01'
    AND PassengerCount > 0
	AND z.ServiceZone = 'Yellow Zone';
```















