<properties
	pageTitle="Spostare dati da e in Archivio Azure Data Lake | Azure Data Factory"
	description="Informazioni su come spostare i dati da e in Archivio Azure Data Lake con Azure Data Factory"
	services="data-factory"
	documentationCenter=""
	authors="linda33wj"
	manager="jhubbard"
	editor="monicar"/>  

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="jingwang"/>

# Spostare dati da e in Archivio Azure Data Lake con Azure Data Factory
Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da Azure Data Lake Store in un altro archivio dati e viceversa. Questo articolo si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia e delle combinazioni di archivio dati supportate.

> [AZURE.NOTE]
Prima di creare una pipeline con un'attività di copia per spostare i dati da e in Azure Data Lake Store, creare un account Azure Data Lake Store. Per informazioni su Azure Data Lake Store, vedere [Introduzione ad Azure Data Lake Store con il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Per la procedura dettagliata per la creazione di una data factory, dei servizi collegati, dei set di dati e di una pipeline, vedere l'[Esercitazione: Creare la prima pipeline](data-factory-build-your-first-pipeline.md). Usare i frammenti JSON con l'editor di Data Factory o Visual Studio o Azure PowerShell per creare le entità di Data Factory.

## Copia di dati guidata
Il modo più semplice per creare una pipeline che copia i dati in o da Azure Data Lake Store consiste nell'usare la Copia dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tali esempi mostrano come copiare dati da/in Azure Data Lake Store e Archiviazione BLOB di Azure. Tuttavia, i dati possono essere copiati**direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Data factory di Azure.


## Esempio: Copiare dati dal BLOB di Azure in Archivio Azure Data Lake
L'esempio seguente mostra:

1.	Un servizio collegato di tipo [AzureStorage](#azure-storage-linked-service-properties).
2.	Un servizio collegato di tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](#azure-blob-copy-activity-type-properties) e [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

L'esempio copia i dati di una serie temporale da un archivio BLOB di Azure in Azure Data Lake Store ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.


**Servizio collegato Archiviazione di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Servizio collegato di Azure Data Lake:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### Per creare un servizio collegato di Azure Data Lake con l'editor di Data Factory
La procedura seguente descrive i passaggi per la creazione di un servizio collegato di Archivio Azure Data Lake usando l'editor di Data Factory.

1. Fare clic su **Nuovo archivio dati** sul barra dei comandi e scegliere **Archivio Azure Data Lake**.
2. Nell'editor JSON per la proprietà **dataLakeStoreUri** immettere l'URI per il Data Lake.
3. Fare clic sul pulsante **Autorizza** sulla barra dei comandi. Verrà visualizzata una finestra popup.

	![Pulsante Autorizza](./media/data-factory-azure-data-lake-connector/authorize-button.png)  

4. Usare le credenziali per accedere. A questo punto alla proprietà **authorization** in JSON dovrebbe essere assegnato un valore.
5. (Facoltativo) Specificare i valori per i parametri facoltativi, ad esempio **accountName**, **subscriptionID** e **resourceGroupName** in JSON oppure eliminare queste proprietà da JSON.
6. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

> [AZURE.IMPORTANT] Il codice di autorizzazione generato con il pulsante **Autorizza** ha una scadenza. Alla **scadenza del token**, **ripetere l'autorizzazione** con il pulsante **Autorizza** e ridistribuire il servizio collegato. Per informazioni dettagliate, vedere la sezione [Proprietà del servizio collegato dell'Archivio Azure Data Lake](#azure-data-lake-store-linked-service-properties).



**Set di dati di input del BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ]
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


**Set di dati di output di Azure Data Lake:**

L'esempio copia i dati in un Archivio Azure Data Lake. Nuovi dati vengono copiati in Archivio Azure Data Lake ogni ora.

	{
		"name": "AzureDataLakeStoreOutput",
	  	"properties": {
			"type": "AzureDataLakeStore",
		    "linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/output/"
		    },
	    	"availability": {
	      		"frequency": "Hour",
	      		"interval": 1
	    	}
	  	}
	}



