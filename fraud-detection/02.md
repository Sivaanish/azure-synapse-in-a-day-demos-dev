## Exercise 2:  Perform Ad Hoc Queries from the Storage Account

Duration: 50 minutes

Plot the trends of the cities where unauthorized use of credit cards occurred and the amounts on a map, so that you can ascertain the geographical factors associated with frequent unauthorized use and the cities where large losses frequently occur.

### Task 1:  Create a Dataset

This first task will export credit card predictions from the prior exercise into CSV format.

1. In the [Azure portal](https://portal.azure.com), type in "azure synapse analytics" in the top search menu and then select **Azure Synapse Analytics** from the results.

    ![In the Services search result list, Azure Synapse Analytics is selected.](media/azure-create-synapse-search.png 'Azure Synapse Analytics')

2. Select the workspace you created before the hands-on lab.

    ![The Azure Synapse Analytics workspace for the lab is selected.](media/azure-synapse-select.png 'synapselabfraudjdhasws workspace')

3. Select **Open** underneath **Open Synapse Studio** from the Synapse workspace page.

    ![Launch Synapse Studio is selected.](media/azure-synapse-launch-studio.png 'Launch Synapse Studio')

4. Select the **Manage** tab from Synapse studio.

    ![The Manage option is selected.](media/azure-synapse-manage.png 'Manage')

5. Select the **Linked services** option from the External connections section. Then select **+ New** to add a new linked service.

    ![The option to add a new linked service is selected.](media/azure-synapse-new-linked-service.png 'New linked service')

6. Enter **synapse** into the search menu and select the **Azure Synapse Analytics (formerly SQL DW)** option. Then, select **Continue**.

    ![The option to add a new Azure Synapse Analytics linked service is selected.](media/azure-synapse-new-linked-service-1.png 'New Azure Synapse Analytics service')

7. In the **New linked service** tab, complete the following and then select **Create** to create a new linked service connection.

    | Field                           | Value                                              |
    | ------------------------------  | ------------------------------------------         |
    | Name                            | _`FraudDetectionSynapse`_                          |
    | Connect via integration runtime | _select `AutoResolveIntegrationRuntime`_           |
    | Account selection method        | _select `From Azure subscription`_                 |
    | Azure subscription              | _select your Azure subscription_                   |
    | Server name                     | _`synapselabfraud` + unique id (example: `synapselabfraud311554`)_ |
    | Database name                   | _select `synapsesql`_                              |
    | Authentication type             | _select `SQL authentication`_                      |
    | User name                       | _enter `asa.sql.admin`_                             |
    | Password                        | _enter the password you created earlier or that was provided to you for your hosted lab environment_           |

    ![In the New linked service tab, form field entries are filled in.](media/azure-synapse-new-linked-service-2.png 'New linked service')

8. Select the **Data** tab from Synapse studio.

    ![The Data option is selected.](media/azure-synapse-data.png 'Data')

9. Drill down the SQL pool tables and then select the ellipsis (...) next to the **synapse.CreditCard** table.  From there, select **Data flow** to open a new data flow.

    ![Create a new Data flow.](media/azure-synapse-new-data-flow.png 'Data flow')

10. Name the data flow **DataflowCreditCard** and the integration dataset name **DatasetSqlPoolCreditCard**. Then, select **Create** to create the data flow.

    ![Names are provided for the data flow and dataset.](media/azure-synapse-dataflowcreditcard.png 'DataflowCreditCard')

11. Name the source **sourceCreditCard**.  Then select the **Open** option next to the Dataset.

    ![The output stream name is sourceCreditCard.](media/azure-synapse-sourcecreditcard.png 'Output stream name')

12. In the Linked service menu, select **FraudDetectionSynapse**.  Then, in the Table menu, select **synapse.CreditCard**.

    ![The output stream name is sourceCreditCard.](media/azure-synapse-sourcecreditcard-2.png 'Output stream name')

13. Select the **DataflowCreditCard** tab to return to the data flow.  Then, select the **+** option next to the source and choose **Select** from the Schema modifier list.

    ![The schema modifier Select is selected.](media/azure-synapse-new-select.png 'Select')

14. Change the name of the new output stream to **SelectCreditCard**.

    ![The output stream name is SelectCreditCard.](media/azure-synapse-selectcreditcard.png 'Output stream name')

15. Select the **+** option next to the source and choose **Sink** from the Destination list.

    ![The destination Sink is selected.](media/azure-synapse-new-sink.png 'Sink')

16. Change the name of the new output stream to **SinkCreditCard**.  Then select the **+ New** option to create a new Dataset.

    ![The new Dataset option is selected.](media/azure-synapse-sinkcreditcard.png 'New Dataset')

17. From the New integration dataset menu, select **Azure Data Lake Storage Gen2** and then select **Continue**.

    ![The new Dataset option is selected.](media/azure-synapse-sink-dlsgen2.png 'New Dataset')

18. On the Select format menu, choose **DelimitedText** for the output type and then select **Continue**.

    ![The delimited text option is selected.](media/azure-synapse-sink-format.png 'Select format')

19. Enter **outputCreditCardCSV** as the file name.  Then, from the Linked service menu, choose **+ New**.

    ![The new linked service is selected.](media/azure-synapse-sink-properties.png 'Set properties')

20. In the **New linked service** menu, complete the following and then select **Create** to create a new Azure Data Lake Storage Gen2 linked service.

    | Field                          | Value                                              |
    | ------------------------------ | ------------------------------------------         |
    | Name                           | _`SynapseLabFraudADLS`_ |
    | Authentication method          | _select `Account key`_                             |
    | Account selection method       | _select `From Azure subscription`_                 |
    | Azure subscription             | _select your Azure subscription_                   |
    | Storage account name           | _`synapselabfraud` + unique id (example: `synapselabfraud312535`)_ |

    ![The new linked service is selected.](media/azure-synapse-linked-service.png 'Set properties')

21. In the Set properties menu, in the File path section, enter **synapse** into the **File System** box.  Then, select the **First row as header** option and select **OK**.

    ![The output properties are set.](media/azure-synapse-linked-service-file-path.png 'File path')

22. Enable data flow debugging by toggling the **Data flow debug** option.

    ![The data flow debug option is selected.](media/azure-synapse-data-flow-debug.png 'Data flow debug')

23. Choose the **AutoResolveIntegrationRuntime** integration runtime, select **1 hour** for time to live, and select **OK**. It may take several minutes for setup to complete.

    ![The data flow debug option is selected.](media/azure-synapse-data-flow-ir.png 'Data flow debug')

24. After the debugger starts, select **Publish all**.

    ![The Publish all option is selected.](media/azure-synapse-data-flow-publish-all.png 'Publish all')

25. Select the **Publish** option to save your changes.

    ![The Publish option is selected.](media/azure-synapse-data-flow-publish.png 'Publish')

26. Navigate to the **Integrate** hub.

    ![Integrate hub.](media/integrate-hub.png "Integrate hub")

27. Select **+** and then choose **Pipeline** to create a new pipeline.

    ![The new Pipeline option is selected.](media/azure-synapse-pipeline.png 'Pipeline')

28. Set the name of the pipeline to **PipelineCreditCard**.

    ![The new Pipeline has a name.](media/azure-synapse-pipelinecreditcard.png 'PipelineCreditCard')

29. Drill down into the **Move & transform** menu and bring a **Data flow** onto the canvas.

    ![Create a data flow.](media/azure-synapse-pipeline-dataflow.png 'Data flow')

30. In the Adding data flow tab, select **DataflowCreditCard** from the Existing data flow drop-down list.  Then, select **OK** to continue.

    ![The data flow is selected.](media/azure-synapse-pipeline-dataflow-1.png 'Data flow')

31. Select the data flow and then navigate to the **Settings** menu.  In the PolyBase sub-menu, change the Staging linked service to **SynapseLabFraudADLS**, the staging storage folder's container to **synapse** and folder to **creditcard**.

    ![The PolyBase settings are created.](media/azure-synapse-pipeline-dataflow-settings.png 'Settings')

32. Select **Debug** to run the pipeline.

    ![The option to debug the newly-created pipeline is selected.](media/azure-synapse-pipeline-dataflow-debug.png 'Debug')

33. Wait until the Output tab has a **Succeeded** status message. You may need to periodically select the **Refresh** button to view the updated status.

    ![The debug run has succeeded.](media/azure-synapse-pipeline-dataflow-succeeded.png 'Debug Succeeded')

34. Select the **Data** tab from Synapse studio.

    ![The Data option is selected.](media/azure-synapse-data.png 'Data')

35. Navigate to the **synapselabfraud###asws** option and navigate to **synapse (Primary)**.  In the root directory, there will be two files which start with **part-00001-**.  Right-click on the file which is 101.2 MB and choose **Rename**.

    ![The Rename option is selected for the scored credit card data.](media/azure-synapse-rename.png 'Rename')

36. Change the file name to **CreditCardScored.csv** and select **Apply**.

    ![The scored credit card data file has been renamed.](media/azure-synapse-rename-1.png 'Rename file')

37. Right-click on the **CreditCardScored.csv** file and select **Preview**.

    ![The Preview option has been renamed.](media/azure-synapse-preview.png 'Preview')

38. Ensure that the **CreditCardScored.csv** file has the correct shape.

    ![The scored credit card file has been loaded with the appropriate column names.](media/azure-synapse-preview-file.png 'CreditCardScored.csv')

### Task 2:  Create a View

1. Select the **Develop** tab from Synapse studio.

    ![The Develop option is selected.](media/azure-synapse-develop.png 'Develop')

2. From the **+** menu, choose **SQL script** to open a new script.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

3. Ensure that you are connected to the **Built-in** option. Then, enter the following script into the script window and select **Run**.

    ```sql
    CREATE DATABASE synapse
    ```

    ![Create a new database.](media/azure-synapse-on-demand-db.png 'Create a synapse database')

4. Change the name of the script in the properties to **CreateServerlessDB**.

    ![The script is named CreateServerlessDB](media/azure-synapse-createserverlessdb.png "CreateServerlessDB")

5. From the **+** menu, choose **SQL script** to open a new script.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

6. Ensure that you are connected to the **Built-in** option. Then, in the Use database drop-down, select **synapse**.

    ![The synapse database is selected.](media/azure-synapse-synapse-database.png 'Use database synapse')

7. Change the name of the script to **CreateViewCreditCardLonLat**.

    ![The script is named CreateViewCreditCardLonLat.](media/azure-synapse-createview.png 'New script name')

8. Copy and paste the following into the script window. Change the three `synapselabfraud###` references to the primary ADLS Gen2 storage account for the Synapse workspace. Then select **Run** to execute the script.

    ```sql
    CREATE VIEW dbo.CreditCardLonLat AS
    SELECT
            credit.Time,
            city.name,
            city.lon,
            city.lat,
            city.alpha2,
            country.companyen,
            credit.V1,
            credit.V2,
            credit.V3,
            credit.V4,
            credit.V5,
            credit.V6,
            credit.V7,
            credit.V8,
            credit.V9,
            credit.V10,
            credit.V11,
            credit.V12,
            credit.V13,
            credit.V14,
            credit.V15,
            credit.V16,
            credit.V17,
            credit.V18,
            credit.V19,
            credit.V20,
            credit.V21,
            credit.V22,
            credit.V23,
            credit.V24,
            credit.V25,
            credit.V26,
            credit.V27,
            credit.V28,
            credit.Amount,
            credit.Class,
            credit.id
    FROM
    OPENROWSET(
            BULK 'https://synapselabfraud###.blob.core.windows.net/synapse/CreditCardScored.csv',
            FORMAT = 'CSV',
            FIELDTERMINATOR =',',
            FIRSTROW = 2,
            ESCAPECHAR = '\\'
        )
        WITH (
            [Time] float,
            [V1] float,[V2] float,[V3] float,[V4] float,[V5] float,[V6] float,[V7] float,[V8] float,[V9] float,[V10] float,
            [V11] float,[V12] float,[V13] float,[V14] float,[V15] float,[V16] float,[V17] float,[V18] float,[V19] float,[V20] float,
            [V21] float,[V22] float,[V23] float,[V24] float,[V25] float,[V26] float,[V27] float,[V28] float,
            [Amount] float,[Class] int,[id] varchar(20)
        ) AS [credit]
    LEFT JOIN
    OPENROWSET(
            BULK 'https://synapselabfraud###.dfs.core.windows.net/synapse/CityList.csv',
            FORMAT = 'CSV',
            FIELDTERMINATOR =',',
            FIRSTROW = 2,
            ESCAPECHAR = '\\'
        )
        WITH (
            [id] VARCHAR (20) ,
            [name] VARCHAR (100) COLLATE Latin1_General_100_CI_AI_SC_UTF8,
            [state] VARCHAR (10) ,
            [alpha2] VARCHAR (2) ,
            [lon] float,
            [lat] float
        ) AS [city] ON
        credit.id = city.id
    LEFT JOIN
    OPENROWSET(
            BULK 'https://synapselabfraud###.dfs.core.windows.net/synapse/CountryList.csv',
            FORMAT = 'CSV',
            FIELDTERMINATOR =',',
            FIRSTROW = 2,
            ESCAPECHAR = '\\'
        )
        WITH (
            [companyjp] VARCHAR (20) COLLATE Latin1_General_100_CI_AI_SC_UTF8,
            [companyen] VARCHAR (100) COLLATE Latin1_General_100_CI_AI_SC_UTF8,
            [numeric] decimal,
            [alpha3] VARCHAR (3) ,
            [alpha2] VARCHAR (2) ,
            [location] VARCHAR (100) COLLATE Latin1_General_100_CI_AI_SC_UTF8,
            [subvivision] VARCHAR (15) COLLATE Latin1_General_100_CI_AI_SC_UTF8
        ) AS [country] ON
        city.alpha2 = country.alpha2
    ```

### Task 3:  Power BI Fraud Map Report Development

1. Open the RDP file from the Before the Hands-On Lab section and select **Connect** to access the virtual machine.  When prompted for credentials, enter `powerbiuser` for the username and the password you chose.

    ![Connect to a remote host.](media/azure-vm-connect-3.png 'Connect to a remote host')

2. Open a browser in the virtual machine.  In the [Azure portal](https://portal.azure.com), type in "azure synapse analytics" in the top search menu and then select **Azure Synapse Analytics** from the results.

    ![In the Services search result list, Azure Synapse Analytics is selected.](media/azure-create-synapse-search.png 'Azure Synapse Analytics')

3. Select the workspace you created before the hands-on lab.

    ![The Azure Synapse Analytics workspace for the lab is selected.](media/azure-synapse-select.png 'The fraud detection Synapse workspace')

4. Select **Open** underneath **Open Synapse Studio** from the Synapse workspace page.

    ![Launch Synapse Studio is selected.](media/azure-synapse-launch-studio.png 'Launch Synapse Studio')

5. Select the **Develop** tab from Synapse studio.

    ![The Develop option is selected.](media/azure-synapse-develop.png 'Develop')

6. Drill down into the **Power BI** menu and then the **FraudDetection** menu, followed by **Power BI datasets**.  Choose the **+ New Power BI dataset** option.

    ![Create a new Power BI dataset.](media/azure-synapse-new-power-bi-dataset.png 'New Power BI dataset')

7. Hover over the **synapsesql** serverless SQL database and then select **Download .pbids file**.

    ![The option to download a Power BI dataset file is selected.](media/power-bi-pbids.png 'New Power BI dataset')

8. Open the downloaded Power BI dataset file in Power BI Desktop. If you are prompted to sign in to the Power BI dataset, select **Microsoft account** in the left-hand menu, then click the **Sign in** button to enter your Azure credentials.

    ![The Microsoft account tab and sign in button are highlighted.](media/power-bi-serverless-signin.png "SQL Server database")

9. After signing in with your Azure credentials, click **Connect**.

    ![The Connect button is highlighted.](media/power-bi-serverless-signin2.png "SQL Server database")

10. On the Navigator page, select the **CreditCardLonLat** view and then select **Load**.

    ![The credit card longitude and latitude view is selected.](media/power-bi-lonlat-1.png 'Navigator')

11. In the Connection settings modal dialog, select **Import** and then select **OK**.

    ![The Import option is selected.](media/power-bi-new-dataset-6.png 'Connection settings')

12. Select the **lat** column. In the **Column tools** tab, select the drop-down for Summarization and select **Don't summarize**. Select the drop-down for Data category and choose **Latitude**. Repeat this for the **lon** column, setting the Data category to **Longitude** and Summarization to **Don't summarize**.

    ![The lat column has a data category of Latitude.](media/power-bi-new-lonlat-lat.png 'Data category')

13. Select the **ArcGIS Maps for Power BI** visualization and drag it onto the canvas. Expand it to fill the canvas.

    ![The ArcGIS Map visualization is selected.](media/power-bi-new-lonlat-gismap.png "ArcGIS Maps for Power BI Visualization")

    > **Note**: You do not need to sign in to the ArcGIS service.

14. Drag the **Amount** data field into `Size` and `Color`, the **lat** data field into `Latitude`, and the **lon** data field into `Longitude`.

    ![The credit card data fields are added to the map.](media/power-bi-new-lonlat2.png 'Visualizations and Filters')

15. Drag the **Class** data field into the **Filters on this visual** section. In the Filter type drop-down, select **Basic filtering**.

    ![The Class filter has been added to the map visual.](media/power-bi-new-lonlat-3.png 'Filters on this visual')

16. Change the filter on **Class** to include only fraudulent transactions by checking the **1 checkbox**. This report allows you to understand the geographical features of the unauthorized use of credit cards and to implement concrete measures to temporarily restrict the use of credit cards in cities that show the beginnings of a concentration of unauthorized uses of credit cards for large amounts.

    ![The Class filter has been added to the map visual.](media/power-bi-new-lonlat4.png 'Filters on this visual')
