---
title: Ottenere l'attività dei metadati in Azure Data FactoryGet Metadata activity in Azure Data Factory
description: Informazioni su come usare l'attività Ottieni metadati in una pipeline di Data Factory.Learn how to use the Get Metadata activity in a Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 344ad8e106c119c1de59570d1ec4e3df5e1cc8af
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417117"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Ottenere l'attività dei metadati in Azure Data FactoryGet Metadata activity in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

È possibile usare l'attività Recupera metadati per recuperare i metadati di tutti i dati in Azure Data Factory.You can use the Get Metadata activity to retrieve the metadata of any data in Azure Data Factory. È possibile utilizzare questa attività negli scenari seguenti:You can use this activity in the following scenarios:

- Convalidare i metadati di tutti i dati.
- Attivare una pipeline quando i dati sono pronti/disponibili.

La funzionalità seguente è disponibile nel flusso di controllo:

- È possibile usare l'output dell'attività Ottieni metadati nelle espressioni condizionali per eseguire la convalida.
- È possibile attivare una pipeline quando una condizione viene soddisfatta tramite il ciclo Do Until.You can trigger a pipeline when a condition is satisfied via Do Until looping.

## <a name="capabilities"></a>Capabilities

L'attività Get Metadata accetta un set di dati come input e restituisce informazioni sui metadati come output. Attualmente sono supportati i connettori seguenti e i metadati recuperabili corrispondenti. La dimensione massima dei metadati restituiti è 2 MB.

>[!NOTE]
>Se si esegue l'attività Ottieni metadati in un runtime di integrazione self-hosted, le funzionalità più recenti sono supportate nella versione 3.6 o successiva.

### <a name="supported-connectors"></a>Connettori supportati

**Archiviazione dei file**

| Connettore/Metadati | itemName<br>(file/cartella) | itemType<br>(file/cartella) | size<br>(file) | created<br>(file/cartella) | LastModified<br>(file/cartella) |childItems<br>(cartella) |contentMD5<br>(file) | structure<br/>(file) | columnCount<br>(file) | esiste<br>(file/cartella) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Archiviazione BLOB di AzureAzure Blob storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [File di Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [File system](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Quando si utilizza l'attività Get Metadata in una cartella, assicurarsi di disporre dell'autorizzazione LIST/EXECUTE per la cartella specificata.
- Per Amazon S3 e `lastModified` Google Cloud Storage, si applica al bucket `exists` e alla chiave ma non alla cartella virtuale e si applica al bucket e alla chiave, ma non al prefisso o alla cartella virtuale.
- Per l'archiviazione `lastModified` BLOB di Azure, si applica al contenitore e al BLOB, ma non alla cartella virtuale.
- `lastModified`filtro attualmente si applica al filtro degli elementi figlio, ma non alla cartella o al file specificato.
- Il filtro con caratteri jolly per cartelle/file non è supportato per l'attività Ottieni metadati.

**Database relazionale**

| Connettore/Metadati | structure | columnCount | esiste |
|:--- |:--- |:--- |:--- |
| [Database SQL di Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Istanza gestita di Database SQL di Azure](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opzioni dei metadati

È possibile specificare i seguenti tipi di metadati nell'elenco di campi Attività metadati get per recuperare le informazioni corrispondenti:

| Tipo di metadati | Descrizione |
|:--- |:--- |
| itemName | Nome del file o della cartella. |
| itemType | Tipo di file o di cartella. Il valore `File` restituito `Folder`è o . |
| size | Dimensione del file, in byte. Applicabile solo ai file. |
| created | Data/ora di creazione del file o della cartella. |
| LastModified | Data/ora dell'ultima modifica del file o della cartella. |
| childItems | Elenco delle sottocartelle e dei file nella cartella specificata. Applicabile solo alle cartelle. Valore restituito è un elenco del nome e del tipo di ogni elemento figlio. |
| contentMD5 | MD5 del file. Applicabile solo ai file. |
| structure | Struttura dei dati del file o della tabella di database relazionale. Valore restituito è un elenco di nomi di colonna e tipi di colonna. |
| columnCount | Numero di colonne nel file o nella tabella relazionale. |
| esiste| Se esiste un file, una cartella o una tabella. Si noti che se `exists` viene specificato nell'elenco di campi Ottieni metadati, l'attività non avrà esito negativo anche se il file, la cartella o la tabella non esiste. Al `exists: false` contrario, viene restituito nell'output. |

>[!TIP]
>Per convalidare l'esistito di un `exists` file, di una cartella o di una tabella, specificare nell'elenco dei campi Attività metadati. È quindi possibile `exists: true/false` controllare il risultato nell'output dell'attività. Se `exists` non è specificato nell'elenco dei campi, l'attività Ottieni metadati avrà esito negativo se l'oggetto non viene trovato.

>[!NOTE]
>Quando si ottengono metadati `modifiedDatetimeStart` `modifiedDatetimeEnd`da `childItems` archivi di file e si configura o , l'output in includerà solo i file nel percorso specificato con un'ora dell'ultima modifica all'interno dell'intervallo specificato. In non includerà elementi nelle sottocartelle.

## <a name="syntax"></a>Sintassi

**Ottenere l'attività dei metadatiGet Metadata activity**

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

**Set di dati**

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
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Proprietà del tipo

Attualmente, l'attività Get Metadata può restituire i seguenti tipi di informazioni sui metadati:

Proprietà | Descrizione | Obbligatoria
-------- | ----------- | --------
fieldList | Tipi di informazioni sui metadati necessari. Per informazioni dettagliate sui metadati supportati, vedere la sezione [Opzioni metadati](#metadata-options) di questo articolo. | Sì 
dataset | Set di dati di riferimento i cui metadati devono essere recuperati dall'attività Get Metadata. Vedere la sezione [Funzionalità](#capabilities) per informazioni sui connettori supportati. Fare riferimento agli argomenti specifici relativi ai connettori per informazioni dettagliate sulla sintassi del set di dati. | Sì
formatImpostazioni | Applicare quando si utilizza il set di dati di tipo formato. | No
StoreImpostazioni | Applicare quando si utilizza il set di dati di tipo formato. | No

## <a name="sample-output"></a>Output di esempio

I risultati Di ottenere i metadati vengono visualizzati nell'output dell'attività. Di seguito sono riportati due esempi che illustrano opzioni di metadati estese. Per utilizzare i risultati di un'attività `@{activity('MyGetMetadataActivity').output.itemName}`successiva, utilizzare questo modello: .

### <a name="get-a-files-metadata"></a>Ottenere i metadati di un file

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Ottenere i metadati di una cartella

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni su altre attività del flusso di controllo supportate da Data Factory:Learn about other control flow activities supported by Data Factory:

- [Attività Esegui pipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
