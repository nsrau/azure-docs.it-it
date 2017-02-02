---
title: Gestire Azure Data Lake Analytics con Azure PowerShell | Documentazione Microsoft
description: 'Informazioni su come gestire processi, origini dati e utenti di Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 9be227415ee18b6333d5a469e571b3802a3d87ef
ms.openlocfilehash: 30926a9937cd7dce0e38558d7880d1d22997ddd4


---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gestire Azure Data Lake Analytics tramite Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, gli utenti e i processi di Azure Data Lake Analytics tramite Azure PowerShell. Per visualizzare gli argomenti relativi alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

<!-- ################################ -->
<!-- ################################ -->


## <a name="install-azure-powershell-10-or-greater"></a>Installare Azure PowerShell 1.0 o versioni successive
Vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

## <a name="manage-accounts"></a>Gestire account
Prima di eseguire qualsiasi processo di Analisi Data Lake, è necessario disporre di un account di Analisi Data Lake. A differenza di Azure HDInsight, un account di Analisi non è soggetto ad alcun pagamento fino a quando il processo non è in esecuzione.  Il pagamento, infatti, viene richiesto solo per la durata di esecuzione di un processo.  Per altre informazioni, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Creare account
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeStoreName = "<DataLakeAccountName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $location = "<Microsoft Data Center>"

    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsAccountName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsAccountName  

