---
title: "Attività Lookup in Azure Data Factory | Microsoft Docs"
description: "Informazioni su come usare l'attività Lookup per cercare un valore da un'origine esterna. Questo output può essere referenziato ulteriormente dalle attività successive."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: spelluru
ms.openlocfilehash: f287b0287ad85ffe1654e0d574cd44aa4dd81a0f
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Attività Lookup in Azure Data Factory
L'attività Lookup può essere usata per la lettura o la ricerca di un record/nome di tabella/valore da qualsiasi origine esterna. Questo output può essere referenziato ulteriormente dalle attività successive. 

L'attività Lookup risulta utile quando si vuole recuperare in modo dinamico un elenco di file/record/tabelle da un file di configurazione o un'origine dati. L'output dall'attività può essere usato anche da altre attività per eseguire l'elaborazione specifica solo di tali elementi.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Per l'attività Lookup sono attualmente supportate le origini dati seguenti:
- File JSON nel BLOB di Azure
- File JSON nel File System
- Database SQL di Azure (dati JSON convertiti da query)
- Azure SQL Data Warehouse (dati JSON convertiti da query)
- SQL Server (dati JSON convertiti da query)
- Archiviazione tabelle di Azure (dati JSON convertiti da query)

## <a name="syntax"></a>Sintassi

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Proprietà del tipo
NOME | DESCRIZIONE | type | Obbligatoria
---- | ----------- | ---- | --------
dataset | L'attributo del set di dati consiste nel fornire il riferimento al set di dati per la ricerca. I tipi di set di dati attualmente supportati sono:<ul><li>`AzureBlobDataset`per [archiviazione Blob di Azure](connector-azure-blob-storage.md#dataset-properties) come origine</li><li>`FileShareDataset`per [File System](connector-file-system.md#dataset-properties) come origine</li><li>`AzureSqlTableDataset`per [Database SQL di Azure](connector-azure-sql-database.md#dataset-properties) o [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#dataset-properties) come origine</li><li>`SqlServerTable`per [SQL Server](connector-sql-server.md#dataset-properties) come origine</li><li>`AzureTableDataset`per [archiviazione tabelle di Azure](connector-azure-table-storage.md#dataset-properties) come origine</li> | coppia chiave/valore | Sì
una sezione source | Proprietà di origine specifici di DataSet, come copia origine dell'attività. Visualizzare i dettagli della sezione "Copiare le proprietà dell'attività" in ogni articolo connettore corrispondente. | Coppia chiave/valore | Sì
firstRowOnly | Indica se restituire solo la prima riga o tutte le righe. | boolean | di serie Il valore predefinito è `ture`.

## <a name="use-lookup-activity-result-in-subsequent-activity"></a>Utilizzare i risultati di ricerca attività nell'attività successiva

Cui viene restituito il risultato di ricerca di `output` sezione il risultato di esecuzione dell'attività.

**Quando `firstRowOnly` è impostato su `true` (impostazione predefinita)**, il formato di output è il seguente. Il risultato di ricerca in fisse `firstRow` chiave. Per utilizzare il risultato nell'attività successiva, utilizzare il modello di `@{activity('MyLookupActivity').output.firstRow.TableName}`.

```json
{
    "firstRow":
    {
        "Id": "1",
        "TableName" : "Table1"
    }
}
```

**Quando `firstRowOnly` è impostato su `false`** , il formato di output è il seguente. Oggetto `count` campo indica il numero di record restituito, e sono valori dettagliati in fisse `value` matrice. In tal caso, l'attività di ricerca è generalmente seguito da un [attività Foreach](control-flow-for-each-activity.md), è possibile passare il `value` matrice in cui l'attività ForEach `items` campo usando il modello di `@activity('MyLookupActivity').output.value`. Per accedere agli elementi nel `value`, utilizzare la seguente sintassi: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Di seguito è riportato un esempio:`@{activity('lookupActivity').output.value[0].tablename}`

```json
{
    "count": "2",
    "value": [
        {
            "Id": "1",
            "TableName" : "Table1"
        },
        {
            "Id": "2",
            "TableName" : "Table2"
        }
    ]
} 
```

## <a name="example"></a>Esempio
In questo esempio, l'attività di copia copia dati da una tabella SQL nel database SQL di Azure in archiviazione Blob di Azure. Il nome della tabella SQL viene archiviato in un file JSON in Archiviazione BLOB. L'attività di ricerca ricerca il nome della tabella in fase di esecuzione. Questo approccio consente di modificare JSON dinamicamente senza ridistribuire le pipeline o i set di dati. 

In questo esempio viene prima riga solo la ricerca. Per cercare tutte le righe e catena con attività ForEach, vedere [esercitazione - copia di dati in blocco](tutorial-bulk-copy.md) esempio.

### <a name="pipeline"></a>Pipeline
Questa pipeline contiene due attività: **Lookup** e **Copy**. 

- L'attività Lookup è configurata per usare LookupDataset, che fa riferimento a un percorso di Archiviazione BLOB di Azure. L'attività Lookup legge il nome della tabella SQL da un file JSON in questo percorso. 
- L'attività Copy usa l'output dell'attività Lookup (nome della tabella SQL). Nel set di dati di origine (SourceDataset) TableName è configurato per usare l'output dall'attività Lookup. L'attività Copy copia i dati dalla tabella SQL in un percorso di Archiviazione BLOB di Azure specificato da SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Set di dati di ricerca
Il set di dati di ricerca fa riferimento al file sourcetable.json nella cartella di ricerca in Archiviazione di Azure specificata da AzureStorageLinkedService. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>Set di dati di origine per l'attività Copy
Il set di dati di origine usa l'output dell'attività Lookup, ovvero il nome della tabella SQL. L'attività Copy copia i dati da questa tabella SQL in un percorso di Archiviazione BLOB di Azure specificato dal set di dati sink. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Set di dati sink per l'attività Copy
L'attività Copy copia i dati dalla tabella SQL nel file filebylookup.csv file nella cartella csv in Archiviazione di Azure specificata da AzureStorageLinkedService. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Questo account di archiviazione contiene il file JSON con i nomi delle tabelle SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Servizio collegato per il database SQL Azure
Il database SQL di Azure contiene i dati da copiare in Archiviazione BLOB. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Set di oggetti

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>Matrice di oggetti

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Web](control-flow-web-activity.md)
