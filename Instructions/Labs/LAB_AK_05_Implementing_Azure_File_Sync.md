---
lab:
    title: 'Lab: Implementing Azure File Sync'
    type: 'Answer Key'
    module: 'Module 5: Implementing File Services in Hybrid Scenarios'
---

# Lab: Implementing Azure File Sync

## Exercise 1: Implementing Distributed File System (DFS) Replication in your on-premises environment

### Task 1: Deploy DFS

1. Sign in to **SEA-CL1** as **Contoso\\Administrator** and use **Pa55w.rd** as the password.
2. On the taskbar, select **File Explorer**. If in the Lab Setup the **Allfiles** folder was copied to the **Desktop**, then open the **Allfiles** folder, and copy the **Labfiles** folder to **C:**.  Then share the **Labfiles** folder to **Everyone** with **Read** access.
3. In File Explorer, browse to the C:\Labfiles\Mod05 folder.
4. In File Explorer, in the **details** pane, right-click or access the context menu for **M05_DeployDFS.ps1**, and then select **Run with PowerShell**.

### Task 2: Test DFS deployment

1. On **SEA-CL1**, select **Start**, enter **DFS**, and then select **DFS Management**.
2. In **DFS Management**, in the **navigation** pane, right-click or access the context menu for **Namespaces**, and then select **Add Namespaces to Display**.
3. In the **Add Namespaces to Display** dialog box, ensure that ```\\Contoso.com\Root``` is selected in **Namespaces** section, and then select **OK**.
4. In the **navigation** pane, right-click or access the context menu for **Replication**, and then select **Add Replication Groups to Display**.
5. In **Add Replication Groups to Display** dialog box, in **Replication Groups** section, select **Branch1**, and then select **OK**.
6. In the **navigation** pane, expand the ```\\Contoso.com\Root``` namespace, and then select the **Data** folder.
7. In the **details** pane, verify that the **Data** folder has two referrals, on **LON-SVR1** and **SEA-SVR1**.
8. In the **navigation** pane, select **Branch1**.
9. In the **details** pane, verify that the **D:\\Data** folder on **LON-SVR1** and on **SEA-SVR1** are members of the **Branch1** replication group.

    >**Note:** DFS Replication replicates the content between the **D:\\Data** folders on **LON-SR1** and **SEA-SVR1**.

10. Open two instances of File Explorer. In the first File Explorer instance, connect to **\\\\LON-SVR1\\Data**, and then in the second File Explorer instance, connect to **\\\\SEA-SVR1\\Data**.

    >**Note:** Wait until the files are replicated and both the File Explorer windows record the same content.

11. Create a new file with your name in **\\\\LON-SVR1\\Data**.
12. Verify that the file with your name replicates to **\\\\SEA-SVR1\\Data** after a few seconds. This confirms that DFS Replication is working.

## Exercise 2: Creating and configuring a sync group

### Task 1: Create an Azure file share

1. On **SEA-CL1**, select **Microsoft Edge** on the taskbar.
2. In Microsoft Edge, open the Azure portal, and then authenticate with your Azure credentials.
3. In the Azure portal, select **Storage accounts**, and then select **Add**.
4. Create a storage account by using following settings:

    - Resource group: Select **Create new**, enter **RG1** as resource group name, and then select **OK**.
    - Storage account name: All letters in the name must be lowercase, and the name must be unique. For example, you can specify the storage account name in the following format: \<*YourLowercaseInitials*>*DDMMYY*; for example, **dt150620** if your name is Devon Torres and you're creating a storage account on June 15, 2020.
    If that name is already taken, add another character to the name until the name is available. Accept the default values for all other settings, select **Review** **create**, and then select **Create**.

    >**Note:** Use the same region for deploying all resources in this lab.

5. After the storage account is created, select **Go to resource**.
6. On the **storage account** blade, select **File shares**, and then select **File share**.
7. On the **New file share** tab, enter **share1** in the **Name** text box, and then select **Create**.

### Task 2: Use an Azure file share

