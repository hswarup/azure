Provision an Azure Data Factory workspace, create pipeline activities to migrate data from an Azure SQL Database into a data lake, and create partitions as you migrate data.


Solution
Log in to the Azure portal using the credentials provided on the lab page. Be sure to use an incognito or private browser window to ensure you're using the lab account rather than your own.

Prepare the Environment
Navigate to Storage accounts.
Click Create storage account.
For Resource group, select the existing resource group.
For Storage account name, enter storagedp203XXXXXX, replacing the Xs to make it unique.
For Region, select West US.
Ensure Performance is set to Standard.
For Redundancy, select Locally-redundant storage (LRS).
Click Next: Advanced.
Under Hierarchical Namespace, ensure Enable hierarchical namespace is selected.
Click Next until you arrive at Data protection.
De-select the following three features:
Enable soft delete for blobs
Enable soft delete for containers
Enable soft delete for file shares
Click Next through the rest of the settings, then click Create.
Create an Azure Data Factory
In the search bar at the top of the page, search for and select Data factories.
Click Create data factory, and set the following:
Name: Enter dfdp203XXXXXX, replacing the Xs to make it unique.
Region: Select West US.
Resource group: Select the existing resource group.
Click Next until you arrive at Networking.
For Connect via, select Public endpoint.
Click Next through the rest of the settings, then click Create.
Create an SQL Database Instance
In the search bar at the top of the page, search for and select SQL databases.
Click Create SQL database.
For Resource group, select the existing resource group.
For Database name, enter sqldb203XXXX, replacing the Xs to make it unique.
For Server, select Create new, and enter the following:
Server name: Enter sqldb203XXXXX, replacing the Xs to make it unique.
Location: Select West US.
Authentication method: Select Use SQL authentication.
Server admin login: Enter adminp.
Password: Create a password.
Confirm password: Confirm the password.
Click OK.
Back on the Create SQL Database page, for Want to use SQL elastic pool?, select No.
For Workload environment, ensure Development is selected.
Next to Compute + storage, click Configure database.
For Server tier, select Basic (For less demanding workloads).
Leave the other settings at default, and click Apply.
Back on the Create SQL Database page, for Backup storage redundancy, select Locally-redundant backup storage.
Click Next: Networking, and set the following values:
Connectivity method: Select Public endpoint.
Allow Azure services and resources to access this server: Select Yes.
Add current client IP address: Select Yes.
Leave the other settings at default, and click Next: Security > Next: Additional settings.
For Use existing data, select Sample, and click OK on the AdventureWorksLT pop-up that may appear.
Click Next through the rest of the settings, then click Review + create.
Click Create.
Note: This may take up to 5 minutes to deploy. You can click on the bell icon in the upper right corner of the window to confirm when the resource is deployed.

Create the Data Sources
In the search bar at the top of the page, search for and select Data factories.
Click on the available dfdp203 Data factory resource.
Click Launch studio.
In the left navigation menu, click on the Expand icon.
Click Author.
In the Factory Resources sub-navigation menu, click + to add a new resource, and select Dataset.
In the New dataset menu that appears on the right,search for and select Azure SQL Database.
Click Continue.
For Set properties, enter the following:
Linked service: Select + New, and enter the following:
Server name: Select the available server.
Database name: Select sqldb.
Authentication type: Select SQL authentication.
User name: Enter adminp.
Password: Enter the password you created in the first objective.
In the bottom right corner, click Test connection; you should see the Connection successful notification.
Click Create.
For Table name, select SalesLT.Customer .
Click OK.
In the Factory Resources sub-navigation menu, expand Datasets; you should see the AzureSqlTable1 dataset.
Create a Data Flow
In the Factory Resources sub-navigation menu, click + to add a new resource, and select Data flow > Data flow.
In the new dataflow1 tab, click on the Add Source tile located on the canvas, and select Add Source.
Navigate to the Source settings tab on the bottom half of the page.
For Output stream name, enter SalesLTCustomer.
For Dataset, select AzureSqlTable1.
Navigate to the Data preview tab.
In the upper left corner of the canvas, slide the toggle from Validate to Data flow debug.
In the Turn on data flow debug menu that appears on the right, click OK.
Click on the SalesLTCustomer tile located on the canvas.
On the bottom-right corner of the tile, click the + icon, and select Sink.
Ensure the sink1 tile is now selected, and navigate to the Sink tab.
For Output stream name, enter DataLake.
For Dataset, select + New.
Under New dataset, search for and select Azure Blob Storage.
Click Continue.
Select Parquet.
Click Continue.
For Set properties, enter the following:
Linked service: Select + New, and enter the following:
Azure subscription: Select the available subscription.
Storage account name: Select the available storage account.
Click Create.
Create a Sample Container
Next to File path, click on the folder icon to browse, and confirm you don't see any files.
Navigate back to the Data factory browser tab; in the search bar, enter and select Storage accounts.
Click on the available storagedp203 storage account resource.
In the left sub-navigation menu, under Data storage, select Containers.
Click + Container.
In the New container menu that appears on the right, for Name, enter sample.
Click Create.
Navigate back to the Data factory browser tab.
Next to File path, click on the folder icon to browse, and select sample.
Note: Alternatively, you could enter sample in the File path > Container field.

Click OK.
Next to Dataset, click Test Connection; you should see the Connection successful notification.
Select and Create Five Hash Partitions
Navigate to the Optimize tab, and set the following:
Partition option: Choose Set partitioning.
Partition type: Choose Hash.
Number of partitions: Select 5.
Column values to hash on: For this value, complete the following steps:
Navigate to the SalesLTCustomer tile on the canvas.
Click on the Data preview tab.
Click Refresh; this will fetch the necessary data. Due to the high cardinality of CustomerID, you will be utilizing this column for the lab.
Navigate back to the DataLake tile.
For Column values to hash on, select CustomerID.
In the upper left corner above the canvas, click Publish all.
In the menu that appears on the right reviewing the changes, click Publish.
Note: This may take a few minutes to publish.

Create a Data Pipeline
In the left navigation menu, click Author.
In the Factory Resources sub-navigation menu, click + to add a new resource, and select Pipeline > Pipeline.
In the Activities sub-navigation menu, under Move and transform, select Data flow, and drag and drop it onto the canvas.
Ensure the Data flow1 tile is selected, and navigate to the Settings tab.
For Data flow, select dataflow1.
In the upper left corner of the canvas, click Validate.
In the menu that appears on the right, click Close.
In the upper left corner above the canvas, click Publish all.
In the menu that appears on the right reviewing the changes, click Publish.
Note: This may take a few minutes to publish.

Once the pipeline is published, in the upper left corner of the canvas, click Add trigger > Trigger now.
Click OK.
Note: It may take a few minutes to run.

Verify Partitioning
In the left navigation menu, click on Monitor; you should see the pipeline1 run is complete.
Click pipeline1.
Under Activity runs on the bottom half of the page, click data flow 1.
Click the glasses icon that appears next to data flow 1.
On the canvas, click on the Sink - DataLake tile.
Under DataLake diagnostics, review Total partition, and confirm the final number of partitions is five.
