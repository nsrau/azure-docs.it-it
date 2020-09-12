---
title: Attività Lookup in Azure Data Factory
description: Informazioni su come usare l'attività Lookup per cercare un valore da un'origine esterna. Questo output può essere usato ulteriormente come riferimento dalle attività successive.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: e75921e5ee5b148d81c637800f46403d3d410f42
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613479"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Attività Lookup in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'attività Lookup consente di recuperare un set di dati da qualsiasi origine dati supportata da Azure Data Factory. Usarla nel seguente scenario:
- Determinare in modo dinamico gli oggetti su cui operare in un'attività successiva, anziché impostare come hardcoded il nome dell'oggetto. Alcuni esempi di oggetti sono file e tabelle.

L'attività Lookup legge e restituisce il contenuto di un file di configurazione o una tabella. Restituisce anche il risultato dell'esecuzione di una query o di una stored procedure. L'output dall'attività Lookup può essere usato in un'attività di trasformazione o di copia successiva se è un valore singleton. Può essere usato in un'attività ForEach se è una matrice di attributi.

## <a name="supported-capabilities"></a>Funzionalità supportate

Per l'attività Lookup attualmente sono supportate le origini dati seguenti. 

L'attività Lookup può restituire fino a 5000 righe; Se il set di risultati contiene più record, verranno restituite le prime 5000 righe. L'output dell'attività di ricerca supporta fino a 2 MB di dimensioni. l'attività avrà esito negativo se le dimensioni superano il limite. Attualmente, la durata massima per l'attività Lookup prima del timeout è un'ora.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Sintassi

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Proprietà del tipo

Nome | Descrizione | Type | Obbligatorio?
---- | ----------- | ---- | --------
dataset | Fornisce il riferimento al set di dati per la ricerca. Per i dettagli, vedere la sezione **Proprietà del set di dati** nell'articolo del connettore corrispondente. | Coppia chiave/valore | Sì
source | Contiene proprietà di origine specifiche del set di dati, come per l'origine dell'attività Copy. Visualizzare i dettagli della sezione **Proprietà dell'attività di copia** nell'articolo del connettore corrispondente. | Coppia chiave/valore | Sì
firstRowOnly | Indica se restituire solo la prima riga o tutte le righe. | Boolean | No. Il valore predefinito è `true`.

> [!NOTE]
> 
> * Le colonne Source con tipo **ByteArray** non sono supportate.
> * **Structure** non è supportato nella definizione del set di dati. Per i file in formato testo, è possibile usare la riga di intestazione per specificare il nome della colonna.
> * Se l'origine della ricerca è un file JSON, l'impostazione `jsonPathDefinition` per la modifica della forma dell'oggetto JSON non è supportata. Vengono recuperati gli interi oggetti.

## <a name="use-the-lookup-activity-result"></a>Usare il risultato dell'attività di ricerca

Il risultato della ricerca viene restituito nella sezione `output` del risultato dell'esecuzione attività.

* **Se `firstRowOnly` è impostato su `true` (impostazione predefinita)** , il formato di output è mostrato nel codice seguente. Il risultato della ricerca è sotto una chiave `firstRow` fissa. Per usare il risultato nell'attività successiva, usare il modello di  `@{activity('LookupActivity').output.firstRow.table` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **Se `firstRowOnly` è impostato su `false`** , il formato di output è mostrato nel codice seguente. Un campo `count` indica il numero di record restituiti. I valori dettagliati sono visualizzati sotto una matrice `value` fissa. In questo caso l'attività Lookup è seguita da un'[attività ForEach](control-flow-for-each-activity.md). Per passare l'array `value` al campo `items` dell'attività ForEach, usare il criterio di `@activity('MyLookupActivity').output.value`. Per accedere agli elementi nell'array `value`, usare la sintassi seguente: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Un esempio è `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Esempio

In questo esempio la pipeline contiene due attività: **Lookup** e **Copy**. L'attività di copia copia i dati da una tabella SQL nell'istanza del database SQL di Azure all'archivio BLOB di Azure. Il nome della tabella SQL viene archiviato in un file JSON in Archiviazione BLOB. L'attività Lookup cerca il nome della tabella in fase di esecuzione. Il file JSON viene modificato in modo dinamico usando questo approccio. Non è necessario ridistribuire le pipeline o i set di dati. 

In questo esempio viene illustrata la ricerca solo per la prima riga. Per la ricerca per tutte le righe e per concatenare i risultati con l'attività ForEach, vedere gli esempi in [Copiare più tabelle in blocco con Azure Data Factory](tutorial-bulk-copy.md).


### <a name="pipeline"></a>Pipeline

- L'attività Lookup è configurata per usare **LookupDataset**, che fa riferimento a un percorso di Archiviazione BLOB di Azure. L'attività Lookup legge il nome della tabella SQL da un file JSON in questo percorso. 
- L'attività Copy usa l'output dell'attività Lookup, che è il nome della tabella SQL. La proprietà **tableName** in **SourceDataset** è configurata in modo da usare l'output dell'attività Lookup. L'attività Copy copia i dati dalla tabella SQL in un percorso di Archiviazione BLOB di Azure. Il percorso è specificato dalla proprietà **SinkDataset**. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Set di dati di ricerca

Il set di dati di **ricerca** è il **sourcetable.js** file nella cartella di ricerca di archiviazione di Azure specificata dal tipo **AzureBlobStorageLinkedService** . 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>Set di dati di **origine** per l'attività Copy

Il set di dati di **origine** usa l'output dell'attività Lookup, ovvero il nome della tabella SQL. L'attività Copy copia i dati da questa tabella SQL in un percorso di Archiviazione BLOB di Azure. Il percorso è specificato dal set di dati **sink**. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Set di dati **sink** per l'attività Copy

L'attività Copy copia i dati dalla tabella SQL nel file **filebylookup.csv** file della cartella **csv** in Archiviazione di Azure. Il file è specificato dalla proprietà **AzureBlobStorageLinkedService** . 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

È possibile utilizzare i due tipi di formati seguenti per **sourcetable.jssu** file.

#### <a name="set-of-objects"></a>Set di oggetti

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Matrice di oggetti

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Limitazioni e soluzioni alternative

Di seguito vengono descritte alcune limitazioni dell'attività Lookup con le soluzioni alternative suggerite.

| Limitazione | Soluzione alternativa |
|---|---|
| Per l'attività Lookup sono previsti un massimo di 5.000 righe e dimensioni massime di 2 MB. | Progettare una pipeline a due livelli in cui la pipeline esterna esegue l'iterazione su una pipeline interna, che recupera i dati che non superano il numero massimo di righe o le dimensioni massime. |
| | |

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Web](control-flow-web-activity.md)
