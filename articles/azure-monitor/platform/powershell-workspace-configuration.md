---
title: Usare PowerShell per creare e configurare un'area di lavoro di Log Analytics | Documentazione Microsoft
description: Le aree di lavoro di log Analitica in Monitoraggio di Azure archiviano i dati dai server in locale o cloud dell'infrastruttura. È possibile raccogliere i dati del computer dall'archiviazione di Azure quando vengono generati dalla diagnostica di Azure.
services: log-analytics
author: richrundmsft
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: richrund
ms.openlocfilehash: f37c8290defa5e7c9baa3b705393aba376936fd8
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58539378"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Gestire aree di lavoro di Log Analitica in Monitoraggio di Azure con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

È possibile usare la [i cmdlet di PowerShell per Log Analitica](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) per eseguire varie funzioni in un Log Analitica dell'area di lavoro in Monitoraggio di Azure dalla riga di comando o come parte di uno script.  Esempi di attività che è possibile eseguire con PowerShell:

* Creare un'area di lavoro
* Aggiungere o rimuovere una soluzione
* Importare ed esportare ricerche salvate
* Creare un gruppo di computer
* Abilitare la raccolta dei log IIS dai computer su cui è stato installato l'agente di Windows
* Raccogliere i contatori delle prestazioni dai computer Linux e Windows
* Raccogliere gli eventi dal syslog sui computer Linux
* Raccogliere gli eventi dai log eventi di Windows
* Raccogliere i log eventi personalizzati
* Aggiungere l'agente Log Analytics a una macchina virtuale di Azure
* Configurare Log Analytics per indicizzare i dati raccolti tramite Diagnostica di Azure

Questo articolo presenta due codici di esempio con cui vengono illustrate alcune delle funzioni che è possibile eseguire da PowerShell.  Per altre funzioni, è possibile fare riferimento all'articolo sui [cmdlet di PowerShell per Log Analytics](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) .

> [!NOTE]
> In precedenza Log Analytics veniva chiamato Operational Insight, motivo per cui nei cmdlet viene usato questo nome.

## <a name="prerequisites"></a>Prerequisiti
Questi esempi funzionano con la versione 1.0.0 o versione successiva del modulo Az.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Creare e configurare un'area di lavoro di Log Analytics
Lo script di esempio seguente illustra come:

1. Creare un'area di lavoro
2. Elencare le soluzioni disponibili
3. Aggiungere soluzioni all'area di lavoro
4. Importare le ricerche salvate
5. Esportare le ricerche salvate
6. Creare un gruppo di computer
7. Abilitare la raccolta dei log IIS dai computer su cui è stato installato l'agente di Windows
8. Raccogliere i dati dei contatori delle prestazioni del disco logico dai computer Linux (% inodi usati; megabyte liberi; % di spazio usato; trasferimenti/sec del disco; letture/sec del disco; scritture/sec del disco)
9. Raccogliere gli eventi syslog dai computer Linux
10. Raccogliere gli eventi di errore e di avviso dal log eventi dell'applicazione dai computer Windows
11. Raccogliere i dati del contatore delle prestazioni dei Mbyte di memoria disponibili dai computer Windows
12. Raccogliere i dati di un log personalizzato

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
Nell'esempio precedente regexDelimiter è stato definito come "\\n" per la nuova riga. Il delimitatore di log può anche essere un timestamp.  Questi sono i formati supportati:

