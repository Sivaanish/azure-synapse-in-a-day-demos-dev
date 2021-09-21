## Exercise 1:  Scoring predictions from T-SQL using a pre-trained model

Duration: 45 minutes

You will use masked data, obtained by applying principal component analysis to credit card transaction data, to evaluate which transactions are fraudulent and to analyze trends in elapsed time and fraud amounts.

### Task 1: Dataset Creation

1. In the [Azure portal](https://portal.azure.com), type in "azure synapse analytics" in the top search menu and then select **Azure Synapse Analytics** from the results.

    ![In the Services search result list, Azure Synapse Analytics is selected.](media/azure-create-synapse-search.png 'Azure Synapse Analytics')

2. Select the workspace you created before the hands-on lab, or that is provided by your hosted lab environment.

    ![The Azure Synapse Analytics workspace for the lab is selected.](media/azure-synapse-select.png 'synapselabfraudjdhasws workspace')

3. Select the `synapsesql` dedicated SQL pool on the Overview pane of the Synapse workspace.

    ![The dedicated SQL pool is selected.](media/select-dedicated-sql-pool.png "SQL pools")

4. If the dedicated SQL pool is currently paused, select **Resume**.

    ![The resume button is highlighted.](media/dedicated-sql-pool-resume.png "Resume")

5. When prompted whether you are sure you want to resume the pool, select **Yes**.

6. Select **Overview** in the left-hand menu **after** the SQL pool resumes.

    ![The Overview link is highlighted.](media/synapse-workspace-select-overview.png "Overview")

7. Select **Open** underneath **Open Synapse Studio** from the Synapse workspace page.

    ![Launch Synapse Studio is selected.](media/azure-synapse-launch-studio.png 'Launch Synapse Studio')

8. Select the **Develop** tab from Synapse studio.

    ![The Develop option is selected.](media/azure-synapse-develop.png 'Develop')

9. From the **+** menu, choose **SQL script** to open a new script.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

10. Choose the **synapsesql** connection option and the **synapsesql** database from the database drop-down list.

    ![The synapsesql database is selected.](media/azure-synapse-develop-synapsesql.png 'synapsesql database')

11. Change the name of the script to **CreateSchema**.

    ![The script is named CreateSchema.](media/azure-synapse-script-createschema.png 'CreateSchema')

12. Enter the following code into the script window. Then, select **Run** to execute the code.

    ```sql
    CREATE SCHEMA synapse
    ```

    ![The Run button is highlighted.](media/azure-synapse-create-schema-script-run.png "Run")

13. From the **+** menu, choose **SQL script** to open a new script. Ensure that you are connected to the **synapsesql** SQL pool and the **synapsesql** database.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

14. Change the name of this script to **CreateMasterKey**. Enter the following into the script window, changing `{Password}` to a password you can remember. Then, select **Run** to execute the code.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '{Password}'
    ```

    ![The master key creation script has been run.](media/azure-synapse-script-createmasterkey.png 'Create Master Key')

15. From the **+** menu, choose **SQL script** to open a new script.  Ensure that you are connected to the **synapsesql** SQL pool and the **synapsesql** database.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

16. Change the name of this script to **CreateAzureStorageAccountKey**. Connect to the **synapsesql** dedicated SQL pool, then enter the following into the script window, filling in your storage account name and access key. Select **Run** to execute the code.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL AzureStorageAccountKey
    WITH IDENTITY = '<Your Storage Account>',
    SECRET = '<Your Access Key>';
    ```

    ![The database scoped credential has been created.](media/azure-synapse-script-create-dsc.png 'Create Database Scoped Credential')

    To find the storage account name and access key, navigate to the resource group for your lab and open the provisioned Azure storage account. Select **Access keys** in the left-hand menu. Here you can copy the **Storage account name** and paste it in place of `<Your Storage Account>` in the SQL script, and copy the **Key** to replace `<Your Access Key>` in the SQL script.

    ![The storage account name and key are highlighted.](media/storage-account-keys.png "Access keys")

17. From the **+** menu, choose **SQL script** to open a new script.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

18. Change the name of this script to **CreateCSVDataSource**. Enter the following into the script window, filling in your storage account name in place of `<Your Storage Account>`. Ensure that you are connected to the **synapsesql** SQL pool and the **synapsesql** database. Then, select **Run** to execute the code.

    ```sql
    CREATE EXTERNAL DATA SOURCE CSVDataSource WITH
    (
        TYPE = HADOOP,
        LOCATION = 'wasbs://synapse@<Your Storage Account>.blob.core.windows.net',
        CREDENTIAL = AzureStorageAccountKey
    );
    ```

    ![The external data source has been created.](media/azure-synapse-create-data-source.png 'Create External Data Source')

19. From the **+** menu, choose **SQL script** to open a new script.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

20. Change the name of this script to **CreateCSVFileFormat**. Enter the following into the script window to create two external file formats. One is for reading the source CSV files and the other is to read the machine learning (ML) model file. Ensure that you are connected to the **synapsesql** SQL pool and the **synapsesql** database. Then, select **Run** to execute the code.

    ```sql
    CREATE EXTERNAL FILE FORMAT CSVFileFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS (
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '"',
            FIRST_ROW = 2,
            USE_TYPE_DEFAULT=TRUE
        )
    );
    GO

    CREATE EXTERNAL FILE FORMAT csv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS (
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );
    GO
    ```

    ![The external file format has been created.](media/azure-synapse-script-create-fileformat.png 'Create External File Format')

21. From the **+** menu, choose **SQL script** to open a new script.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

22. Change the name of this script to **CreateExternalCreditCard**. Enter the following into the script window to create an external table for the credit card CSV data and an external table for the ML model. Ensure that you are connected to the **synapsesql** SQL pool and the **synapsesql** database. Then, select **Run** to execute the code.

    ```sql
    CREATE EXTERNAL TABLE synapse.exCreditCard
    (
            [Time] float,
            [V1] float,[V2] float,[V3] float,[V4] float,[V5] float,[V6] float,[V7] float,[V8] float,[V9] float,[V10] float,
            [V11] float,[V12] float,[V13] float,[V14] float,[V15] float,[V16] float,[V17] float,[V18] float,[V19] float,[V20] float,
            [V21] float,[V22] float,[V23] float,[V24] float,[V25] float,[V26] float,[V27] float,[V28] float,
            [Amount] float,[Class] bigint,[id] bigint
    )
    WITH
    (
            LOCATION = 'CreditCard.csv',
            DATA_SOURCE = [CSVDataSource],
            FILE_FORMAT = [CSVFileFormat]
    );
    GO

    CREATE EXTERNAL TABLE synapse.[MLModelExt]
    (
    [Model] [varbinary](max) NULL
    )
    WITH
    (
        LOCATION='credit_card_model.onnx.hex' ,
        DATA_SOURCE = [CSVDataSource] ,
        FILE_FORMAT = csv ,
        REJECT_TYPE = VALUE ,
        REJECT_VALUE = 0
    );
    GO
    ```

    ![The external table has been created.](media/azure-synapse-script-create-externalcc.png 'Create External Table')

23. Select the **Data** hub in the left-hand menu.

    ![Data hub.](media/data-hub.png "Data hub")

24. Expand **Databases** under the Workspace tab, expand **synapsesql**, then expand **External tables**. Right-click on the **synapse.exCreditCard** table and choose **New SQL script** and then **Select TOP 100 rows**.

    ![Select the top 100 rows is selected.](media/azure-synapse-script-select-top100.png 'Select TOP 100 rows')

25. Select the **Properties** icon to display the menu. Rename the name to **SelectExternalCreditCard**. Then, select **Publish all**.

    ![The publish all option is selected.](media/azure-synapse-publish-all-2.png 'Publish all')

26. Select the **Publish** option to save these scripts.

    ![The Publish option is selected.](media/azure-synapse-publish-2.png 'Publish')

### Task 2:  Query Development

1. Navigate to the **Develop** hub.

    ![Develop hub.](media/develop-hub.png "Develop hub")

2. From the **+** menu, choose **SQL script** to open a new script.

    ![Create a new SQL script.](media/azure-synapse-new-script.png 'SQL script')

3. Change the name of this script to **SelectIntoCreditCard**. Enter the following into the script window to create an external table for the credit card CSV data and an external table for the ML model. Ensure that you are connected to the **synapsesql** SQL pool and the **synapsesql** database. Then, select **Run** to execute the script and let it insert data into a new `synapse.CreditCard` table.

    ```sql
    DECLARE @modelexample varbinary(max) = (SELECT [Model] FROM synapse.[MLModelExt]);

    SELECT
    d.*, p.*
    INTO synapse.CreditCard
    FROM PREDICT(MODEL = @modelexample,
        DATA = synapse.exCreditCard AS d,
        RUNTIME = ONNX) WITH (output_label bigint) AS p;
    ```

    ![The scored credit card prediction table has been created.](media/azure-synapse-script-create-creditcard.png 'Run predictions')

4. In the **Tables** folder for **synapsesql**, select the ellipsis (...) and choose **Refresh** to see the `synapse.CreditCard` table.

    ![The list of tables is refreshed.](media/azure-synapse-refresh-tables.png 'Refresh')

5. In the **synapse.CreditCard** table entry, select the ellipsis (...) and choose **New SQL Script** and then **Select TOP 100 rows** to open a new script pre-populated with a SQL query.

    ![The option to select the top 100 rows in the credit card predictions table is selected.](media/azure-synapse-creditcard-select.png 'Select TOP 100 rows')

    The `Output_label` field shows the prediction from the model.

    ![The output label field is highlighted.](media/azure-synapse-creditcard-select-output.png "Query results")

### Task 3:  Power BI Report Development

1. Open the RDP file from the Before the Hands-On Lab section and select **Connect** to access the virtual machine.  When prompted for credentials, enter `powerbiuser` for the username and the password you chose, or which were provided to you in your hosted lab environment details.

    ![Connect to a remote host.](media/azure-vm-connect-3.png 'Connect to a remote host')

2. Open a browser in the virtual machine.  In the [Azure portal](https://portal.azure.com), type in "azure synapse analytics" in the top search menu and then select **Azure Synapse Analytics** from the results.

    ![In the Services search result list, Azure Synapse Analytics is selected.](media/azure-create-synapse-search.png 'Azure Synapse Analytics')

3. Select the workspace you created before the hands-on lab.

    ![The Azure Synapse Analytics workspace for the lab is selected.](media/azure-synapse-select.png 'synapse-lab-fraud-detection workspace')

4. Select **Open** underneath **Open Synapse Studio** from the Synapse workspace page.

    ![Launch Synapse Studio is selected.](media/azure-synapse-launch-studio.png 'Launch Synapse Studio')

5. Select the **Develop** hub.

    ![Develop hub.](media/develop-hub.png "Develop hub")

6. Drill down into **Power BI** and then **FraudDetectionWorkspace**. Select **Power BI datasets**.

    ![The Open option is selected.](media/azure-synapse-power-bi-dataset-open.png 'Open')

    If you do not see the **Power BI** section under Develop, select **Refresh** in the upper-right corner of Synapse Studio to refresh the UI.

    ![The refresh button is highlighted.](media/synapse-studio-refresh.png "Refresh")

7. Select **+ New Power BI dataset**.

    ![The New Power BI dataset option is selected.](media/azure-synapse-power-bi-dataset-new.png 'New Power BI dataset')

8. Select **Start** to begin the process.

    ![The option to start is selected.](media/power-bi-new-dataset-1.png 'Start')

9. Select the **synapsesql** SQL pool to use as a data source and then select **Continue**.

    ![The synapse SQL pool is selected.](media/power-bi-new-dataset-2.png 'Synapse SQL Pool')

10. Select **Download** to download the Power BI dataset file.  After download completes, select **Continue**.

    ![The Power BI dataset file is downloaded.](media/power-bi-new-dataset-3.png 'Download pbids file')

11. Open the downloaded Power BI dataset file in Power BI Desktop.  When prompted to enter a username and password, select **Database** and enter the Synapse username and password you created before the hands-on lab, or which was provided to you as part of a hosted environment. The username will be **asa.sql.admin**. Then select **Connect** to connect to the SQL pool.

    ![The credentials for the Synapse admin user are entered.](media/power-bi-new-dataset-4.png 'Connect to SQL Pool')

12. On the Navigator page, select the **synapse.CreditCard** table and then select **Load**.

    ![The credit card table are selected.](media/power-bi-new-dataset-5.png 'Navigator')

13. In the Connection settings modal dialog, select **Import** and then select **OK**.

    ![The Import option is selected.](media/power-bi-new-dataset-6.png 'Connection settings')

14. Once the table is loaded, select the **Data** tab and then select the **Time** column.  In the Format section, select the drop-down list and choose **Decimal number**.

    ![The Time column is changed to a decimal data type.](media/power-bi-new-dataset-7.png 'Decimal number')

15. Right-click the Time column and choose **Sort ascending**.

    ![The Sort ascending option is selected.](media/power-bi-new-dataset-8.png 'Sort ascending')

16. Choose the **Report** view and then select the **Clustered column chart** option.  Select and drag the **Time** attribute into the **Axis** field to make time the X axis.  From there, select and drag the **Amount** attribute into the **Values** field to make amount the Y axis.

    ![The amount by time report has been created.](media/power-bi-amount-by-time.png 'Amount by Time')

17. Expand out the **Filters** pane. Select and drag the **Class** attribute into the **Filters on this visual** menu.  Change the filter type to **Basic filtering** and select **1** to filter down to fraudulent transactions.

    ![The amount by time report filter has been added.](media/power-bi-amount-by-time-filter.png 'Amount by Time filter')

18. Save the file as **FraudDetectionReport**.

    ![The Power BI report is saved.](media/power-bi-fraud-detection-report.png 'FraudDetectionReport')

19. In the **File** menu, select **Publish** and then **Publish to Power BI**.

    ![The Publish to Power BI option is selected.](media/power-bi-publish.png 'Publish to Power BI')

20. Sign into your Power BI workspace.

    ![The option to sign into Power BI is selected.](media/power-bi-login.png 'Sign in')

21. Select the **FraudDetection** workspace and then choose **Select**.

    ![The FraudDetection workspace is selected.](media/power-bi-select-destination.png 'Select a destination')

22. After the Power BI report deploys, return to Azure Synapse Analytics Studio and select **Continue** and then **Close and refresh**.  Select the **Power BI reports** menu and then the **FraudDetectionReport** to review the published report.

    ![The fraud detection report is now available.](media/azure-synapse-fraud-detection-report.png 'Fraud Detection Report')
