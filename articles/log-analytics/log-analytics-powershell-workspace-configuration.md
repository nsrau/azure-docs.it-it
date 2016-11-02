<properties
    pageTitle="Usare PowerShell per creare e configurare un'area di lavoro di Log Analytics | Microsoft Azure"
    description="Log Analytics usa i dati provenienti dai server nell'infrastruttura locale o cloud. È possibile raccogliere i dati del computer dall'archiviazione di Azure quando vengono generati dalla diagnostica di Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="powershell"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="richrund"/>


# <a name="manage-log-analytics-using-powershell"></a>Gestire Log Analytics con PowerShell

È possibile usare i [cmdlet di PowerShell per Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) per eseguire varie funzioni in Log Analytics dalla riga di comando o nell'ambito di uno script.  Esempi di attività che è possibile eseguire con PowerShell:

+ Creare un'area di lavoro
+ Aggiungere o rimuovere una soluzione
+ Importare ed esportare ricerche salvate
+ Creare un gruppo di computer
+ Abilitare la raccolta dei log IIS dai computer su cui è stato installato l'agente di Windows
+ Raccogliere i contatori delle prestazioni dai computer Linux e Windows
+ Raccogliere gli eventi dal syslog sui computer Linux 
+ Raccogliere gli eventi dai log eventi di Windows
+ Raccogliere i log eventi personalizzati
+ Aggiungere l'agente Log Analytics a una macchina virtuale di Azure
+ Configurare Log Analytics per indicizzare i dati raccolti tramite Diagnostica di Azure


Questo articolo presenta due codici di esempio con cui vengono illustrate alcune delle funzioni che è possibile eseguire da PowerShell.  Per altre funzioni, è possibile fare riferimento all'articolo sui [cmdlet di PowerShell per Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) .

> [AZURE.NOTE] In precedenza Log Analytics veniva chiamato Operational Insight, motivo per cui nei cmdlet viene usato questo nome.

## <a name="prerequisites"></a>Prerequisiti

Per usare PowerShell con la propria area di lavoro di Log Analytics, è necessario soddisfare i seguenti prerequisiti:

+ Disporre di una sottoscrizione di Azure 
+ L'area di lavoro di Azure Log Analytics deve essere collegata alla propria sottoscrizione di Azure.

Se è stata creata un'area di lavoro OMS, che non risulta ancora collegata a una sottoscrizione di Azure, è possibile creare il collegamento:

+ Nel portale di Azure
+ Nel portale di OMS 
+ Tramite i cmdlet Get-AzureRmOperationalInsightsLinkTargets e New-AzureRmOperationalInsightsWorkspace.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Creare e configurare un'area di lavoro di Log Analytics

Lo script di esempio seguente illustra come:

1.  Creare un'area di lavoro
2.  Elencare le soluzioni disponibili
3.  Aggiungere soluzioni all'area di lavoro
4.  Importare le ricerche salvate
5.  Esportare le ricerche salvate
6.  Creare un gruppo di computer
7.  Abilitare la raccolta dei log IIS dai computer su cui è stato installato l'agente di Windows
8.  Raccogliere i dati dei contatori delle prestazioni del disco logico dai computer Linux (% inodi usati; megabyte liberi; % di spazio usato; trasferimenti/sec del disco; letture/sec del disco; scritture/sec del disco)
9.  Raccogliere gli eventi syslog dai computer Linux
10. Raccogliere gli eventi di errore e di avviso dal log eventi dell'applicazione dai computer Windows
11. Raccogliere i dati del contatore delle prestazioni dei Mbyte di memoria disponibili dai computer Windows
12. Raccogliere i dati di un log personalizzato 


```

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
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configurazione di Log Analytics per indicizzare Diagnostica di Azure 

Per il monitoraggio delle risorse di Azure senza agenti, è necessario che la diagnostica Azure delle risorse sia abilitata e configurata per la scrittura di un account di archiviazione. Dopodiché, è possibile configurare Log Analytics per raccogliere i log dall'account di archiviazione. Le risorse necessarie per eseguire la configurazione precedente sono:

+ Servizi cloud classici (ruoli di lavoro e Web)
+ Cluster di Service Fabric
+ Gruppi di sicurezza di rete
+ Insiemi di credenziali delle chiavi 
+ Gateway di applicazione

È inoltre possibile usare PowerShell per configurare un'area di lavoro di Log Analytics in una sottoscrizione di Azure, in modo da poter raccogliere i log da diverse sottoscrizioni di Azure.

L'esempio seguente illustra come:

1.  Elencare gli account di archiviazione esistenti e i percorsi da cui Log Analytics eseguirà l'indicizzazione dei dati
2.  Creare una configurazione che possa essere letta da un account di archiviazione
3.  Aggiornare la configurazione appena creata in modo da poter indicizzare i dati anche da altri percorsi
4.  Eliminare la configurazione appena creata

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## <a name="next-steps"></a>Passaggi successivi

- [cmdlet di PowerShell per Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) .




<!--HONumber=Oct16_HO2-->


