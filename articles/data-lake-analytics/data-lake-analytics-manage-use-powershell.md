---
title: Gestire Azure Data Lake Analytics tramite Azure PowerShell
description: Questo articolo descrive come usare Azure PowerShell per gestire utenti, processi, origini dati e account Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 4273828c9c2bdb75fcbc1de45da55c5a03dd615f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66156417"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gestire Azure Data Lake Analytics tramite Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Questo articolo descrive come gestire utenti, processi, origini dati e account Azure Data Lake Analytics tramite Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per usare PowerShell con Data Lake Analytics, raccogliere le informazioni seguenti: 

* **ID sottoscrizione**: l'ID della sottoscrizione di Azure in cui è presente l'account Data Lake Analytics.
* **Gruppo di risorse**: il nome del gruppo di risorse di Azure che contiene l'account Data Lake Analytics.
* **Nome dell'account Data Lake Analytics**: il nome dell'account Data Lake Analytics.
* **Nome dell'account Data Lake Store predefinito**: ogni account Data Lake Analytics ha un account Data Lake Store predefinito.
* **Località**: la posizione dell'account Data Lake Analytics, ad esempio "Stati Uniti orientali 2" o altre posizioni supportate.

I frammenti di codice di PowerShell in questa esercitazione usano le variabili seguenti per archiviare queste informazioni

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Accedere ad Azure

### <a name="log-in-using-interactive-user-authentication"></a>Accedere usando l'autenticazione utente interattiva

Accedere usando un ID o un nome di sottoscrizione

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Salvataggio del contesto di autenticazione

Il cmdlet `Connect-AzAccount` chiede sempre le credenziali. È possibile evitare tale richiesta usando i cmdlet seguenti:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Accedere usando un'identità dell'entità servizio

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Gestisci account


### <a name="list-accounts"></a>Elenca account

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Crea un account

Per ogni account Data Lake Analytics deve essere configurato un account Data Lake Store che viene usato per l'archiviazione dei log. È possibile riusare un account esistente o creare un account. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Ottenere le informazioni sull'account

Ottenere dettagli su un account.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Verificare l'esistenza di un account

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Gestire le origini dati
Azure Data Lake Analytics supporta attualmente le origini dati seguenti:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Archiviazione di Azure](../storage/common/storage-introduction.md)

Ogni account Data Lake Analytics ha un account Data Lake Store predefinito. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. 

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
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Inviare processi U-SQL

### <a name="submit-a-string-as-a-u-sql-job"></a>Inviare una stringa come processo U-SQL

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

### <a name="submit-a-file-as-a-u-sql-job"></a>Inviare un file come processo U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Elencare i processi

L'output include i processi attualmente in esecuzione e quelli completati di recente.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Elencare i processi N principali

Per impostazione predefinita, l'elenco dei processi viene ordinato in base all'ora di invio. Pertanto, i processi inviati di recente vengono visualizzati per primi. Per impostazione predefinita, l'account di ADLA memorizza i processi per 180 giorni, ma il cmdlet Get-AdlJob restituisce solo i primi 500. Utilizzare i parametri superiori per elencare un numero specifico di processi.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Elencare i processi in base allo stato

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

### <a name="list-jobs-by-job-result"></a>Elencare i processi in base al risultato

Usare il parametro `-Result` per rilevare se i processi finiti sono stati completati correttamente. Dispone di questi valori:

* Annullata
* Failed
* Nessuna
* Riuscita

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Elencare i processi in base alla persona che invia la richiesta

Il parametro `-Submitter` consente di identificare chi ha inviato un processo.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Elencare i processi in base all'ora di invio

Il `-SubmittedAfter` è utile per un filtraggio in base a un intervallo di tempo.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Ottenere lo stato di processo

Ottenere lo stato di un processo specifico.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>Annullare un processo

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Attendere il completamento di un processo

Anziché ripetere `Get-AdlAnalyticsJob` finché non termina un processo, è possibile usare il cmdlet `Wait-AdlJob` per attendere la fine del processo.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analisi della cronologia processo

L'uso di Azure PowerShell per analizzare la cronologia dei processi eseguiti in Data Lake Analytics è una tecnica potente. In questo modo, è possibile ottenere informazioni dettagliate sull'utilizzo e sui costi. Per altre informazioni, vedere il [repository di esempio di analisi della cronologia processo](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Elencare pipeline e ricorrenze dei processi

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


## <a name="manage-compute-policies"></a>Gestire i criteri di calcolo

### <a name="list-existing-compute-policies"></a>Elenco di criteri di calcolo esistenti

Il cmdlet `Get-AdlAnalyticsComputePolicy` recupera informazioni sui criteri di calcolo per un account Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Creare un criterio di calcolo

Il cmdlet `New-AdlAnalyticsComputePolicy` crea un nuovo criterio di calcolo per un account Data Lake Analytics. Questo esempio imposta le unità di analisi massime disponibili per l'utente specificato su 50 e la priorità minima del processo su 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Gestire i file

### <a name="check-for-the-existence-of-a-file"></a>Verificare l'esistenza di un file.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Caricamento e download

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

## <a name="manage-the-u-sql-catalog"></a>Gestire il catalogo U-SQL

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

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Elencare tutti gli assembly nel catalogo U-SQL

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

### <a name="store-credentials-in-the-catalog"></a>Archiviare le credenziali nel catalogo

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

## <a name="manage-firewall-rules"></a>Gestire le regole del firewall

### <a name="list-firewall-rules"></a>Elencare le regole del firewall

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Aggiungere una regola del firewall

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Modificare una regola del firewall

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Rimuovere una regola del firewall

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Consentire gli indirizzi IP di Azure

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Utilizzo di Azure

### <a name="get-error-details"></a>Ottenere i dettagli dell'errore

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Verificare se si sta eseguendo un processo come amministratore nel computer Windows

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
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Da un ID sottoscrizione:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
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

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Elencare tutti gli ID di sottoscrizioni e tenant

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Creare un account Data Lake Analytics usando un modello

È inoltre possibile usare un modello di gruppo di risorse di Azure tramite l'esempio seguente: [Creare un account Data Lake Analytics usando un modello](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* Introduzione ad Azure Data Lake Analytics con [il portale di Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Interfaccia della riga di comando di Azure](data-lake-analytics-get-started-cli.md)
* Gestire Azure Data Lake Analytics con [il portale di Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [l'interfaccia della riga di comando](data-lake-analytics-manage-use-cli.md) 