**Pipeline con un’attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo **source** è impostato su **BlobSource** e il tipo **sink** è impostato su **AzureDataLakeStoreSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":
		{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline with copy activity",
	    	"activities":
			[  
	      		{
	        		"name": "AzureBlobtoDataLake",
		        	"description": "Copy Activity",
		        	"type": "Copy",
		        	"inputs": [
		          	{
			            "name": "AzureBlobInput"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "AzureDataLakeStoreOutput"
		          	}
		        	],
		        	"typeProperties": {
			        	"source": {
		            		"type": "BlobSource",
			    			"treatEmptyAsNull": true,
		            		"blobColumnSeparators": ","
		          		},
		          		"sink": {
		            		"type": "AzureDataLakeStoreSink"
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

## Esempio: Copiare dati da Archivio Azure Data Lake nel BLOB di Azure
L'esempio seguente mostra:

1.	Un servizio collegato di tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](#azure-blob-dataset-type-properties).
5.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) e [BlobSink](#azure-blob-copy-activity-type-properties).

L'esempio copia i dati di una serie temporale da Azure Data Lake Store in un archivio BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato dell'Archivio Azure Data Lake:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE] Vedere la procedura illustrata nell'esempio precedente per ottenere l'URL di autorizzazione.

**Servizio collegato Archiviazione di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input di Azure Data Lake:**

Impostando **"external" su "true"** si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

	{
		"name": "AzureDataLakeStoreInput",
	  	"properties":
		{
	    	"type": "AzureDataLakeStore",
	    	"linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/input/",
            	"fileName": "SearchLog.tsv",
            	"format": {
                	"type": "TextFormat",
	                "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
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

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo **source** è impostato su **AzureDataLakeStoreSource** e il tipo **sink** è impostato su **BlobSink**.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureDakeLaketoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureDataLakeStoreInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "AzureDataLakeStoreSource",
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


## Proprietà del servizio collegato dell'Archivio Azure Data Lake

È possibile collegare un account di archiviazione di Azure a una data factory di Azure tramite un servizio collegato Archiviazione di Azure. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato Archiviazione di Azure.

| Proprietà | Descrizione | Obbligatorio |
| :-------- | :----------- | :-------- |
| type | La proprietà type deve essere impostata su: **AzureDataLakeStore** | Sì |
| dataLakeStoreUri | Specificare le informazioni sull'account Archivio Azure Data Lake. È nel formato seguente: https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 | Sì |
| autorizzazione | Fare clic sul pulsante **Autorizza** nell'**editor di Data Factory** e immettere le credenziali per assegnare l'URL di autorizzazione generato automaticamente a questa proprietà. | Sì |
| sessionId | ID sessione OAuth dalla sessione di autorizzazione oauth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questa impostazione viene generata automaticamente quando si usa l'editor di Data Factory. | Sì |  
| accountName | Nome dell'account Data Lake | No |
| subscriptionId | ID sottoscrizione di Azure. | No (se non specificata, viene usata la sottoscrizione della Data factory). |
| resourceGroupName | Nome del gruppo di risorse di Azure | No (se non specificata, viene usato il gruppo di risorse di Data Factory). |

## Scadenza del token 
Il codice di autorizzazione generato con il pulsante **Autorizza** ha una scadenza. Per le scadenze dei diversi tipi di account utente, vedere la tabella seguente. Alla **scadenza del token** di autenticazione potrebbe essere visualizzato il messaggio di errore seguente: "Errore dell'operazione relativa alle credenziali: invalid\_grant - AADSTS70002: Errore di convalida delle credenziali. AADSTS70008: La concessione dell'accesso specificata è scaduta o è stata revocata. ID traccia: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID correlazione: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z".


| Tipo di utente | Scade dopo |
| :-------- | :----------- | 
| Account utente NON gestiti da Azure Active Directory, come @hotmail.com, @live.com e così via. | 12 ore |
| Account utente gestiti da Azure Active Directory (AAD) | 14 giorni dopo l'esecuzione dell'ultima sezione. <br/><br/>90 giorni, se viene eseguita una sezione basata sul servizio collegato OAuth almeno una volta ogni 14 giorni. |

Se si modifica la password prima dell'ora di scadenza del token, il token scade immediatamente e viene visualizzato l'errore visto in questa sezione.

Per evitare/risolvere questo problema, alla **scadenza del token** ripetere l'autorizzazione usando il pulsante **Autorizza** e ridistribuire il servizio collegato. È anche possibile generare valori per le proprietà **sessionId** e **authorization** a livello di codice usando il codice riportato nella sezione seguente:

### Per generare valori sessionId e authorization a livello di codice 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Per informazioni dettagliate sulle classi di Data Factory usate nel codice, vedere gli argomenti [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Aggiungere un riferimento alla versione **2.9.10826.1824** di **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** per la classe WindowsFormsWebAuthenticationDialog usata nel codice.
 

## Proprietà del tipo di set di dati di Azure Data Lake

Per un elenco completo delle proprietà e delle sezioni JSON disponibili per la definizione dei set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione, il formato dei dati e così via nell'archivio dati. La sezione typeProperties per il set di dati di tipo **AzureDataLakeStore** include le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| :-------- | :----------- | :-------- |
| folderPath | Percorso del contenitore e della cartella nell'Archivio Azure Data Lake. | Sì |
| fileName | Il nome del file in fileName nell'archivio di Azure Data Lake è facoltativo e distingue tra maiuscole e minuscole. <br/><br/>Se si specifica un nome file, l'attività (inclusa la copia) funziona sul file specifico.<br/><br/>Quando la proprietà fileName non è specificata, la copia include tutti i file nella proprietà folderPath per il set di dati di input.<br/><br/>Quando la proprietà fileName non è specificata per un set di dati di output, il nome del file generato sarà nel formato seguente: Data.<Guid>.txt. Ad esempio, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | No |
| partitionedBy | partitionedBy è una proprietà facoltativa. Può essere utilizzata per specificare una proprietà folderPath dinamica e un nome file per i dati della serie temporale. Ad esempio, è possibile includere parametri per ogni ora di dati in folderPath. Per informazioni dettagliate ed esempi, vedere la sezione [Uso della proprietà partitionedBy](#using-partitionedby-property). | No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per informazioni dettagliate, vedere le sezioni [Specifica TextFormat](#specifying-textformat), [Specifica AvroFormat](#specifying-avroformat), [Specifica JsonFormat](#specifying-jsonformat), [Specifica OrcFormat](#specifying-orcformat) e [Specifica ParquetFormat](#specifying-parquetformat). Se si desidera copiare i file così come sono tra archivi basati su file (copia binaria), è possibile saltare la sezione formato sia nella definizione del set di dati di output che in quella di input.| No
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono: **GZip**, **Deflate** e **BZip2** e i livelli supportati sono: **Ottimale** e **Più veloce**. Le impostazioni di compressione non sono attualmente supportate per i dati **AvroFormat** o **OrcFormat**. Per maggiori informazioni, vedere la sezione [Supporto della compressione](#compression-support). | No |

### Uso della proprietà partitionedBy
È possibile specificare una proprietà folderPath dinamica e il nome file per i dati di una serie temporale con la sezione **partitionedBy**, macro Data Factory e le variabili di sistema SliceStart e SliceEnd, che indicano l'ora di inizio e fine per una sezione di dati specificata.

Per altre informazioni sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Set di dati in Azure Data Factory](data-factory-create-datasets.md) e [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md).

#### Esempio 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy":
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH) specificato. SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. For example: wikidatagateway/wikisampledataout/2014100103 or wikidatagateway/wikisampledataout/2014100104

#### Esempio 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy":
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
	],

In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà folderPath e fileName.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
 

### Supporto della compressione  
L'elaborazione di set di dati di grandi dimensioni può causare colli di bottiglia I/O e di rete. Pertanto, i dati compressi negli archivi possono non solo velocizzare il trasferimento dei dati attraverso la rete e risparmiare spazio su disco, ma apportare anche miglioramenti significativi delle prestazioni nell'elaborazione di dati di grandi dimensioni. Attualmente, la compressione è supportata per gli archivi di dati basati su file, ad esempio BLOB di Azure o il file system locale.

Per specificare la compressione per un set di dati, usare la proprietà **compression** nel set di dati JSON come illustrato di seguito:

	{  
		"name": "AzureDatalakeStoreDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureDatalakeStore",  
	    	"linkedServiceName": "DataLakeStoreLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
La sezione **compression** ha due proprietà:
  
- **Type:** il codec di compressione, che può essere **GZIP**, **Deflate** o **BZIP2**.
- **Level:** il rapporto di compressione, che può essere **Optimal** o **Fastest**.
	- **Fastest:** l'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale.
	- **Optimal**: l'operazione di compressione deve comprimere il file in modo ottimale, anche se il completamento richiede più tempo.
	
	Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx).

Si supponga che il set di dati di esempio venga usato come output di un'attività di copia. L'attività di copia comprime i dati di output con il codec GZIP usando un rapporto ottimale e quindi scrive i dati compressi in un file denominato pagecounts.csv.gz in Azure Data Lake Store.

Quando si specifica la proprietà di compressione nel codice JSON di un set di dati di input, la pipeline legge i dati compressi dall'origine. Quando si specifica la proprietà nel codice JSON di un set di dati di output, l'attività di copia può scrivere i dati compressi nella destinazione. Di seguito vengono forniti alcuni scenari di esempio:

- Leggere i dati compressi GZIP da Archivio Data Lake di Azure, decomprimerli e scrivere i dati del risultato in un database SQL di Azure. In questo caso, definire il set di dati di input di Archivio Data Lake di Azure con la proprietà compression di JSON.
- Leggere i dati da un file di testo normale dal file system locale, comprimerli usando il formato GZIP e scrivere i dati compressi in Archivio Data Lake di Azure. In questo caso, definire il set di dati di output di Azure Data Lake con la proprietà compression di JSON.
- Leggere i dati compressi GZIP da Archivio Data Lake di Azure, decomprimerli, comprimerli usando BZIP2 e scrivere i dati del risultato in Archivio Data Lake di Azure. Impostare il tipo di compressione su GZIP e BZIP2 rispettivamente per i set di dati di input e di output.


## Proprietà del tipo di attività di copia di tabelle di Azure Data Lake  
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

**AzureDataLakeStoreSource** supporta le proprietà seguenti della sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. | True (valore predefinito), False | No |



**AzureDataLakeStoreSink** supporta le proprietà seguenti della sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Specifica il comportamento di copia. | **PreserveHierarchy:** mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/>**FlattenHierarchy:** tutti i file della cartella di origine vengono creati nel primo livello della cartella di destinazione. I file di destinazione vengono creati con un nome generato automaticamente.<br/><br/>**MergeFiles:** unisce tutti i file della cartella di origine in un file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. | No |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0928_2016-->