<properties 
	pageTitle="Spostare dati da e verso le tabelle di Azure | Data factory di Azure"
	description="Informazioni su come spostare i dati da e verso l'archiviazione tabelle di Azure mediante Data factory di Azure."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags 
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="spelluru"/>

# Spostare dati da e verso le tabelle di Azure mediante Data factory di Azure

Questo articolo illustra come usare l'attività di copia di una data factory di Azure per spostare dati nelle tabelle di Azure da un altro archivio dati e spostare data da un altro archivio dati alle tabelle di Azure. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

## Esempio: Copiare i dati dalle tabelle di Azure al BLOB di Azure

L'esempio seguente mostra:

1.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (usato sia per tabelle che per BLOB).
2.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureTable](#azure-table-dataset-type-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.	La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [AzureTableSource](#azure-table-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

Nell'esempio vengono copiati i dati appartenenti alla partizione predefinita in una tabella di Azure a un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di archiviazione di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input di tabelle di Azure**

Nell'esempio si presuppone di aver creato una tabella "MyTable" in tabelle di Azure.
 
Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data factory che la tabella è esterna e non è prodotta da un'attività al suo interno.

	{
	  "name": "AzureTableInput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline con l’attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **AzureTableSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata con la proprietà **AzureTableSourceQuery** seleziona i dati da copiare dalla partizione predefinita ogni ora.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
		    "end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
				{
	        		"name": "AzureTabletoBlob",
	        		"description": "copy activity",
	        		"type": "Copy",
	        		"inputs": [
	          			{
		            		"name": "AzureTableInput"
		        		}
	        		],
	        		"outputs": [
	          			{
	          	  			"name": "AzureBlobOutput"
		          		}
		        	],
		        	"typeProperties": {
		          		"source": {
		            		"type": "AzureTableSource",
				            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
    	    		},
		        	"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},				
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
				}
		     ]	
		}
	}

## Esempio: Copiare i dati dal BLOB di Azure in tabelle di Azure

L'esempio seguente mostra:

