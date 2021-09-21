## Lab pre-requisite: Create a Power BI Workspace

1. In a new tab or window, navigate to the Power BI website, [https://powerbi.microsoft.com/](https://powerbi.microsoft.com/).  Select **Sign in** and sign in.

    ![The Sign in option is selected.](media/power-bi-sign-in.png 'Sign in')

2. Select the **Workspaces** menu and then choose **Create workspace**.

    ![The Create a workspace option is selected.](media/power-bi-create-workspace.png 'Create a workspace')

3. If you are prompted to upgrade to Power BI Pro, select **Try free**. Select **Got it** to confirm the pro subscription.

4. In the Create a workspace menu, enter **FraudDetection** as the name and select **Save**. If the name "FraudDetection" is not available, append a set of unique characters to the name, such as the `UniqueId` value if you are using a hosted lab environment, or your initials.

    ![The FraudDetection workspace is saved.](media/power-bi-create-workspace-1.png 'FraudDetection workspace')

5. In the [Azure portal](https://portal.azure.com), type in "azure synapse analytics" in the top search menu and then select **Azure Synapse Analytics** from the results.

    ![In the Services search result list, Azure Synapse Analytics is selected.](media/azure-create-synapse-search.png 'Azure Synapse Analytics')

6. Select the workspace you created before the hands-on lab, or that is provided by your hosted lab environment.

    ![The Azure Synapse Analytics workspace for the lab is selected.](media/azure-synapse-select.png 'synapselabfraudjdhasws workspace')

7. Select **Open** underneath **Open Synapse Studio** from the Synapse workspace page.

    ![Launch Synapse Studio is selected.](media/azure-synapse-launch-studio.png 'Launch Synapse Studio')

8. On the home page, select the **Visualize** option.

    ![The Visualize option is selected.](media/azure-synapse-visualize.png 'Visualize')

9. In the **Connect to Power BI** tab, complete the following and then select **Connect** to create a new Power BI connection.

    | Field                          | Value                                              |
    | ------------------------------ | ------------------------------------------         |
    | Name                           | _`FraudDetectionWorkspace`_                        |
    | Tenant                         | _select your Power BI tenant_                      |
    | Workspace name                 | _select the `FraudDetection` workspace_            |

    ![In the Connect to Power BI tab, form field entries are filled in.](media/azure-synapse-connect-power-bi.png 'Connect to Power BI')
