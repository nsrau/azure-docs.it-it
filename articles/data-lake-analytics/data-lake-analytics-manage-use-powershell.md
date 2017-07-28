---
title: Gestire Azure Data Lake Analytics con Azure PowerShell | Microsoft Docs
description: 'Informazioni su come gestire gli account, le origini dati, i processi e gli elementi del catalogo di Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: mahi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 86648ebc5e13570050bb142158bf1aa356d466b3
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gestire Azure Data Lake Analytics tramite Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, i processi e gli elementi del catalogo di Azure Data Lake Analytics usando Azure PowerShell. 

## <a name="prerequisites"></a>Prerequisiti

Durante la creazione di un account di Data Lake Analytics, è necessario sapere:

* **ID sottoscrizione**: l'ID di sottoscrizione di Azure in cui risiede l'account di Data Lake Analytics.
* **Gruppo di risorse**: il nome del gruppo di risorse di Azure che contiene l'account di Data Lake Analytics.
* **Nome dell'account Data Lake Analytics**: il nome dell'account deve contenere solo lettere minuscole e numeri.
* **Account Data Lake Store predefinito**: ogni account Data Lake Analytics ha un account Data Lake Store predefinito. Questi account devono essere nello stesso percorso.
* **Posizione**: la posizione dell'account di Data Lake Analytics, ad esempio "Stati Uniti orientali 2" o altre posizioni supportate. Le posizioni supportate possono essere visualizzate nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

I frammenti di codice di PowerShell in questa esercitazione usano le variabili seguenti per archiviare queste informazioni

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Accesso

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

> [!NOTE]
> Per salvare la sessione di accesso per un utilizzo futuro, usare ``Save-AzureRmProfile``. Per caricare di nuovo la sessione di accesso, usare ``Select-AzureRmProfile``.

## <a name="managing-accounts"></a>Gestione di account

### <a name="create-a-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake

Se non si ha già un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups) da usare, crearne uno. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Per ogni account Data Lake Analytics deve essere configurato un account Data Lake Store che viene usato per l'archiviazione dei log. È possibile riusare un account esistente o creare un account. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Quando sono disponibili un gruppo di risorse e un account Data Lake Store, creare un account Data Lake Analytics.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>Ottenere informazioni su un account

Ottenere dettagli su un account.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Verificare l'esistenza di un account specifico di Data Lake Analytics. Il cmdlet restituisce `True` o `False`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

### <a name="listing-accounts"></a>Elenco di account

Elencare gli account di Data Lake Analytics all'interno della sottoscrizione corrente.

```powershell
Get-AdlAnalyticsAccount
```

Elencare gli account di Data Lake Analytics all'interno di un gruppo di risorse specifico.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>Gestione delle regole del firewall

### <a name="add-or-remove-firewall-rules"></a>Aggiungere o rimuovere le regole del firewall

Elencare le regole del firewall.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

Aggiungere una regola del firewall.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Modificare una regola del firewall.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Rimuovere una regola del firewall.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="enable-or-disable-firewall-rules"></a>Abilitare o disabilitare le regole del firewall

Abilitare le regole del firewall.

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
```

Consentire gli indirizzi IP di Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

Disabilitare le regole del firewall.

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Gestione delle origini dati
Azure Data Lake Analytics supporta attualmente le origini dati seguenti:

* [Archivio Data Lake di Azure](../data-lake-store/data-lake-store-overview.md)
* [Archiviazione di Azure](../storage/storage-introduction.md)

Quando si crea un account di Analytics, è necessario impostare un account di Data Lake Store come origine dati predefinita. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. Dopo aver creato un account di Data Lake Analytics, è possibile aggiungere altri account di Data Lake Store e/o account di archiviazione. 

### <a name="find-the-default-data-lake-store-account"></a>Trovare l'account di Data Lake Store predefinito

```powershell
$dataLakeStoreName = (Get-AdlAnalyticsAccount -Name $adla).DefaultDataLakeAccount
```

### <a name="add-data-sources"></a>Aggiungere origini dati

Aggiungere un altro account di archiviazione (Blob).

```powershell
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"

Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey
```

Aggiungere un altro account di Data Lake Store.

```powershell
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Elencare le origini dati

Elencare tutte le origini dati collegate.

```powershell
Get-AdlAnalyticsDataSource -Name $adla
```

Elencare gli account di Data Lake Store collegati.

