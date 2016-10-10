<properties 
	pageTitle="Spostare i dati da Amazon Redshift usando Data Factory | Microsoft Azure" 
	description="Informazioni su come spostare i dati da origini Amazon Redshift usando Azure Data Factory." 
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
	ms.date="08/23/2016" 
	ms.author="jingwang"/>  

# Spostare i dati da Amazon Redshift usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da Amazon Redshift a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e un elenco di archivio dati di origine/di sincronizzazione.

Data factory supporta attualmente solo lo spostamento di dati da Amazon Redshift ad altri archivi dati, non da altri archivi dati a Amazon Redshift.

## Prerequisiti

- Se si spostano i dati in un archivio di dati locale, concedere al gateway di gestione dati (usa indirizzo IP della macchina) l'accesso al cluster Amazon Redshift. Vedere [Autorizzare l'accesso al cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) per le istruzioni.
- Se si spostano dati in un archivio di dati di Azure, vedere [Intervalli IP del centro dati di Azure](https://www.microsoft.com/download/details.aspx?id=41653) per gli intervalli di indirizzi IP (inclusi gli intervalli SQL) utilizzati dai data center di Microsoft Azure.

## Copia di dati guidata
Il modo più semplice per creare una pipeline che copia i dati da Amazon Redshift consiste nell'usare la procedura Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

L'esempio di seguito fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra come copiare i dati da Amazon Redshift all'archiviazione BLOb di Azure. Tuttavia, i dati possono essere copiati in tutti i sink indicati [qui](data-factory-data-movement-activities.md#supported-data-stores).

## Esempio: copiare i dati da Amazon Redshift al BLOB di Azure
Questo esempio illustra come copiare dati da un database Amazon Redshift a un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.
 
L'esempio include le entità di Data Factory seguenti:

- Un servizio collegato di tipo [AmazonRedshift](#linked-service-properties).
- Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#dataset-type-properties).
- Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia i dati dai risultati della query in Amazon Redshift in un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato Amazon Redshift**

	{
	    "name": "AmazonRedshiftLinkedService",
	    "properties":
	    {
	        "type": "AmazonRedshift",
	        "typeProperties":
	        {
	            "server": "< The IP address or host name of the Amazon Redshift server >",
	            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
	            "database": "<The database name of the Amazon Redshift database>",
	            "username": "<username>",
	            "password": "<password>"
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

**Set di dati di input Redshift Amazon**

Impostando **"external": true** si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno. Impostare questa proprietà su true in un set di dati di input non generato da un'attività nella pipeline.

	{
	    "name": "AmazonRedshiftInputDataset",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "AmazonRedshiftLinkedService",
	        "typeProperties": {
	            "tableName": "<Table name>"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true
	    }
	}


**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

	{
	    "name": "AzureBlobOutputDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.
	
	{
	    "name": "CopyAmazonRedshiftToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AmazonRedshiftInputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutputDataSet"
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
	                "name": "AmazonRedshiftToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Proprietà del servizio collegato

La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato Amazon Redshift.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| type | La proprietà type deve essere impostata su: **Amazon Redshift**. | Sì |
| server | Indirizzo IP o nome host del server Amazon Redshift. | Sì |
| port | Il numero della porta TCP che il server Amazon Redshift usa per ascoltare le connessioni client. | No, valore predefinito: 5439 |
| database | Nome del database Amazon Redshift. | Sì |
| Nome utente | Nome dell'utente che ha accesso al database.| Sì |
| password | La password per l'account utente.| Sì |


## Proprietà del tipo di set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **RelationalTable** (che comprende il set di dati Amazon Redshift) presenta le proprietà seguenti

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella nel database Amazon Redshift a cui fa riferimento il servizio collegato. | No (se la **query** di **RelationalSource** è specificata) | 

## Proprietà del tipo di attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Quando l'origine dell'attività di copia di tipo **RelationalSource** (che comprende Amazon Redshift), sono disponibili le proprietà seguenti nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * da MyTable. | No (se **tableName** di **set di dati** è specificato) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Tipo di mappatura di Amazon Redshift

Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano i dati in Amazon Redshift, verranno usate le seguenti mappature dai tipi di Amazon Redshift ai tipi .NET.

Tipo di Amazon Redshift | Tipo basato su .Net
-------------------- | ---------------
SMALLINT | Int16
INTEGER | Int32
BIGINT | Int64
DECIMAL | Decimal
REAL | Single
DOUBLE PRECISION | Double
BOOLEAN | String
CHAR | String
VARCHAR | String
DATE | DateTime
TIMESTAMP | DateTime
TEXT | String



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

## Passaggi successivi
Vedere gli articoli seguenti:
- [Esercitazione dell'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate della creazione di una pipeline con un'attività di copia.

<!---HONumber=AcomDC_0928_2016-->