1.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (usato sia per tabelle che per BLOB).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureTable](#azure-table-dataset-type-properties). 
4.	La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [AzureTableSink](#azure-table-copy-activity-type-properties). 


Nell’esempio vengono copiati dati appartenenti a una serie temporale dal BLOB di Azure a una tabella in tabelle di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di archiviazione di Azure (per tabelle di Azure e Blob):**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input del BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.
	
	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Set di dati di output di tabelle di Azure**

Nell’esempio vengono copiati dati in una tabella denominata "MyTable" in tabelle di Azure. È necessario creare la tabella in tabelle di Azure con lo stesso numero di colonne di quelle contenute nel file file con estensione csv del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

	{
	  "name": "AzureTableOutput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline con l’attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo di **sink** è impostato su **AzureTableSink**.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoTable",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureTableOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "AzureTableSink",
	            "writeBatchSize": 100,
	            "writeBatchTimeout": "01:00:00"
	          }
	        },
	        "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },						
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## Proprietà del servizio collegato di archiviazione di Azure

È possibile collegare un account di archiviazione di Azure a una data factory di Azure tramite un servizio collegato di archiviazione di Azure. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di archiviazione di Azure.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| type | La proprietà del tipo deve essere impostata su: AzureStorage | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all’archivio Azure per la proprietà connectionString. È possibile ottenere la proprietà connectionString per l'archivio Azure dal Portale di Azure. | Sì |

## Proprietà del tipo del set di dati di tabelle di Azure

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **AzureTable** presenta le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella nell'istanza del database di tabelle di Azure a cui fa riferimento il servizio collegato. | Sì

## Proprietà del tipo di attività di copia di tabelle di Azure

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

**AzureTableSource** supporta le seguenti proprietà della sezione typeProperties:

Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Usare la query personalizzata per leggere i dati. | Stringa di query della tabella di Azure. Esempio: **ColumnA eq ValueA** | No
azureTableSourceIgnoreTableNotFound | Indica se ignorare l'eccezione di tabella inesistente. | TRUE<br/>FALSE | No |

**AzureTableSink** supporta le seguenti proprietà della sezione typeProperties:


Proprietà | Descrizione | Valori consentiti | Obbligatorio  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Valore predefinito della chiave di partizione che può essere usato dal sink. | Valore stringa. | No 
azureTablePartitionKeyName | Nome della colonna specificato dall'utente, i cui valori di colonna vengono usati come chiave di partizione. Se non specificato, AzureTableDefaultPartitionKeyValue viene usato come chiave di partizione. | Nome colonna. | No |
azureTableRowKeyName | Nome della colonna specificato dall'utente, i cui valori di colonna vengono usati come chiave di riga. Se non specificato, usare un GUID per ogni riga. | Nome colonna. | No  
azureTableInsertType | La modalità per inserire dati in tabelle di Azure. | unione<br/>sostituzione | No 
writeBatchSize | Inserisce dati nella tabella di Azure quando viene raggiunto il writeBatchSize o writeBatchTimeout. | Numero intero compreso tra 1 e 100 (unità = conteggio righe) | No. (Predefinito = 100) 
writeBatchTimeout | Inserisce i dati nella tabella di Azure quando viene raggiunto writeBatchSize o writeBatchTimeout | (Unità = intervallo di tempo)Esempio: "00:20:00" (20 minuti). | No. (il valore predefinito è il timeout del client di archiviazione pari a 90 secondi)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per tabelle di Azure

Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano i dati da e verso tabelle di Azure, i seguenti [mapping definiti dal servizio tabelle di Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) verranno utilizzati dai tipi di OData di tabelle di Azure al tipo di .NET e viceversa.

| Tipo di dati OData | Tipo di .NET | Dettagli |
| --------------- | --------- | ------- |
| Edm.Binary | byte | Una matrice di byte di dimensioni fino a 64 KB. |
| Edm.Boolean | bool | Valore booleano. |
| Edm.DateTime | DateTime | Un valore a 64 bit espresso come Coordinated Universal Time (UTC). L'intervallo DateTime supportato inizia dalle 00:00, 1 gennaio, 1601 D.C. (C.E.), UTC. L'intervallo termina il 31 dicembre 9999. |
| Edm.Double | double | Un valore a virgola mobile a 64 bit. |
| Edm.Guid | Guid | Un identificatore univoco globale a 128 bit. |
| Edm.Int32 | Int32 o int | Un valore integer a 32 bit. |
| Edm.Int64 | Int64 o long | Un valore integer a 64 bit. |
| Edm.String | String | Un valore con codifica UTF-16. I valori delle stringhe possono essere di dimensioni fino a 64 KB. |

### Esempio di conversione di tipo

L'esempio seguente riguarda la copia dei dati da un BLOB di Azure a tabelle di Azure con conversioni del tipo.

Si supponga che il set di dati BLOB sia in formato CSV e contenga 3 colonne. Una di esse è una colonna datetime con un formato datetime personalizzato che utilizza nomi abbreviati francesi per il giorno della settimana.

Definire il set di dati di origine BLOB come indicato di seguito e le definizioni di tipo per le colonne.
	
	{
	    "name": " AzureBlobInput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "userid", "type": "Int64"},
	                { "name": "name", "type": "String"},
	                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
	        "external": true,
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	        },
			"policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
			}
	    }
	}

Dato il precedente mapping dei tipi dal tipo OData di tabelle di Azure al tipo di .NET è possibile definire la tabella in tabelle di Azure con lo schema seguente.

**Schema di tabelle di Azure:**

Nome colonna | Tipo
----------- | --------
userid | Edm.Int64
name | Edm.String 
lastlogindate | Edm.DateTime

Quindi si definirà il set di dati di tabelle di Azure come indicato di seguito. Non è necessario specificare la sezione "struttura" con informazioni sul tipo perché le informazioni sul tipo sono già state specificate nell'archivio dati sottostante.

	{
	  "name": "AzureTableOutput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

In questo caso Data Factory eseguirà automaticamente la conversione del tipo incluso il campo Datetime con il formato data personalizzato fr-fr quando si spostano dati da BLOB a tabelle di Azure.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=August15_HO9-->