1. On **SEA-CL1**, in the Azure portal, in the **details** pane, select **share1**.
2. In the **details** pane, select **Upload**.
3. On the Upload files tab, browse to C:\Labfiles\Mod05\File1.txt, select Upload, and when the upload is complete, close the Upload files tab.
4. On the **share1** blade, select **Snapshots**, select **Add snapshot**, and then select **OK**.
5. On the **share1** blade, select **Overview**, select **Connect**, use the **Copy to clipboard** button to copy the script, and then close the **Connect** tab.
6. Right-click or access the context menu for **Start**, and then select **Windows PowerShell**.
7. Right-click or access the context menu for the **Windows PowerShell** window to paste the text from the Clipboard. Select Enter to continue the script.

    >**Note:** The script mounted the Azure file share to drive letter **Z**.

8. On the taskbar, right-click or access the context menu for **File Explorer**, select **File Explorer**, and then in the **navigation** pane, select **share1**.
9. Verify that you can observe **File1.txt** in the **details** pane. This is the file that you uploaded to the Azure file share.
10. Double-click or select **File1.txt**, and then select Enter. Change the file by entering your name, close Notepad, and then save the changes.
11. Right-click or access the context menu for **File1**, select **Properties**, and then select the **Previous Versions** tab.
12. Verify that one previous file version is available. Select that version (**File1.txt**), select **Restore** twice, and then select **OK** twice.
13. Double-click or select **File1.txt**, select Enter, and then confirm that it doesn't include your name. This is because you created the snapshot before you modified the file.
14. Close **Notepad**.

### Task 3: Deploy Storage Sync Service and a File Sync group

1. On **SEA-CL1**, in the Azure portal, select **Create a resource** on the **navigation** blade, enter **Azure File Sync** in the text box, select **Azure File Sync**, and then select **Create**.
2. In the **Deploy File Sync** pane, for **Resource Group**, select **RG1**. Enter **FileSync1** as the Storage Sync Service name, ensure that the same region is selected as was used for the storage account, and then select **Review and create** and **Create**.
3. After Storage Sync Service deploys, select **Go to resource**.
4. On the **Storage Sync Service** blade, select **Sync groups**, and then select **Sync group** to create a new File Sync group.
5. In the **Sync group** pane, enter **Sync1** in the **Sync group name** text box.
6. Select **Select storage account**, and then select the storage account that you created. If you can't find the storage account, it was probably deployed to a different Azure region. You must deploy a new Azure storage account in such a case.
7. In the **Azure File Share** drop-down list, select **share1**, and then select **Create**.
8. On the **Storage Sync Service** blade, select **Registered servers**, and then confirm that no server is currently registered.

## Exercise 3: Replacing DFS Replication with File Sync-based replication

### Task 1: Add SEA-SVR1 as a server endpoint

1. On **SEA-CL1**, in the Azure portal, download the File Sync agent for Windows Server 2019 (**StorageSyncAgent_WS2019.msi**), and then save it to the **C:\\Labfiles** folder. After the download is complete, close the Microsoft Edge tab that opened for the download.

    >**Note:** If you downloaded the file to the default location, you need to copy the file to the **C:\\Labfiles** folder.

2. In File Explorer, browse to the **C:\\Labfiles\\Mod05** folder, and then double-click or select **Install-FileSyncServerCore.ps1**.
3. In Notepad, update the value of the *$RG_name* variable with the name of the resource group to which you deployed **FileSync1** (replace everything inside \<>, including \< and >, but leave the apostrophe at the beginning and at the end), and then save the file.
4. Right-click or access the context menu for **Start**, select **Windows PowerShell**, enter **cd C:\\Labfiles\\Mod05**, and then select Enter.
5. Enter **.\\Install-FileSyncServerCore.ps1**, and then select Enter.
6. Wait while the script runs&mdash;it takes some time. When you get the **WARNING** output, copy the nine-character code in the warning output to the Clipboard.
7. Open a new tab in Microsoft Edge by selecting **+**, and then browse to **https://microsoft.com/devicelogin**.
8. In Microsoft Edge, paste the code in the **Enter code** dialog box, sign in with your Azure credentials, and then close the Microsoft Edge tab that you opened in the previous step.
9. When the script finishes, refresh the list of registered servers in the Azure portal by selecting **Refresh**.
10. Confirm that the ```SEA-SVR1.Contoso.com``` server is now registered in the **FileSync1** Storage Sync Service.
11. In File Explorer, open **\\\\SEA-SVR1\\Data**, and then verify that the folder doesn't contain **File1.txt**.
12. In the Azure portal, on the **Storage Sync Service** blade, select **Sync Groups**, select **Sync1**, and then select **Add server endpoint**.
13. On the **Add server endpoint** tab, select ```SEA-SVR1.Contoso.com``` in the **Registered servers** list.
14. In the **Path** text box, enter **D:\\Data**, and then select **Create**.
15. In the File Explorer that has the **\\\\SEA-SVR1\\Data** folder open, verify that **File1.txt** is present.

   >**Note:** You uploaded **File1.txt** to the Azure file share, from where it was synced to **SEA-SVR1** by File Sync.

