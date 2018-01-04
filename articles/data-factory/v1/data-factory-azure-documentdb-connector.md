---
title: Spostare dati da e verso Azure Cosmos DB | Microsoft Docs
description: Informazioni su come spostare i dati da e verso la raccolta di Azure Cosmos DB mediante Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0338fb386fc4da3f34cb4e810dbd57d50b5d5329
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Spostare dati da e verso il BLOB di Azure mediante Data factory di Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-azure-documentdb-connector.md)
> * [Versione 2 - Anteprima](../connector-azure-cosmos-db.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Connettore Azure Cosmos DB nella versione 2](../connector-azure-cosmos-db.md).

In questo articolo viene illustrato come utilizzare l'attività di copia in Data Factory di Azure per spostare i dati da e verso Azure Cosmos database (SQL API). Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia. 

È possibile copiare i dati da qualsiasi archivio dati di origine supportato ad Azure Cosmos DB o da Azure Cosmos DB a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 

> [!IMPORTANT]
> Azure Cosmos DB connector supporta solo l'API di SQL.

Per copiare i dati così come sono da e verso i file JSON o un'altra raccolta Cosmos DB, vedere [Importare/esportare documenti JSON](#importexport-json-documents).

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso Azure Cosmos DB usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink: 

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per esempi con definizioni JSON per entità di data factory utilizzate per copiare i dati da e verso Cosmos DB, vedere la sezione degli [esempi JSON](#json-examples) in questo articolo. 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di Data factory specifiche di Cosmos DB: 

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di Azure Cosmos DB.

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| --- | --- | --- |
| type |La proprietà del tipo deve essere impostata su: **DocumentDb** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi al database di Azure Cosmos DB. |Sì |

Esempio:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, fare riferimento all'articolo [Creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **DocumentDbCollection** presenta le proprietà seguenti.

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| --- | --- | --- |
| collectionName |Nome della raccolta documenti di Cosmos DB. |Sì |

Esempio:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Schema da Data Factory
Per gli archivi di dati privi di schema, ad esempio Azure Cosmos DB, il servizio Data Factory deduce lo schema in uno dei modi seguenti:  

1. Se si specifica la struttura dei dati tramite la proprietà **structure** nella definizione del set di dati, il servizio Data Factory considera la struttura come schema. In questo caso, se una riga non contiene un valore per una colonna, verrà inserito un valore null.
2. Se non si specifica la struttura dei dati usando la proprietà **structure** nella definizione del set di dati, il servizio Data Factory deduce lo schema usando la prima riga di dati. In questo caso, se la prima riga non contiene lo schema completo, alcune colonne non saranno presenti nel risultato dell'operazione di copia.

Di conseguenza, per le origini dati prive di schema, la procedura consigliata consiste nello specificare la struttura dei dati usando la proprietà **structure** .

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [!NOTE]
> L'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia con origine di tipo **DocumentDbCollectionSource**, sono disponibili le proprietà seguenti nella sezione **typeProperties**:

| **Proprietà** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| --- | --- | --- | --- |
| query |Specificare la query per leggere i dati. |Stringa di query supportata da Azure Cosmos DB. <br/><br/>Esempio: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No  <br/><br/>Se non specificato, l'istruzione SQL eseguita: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Carattere speciale per indicare che il documento è nidificato |Qualsiasi carattere. <br/><br/>Azure Cosmos DB è un archivio NoSQL per i documenti JSON, dove sono consentite strutture nidificate. Azure Data Factory consente di indicare una gerarchia tramite nestingSeparator, ovvero "." negli esempi precedenti. Con il separatore, l'attività copia genererà l'oggetto "Name" con tre elementi figlio First, Middle e Last, in base a "Name.First", "Name.Middle" e "Name.Last" nella definizione della tabella. |No  |

**DocumentDbCollectionSink** supporta le proprietà seguenti:

| **Proprietà** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| --- | --- | --- | --- |
| nestingSeparator |È necessario un carattere speciale nel nome della colonna di origine per indicare tale documento nidificato. <br/><br/>Per l'esempio sopra: `Name.First` nella tabella di output produce la struttura JSON seguente nel documento di Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Carattere utilizzato per separare i livelli di nidificazione.<br/><br/>Il valore predefinito è `.` (punto). |Carattere utilizzato per separare i livelli di nidificazione. <br/><br/>Il valore predefinito è `.` (punto). |
| writeBatchSize |Numero di richieste in parallelo per il servizio Azure Cosmos DB per creare documenti.<br/><br/>È possibile ottimizzare le prestazioni quando si copiano dati da e verso Cosmos DB usando questa proprietà. È possibile prevedere prestazioni migliori quando si aumenta writeBatchSize, poiché vengono inviate più richieste in parallelo a Cosmos DB. Tuttavia è necessario evitare la limitazione che può generare il messaggio di errore: "La frequenza delle richieste è troppo elevata".<br/><br/>La limitazione è dovuta a diversi fattori, inclusi la dimensione dei documenti, il numero di termini nei documenti, i criteri di indicizzazione della raccolta di destinazione, ecc. Per le operazioni di copia, è possibile usare una raccolta migliore, ad esempio S3, per disporre della massima velocità effettiva disponibile, ovvero 2500 unità di richiesta al secondo. |Integer |No (valore predefinito: 5) |
| writeBatchTimeout |Tempo di attesa per il completamento dell’operazione prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No  |

## <a name="importexport-json-documents"></a>Importare/esportare documenti JSON
Usando questo connettore Cosmos DB, è possibile:

* Importare i documenti JSON da diverse origini in Cosmos DB, tra cui BLOB di Azure, Azure Data Lake, file system locale o altri archivi di file supportati da Azure Data Factory.
* Esportare i documenti JSON da raccolte Cosmos DB in diversi archivi basati su file.
* Eseguire la migrazione dei dati tra due raccolte Cosmos DB così come sono.

Per ottenere la copia senza schema, 
* Quando si usa la copia guidata, controllare l'opzione **"Export as-is to JSON files or Cosmos DB collection"** (Esportare così come sono nel file JSON o in una raccolta di Cosmos DB).
* Quando si usa la modifica JSON, non specificare la sezione "struttura" nel set di dati di Cosmos DB o né la proprietà "nestingSeparator" nell'origine/sink di Cosmos DB nell'attività di copia. Per importare/esportare verso i file JSON, nel set di dati dell'archivio file specificare il tipo di formato come "JsonFormat", la configurazione come "filePattern" e ignorare le altre impostazioni del formato, vedere la sezione [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format) per informazioni dettagliate.

## <a name="json-examples"></a>Esempi JSON
Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tali esempi mostrano come copiare dati da e verso Azure Cosmos DB e Archivio BLOB di Azure. I dati possono anche essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Esempio: Copiare dati da Azure Cosmos DB a BLOB di Azure
L'esempio seguente mostra:

1. Un servizio collegato di tipo [DocumentDB](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [DocumentDbCollection](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [DocumentDbCollectionSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia dati da Azure Cosmos DB a BLOB di Azure. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Servizio collegato di archiviazione BLOB di Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Set di dati di input di Azure DocumentDB:**

L'esempio presuppone che sia presente una raccolta denominata **Person** in un database di Azure Cosmos DB.

Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data factory di Azure che la tabella è esterna e non è prodotta da un'attività al suo interno.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Set di dati di output del BLOB di Azure:**

I dati vengono copiati in un nuovo BLOB ogni ora e il percorso del BLOB riflette la data e l'ora specifiche con granularità oraria.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Documento JSON di esempio nella raccolta Person in un database di Cosmos DB:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB supporta l’esecuzione di query di documenti utilizzando una sintassi come SQL su documenti JSON gerarchici.

Esempio: 

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

La pipeline seguente copia i dati dalla raccolta Person nel database di Azure Cosmos DB a un BLOB di Azure. Come parte dell'attività di copia, i set di dati di input e output sono stati specificati.  

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Esempio: Copiare dati da BLOB di Azure ad Azure Cosmos DB 
L'esempio seguente mostra:

1. Un servizio collegato di tipo [DocumentDB](#azure-documentdb-linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).

L'esempio copia dati da BLOB di Azure ad Azure Cosmos DB. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di archiviazione BLOB di Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Servizio collegato di Azure Cosmos DB:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Set di dati di input del BLOB di Azure:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Set di dati di output di Azure Cosmos DB:**

Nell'esempio vengono copiati dati a una raccolta denominata "Person".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
La pipeline seguente copia i dati dal BLOB di Azure alla raccolta Person in Cosmos DB. Come parte dell'attività di copia, i set di dati di input e output sono stati specificati.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          }
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Se l’input BLOB d’esempio è come

```
1,John,,Doe
```
Quindi l'output JSON in Cosmos DB sarà analogo al seguente:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB è un archivio NoSQL per i documenti JSON, dove sono consentite strutture nidificate. Azure Data Factory consente di indicare una gerarchia tramite **nestingSeparator**, ovvero "." in questo esempio. Con il separatore, l'attività copia genererà l'oggetto "Name" con tre elementi figlio First, Middle e Last, in base a "Name.First", "Name.Middle" e "Name.Last" nella definizione della tabella.

## <a name="appendix"></a>Appendice
1. **Domanda:** C’è l'aggiornamento del supporto di attività di copia dei record esistenti?

    **Risposta:** No.
2. **Domanda:** in che modo un nuovo tentativo di copia in Azure Cosmos DB gestisce i record già copiati?

    **Risposta:** se i record dispongono di un campo "ID" e l'operazione di copia tenta di inserire un record con lo stesso ID, l'operazione di copia genera un errore.  
3. **Domanda:** Data Factory supporta il [partizionamento dei dati basato su hash o su intervalli](../../cosmos-db/sql-api-partition-data.md)?

    **Risposta:** No.
4. **Domanda:** è possibile specificare più raccolte di Azure Cosmos DB per una tabella?

    **Risposta:** No. In questo momento, è possibile specificare solo una raccolta.

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).
