---
title: Gestire Azure Data Lake Analytics con Azure PowerShell | Microsoft Docs
description: 'Informazioni su come gestire gli account, le origini dati, i processi e gli elementi del catalogo di Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: dd81e9d6c91387b3873593b84e952ca4f2546c57
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gestire Azure Data Lake Analytics tramite Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, i processi e gli elementi del catalogo di Azure Data Lake Analytics usando Azure PowerShell. 

## <a name="prerequisites"></a>prerequisiti

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

Accesso con un ID di sottoscrizione.

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

Accesso con un nome di sottoscrizione.

```
Login-AzureRmAccount -SubscriptionName $subname 
```

Il cmdlet `Login-AzureRmAccount` richiede sempre le credenziali. È possibile evitare tale richiesta usando i cmdlet seguenti:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

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

Verificare l'esistenza di un account specifico di Data Lake Analytics. Il cmdlet restituisce `$true` o `$false`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Verificare l'esistenza di un account specifico di Data Lake Store. Il cmdlet restituisce `$true` o `$false`.

```powershell
Test-AdlStoreAccount -Name $adls
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

Consentire gli indirizzi IP di Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Gestione delle origini dati
Azure Data Lake Analytics supporta attualmente le origini dati seguenti:

* [Archivio Data Lake di Azure](../data-lake-store/data-lake-store-overview.md)
* [Archiviazione di Azure](../storage/common/storage-introduction.md)

Quando si crea un account di Analytics, è necessario impostare un account di Data Lake Store come origine dati predefinita. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. Dopo aver creato un account di Data Lake Analytics, è possibile aggiungere altri account di Data Lake Store e/o account di archiviazione. 

### <a name="find-the-default-data-lake-store-account"></a>Trovare l'account di Data Lake Store predefinito

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

È possibile trovare l'account Data Lake Store predefinito filtrando l'elenco di origine dati secondo il criterio di proprietà `IsDefault`:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Aggiungere un'origine dati

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Elencare le origini dati

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Inviare processi U-SQL

### <a name="submit-a-string-as-a-u-sql-script"></a>Inviare una stringa come script U-SQL

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-script"></a>Inviare un file come script U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Elencare i processi in un account

### <a name="list-all-the-jobs-in-the-account"></a>Elencare tutti i processi nell'account. 

L'output include i processi attualmente in esecuzione e quelli completati di recente.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Elencare i processi N principali

Per impostazione predefinita, l'elenco dei processi viene ordinato in base all'ora di invio. Pertanto, i processi inviati di recente vengono visualizzati per primi. Per impostazione predefinita, l'account di ADLA memorizza i processi per 180 giorni, ma il cmdlet Get-AdlJob restituisce solo i primi 500. Utilizzare i parametri superiori per elencare un numero specifico di processi.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-based-on-the-value-of-job-property"></a>Elencare i processi in base al valore della proprietà processo

Uso del parametro `-State`. È possibile combinare questi valori:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

Usare il parametro `-Result` per rilevare se i processi finiti sono stati completati correttamente. Dispone di questi valori:

* Operazione annullata
* Operazione non riuscita
* Nessuna
* Succeeded

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

Il parametro `-Submitter` consente di identificare chi ha inviato un processo.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

Il `-SubmittedAfter` è utile per un filtraggio in base a un intervallo di tempo.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Analisi della cronologia processo

L'uso di Azure PowerShell per analizzare la cronologia dei processi eseguiti in Data Lake Analytics è una tecnica potente. In questo modo, è possibile ottenere informazioni dettagliate sull'utilizzo e sui costi. Per altre informazioni, vedere il [repository di esempio di analisi della cronologia processo](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Ottenere informazioni sulle pipeline e l'intervallo di esecuzione

Utilizzare il cmdlet `Get-AdlJobPipeline` per visualizzare le informazioni di pipeline dei processi inviati in precedenza.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Utilizzare il cmdlet `Get-AdlJobRecurrence` per visualizzare le informazioni relative all'intervallo di esecuzione dei processi inviati in precedenza.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>Ottenere informazioni su un processo

### <a name="get-job-status"></a>Ottenere lo stato di processo

Ottenere lo stato di un processo specifico.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Esaminare gli output di processo

Al termine del processo, controllare se il file di output esiste elencando i file in una cartella.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Gestire i processi in esecuzione

### <a name="cancel-a-job"></a>Annullare un processo

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Attendere il completamento di un processo

Anziché ripetere `Get-AdlAnalyticsJob` finché non termina un processo, è possibile usare il cmdlet `Wait-AdlJob` per attendere la fine del processo.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Gestire i criteri di calcolo

### <a name="list-existing-compute-policies"></a>Elenco di criteri di calcolo esistenti

Il cmdlet `Get-AdlAnalyticsComputePolicy` recupera informazioni sui criteri di calcolo per un account Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Creare un criterio di calcolo

Il cmdlet `New-AdlAnalyticsComputePolicy` crea un nuovo criterio di calcolo per un account Data Lake Analytics. In questo esempio le unità di analisi massime disponibili per l'utente specificato sono 50 e la priorità minima del processo è pari a 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Verificare l'esistenza di un file.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Caricamento e download

Caricare un file.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Caricare in modo ricorsivo un'intera cartella.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Scaricare un file.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Scaricare in modo ricorsivo un'intera cartella.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Se il processo di caricamento o download viene interrotto, è possibile tentare di riprendere il processo eseguendo nuovamente il cmdlet con il flag ``-Resume``.

## <a name="manage-catalog-items"></a>Gestire gli elementi del catalogo

Il catalogo di U-SQL viene usato per definire la struttura dei dati e del codice in modo da poterli condividere mediante U-SQL. Il catalogo consente di ottenere le migliori prestazioni possibili con i dati in Azure Data Lake. Per altre informazioni, vedere la pagina di [Usare il catalogo di U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Elencare elementi nel catalogo U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

Elencare tutti gli assembly in tutti i database in un account ADLA.

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Ottenere informazioni dettagliate su un elemento del catalogo

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>Creare le credenziali in un catalogo

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

### <a name="get-basic-information-about-an-adla-account"></a>Ottenere le informazioni di base relative all'account ADLA

Dato un nome di account, il codice seguente cerca le informazioni di base sull'account

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Utilizzo di Azure

### <a name="get-details-of-azurerm-errors"></a>Ottenere i dettagli di errore di AzureRm

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Verificare che il processo sia in esecuzione in modalità amministratore

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Trovare un ID tenant

Da un nome di sottoscrizione:

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

Da un ID di sottoscrizione:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

Da un indirizzo di dominio, ad esempio "contoso.com"


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Elencare tutte le sottoscrizioni e i relativi ID tenant

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
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
* Gestire Azure Data Lake Analytics con [il portale di Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [l'interfaccia della riga di comando](data-lake-analytics-manage-use-cli.md) 
