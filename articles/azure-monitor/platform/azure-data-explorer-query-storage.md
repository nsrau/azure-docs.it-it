---
title: Eseguire query sui dati esportati da monitoraggio di Azure con Esplora dati di Azure (anteprima)
description: Usare Esplora dati di Azure per eseguire query sui dati esportati dall'area di lavoro Log Analytics a un account di archiviazione di Azure.
ms.subservice: logs
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2735aec0f95f5e282bb1dffba6e4f42f966cf117
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186542"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Eseguire query sui dati esportati da monitoraggio di Azure con Esplora dati di Azure (anteprima)
L'esportazione di dati da monitoraggio di Azure a un account di archiviazione di Azure consente la conservazione a basso costo e la possibilità di riallocare i log in aree diverse. Usare Esplora dati di Azure per eseguire query sui dati esportati dalle aree di lavoro di Log Analytics. Una volta configurate, le tabelle supportate inviate dalle aree di lavoro a un account di archiviazione di Azure saranno disponibili come origine dati per Esplora dati di Azure.

Il flusso del processo è il seguente: 

1.  Esportare i dati dall'area di lavoro Log Analytics all'account di archiviazione di Azure.
2.  Creare una tabella esterna nel cluster di Esplora dati di Azure ed eseguendo il mapping per i tipi di dati.
3.  Eseguire query sui dati da Azure Esplora dati.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Il flusso di query dei dati esportati Esplora dati di Azure.":::



## <a name="send-data-to-azure-storage"></a>Inviare dati ad archiviazione di Azure
I log di monitoraggio di Azure possono essere esportati in un account di archiviazione di Azure usando una delle opzioni seguenti.

- Per esportare tutti i dati dall'area di lavoro di Log Analytics a un account di archiviazione di Azure o a un hub eventi, usare la funzionalità di esportazione dei dati Log Analytics area di lavoro dei log di monitoraggio di Azure Vedere [log Analytics l'esportazione dei dati dell'area di lavoro in monitoraggio di Azure (anteprima)](logs-data-export.md)
- Esportazione pianificata da una query di log tramite un'app per la logica. Questa operazione è simile alla funzionalità di esportazione dei dati, ma consente di inviare dati filtrati o aggregati in archiviazione di Azure. Questo metodo, tuttavia, è soggetto ai [limiti della query di log](../service-limits.md#log-analytics-workspaces) . vedere [archiviare i dati dall'area di lavoro log Analytics ad archiviazione di Azure usando l'app](logs-export-logic-app.md)
- Esportare una volta usando un'app per la logica. Vedere [connettore dei log di monitoraggio di Azure per app per la logica e automazione dell'alimentazione](logicapp-flow-connector.md).
- Esportare una volta nel computer locale usando lo script di PowerShell. Vedere [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> È possibile usare un cluster di Azure Esplora dati esistente o creare un nuovo cluster dedicato con le configurazioni necessarie.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Creare una tabella esterna che si trova nell'archivio BLOB di Azure
Usare le [tabelle esterne](/azure/data-explorer/kusto/query/schema-entities/externaltables) per collegare Esplora dati di Azure a un account di archiviazione di Azure. Una tabella esterna è un'entità dello schema kusto che fa riferimento ai dati archiviati all'esterno di un database di Kusto. Analogamente alle tabelle, una tabella esterna presenta uno schema ben definito. Diversamente dalle tabelle, i dati vengono archiviati e gestiti all'esterno di un cluster kusto. I dati esportati dalla sezione precedente vengono salvati in righe JSON.

Per creare un riferimento, è necessario lo schema della tabella esportata. Utilizzare l'operatore [GetSchema](/azure/data-explorer/kusto/query/getschemaoperator) da log Analytics per recuperare queste informazioni che includono le colonne della tabella e i relativi tipi di dati.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Schema della tabella Log Analytics.":::

È ora possibile usare l'output per creare la query kusto per la compilazione della tabella esterna.
Seguendo le istruzioni riportate in [creare e modificare le tabelle esterne in archiviazione di Azure o in Azure Data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake), creare una tabella esterna in formato JSON e quindi eseguire la query dal database di Esplora dati di Azure.

>[!NOTE]
>La creazione della tabella esterna è costituita da due processi. Il primo consiste nel creare la tabella esterna, mentre la seconda crea il mapping.

Tramite lo script di PowerShell seguente vengono creati i comandi [create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) per la tabella e il mapping.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

Nell'immagine seguente viene illustrato un esempio dell'output.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="ExternalTable creare l'output del comando.":::

[![Output di esempio](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Copiare, incollare e quindi eseguire l'output dello script nello strumento client di Azure Esplora dati per creare la tabella e il mapping.
>* Per usare tutti i dati all'interno del contenitore, modificare lo script e modificare l'URL in modo che sia " https://your.blob.core.windows.net/containername ; SecKey'

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Eseguire query sui dati esportati da Azure Esplora dati 

Dopo aver configurato il mapping, è possibile eseguire una query sui dati esportati da Azure Esplora dati. La query richiede la funzione [external_table](/azure/data-explorer/kusto/query/externaltablefunction) come nell'esempio seguente.

```kusto
external_table("HBTest","map") | take 10000
```

[![Eseguire query Log Analytics dati esportati](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [scrivere query in Azure Esplora dati](/azure/data-explorer/write-queries)