| Format | Il formato RegEx JSON usa due \\ per ogni \ in un'espressione RegEx standard, quindi in caso di test in un'app RegEx ridurre \\ a \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\\\d)\|[0-9])/(([0-3]\\\\d)\|(\\\\d))/((\\\\d{2})\|(\\\\d{4}))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\\\s(AM\|PM\|am\|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `((([0-3]\\\\d)\` | `(\\\\d))/(Jan\|Feb\|Mar\|May\|Apr\|Jul\|Jun\|Aug\|Oct\|Sep\|Nov\|Dec\|jan\|feb\|mar\|may\|apr\|jul\|jun\|aug\|oct\|sep\|nov\|dec)/((\\\\d{2})\|(\\\\d{4}))\\\\s((\\\\d)\` | `([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?\|Feb(?:ruary)?\|Mar(?:ch)?\|Apr(?:il)?\|May\|Jun(?:e)?\|Jul(?:y)?\|Aug(?:ust)?\|Sep(?:tember)?\|Sept\|Oct(?:ober)?\|Nov(?:ember)?\|Dec(?:ember)?)).*?((?:(?:[0-2]?\\\\d{1})\|(?:[3][01]{1})))(?![\\\\d]).*?((?:(?:[1]{1}\\\\d{1}\\\\d{1}\\\\d{1})\|(?:[2]{1}\\\\d{3})))(?![\\\\d]).*?((?:(?:[0-1][0-9])\|(?:[2][0-3])\|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\\\s?(?:am\|AM\|pm\|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]\|[1][0-2])([0-2][0-9]\|[3][0-1])\\\\s\\\\s?([0-1]?[0-9]\|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]\|[3][0-1])([0][1-9]\|[1][0-2])[0-9]{2}\\\\s\\\\s?([0-1]?[0-9]\|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s\\\\s?([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0-1]?[0-9]\|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> due spazi dopo MMM | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s\\\\s([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> dove + è + o - <br> dove zzzz è la differenza di orario | `(([0-2][1-9]\|[3][0-1])\\\\/(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\/((19\|20)[0-9][0-9]):([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\\\s[\\\\+\|\\\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T è una valore letterale per la lettera T | `((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))T((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Configurazione di Log Analitica per l'invio della diagnostica di Azure
Per il monitoraggio senza agenti delle risorse di Azure, in queste ultime la diagnostica di Azure deve essere abilitata e configurata per la scrittura in un'area di lavoro di Log Analytics. Questo approccio invia i dati direttamente all'area di lavoro e non richiede dati da scrivere in un account di archiviazione. Le risorse supportate includono:

| Tipo di risorsa | Log | Metriche |
| --- | --- | --- |
| Gateway applicazione    | Sì | Sì |
| Account di Automazione     | Sì | |
| Account Batch          | Sì | Sì |
| Data Lake Analytics     | Sì | |
| Data Lake Store         | Sì | |
| Pool SQL elastico        |     | Sì |
| Spazio dei nomi dell'hub eventi     |     | Sì |
| Hub IoT                |     | Sì |
| Key Vault               | Sì | |
| Servizi di bilanciamento del carico          | Sì | |
| App per la logica              | Sì | Sì |
| Gruppi di sicurezza di rete | Sì | |
| Cache Redis di Azure             |     | Sì |
| Servizi di ricerca         | Sì | Sì |
| Spazio dei nomi del bus di servizio   |     | Sì |
| SQL (versione 12)               |     | Sì |
| Microsoft Azure               |     | Sì |
| Server farm Web        |     | Sì |

Per informazioni dettagliate sulle metriche disponibili, vedere [Metriche supportate con il monitoraggio di Azure](../../azure-monitor/platform/metrics-supported.md).

Per informazioni dettagliate sui registri disponibili, vedere [Servizi supportati e schema per i log di diagnostica](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Il cmdlet precedente può essere usato anche per la raccolta di log da risorse presenti in sottoscrizioni diverse. Il cmdlet funziona in sottoscrizioni diverse perché vengono specificati sia l'ID della risorsa che crea i log sia l'area di lavoro a cui questi vengono inviati.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Area di lavoro di Log Analitica per raccogliere dati di diagnostica Azure da un archivio di configurazione
Per raccogliere dati di log dall'interno di un'istanza in esecuzione di un servizio cloud classico o di un cluster di Service Fabric, è necessario prima scrivere i dati in Archiviazione di Azure. Un'area di lavoro di Log Analitica viene quindi configurato per raccogliere i log dall'account di archiviazione. Le risorse supportate includono:

* Servizi cloud classici (ruoli di lavoro e Web)
* Cluster di Service Fabric

L'esempio seguente illustra come:

1. Elencare l'account di archiviazione esistenti e i percorsi che l'area di lavoro eseguirà l'indicizzazione dei dati da
2. Creare una configurazione che possa essere letta da un account di archiviazione
3. Aggiornare la configurazione appena creata in modo da poter indicizzare i dati anche da altri percorsi
4. Eliminare la configurazione appena creata

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Lo script precedente può essere usato anche per la raccolta di log da account di archiviazione presenti in sottoscrizioni diverse. Lo script funziona in sottoscrizioni diverse perché vengono specificati sia l'ID risorsa dell'account di archiviazione che la chiave di accesso corrispondente. Quando si modifica la chiave di accesso, è necessario aggiornare le informazioni di archiviazione con la nuova chiave.


## <a name="next-steps"></a>Passaggi successivi
* [cmdlet di PowerShell per Log Analytics](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) .

