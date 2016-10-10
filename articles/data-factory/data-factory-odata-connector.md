<properties 
	pageTitle="Spostare i dati da origini OData | Azure Data Factory" 
	description="Informazioni su come spostare i dati da origini OData usando Azure Data Factory." 
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
	ms.date="09/26/2016" 
	ms.author="jingwang"/>

# Spostare i dati da un'origine OData usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da un'origine OData a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

> [AZURE.NOTE] Questo connettore OData supporta la copia dei dati dalle origini OData cloud e locali. Nel secondo caso, è necessario installare il gateway di gestione dati. Vedere l’articolo [Spostare dati tra cloud e locale](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sui Gateway di Gestione dati.

## Copia di dati guidata
Il modo più semplice di creare una pipeline che copia i dati da un'origine OData consiste nell'usare la procedura Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da un'origine OData in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.


## Esempio: Copiare i dati da un'origine OData a BLOB di Azure

Questo esempio illustra come copiare dati da un'origine OData all'archiviazione BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.
 
L'esempio include le entità di Data Factory seguenti:

1.	Un servizio collegato di tipo [OData](#odata-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [ODataResource](#odata-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#odata-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia i dati dall'esecuzione di query su un'origine OData a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato OData**: questo esempio usa l'autenticazione di base. Per i diversi tipi di autenticazione disponibili, vedere la sezione [Servizio collegato OData](#odata-linked-service-properties).

    {
		"name": "ODataLinkedService",
       	"properties": 
        {
        	"type": "OData",
           	"typeProperties": 
            {
            	"url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           	}
       	}
    }


**Servizio collegato Archiviazione di Azure**

	{
	  	"name": "AzureStorageLinkedService",
		"properties": {
	    	"type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    	}
	  	}
	}

**Set di dati di input OData**

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.
	
    {
    	"name": "ODataDataset",
		"properties": 
		{
    		"type": "ODataResource",
    		"typeProperties": 
			{
     			"path": "Products" 
    		},
    		"linkedServiceName": "ODataLinkedService",
    		"structure": [],
    		"availability": {
        		"frequency": "Hour",
        		"interval": 1
    		},
			"external": true,
    		"policy": {
            	"retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
	            "maximumRetry": 3				
			}
		}
	}

La specifica di **path** nella definizione del set di dati è facoltativa.


**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

	{
	    "name": "AzureBlobODataDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** seleziona i dati più recenti dall'origine OData.
	
	{
	    "name": "CopyODataToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "?$select=Name, Description&$top=5",
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "ODataDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobODataDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "ODataToBlob"
	            }
	        ],
	        "start": "2016-02-01T18:00:00Z",
	        "end": "2016-02-03T19:00:00Z"
	    }
	}


La specifica di **query** nella definizione della pipeline è facoltativa. L'**URL** è che il servizio Data Factory usa per recuperare i dati è il seguente: URL specificato nel servizio collegato (obbligatorio) + percorso specificato nel set di dati (facoltativo) + query nella pipeline (facoltativa).

## Proprietà del servizio collegato OData

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato OData.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| type | La proprietà type deve essere impostata su: **OData** | Sì |
| URL| URL del servizio OData. | Sì |
| authenticationType | Tipo di autenticazione usato per connettersi all'origine OData. <br/><br/> Per OData nel cloud, i valori possibili sono Anonima e Di base. Per OData locale, i valori possibili sono Anonima, Di base e Windows. | Sì | 
| username | Specificare il nome utente se si usa l'autenticazione di base. | Sì (solo se si usa l'autenticazione di base) | 
| password | Specificare la password per l'account utente specificato per il nome utente. | Sì (solo se si usa l'autenticazione di base) | 
| gatewayName | Nome del gateway che il servizio Data Factory deve usare per connettersi al servizio OData locale. Specificare solo se si copiano dati da un'origine OData locale. | No |

### Uso dell'autenticazione di base

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
           	"typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### Uso dell'autenticazione anonima
	
    {
		"name": "ODataLinkedService",
       	"properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### Uso dell'autenticazione di Windows per accedere all'origine OData locale

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
           	"typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## Proprietà del tipo di set di dati OData

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **ODataResource**, che include il set di dati OData, presenta le proprietà seguenti

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| path | Percorso della risorsa OData | No | 

## Proprietà del tipo di attività di copia OData

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Se l'origine è di tipo **RelationalSource** (che comprende OData), nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Esempio | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Usare la query personalizzata per leggere i dati. | "?$select=Name, Description&$top=5" | No | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per OData

Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando i dati vengono spostati da archivi OData, i tipi di dati OData vengono mappati ai tipi .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0928_2016-->