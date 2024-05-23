Provision a Data Synapse Analytics workspace, work with data flows, create and trigger data pipelines, and partition data as data is migrated.

Solution
Log in to the Azure portal using the credentials provided on the lab page. Be sure to use an incognito or private browser window to ensure you're using the lab account rather than your own.

Prepare the Environment
Navigate to Azure Synapse Analytics.
Click Create Synapse workspace.
For Resource group, select the existing resource group.
For Workspace name, enter dp203XXXXXX, replacing the Xs to make it unique.
For Region, ensure West US is selected.
For Select Data Lake Storage Gen2 > Account name, click Create new and enter the following:
Name: You can copy and paste in the workspace name.
Click OK.
For File system name, click Create new and enter the following:
Name: You can copy and paste in the workspace name.
Click OK.
Click Next: Security.
Set the following:
SQL Server admin login: Enter adminp.
SQL Password: Create a password.
Confirm password: Confirm the password.
Click Next through the rest of the settings, then click Review + create.
Click Create.
Note: This may take a few minutes to deploy.

Create a Data Flow in Azure Synapse Analytics
Once the deployment is complete, click Go to resource group.
Click on the Synapse workspace resource.
Scroll down, and under Open Synapse Studio, click Open.
In the left navigation menu, click Data.
Next to Data, click on the + icon, and select Browse gallery.
Navigate to the Datasets tab, and select Bing COVID-19 Data.
Click Continue.
Under Datasets, you should see the available datasets in CSV, JSON, JSON-Lines, and Parquet.
Copy the Parquet dataset URL.
Click Add dataset.
Once the dataset is created, in the left navigation menu, click Develop.
Next to Develop, click on the + icon, and select Data flow.
Click on the Add Source tile located on the canvas, and select Add source.
Navigate to the Source settings tab on the bottom half of the page.
For Dataset, click +New.
In the New integration dataset menu that appears on the right,search for and select Azure Blob Storage
Click Continue.
Select Parquet.
Click Continue.
For Set properties, enter the following:
Linked service: Select bing-covid-19-data.
Import schema: Select From connection/store.
File path: Click on the dropdown arrow, and select From specified path.
Click curated > covid-19 > bing_covid-19_data > latest > Scroll down, and select bing_covid-19_data.parquet.
Note: Alternatively, you could paste in the Parquet dataset URL you copied earlier.

Click OK.
Click OK.
Navigate to the Source options and Projection tabs; you should see the column names populated.
Click on the source1 tile located on the canvas.
On the bottom-right corner of the tile, click the + icon, and select Sink.
Click on the sink1 tile.
Navigate to the Sink tab on the bottom half of the page.
For Dataset, select + New.
Under New integration set, search for and select Azure Blob Storage.
Click Continue.
Select Parquet.
Click Continue.
For Set properties, enter the following:
Linked service: Select + New, and enter the following:
Azure subscription: Select the available subscription.
Storage account name: Select the available storage account.
In the bottom right corner, click Test connection; you should see the Connection successful notification.
Click Create.
Next to File path, click on the folder icon to browse, and select the folder beginning with dp203.
Click OK.
For Import schema, select None.
Click OK.
Navigate to the Optimize tab, and set the following:
Partition option: Choose Set partitioning.
Partition type: Choose Round Robin.
Number of partitions: Select 5.
Create a Data Pipeline in Azure Synapse Analytics
In the left navigation menu, click Integrate.
Next to Integrate, click on the + icon, and select Pipeline.
In the Activities left navigation menu, under Move and transform, select Data flow, and drag and drop it onto the canvas.
Ensure the Data flow1 tile is selected, and navigate to the Settings tab.
For Data flow, select Dataflow1.
In the upper left corner of the canvas, click Validate.
In the menu that appears on the right, click Close.
In the upper left corner above the canvas, click Publish all.
In the menu that appears on the right reviewing the changes, click Publish.
Note: This may take a few minutes to publish.

Run the Data Pipeline and Verify Partitioning
Once the pipeline is published, in the upper left corner of the canvas, click Add trigger > Trigger now.
Click OK.
In the left navigation menu, click on Monitor; you should see the Pipeline 1 run is in progress.
Note: It may take a few minutes to run.

Once the pipeline run is complete, click Pipeline 1.
Under Activity name on the bottom half of the page, click Data flow1.
Click the glasses icon that appears next to Data flow1.
On the canvas, click on the Source - source1 tile.
Under source1 diagnostics, review the partition chart confirming the initial number of partitions (i.e, under five).
On the canvas, click on the Sink - sink1 tile.
Under sink1 diagnostics, confirm the final number of partitions is five.
