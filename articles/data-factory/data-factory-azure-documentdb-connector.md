<properties 
	pageTitle="Spostare dati da e verso DocumentDB | Data factory di Azure" 
	description="Informazioni su come spostare i dati da e verso la raccolta di Azure DocumentDB mediante Data factory di Azure" 
	services="data-factory, documentdb" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="multiple" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2016" 
	ms.author="spelluru"/>

# Spostare dati da e verso DocumentDB mediante Data factory di Azure

Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati in Azure DocumentDB da un altro archivio dati e spostare i dati da un altro archivio dati a DocumentDB. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Gli esempi seguenti mostrano come copiare dati da e in Azure DocumentDB e nell'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Data factory di Azure.


## Esempio: Copiare i dati da DocumentDB a BLOB di Azure

L'esempio seguente mostra:

1. Un servizio collegato di tipo [DocumentDB](#azure-documentdb-linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio vengono copiati dati da Azure DocumentDB a BLOB di Azure. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di Azure DocumentDB:**

	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Servizio collegato di archiviazione BLOB di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input di Azure DocumentDB:**

L'esempio presuppone di avere una raccolta denominata **Person** in un database di Azure DocumentDB.
 
Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data factory di Azure che la tabella è esterna e non è prodotta da un'attività al suo interno.

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
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


**Set di dati di output del BLOB di Azure:**

I dati vengono copiati in un nuovo BLOB ogni ora e il percorso del BLOB riflette la data e l'ora specifiche con granularità oraria.

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

Documento JSON di esempio nella raccolta Person in un database di DocumentDB:

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

DocumentDB supporta l’esecuzione di query di documenti utilizzando una sintassi come SQL su documenti JSON gerarchici.

Esempio: SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person

La pipeline seguente copia i dati dalla raccolta Person nel database di DocumentDB a un BLOB di Azure. Come parte dell'attività di copia, i set di dati di input e output sono stati specificati.
	
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
	            "name": "PersonDocumentDbTable"
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

## Esempio: Copiare i dati dal BLOB di Azure ad Azure DocumentDB

L'esempio seguente mostra:

1. Un servizio collegato di tipo [DocumentDB](#azure-documentdb-linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


Nell’esempio vengono copiati dati dal BLOB di Azure ad Azure DocumentDB. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di archiviazione BLOB di Azure:**
	
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Servizio collegato di Azure DocumentDB:**
	
	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Set di dati di input del BLOB di Azure:**

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

**Set di dati di output di Azure DocumentDB:**

Nell'esempio vengono copiati dati a una raccolta denominata "Person".

	{
	  "name": "PersonDocumentDbTableOut",
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
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

La pipeline seguente copia i dati dal BLOB di Azure alla raccolta Person in DocumentDB. Come parte dell'attività di copia, i set di dati di input e output sono stati specificati.
	
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
	            "name": "PersonDocumentDbTableOut"
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
 
Se l’input BLOB d’esempio è come

	1,John,,Doe

Quindi l'output JSON in DocumentDB sarà come:

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
DocumentDB è un archivio NoSQL per i documenti JSON, dove sono consentite strutture nidificate. Data factory di Azure consente di indicare una gerarchia tramite **nestingSeparator**, ovvero "." in questo esempio. Con il separatore, l'attività copia genererà l'oggetto "Name" con tre elementi figlio First, Middle e Last, in base a "Name.First", "Name.Middle" e "Name.Last" nella definizione della tabella.

## Proprietà del servizio collegato di Azure DocumentDB

La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di Azure DocumentDB.

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| -------- | ----------- | --------- |
| type | La proprietà del tipo deve essere impostata su: **DocumentDb** | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi al database di Azure DocumentDB. | Sì |

## Proprietà del tipo del set di dati di Azure DocumentDB

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, fare riferimento all'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).
 
La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **DocumentDbCollection** presenta le proprietà seguenti.

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| -------- | ----------- | -------- |
| collectionName | Nome della raccolta documenti di DocumentDB. | Sì |


Esempio:

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
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

### Schema da Data Factory
Per gli archivi di dati privi di schema, ad esempio DocumentDB, il servizio Data Factory deduce lo schema in uno dei modi seguenti:

1.	Se si specifica la struttura dei dati tramite la proprietà **structure** nella definizione del set di dati, il servizio Data Factory considera la struttura come schema. In questo caso, se una riga non contiene un valore per una colonna, verrà inserito un valore null.
2.	Se non si specifica la struttura dei dati tramite la proprietà **structure** nella definizione del set di dati, il servizio Data Factory deduce lo schema usando la prima riga di dati. In questo caso, se la prima riga non contiene lo schema completo, alcune colonne non saranno presenti nel risultato dell'operazione di copia.

Pertanto, per le origini dati prive di schema, la procedura consigliata consiste nello specificare la struttura dei dati usando la proprietà **structure**.

## Proprietà del tipo di attività di copia di Azure DocumentDB

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.
 
**Nota:** l'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia con origine di tipo **DocumentDbCollectionSource**, sono disponibili le proprietà seguenti nella sezione **typeProperties**:

| **Proprietà** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| ------------ | --------------- | ------------------ | ------------ |
| query | Specificare la query per leggere i dati. | Stringa di query supportata da DocumentDB. <p>Esempio: SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00"</p> | No <p> Se non è specificata, corrisponde all'istruzione SQL eseguita: selezionare <columns defined in structure> da mycollection </p>
| nestingSeparator | Carattere speciale per indicare che il documento è nidificato | Qualsiasi carattere. <p>DocumentDB è un archivio NoSQL per i documenti JSON, dove sono consentite strutture nidificate. Data factory di Azure consente di indicare una gerarchia tramite nestingSeparator, ovvero "." negli esempi precedenti. Con il separatore, l'attività copia genererà l'oggetto "Name" con tre elementi figlio First, Middle e Last, in base a "Name.First", "Name.Middle" e "Name.Last" nella definizione della tabella.</p> | No

**DocumentDbCollectionSink** supporta le proprietà seguenti:

| **Proprietà** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | È necessario un carattere speciale nel nome della colonna di origine per indicare tale documento nidificato. <p>Ad esempio: Name.First nella tabella di output produce la seguente struttura JSON nel documento di DocumentDB:</p><p>"Name": {<br/>"First": "John"<br/>},</p> | Carattere usato per separare i livelli di nidificazione.<p>Il valore predefinito è . (punto).</p> | Carattere utilizzato per separare i livelli di nidificazione. <p>Il valore predefinito è . (punto).</p> | No | 
| writeBatchSize | Numero di richieste in parallelo per il servizio DocumentDB per creare documenti.<p>È possibile ottimizzare le prestazioni quando si copiano dati da e verso DocumentDB usando questa proprietà. È possibile prevedere prestazioni migliori quando si aumenta writeBatchSize, poiché vengono inviate più richieste in parallelo a DocumentDB. Tuttavia è necessario evitare la limitazione che può generare il messaggio di errore relativo alla dimensione della frequenza di richiesta.</p><p>La limitazione viene stabilita da numerosi fattori, tra cui le dimensioni dei documenti, il numero di termini nei documenti, l'indicizzazione dei criteri di raccolta di destinazione e così via. Per le operazioni di copia, è possibile usare una raccolta migliore (ad esempio S3) per disporre della massima velocità effettiva disponibile (2500 unità di richiesta al secondo).</p> | Valore integer | No |
| writeBatchTimeout | Tempo di attesa per il completamento dell’operazione prima del timeout. | (Unità = intervallo di tempo) Esempio: "00:30:00" (30 minuti). | No |
 
## Appendice
1. **Domanda:** C’è l'aggiornamento del supporto di attività di copia dei record esistenti?

	**Risposta:** No.

2. **Domanda:** come fa un nuovo tentativo di una copia del DocumentDB ad affrontare i record copiati?

	**Risposta:** se i record dispongono di un campo "ID" e l'operazione di copia tenta di inserire un record con lo stesso ID, l'operazione di copia genera un errore.
 
3. **Domanda:** Il Data Factory supporta [il partizionamento dei dati basato su hash o sull’intervallo]( https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)?

	**Risposta:** No. 
4. **Domanda:** è possibile specificare più di una raccolta di DocumentDB per una tabella?
	
	**Risposta:** No. In questo momento, è possibile specificare solo una raccolta.
     

<!---HONumber=AcomDC_0302_2016-->