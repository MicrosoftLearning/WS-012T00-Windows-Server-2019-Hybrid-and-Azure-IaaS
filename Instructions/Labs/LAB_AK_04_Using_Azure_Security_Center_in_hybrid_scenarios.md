---
lab:
    title: 'Lab: Using Azure Security Center in hybrid scenarios'
    type: 'Answer Key'
    module: 'Module 4: Implementing Security Solutions in Hybrid Scenarios'
---

# Lab: Using Azure Security Center in hybrid scenarios

## Exercise 1: Provisioning Azure VMs running Windows Server 2019

### Task 1: Start Azure Cloud Shell

1. Sign in to **SEA-CL1** as **Contoso\\\\Administrator** with password **Pa55w.rd**.
1. Open Microsoft Edge, and then browse to the [Azure portal](https://portal.azure.com).
1. Sign in by using the credentials that you created for this course.
1. On the Azure portal, select **Cloud Shell**.
1. Select **PowerShell**.
1. When prompted, verify that your subscription is chosen, and then select **Create storage**.

### Task 2: Create an Azure VM by using Azure Resource Manager templates

**Upload the Resource Manager templates**

1. In Cloud Shell, select **Upload/Download files**, and then select **Upload**.
1. Browse to the Desktop folder and then **Allfiles\Labfiles\Mod04** folder.
1. Select the **M04-lab-rg_template.json** file, and then select **Open**.
1. Repeat steps 1 through 3 for the following files:
    - **M04-lab-rg_template.parameters.json**
    - **M04-lab-sub_template.json**

**Create a resource group**

1. In Cloud Shell, enter the following command, replacing *\<region>* with an Azure region that's close to you:

    ```powershell
    New-AzSubscriptionDeployment -Location '<region>' -Name ws2019-m04deployment -TemplateFile ./M04-lab-sub_template.json -rgLocation '<region>' -rgName 'm04-rg'
    ```

**Create a Windows Server virtual machine (VM)**

1. In Cloud Shell, enter the following command:

    ```powershell
    New-AzResourceGroupDeployment -Name windows-m04rgDeployment -ResourceGroupName m04-rg -TemplateFile ./M04-lab-rg_template.json -TemplateParameterFile ./M04-lab-rg_template.parameters.json
    ```

1. Close Cloud Shell.
1. Wait for deployment to complete.

> **Note:** You can close Cloud Shell before deployment is complete.

## Exercise 2: Configuring Azure Security Center

### Task 1: Make Security Center available and upgrade to the Standard pricing tier

1. Switch to Microsoft Edge, and then verify that you're signed in to the Azure portal.
1. In the search box, enter **Security Center**, and then select **Security Center** from the results.
1. On the **Security Center** menu, select **Getting started**.
1. On the **Upgrade** tab of the **Getting started** pane, for **Enable standard tier on 1 subscriptions**, select your subscription.
1. Select **Upgrade**.
1. Select **Continue without installing agents**.

### Task 2: Turn on automatic provisioning of the Log Analytics agent

1. Browse back to Security Center.
1. In Security Center, select **Pricing & settings**, select your subscription, and then select **Data Collection**.
1. In the **Auto Provisioning** section, select **On**.
1. In the **Workspace configuration** section, verify that **Use workspace(s) created by Security Center (default)** is selected, and then select **Save**.
1. Refresh the page, and then browse back to the **Data Collection** page.
1. In the **Windows security events** section, select **All Events**, and then select **Save** again.

> **Note:** It might take up to 30 minutes for the **Log Analytics** workspace to be created after selecting **Save** in step 4. You might need to refresh the page several times before the option to select **All Events** is available. Continue only after you're able to select **All events** and save the setting.

### Task 3: Review the features and capabilities that apply to hybrid scenarios

1. In the **RESOURCE SECURITY HYGIENE** section of Security Center, select **Compute & apps**.
1. Review the **Recommendation** list.

   > **Note:** that the **Failed Resources** box indicates the type of resource to which the recommendation applies and lists how many resources file the recommendation.

1. Select **VMs and Servers**, and then select the **ws2019-m04-vm0** VM.
1. Note the details of the VM's security health.
1. Close the **ws2019-m04-vm0** pane.
1. In the **POLICY & COMPLIANCE** section, select **Regulatory compliance**.
1. Select **Azure CIS 1.1.0**, expand the **Virtual Machines** controls, and then review the other items on the **Security Center** menu.

## Exercise 3: Onboarding on-premises Windows Server 2019 into Azure Security Center

### Task 1: Download and install the Log Analytics agent

1. On **SEA-CL1**, switch to Microsoft Edge, and then verify that you're signed in to the Azure portal.
1. In the search box, enter **Log Analytics**, and then select **Log Analytics workspaces** from the results.
1. Select the listed Log Analytics workspace. There should only be one, and the name will start with "DefaultWorkspace".
1. Select **Agents management**.
1. Copy and save the **WORKSPACE ID** and the **PRIMARY KEY**.
    > **Note:** You can save these values in Notepad or make a note of them.
1. Select the **Download Windows Agent (64 bit)** link.
1. In the download status bar, on the context menu for **MMASetup-AMD64.exe**, select **Show in folder**.
1. Copy the file to c:\labfiles\mod04. Create the folder if needed.
1. Select **Start**, and then open Windows PowerShell.
1. In PowerShell, browse to c:\labfiles\Mod04.
1. Enter the following command:

    ```powershell
    .\MMASetup-amd64.exe /c /t:c:\labfiles\mod04
    ```

1. Open a new tab in Microsoft Edge, open **Windows Admin Center**, and then sign in as **Contoso\\\\Administrator** with password **Pa55w.rd**.
1. Select **Add**.
1. Under **Windows Server**, select **Add**.
1. Under **Server name**, enter ```sea-svr1.contoso.com```, and then select **Add**.
1. In **Windows Admin Center**, select **SEA-SVR1**, and then sign in as **Contoso\\\\Administrator** with password **Pa55w.rd**.
1. Select **PowerShell**, and then enter the password **Pa55w.rd**.
1. Enter the following commands:

    ```powershell
    mkdir c:\labfiles\mod04
    copy \\SEA-CL1\c$\labfiles\mod04\*.* c:\labfiles\mod04\
    ```

1. Browse to **c:\\labfiles\\mod04**.
1. Enter the following command:

    ```powershell
    .\setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<workspaceID>" OPINSIGHTS_WORKSPACE_KEY="<primarykey>" AcceptEndUserLicenseAgreement=1
    ```

    where *\<workspaceID>* is the WORKSPACE ID that you copied earlier and *\<primarykey>* is the PRIMARY KEY that you copied earlier.

1. Wait for the installation to complete.

## Exercise 4: Verifying the hybrid capabilities of Azure Security Center

### Task 1: Validate the Security Center capabilities for Azure VMs

1. Open Microsoft Edge, and then browse to the [Azure portal](https://portal.azure.com).

1. Sign in by using the credentials that you created for this course.

1. In the search box, enter **Virtual Machines**, and then select **Virtual Machines** from the results.

1. Select the **ws2019-m04-vm0** VM.

1. Select **Connect**, select **RDP**, and then select **Download RDP File**.

1. In the download status bar, select the context menu for the **ws2019-mo4-vm0.rdp** file, and then select **Open**.

1. If prompted, select **Connect**.

1. Enter the username **Student** with password **Pa55w.rd1234**.

1. In the VM's Remote Desktop session, select **Start**, and then select **Windows PowerShell**.

1. Enter the following commands:

    ```powershell
    mkdir c:\temp
    powershell -nop -exec bypass -EncodedCommand "cABvAHcAZQByAHMAaABlAGwAbAAgAC0AYwBvAG0AbQBhAG4AZAAgACIAJgAgAHsAIABpAHcAcgAgAGgAdAB0AHAAcwA6AC8ALwBkAG8AdwBuAGwAbwBhAGQALgBzAHkAcwBpAG4AdABlAHIAbgBhAGwAcwAuAGMAbwBtAC8AZgBpAGwAZQBzAC8AUwB5AHMAbQBvAG4ALgB6AGkAcAAgAC0ATwB1AHQARgBpAGwAZQAgAGMAOgBcAHQAZQBtAHAAXABzAHYAYwBoAG8AcwB0AC4AZQB4AGUAIAB9ACIA"
    ```

1. In Microsoft Edge, switch to the Azure portal tab.

1. In the search box, enter **Security Center**, and then select **Security Center** from the results.

1. On the **Overview** page, review the **Threat protection** section. You should have one new alert. If not, wait a few minutes.

1. In the **THREAT PROTECTION** section of the **Security Center** menu, select **Security alerts**. There should be one alert.

1. In the **RESOURCE SECURITY HYGIENE** section of the **Security Center** menu, select **Compute & apps**.

1. Select **VMs and Servers**, and then select the **ws2019-mo4-vm0** VM.

1. Review the recommendations.

### Task 2: Validate the Security Center capabilities for on-premises VMs

1. In Microsoft Edge, switch to the **Windows Admin Center** tab.
1. Verify that you're signed in to **SEA-SVR1** as **Contoso\\\\Administrator**, and then select **PowerShell**.
1. Enter the following commands:

    ```powershell
    mkdir c:\temp
    powershell -nop -exec bypass -EncodedCommand "cABvAHcAZQByAHMAaABlAGwAbAAgAC0AYwBvAG0AbQBhAG4AZAAgACIAJgAgAHsAIABpAHcAcgAgAGgAdAB0AHAAcwA6AC8ALwBkAG8AdwBuAGwAbwBhAGQALgBzAHkAcwBpAG4AdABlAHIAbgBhAGwAcwAuAGMAbwBtAC8AZgBpAGwAZQBzAC8AUwB5AHMAbQBvAG4ALgB6AGkAcAAgAC0ATwB1AHQARgBpAGwAZQAgAGMAOgBcAHQAZQBtAHAAXABzAHYAYwBoAG8AcwB0AC4AZQB4AGUAIAB9ACIA"
    ```

1. Open Microsoft Edge, and then browse to the [Azure portal](https://portal.azure.com).
1. Sign in by using the credentials that you created for this course.
1. In the search box, enter **Security Center**, and then select **Security Center** from the results.
1. On the **Overview** page, review the **Threat protection** section. You should have one new alert for **SEA-SVR1**. If not, wait a few minutes.
1. In the **THREAT PROTECTION** section of the **Security Center** menu, select **Security alerts**. There should be a new alert for **SEA-SVR1**.
1. In the **RESOURCE SECURITY HYGIENE** section of the **Security Center** menu, select **Compute & apps**, and then select **SEA-SVR1**.
1. Review the recommendations for **SEA-SVR1**.

> **Note:** You might need to wait several minutes for **SEA-SVR1** to be listed.

## Exercise 5: Deprovisioning the Azure environment

### Task 1: Start a PowerShell session in Cloud Shell

1. On **SEA-CL1**, in Microsoft Edge, switch to the Azure portal.
1. Select **Cloud Shell**.

### Task 2: Identify and remove all Azure resources that were provisioned in the lab

- In Cloud Shell, enter the following command to find and remove all resource groups:

    ```powershell
    Get-AzResourceGroup | Remove-AzResourceGroup -Force
    ```

### Task 3: Prepare for the next module

- End the lab.
