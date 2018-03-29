---
title: Attività Get Metadata in Azure Data Factory | Microsoft Docs
description: Informazioni sull'uso dell'attività di stored procedure di SQL Server da una pipeline di Data factory per richiamare una stored procedure in un database SQL di Azure o in Azure SQL Data Warehouse.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: e8e40b763f0c6f1f994535ab2ff335cfcbf02cf7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Attività Get Metadata in Azure Data Factory
Questa attività può essere usata per recuperare i metadati di tutti i dati in Azure Data Factory. Questa attività è supportata solo per data factory con la versione 2. Può essere usata negli scenari seguenti:

- Convalidare le informazioni sui metadati di tutti i dati
- Attivare una pipeline quando i dati sono pronti o disponibili

La funzionalità seguente è disponibile nel flusso di controllo:
- L'output dall'attività GetMetadata può essere usato nelle espressioni condizionali per eseguire la convalida.
- Una pipeline può essere attivata quando viene soddisfatta una condizione specificata nel ciclo Do-Until

L'attività GetMetadata accetta un set di dati come input obbligatorio e restituisce informazioni sui metadati disponibili come output. Attualmente sono supportati solo i set di dati BLOB di Azure. I campi di metadati supportati sono size, structure e lastModified time.  

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).


## <a name="syntax"></a>Sintassi

### <a name="get-metadata-activity-definition"></a>Definizione dell'attività Get Metadata:
Nell'esempio seguente, l'attività GetMetadata restituisce i metadati relativi ai dati rappresentati da MyDataset. 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>Definizione del set di dati:

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Output
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Proprietà del tipo
Attualmente l'attività GetMetadata può recuperare i tipi di informazioni seguenti dei metadati da un set di dati di archiviazione di Azure.

Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
fieldList | Elenca i tipi di informazioni dei metadati necessarie.  | <ul><li>size</li><li>structure</li><li>LastModified</li></ul> |    No <br/>Se è vuota, l'attività restituisce tutte e 3 le informazioni dei metadati supportate. 
dataset | Set di dati di riferimento la cui attività dei metadati deve essere recuperata dall'attività GetMetadata. <br/><br/>Il tipo di set di dati attualmente supportato è BLOB di Azure. Il set di dati include le due proprietà secondarie seguenti: <ul><li><b>referenceName</b>: riferimento a un set di dati BLOB di Azure esistente</li><li><b>type</b>: poiché si fa riferimento al set di dati, è di tipo "DatasetReference"</li></ul> |    <ul><li>string</li><li>DatasetReference</li></ul> | Sì

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory: 

- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)