### Task 2: Register LON-SVR1 with File Sync

1. On **SEA-CL1**, in File Explorer, double-click or select **Install-FileSyncServerCore.ps1** in the **C:\\Labfiles\\Mod05** folder, and then select Enter.
2. In Notepad, for the *$Server* variable, replace **SEA-SVR1** with **LON-SVR1**, and then save the file.
3. On the taskbar, select **Windows PowerShell**, enter **.\\Install-FileSyncServerCore.ps1**, and then select Enter.
4. Wait while the script runs&mdash;it takes some time. When you get the **WARNING** output, copy the nine-character code in the warning output to the Clipboard.
5. In Microsoft Edge, open a new tab by selecting **+**, and then browse to **https://microsoft.com/devicelogin**.
6. In Microsoft Edge, paste the code in the **Enter code** dialog box, sign in with your Azure credentials, and then close the Microsoft Edge tab that you opened in the previous step.
7. When the script finishes, in the Azure portal, select **Registered servers** on the **FileSync1** blade.
8. Confirm that ```SEA-SVR1.Contoso.com``` and ```LON-SVR1.Contoso.com``` are registered with the **FileSync1** Storage Sync Service.

### Task 3: Remove DFS Replication and add LON-SVR1 as a server endpoint

1. On **SEA-CL1**, select **DFS Management** on the taskbar.
2. In **DFS Management**, in the **navigation** pane, right-click or access the context menu for **Branch1**, select **Delete**, select the **Yes, delete the replication group, stop replicating all associated replicated folders, and delete all members of the replication group** option, and then select **OK**.
3. In the Microsoft Edge tab that has the Azure portal open, select **Sync Groups**, select **Sync1**, and then select **Add server endpoint**.
4. On the **Add server endpoint** tab, select ```LON-SVR1.Contoso.com``` in the **Registered servers** list, enter **D:\\Data** in the **Path** text box, and then select **Create**.

## Exercise 4: Verifying replication and enabling cloud tiering

### Task 1: Verify File Sync

1. On **SEA-CL1**, use two instances of File Explorer. In the first File Explorer instance, connect to **\\\\LON-SVR1\\Data**, and then in the second File Explorer instance, connect to **\\\\SEA-SVR1\\Data**.
2. Create a file with your last name in the **\\\\LON-SVR1\\Data** folder.
3. Verify that after some time, the file with your last name also appears in the **\\\\SEA-SVR1\\Data** folder.

    >**Note** You removed DFS Replication in the previous exercise. File Sync replicated the file with your name.

### Task 2: Enable cloud tiering

1. On **SEA-CL1**, in the Azure portal, browse to the **Sync1** sync group in the **FileSync1** Storage Sync Service.
2. In the Azure portal, select ```LON-SVR1.Contoso.com``` in the **Server endpoint** section.
3. On the **Server Endpoint Properties** tab, select **Enabled** in the **Cloud Tiering** section.
4. In the **Always preserve the specified percentage of free space on the volume** text box, enter **90**, select **Enabled** for the **date policy**. In the **Only cache files that were accessed or modified within the specified number of days** text box, enter **14**, and then select **Save**.

    >**Note:** After some time, files on **LON-SVR1** would automatically tier. You can trigger tiering immediately by running the **Invoke-StorageSyncCloudTiering** cmdlet.

5. In the File Explorer instance that's connected to the **\\\\LON-SVR1\\Data** folder, add the **Attributes** column in the **details** pane by right-clicking or accessing the context menu for the **Title** column in **details** pane; for example, on **Name**, select **More**, select the **Attributes** check box, and then select **OK**.
6. Drag the **Attributes** column to be next to the **Name** column, and then note the file dates and their attributes.
7. Verify the attributes of the files. Additionally, verify the size on the disk for the **Windows Server 2016 Hybrid Cloud.pdf** file by reviewing its properties.

