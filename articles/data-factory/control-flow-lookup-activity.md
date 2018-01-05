---
title: "Attività Lookup in Azure Data Factory | Microsoft Docs"
description: "Informazioni su come utilizzare l'attività di ricerca per cercare un valore da un'origine esterna. Questo output può essere referenziato ulteriormente dalle attività successive."
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
ms.openlocfilehash: 3c4f401682e5d1789c6e15597ced145a230bbcd6
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Attività Lookup in Azure Data Factory
È possibile utilizzare l'attività di ricerca per la lettura o cercare un record, un nome di tabella o un valore di qualsiasi origine esterna. Questo output può essere referenziato ulteriormente dalle attività successive. 

Attività di ricerca risulta utile quando si desidera recuperare in modo dinamico un elenco di file, record o le tabelle da un file di configurazione o un'origine dati. L'output dall'attività può essere usato anche da altre attività per eseguire l'elaborazione specifica solo di tali elementi.

> [!NOTE]
> Questo articolo è applicabile alla versione 2 di Azure Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Le origini dati seguenti sono attualmente supportate per la ricerca:
- File JSON nell'archiviazione Blob di Azure
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
NOME | DESCRIZIONE | type | Obbligatorio?
---- | ----------- | ---- | --------
dataset | Fornisce il set di dati riferimento per la ricerca. I tipi di set di dati attualmente supportati sono:<ul><li>`AzureBlobDataset`per [archiviazione Blob di Azure](connector-azure-blob-storage.md#dataset-properties) come origine</li><li>`FileShareDataset`per [File System](connector-file-system.md#dataset-properties) come origine</li><li>`AzureSqlTableDataset`per [Database SQL di Azure](connector-azure-sql-database.md#dataset-properties) o [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#dataset-properties) come origine</li><li>`SqlServerTable`per [SQL Server](connector-sql-server.md#dataset-properties) come origine</li><li>`AzureTableDataset`per [archiviazione tabelle di Azure](connector-azure-table-storage.md#dataset-properties) come origine</li> | Coppia chiave/valore | Sì
una sezione source | Contiene le proprietà di origine specifici di dataset, la stessa origine di attività di copia. Recuperare i dettagli della sezione "Copiare le proprietà dell'attività" in ogni articolo connettore corrispondente. | Coppia chiave/valore | Sì
firstRowOnly | Indica se restituire solo la prima riga o tutte le righe. | boolean | di serie Il valore predefinito è `true`.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Usare il risultato di attività di ricerca in un'attività successiva

Cui viene restituito il risultato di ricerca di `output` sezione il risultato di esecuzione dell'attività.

* **Quando `firstRowOnly` è impostato su `true` (impostazione predefinita)**, il formato di output è illustrato nel codice seguente. Il risultato di ricerca in fisse `firstRow` chiave. Per utilizzare il risultato nell'attività successiva, utilizzare il modello di `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quando `firstRowOnly` è impostato su `false`** , il formato di output è illustrato nel codice seguente. Oggetto `count` campo indica il numero di record restituito, e vengono visualizzati valori dettagliati in fisse `value` matrice. In tal caso, l'attività di ricerca è generalmente seguito da un [attività Foreach](control-flow-for-each-activity.md). È possibile passare il `value` matrice in cui l'attività ForEach `items` campo utilizzando il modello di `@activity('MyLookupActivity').output.value`. Per accedere agli elementi nel `value` matrice, usare la sintassi seguente: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Di seguito è riportato un esempio: `@{activity('lookupActivity').output.value[0].tablename}`.

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
In questo esempio, l'attività di copia copia dati da una tabella SQL nell'istanza di Database SQL di Azure in archiviazione Blob di Azure. Il nome della tabella di SQL viene archiviato in un file JSON nell'archiviazione Blob. L'attività Lookup cerca il nome della tabella in fase di esecuzione. Questo approccio consente di modificare dinamicamente senza ridistribuire la pipeline o set di dati JSON. 

Questo esempio illustra una ricerca per la prima riga. Per la ricerca per tutte le righe e per concatenare i risultati con attività ForEach, vedere gli esempi in [copiare più tabelle in blocco usando Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Questa pipeline contiene due attività: *ricerca* e *copia*. 

- L'attività di ricerca è configurata per utilizzare LookupDataset, che fa riferimento a un percorso di archiviazione Blob di Azure. L'attività Lookup legge il nome della tabella SQL da un file JSON in questo percorso. 
- L'attività di copia viene utilizzato l'output dell'attività di ricerca (nome della tabella di SQL). La proprietà tableName del set di dati di origine (SourceDataset) è configurata per utilizzare l'output dall'attività di ricerca. L'attività di copia copia i dati dalla tabella SQL a un percorso di archiviazione Blob di Azure specificato dalla proprietà SinkDataset. 


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
Il set di dati di ricerca si intende il *sourcetable.json* file nella cartella di ricerca di archiviazione di Azure specificato dal tipo AzureStorageLinkedService. 

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
Il set di dati di origine usa l'output dell'attività Lookup, ovvero il nome della tabella SQL. L'attività di copia copia dati da questa tabella SQL in una posizione di archiviazione Blob di Azure specificata dal set di dati sink. 

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
L'attività di copia copia i dati dalla tabella di SQL la *filebylookup.csv* file nel *csv* cartella nello spazio di archiviazione Azure è specificato dalla proprietà AzureStorageLinkedService. 

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
Questa istanza di Database SQL di Azure contiene i dati da copiare nell'archiviazione Blob. 

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
Vedere altre attività del flusso di controllo supportati da Data Factory: 

- [Eseguire l'attività di Pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere attività metadati](control-flow-get-metadata-activity.md)
- [Attività Web](control-flow-web-activity.md)