```powershell
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"
```

Elencare gli account di archiviazione collegati.

```powershell
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="managing-jobs"></a>Gestione dei processi

### <a name="submit-a-job"></a>Inviare un processo

Creare un file di testo locale con lo script U-SQL seguente.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Inviare lo script.

```powershell
Submit-AdlJob -AccountName $adla –ScriptPath "<LocalPathToScriptFile>"
```

### <a name="list-jobs"></a>Elencare i processi

Elencare tutti i processi nell'account. L'output include i processi attualmente in esecuzione e quelli completati di recente.

```powershell
Get-AdlJob -Account $adla
```

Elencare tutti i progetti non riusciti inviati da "joe@contoso.com" negli ultimi sette giorni.

```powershell
Get-AdlJob -Account $adla `
    -Submitter "joe@contoso.com" `
    -SubmittedAfter (Get-Date).AddDays(-7) `
    -Result Failed
```

### <a name="get-information-about-a-job"></a>Ottenere informazioni su un processo

Ottenere lo stato di un processo specifico.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Anziché ripetere `Get-AdlAnalyticsJob` finché non termina un processo, è possibile usare il cmdlet `Wait-AdlJob` per attendere la fine del processo.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Al termine del processo, controllare se il file di output esiste elencando i file in una cartella.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Verificare l'esistenza di un file.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="cancel-a-job"></a>Annullare un processo

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

## <a name="uploading-and-downloading"></a>Caricamento e download

Caricare un file.

```powershell
Import-AdlStoreItem -AccountName $adls `
    -Path "<LocalPath>\data.tsv" `
    -Destination "/data_copy.csv" 
```

Caricare una cartella intera.

```powershell
Import-AdlStoreItem -AccountName $adls `
    -Path "<LocalPath>\myData\" `
    -Destination "/myData/" `
    -Recurse
```

Scaricare un file.

```powershell
Export-AdlStoreItem -AccountName $adls `
    -Path "/data.csv" `
    -Destination "<LocalPath>\data.csv"
```

Scaricare una cartella intera.

```powershell
Export-AdlStoreItem -AccountName $adls `
    -Path "/" `
    -Destination "<LocalPath>\myData\" `
    -Recurse
```

> [!NOTE]
> Se il processo di caricamento o download viene interrotto, è possibile tentare di riprendere il processo eseguendo nuovamente il cmdlet con il flag ``-Resume``.

## <a name="manage-catalog-items"></a>Gestire gli elementi del catalogo
Il catalogo di U-SQL viene usato per definire la struttura dei dati e del codice in modo da poterli condividere mediante U-SQL. Il catalogo consente di ottenere le migliori prestazioni possibili con i dati in Azure Data Lake. Per altre informazioni, vedere la pagina di [Usare il catalogo di U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items"></a>Elencare gli elementi

Elencare i database U-SQL.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Database 
```

Elencare le tabelle all'interno di un database.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "master"
```

Elencare le tabelle all'interno di uno schema.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "master.dbo"
```

### <a name="get-information-about-a-catalog-item"></a>Ottenere informazioni su un elemento del catalogo

Ottenere i dettagli di un database U-SQL.

```powershell
Get-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

Ottenere i dettagli di una tabella in un database U-SQL.

```powershell
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"
```

Verificare l'esistenza di un database U-SQL.

```powershell
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-catalog-items"></a>Creare gli elementi del catalogo

All'interno di un database U-SQL creare un oggetto credenziali per un database ospitato in Azure. Attualmente le credenziali di U-SQL sono l'unico tipo di elemento del catalogo che è possibile creare tramite PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Creare un account Data Lake Analytics usando un modello

È inoltre possibile usare un modello di gruppo di risorse di Azure tramite lo script di PowerShell seguente:

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

Per altre informazioni, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](../azure-resource-manager/resource-group-template-deploy.md) e [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

**Modello di esempio**

Salvare il testo seguente come un file `.json` e usare lo script di PowerShell precedente per usare il modello. 

```json
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
```

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* Introduzione a Data Lake Analytics con [ il portale di Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [l'interfaccia della riga di comando 2.0](data-lake-analytics-get-started-cli2.md)
* Gestire Azure Data Lake Analytics con [il portale di Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) |  [il portale di Azure](data-lake-analytics-manage-use-portal.md) | [l'interfaccia della riga di comando](data-lake-analytics-manage-use-cli.md) 