È inoltre possibile usare un modello di Gruppo di risorse di Azure. Un modello per la creazione di un account Data Lake Analytics e dell'account Data Lake Store dipendente è descritto nell'[appendice A](#appendix-a). Salvare il modello in un file con estensione JSON e quindi usare il seguente script di PowerShell per chiamarlo:

    $AzureSubscriptionID = "<Your Azure Subscription ID>"

    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    $DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
    $DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"

    $DeploymentName = "MyDataLakeAnalyticsDeployment"
    $ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID

    # Create the resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Create the Data Lake Analytics account with the default Data Lake Store account.
    $parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
    New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters


### <a name="list-account"></a>Elencare gli account
Elencare gli account di Data Lake Analytics all'interno della sottoscrizione corrente

    Get-AzureRmDataLakeAnalyticsAccount

L'output è:

    Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
    Location   : eastus2
    Name       : learn1021adla
    Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
    Tags       : {}
    Type       : Microsoft.DataLakeAnalytics/accounts

Elencare gli account di Data Lake Analytics all'interno di un gruppo di risorse specifico

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Ottenere i dettagli di un account specifico di Data Lake Analytics

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Verificare l'esistenza di un account specifico di Data Lake Analytics

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Il cmdlet restituirà **True** oppure **False**.

### <a name="delete-data-lake-analytics-accounts"></a>Eliminare gli account di Data Lake Analytics
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName

L'eliminazione di un account Data Lake Analytics non comporta l'eliminazione dell'account di archiviazione dipendente di Data Lake. Nell'esempio seguente vengono eliminati l'account di Data Lake Analytics e l'account di Data Lake Store predefinito

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gestire le origini dati degli account
Analisi Data Lake supporta attualmente le seguenti origini dati:

* [Archivio Data Lake di Azure](../data-lake-store/data-lake-store-overview.md)
* [Archiviazione di Azure](../storage/storage-introduction.md)

Quando si crea un account di Analytics, è necessario impostare un account di archiviazione di Azure Data Lake come account di archiviazione predefinito. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. Dopo aver creato un account di Analytics, è possibile aggiungere altri account di archiviazione di Data Lake e/o account di archiviazione di Azure.

### <a name="find-the-default-data-lake-store-account"></a>Trovare l'account di Data Lake Store predefinito
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### <a name="add-additional-azure-blob-storage-accounts"></a>Aggiungere altri account di archiviazione BLOB di Azure
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"

    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### <a name="add-additional-data-lake-store-accounts"></a>Aggiungere altri account di Data Lake Store
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"

    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName

### <a name="list-data-sources"></a>Elencare le origini dati:
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts



<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>Gestire i processi
È necessario disporre di un account di Data Lake Analytics prima di poter creare un processo.  Per altre informazioni, vedere [Gestire gli account di Analisi Data Lake](#manage-data-lake-analytics-accounts).

### <a name="list-jobs"></a>Elencare i processi
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"    

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### <a name="get-job-details"></a>Ottenere i dettagli dei processi
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>

### <a name="submit-jobs"></a>Inviare i processi
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -Script $scriptContents

> [!NOTE]
> La priorità predefinita di un processo è 1000 e il livello predefinito di parallelismo per un processo è 1.
>
>

### <a name="cancel-jobs"></a>Annullare i processi
    Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -JobID $jobID


## <a name="manage-catalog-items"></a>Gestire gli elementi del catalogo
Il catalogo di U-SQL viene usato per definire la struttura dei dati e del codice in modo da poterli condividere mediante U-SQL. Il catalogo consente di ottenere le migliori prestazioni possibili con i dati in Azure Data Lake. Per altre informazioni, vedere la pagina di [Usare il catalogo di U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-catalog-items"></a>Elencare gli elementi del catalogo
    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database



    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

### <a name="get-catalog-item-details"></a>Ottenere i dettagli degli elementi del catalogo
    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

### <a name="test-existence-of--catalog-item"></a>Verificare l'esistenza dell'elemento del catalogo
    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

### <a name="create-catalog-secret"></a>Creare il segreto del catalogo
    New-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")

### <a name="modify-catalog-secret"></a>Modificare il segreto del catalogo
    Set-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")



### <a name="delete-catalog-secret"></a>Eliminare il segreto del catalogo
    Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master"


## <a name="use-azure-resource-manager-groups"></a>Usare i gruppi Gestione risorse di Azure
Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Gestione risorse di Azure (ARM) consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, staging  e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md).

Un servizio di Analisi Data Lake può includere i componenti seguenti:

* Account di Azure Data Lake Analytics
* Account di archiviazione predefinito obbligatorio di Azure Data Lake
* Account di archiviazione aggiuntivi di Azure Data Lake
* Account di archiviazione aggiuntivi di Azure

È possibile creare tutti questi componenti in un unico gruppo ARM per semplificarne la gestione.

![Account e archiviazione di Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un account di Analisi Data Lake e gli account di archiviazione dipendenti devono trovarsi nello stesso data center di Azure,
mentre il gruppo ARM può trovarsi anche in un data center diverso.  

## <a name="see-also"></a>Vedere anche
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introduzione a Analisi Data Lake tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Gestire Analisi Data Lake di Azure tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="appendix-a---data-lake-analytics-arm-template"></a>Appendice A - Modello ARM di Analisi Data Lake
Il seguente modello ARM può essere usato per distribuire un account di Data Lake Analytics e il relativo account di Data Lake Store dipendente.  Salvarlo come file con estensione JSON e quindi usare uno script di PowerShell per chiamare il modello. Per altre informazioni, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](../azure-resource-manager/resource-group-template-deploy.md#deploy) e [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md).

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adlAnalyticsName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Analytics account to create."
          }
        },
        "adlStoreName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Store account to create."
          }
        }
      },
      "resources": [
        {
          "name": "[parameters('adlStoreName')]",
          "type": "Microsoft.DataLakeStore/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ ],
          "tags": { }
        },
        {
          "name": "[parameters('adlAnalyticsName')]",
          "type": "Microsoft.DataLakeAnalytics/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
          "tags": { },
          "properties": {
            "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
            "dataLakeStoreAccounts": [
              { "name": "[parameters('adlStoreName')]" }
            ]
          }
        }
      ],
      "outputs": {
        "adlAnalyticsAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
        },
        "adlStoreAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
        }
      }
    }



<!--HONumber=Dec16_HO1-->