## Exercise 5: Troubleshooting replication issues

### Task 1: Monitor File Sync replication

1. On **SEA-CL1**, use File Explorer to copy the **C:\\Windows\\INF** folder to **\\\\LON-SVR1\\Data\\**. The folder will sync to the cloud endpoint, which will cause sync traffic.
2. In the Azure portal, browse to the **Sync1** sync group in the **FileSync1** Storage Sync Service.
3. In the **Server endpoint** section, verify that the **Health** of both endpoints has green check marks.
4. Select the ```LON-SVR1.Contoso.com``` endpoint in the **Server Endpoint Properties** pane, review **Sync Activity**, and then close the pane.
5. Select the **Files Synced** graph, and then explore how you can customize the graph by using a filter.
6. In File Explorer, verify if the **INF** folder is syncing to drive **Z**.
7. In the Azure portal, verify that the **INF** sync traffic is visible in the **Files Synced** and **Bytes Synced** graphs. The **INF** folder has more than 800 files, and its size is more than 40 MB.

### Task 2: Test replication conflict resolution

1. On **SEA-CL1**, in File Explorer, verify that **File1.txt** is available in **\\\\LON-SVR1\Data\\**. Remember that you uploaded **File1.txt** to the Azure file share, from where it was synced to **SEA-SVR1** by File Sync.
2. Create a file named **Sync.txt** in **\\\\LON-SVR1\Data\\**.
3. In File Explorer, verify that the **Sync.txt** file is also on drive **Z**. Remember that drive **Z** is the mounted Azure file share, where the file was synced from **LON-SVR1** by File Sync.
4. In File Explorer, create a file named **Demo.txt** on drive **Z**.
5. In File Explorer, create a file named **Demo.txt** on **\\\\LON-SVR1\Data\\**.

    >**Note:** You're creating files with the same name to cause a sync conflict.

6. In File Explorer, review the content of drive **Z**. Verify that drive **Z**, which is the mounted Azure file share, includes the **Demo.txt** and **Demo-LON-SVR1.txt** (or **Demo-Cloud.txt**) files. This is because File Sync detected a sync conflict and added the endpoint name (**LON-SVR1** or Cloud) to the file that caused the conflict.

    >**Note:** You might need to wait up to a minute for the sync conflict to occur and for both files to appear on drive **Z**.

## Exercise 6: Cleaning up the Azure subscription

### Task 1: Delete the Azure resources that were created in the lab

1. On **SEA-CL1**, in the Azure portal, browse to the **FileSync1** Storage Sync Service.
1. In the **Storage Sync Service** pane, select **Registered Servers**, in the **details** pane, right-click or access the context menu for ```LON-SVR1.Contoso.com```, select **Unregister server**, in the **Unregister server** pane, enter ```LON-SVR1.Contoso.com``` in a text box, and then select **Unregister**.
1. In **Storage Sync Service** pane, select **Registered Servers**, in the **details** pane, right-click or access the context menu for ```SEA-SVR1.Contoso.com```, select **Unregister server**, in the **Unregister server** pane, enter ```SEA-SVR1.Contoso.com``` in a text box, and then select **Unregister**.
1. Wait until the registration for both servers is removed.
1. In the **Storage Sync Service** pane, select **Sync groups**, and then in the **details** pane, select **Sync1**.
1. In the **Sync1** pane, right-click or access the context menu for **share1** in the **cloud endpoints** section, select **Delete** and then select **OK**.
1. Wait until **share1** is deleted.
1. Select **Delete**, and then select **OK**.
1. In the **navigation** pane, select **All resources**.
1. In the **details** pane, select **FileSync1** and the Azure storage account that you created in the lab (the storage account has a name in the \<*YourLowercaseInitials*>*DDMMYY* format).
1. In the **Delete Resources** pane, select **Delete**, enter **yes** in a text box, and then select **Delete**.
1. In the **navigation** pane, select **Resource groups**.
1. In the **details** pane select **RG1**, select **Delete resource group**, enter **RG1**, and then select **Delete**.
