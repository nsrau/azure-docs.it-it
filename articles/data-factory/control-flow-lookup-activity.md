---
title: Attività Lookup in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare l'attività Lookup per cercare un valore da un'origine esterna. Questo output può essere referenziato ulteriormente dalle attività successive.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: shlo
ms.openlocfilehash: b6c2e2b685855455550612abb58ada6a694bbdff
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011527"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Attività Lookup in Azure Data Factory

L'attività di ricerca può essere usata per recuperare un set di dati da qualsiasi origine dati supportata da file di definizione dell'applicazione (AFD).  Può essere usata nel seguente scenario:
- Determinare in modo dinamico gli oggetti (file, tabelle e così via) su cui operare in un'attività successiva, anziché impostare come hardcoded il nome dell'oggetto

L'attività di ricerca può leggere e restituire il contenuto di un file di configurazione, di una tabella di configurazione o il risultato dell'esecuzione di una query o di una stored procedure.  L'output dall'attività di ricerca può essere usato in un'attività di trasformazione o di copia successive se è un valore singleton, o in un'attività ForEach se è una matrice di attributi.

> [!NOTE]
> Questo articolo è applicabile alla versione 2 di Azure Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Per l'attività di ricerca attualmente sono supportate le origini dati seguenti. Il numero massimo di righe restituite dall'attività di ricerca è **5000** con una dimensione massima di **2 MB**. E la durata massima per l'attività di ricerca prima del timeout è attualmente un'ora.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

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
dataset | Fornisce il riferimento al set di dati per la ricerca. Per i dettagli, vedere la sezione "Proprietà del set di dati" nell'articolo del connettore corrispondente. | Coppia chiave/valore | Sì
una sezione source | Contiene proprietà di origine specifiche del set di dati, come per l'origine dell'attività Copy. Visualizzare i dettagli della sezione "Proprietà dell'attività di copia" nell'articolo del connettore corrispondente. | Coppia chiave/valore | Sì
firstRowOnly | Indica se restituire solo la prima riga o tutte le righe. | boolean | di serie Il valore predefinito è `true`.

**Tenere presente quanto segue:**

1. La colonna Source con tipo ByteArray non è supportata.
2. Structure non è supportato nella definizione del set di dati. In particolare per i file in formato testo, è possibile usare la riga di intestazione per specificare il nome della colonna.
3. Se l'origine di ricerca è un file JSON, l'impostazione `jsonPathDefinition` per rieseguire il data shaping dell'oggetto JSON non è supportata. Verranno recuperati gli interi oggetti.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Usare il risultato dell'attività Lookup in un'attività successiva

Il risultato della ricerca viene restituito nella sezione `output` del risultato dell'esecuzione attività.

* **Se `firstRowOnly` è impostato su `true` (impostazione predefinita)**, il formato di output è mostrato nel codice seguente. Il risultato della ricerca è sotto una chiave `firstRow` fissa. Per usare il risultato in un'attività successiva, usare il criterio di `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Se `firstRowOnly` è impostato su `false`** , il formato di output è mostrato nel codice seguente. Un campo `count` indica il numero di record restituiti. I valori dettagliati sono visualizzati in un array `value` fisso. In tal caso, l'attività Lookup in genere è seguita da un'[attività ForEach](control-flow-for-each-activity.md). È possibile passare l'array `value` al campo `items` dell'attività ForEach usando il formato di `@activity('MyLookupActivity').output.value`. Per accedere agli elementi nell'array `value`, usare la sintassi seguente: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Di seguito è fornito un esempio: `@{activity('lookupActivity').output.value[0].tablename}`.

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
In questo esempio, l'attività Copy copia i dati da una tabella SQL dell'istanza del database SQL di Azure in Archiviazione BLOB di Azure. Il nome della tabella SQL viene archiviato in un file JSON in Archiviazione BLOB. L'attività Lookup cerca il nome della tabella in fase di esecuzione. Questo approccio consente di modificare JSON dinamicamente senza dover ridistribuire le pipeline o i set di dati. 

In questo esempio viene illustrata la ricerca solo per la prima riga. Per la ricerca per tutte le righe e per concatenare i risultati con l'attività ForEach, vedere gli esempi in [Copiare più tabelle in blocco con Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Questa pipeline contiene due attività: *Lookup* e *Copy*. 

- L'attività Lookup è configurata per usare LookupDataset, che fa riferimento a un percorso di Archiviazione BLOB di Azure. L'attività Lookup legge il nome della tabella SQL da un file JSON in questo percorso. 
- L'attività Copy usa l'output dell'attività Lookup (nome della tabella SQL). Nel set di dati di origine (SourceDataset) la proprietà TableName è configurata per usare l'output dall'attività Lookup. L'attività Copy copia i dati dalla tabella SQL in un percorso di Archiviazione BLOB di Azure specificato dalla proprietà SinkDataset. 


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
Il set di dati di ricerca fa riferimento al file *sourcetable.json* nella cartella di ricerca in Archiviazione di Azure specificata dal tipo AzureStorageLinkedService. 

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
L'attività Copy copia i dati dalla tabella SQL nel file *filebylookup.csv* nella cartella *csv* in Archiviazione di Azure specificata dalla proprietà AzureStorageLinkedService. 

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
L'istanza del database SQL di Azure contiene i dati da copiare in Archiviazione BLOB. 

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
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Web](control-flow-web-activity.md)
