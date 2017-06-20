---
title: Mapping delle colonne del set di dati in Azure Data Factory | Documentazione Microsoft
description: Informazioni su come eseguire il mapping delle colonne di origine alle colonne di destinazione.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 444ac59b0b21795228e08f79a25a638d72403399
ms.contentlocale: it-it
ms.lasthandoff: 03/29/2017


---

# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati di destinazione
Mapping di colonne utilizzabile per specificare come le colonne specificate nella "struttura" di mapping della tabella di origine alle colonne specificate nella "struttura" della tabella di sink. La proprietà **columnMapping** è disponibile nella sezione **typeProperties** dell'attività di copia.

Il mapping di colonne supporta gli scenari seguenti:

* Tutte le colonne della struttura del set di dati di origine vengono mappate a tutte le colonne della struttura del set di dati del sink.
* Un sottoinsieme delle colonne nella struttura del set di dati di origine viene mappato a tutte le colonne della struttura del set di dati del sink.

Le seguenti sono condizioni di errore che generano un'eccezione:

* Un numero inferiore o superiore di colonne nella "struttura" della tabella di sink di quanto specificato nel mapping.
* Mapping duplicato.
* Il risultato della query SQL non ha un nome colonna specificato nel mapping.

> [!NOTE]
> Gli esempi che seguono sono per SQL Azure e Azure Blob, ma sono applicabili per qualsiasi archivio dati che supporti set di dati rettangolari. Modificare i set di dati e le definizioni dei servizi collegati negli esempi per puntare a dati nell'origine dati pertinente.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Esempio 1: mapping di colonne da Azure SQL al BLOB di Azure
In questo esempio, la tabella di input ha una struttura che punta a una tabella SQL in un database SQL di Azure.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

In questo esempio, la tabella di output ha una struttura che punta a un BLOB in un'archiviazione BLOB di Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Il codice JSON seguente definisce un'attività di copia in una pipeline. Le colonne dell'origine vengono mappate alle colonne del sink (**columnMappings**) usando la proprietà **Translator**.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Flusso del mapping di colonne:**

![Flusso del mapping di colonne](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Esempio 2: mapping di colonne con query SQL da Azure SQL al BLOB di Azure
In questo esempio, una query SQL viene utilizzata per estrarre dati da Azure SQL invece di specificare semplicemente il nome della tabella e i nomi delle colonne nella sezione "struttura". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
In questo caso, i risultati della query vengono prima mappati alle colonne specificate in "struttura" di origine. Successivamente, le colonne di origine "struttura" vengono mappate alle colonne nel sink "struttura" con le regole specificate nel columnMappings.  Si supponga che la query restituisca 5 colonne, due in più di quelle specificate nella "struttura" dell'origine.

**Flusso del mapping di colonne**

![Flusso del mapping di colonne-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo per un'esercitazione sull'uso dell'attività di copia: 

- [Copiare dati da un archivio BLOB a un database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

