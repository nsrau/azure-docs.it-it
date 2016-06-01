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
	ms.date="05/16/2016"
	ms.author="richrund"/>

# Gestire Log Analytics con PowerShell

È possibile usare i [cmdlet di PowerShell per Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) per eseguire una serie di funzioni in Log Analytics dalla riga di comando o nell'ambito di uno script. Esempi di attività che è possibile eseguire con PowerShell:

+ Creare un'area di lavoro
+ Aggiungere o rimuovere una soluzione
+ Importare ed esportare ricerche salvate
+ Aggiungere l'agente Log Analytics a una macchina virtuale di Azure
+ Configurare Log Analytics per indicizzare i dati raccolti tramite Diagnostica di Azure

Questo articolo presenta due codici di esempio con cui vengono illustrate alcune delle funzioni che è possibile eseguire da PowerShell. Per altre funzioni, è possibile fare riferimento all'articolo sui [cmdlet di PowerShell per Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx).

> [AZURE.NOTE] Log Analytics in precedenza era chiamato Operational Insight ed è per questo che nei cmdlet viene usato questo nome.

## Prerequisiti

Per usare PowerShell con la propria area di lavoro di Log Analytics, è necessario soddisfare i seguenti prerequisiti:

+ Disporre di una sottoscrizione di Azure 
+ L'area di lavoro di Azure Log Analytics deve essere collegata alla propria sottoscrizione di Azure.

Se l'area di lavoro di OMS è già stata creata ma non è ancora stata collegata a una sottoscrizione di Azure, è possibile creare il collegamento nel portale di Azure, nel portale OMS o tramite i cmdlet AzureRMOperationalInsightsLinkTargets e New-AzureRMOperationalInsightsWorkspace cmdlets.

## Creare un'area di lavoro di Log Analytics e aggiungere soluzioni e ricerche salvate

Lo script di esempio seguente illustra come:

1.	Creare un'area di lavoro
2.	Elencare le soluzioni disponibili
3.	Aggiungere soluzioni all'area di lavoro
4.	Importare le ricerche salvate
5.	Esportare le ricerche salvate

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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName="C:" CounterName="Current Disk Queue Length"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

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
foreach ($soln in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $soln -Enabled $true
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

```

## Configurazione di Log Analytics per indicizzare Diagnostica di Azure 

Per un monitoraggio senza agenti di alcune risorse di Azure, ad esempio ruoli Web e di lavoro, cluster di Service Fabric, gruppi di sicurezza di rete, insiemi di credenziali delle chiavi e gateway applicazione, è necessario che per le risorse sia abilitato Diagnostica di Azure, in modo che sia consentita la scrittura in un account di archiviazione, e che Log Analytics sia configurato in modo da raccogliere i log dall'account di archiviazione.

È inoltre possibile usare PowerShell per configurare un'area di lavoro di Log Analytics in una sottoscrizione di Azure, in modo da poter raccogliere i log da diverse sottoscrizioni di Azure.

L'esempio seguente illustra come:

1.	Elencare gli account di archiviazione esistenti e i percorsi da cui Log Analytics eseguirà l'indicizzazione dei dati
2.	Creare una nuova configurazione che possa essere letta da un account di archiviazione
3.	Aggiornare la configurazione appena creata in modo da poter indicizzare i dati anche da altri percorsi
4.	Eliminare la configurazione appena creata

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two with the storage account resource id and the storage account key for the storage account you want to Log Analytics to  
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

## Passaggi successivi

- Per altre informazioni sull'utilizzo di PowerShell per la configurazione di Log Analytics, vedere l'articolo sui [cmdlet di PowerShell per Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx).

<!---HONumber=AcomDC_0518_2016-->