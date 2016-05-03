<properties 
	pageTitle="Spostare dati da archivi dati ODBC | Data factory di Azure" 
	description="Informazioni su come spostare dati da archivi dati ODBC con Data factory di Azure." 
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
	ms.date="04/13/2016" 
	ms.author="spelluru"/>

# Spostare dati da archivi dati ODBC con Data factory di Azure
Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da un archivio dati ODBC locale a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data factory supporta attualmente solo lo spostamento di dati da un archivio dati ODBC ad altri archivi dati, ma non da altri archivi dati a un archivio dati ODBC locale.


## Abilitazione della connettività
Il servizio Data factory supporta la connessione a origini ODBC locali tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway. È necessario usare il gateway per connettersi a un archivio dati ODBC anche se è ospitato in una macchina virtuale IaaS di Azure.

Anche se è possibile installare il gateway nello stesso computer locale o macchina virtuale di Azure dell'archivio dati ODBC, è consigliabile installarlo in un diverso computer o macchina virtuale IaaS di Azure per evitare conflitti di risorse e per ottenere prestazioni migliori. Quando si installa il gateway in un computer separato, questo deve poter accedere al computer l'archivio dati ODBC.

Oltre a Gateway di gestione dati, è necessario installare anche il driver ODBC per l'archivio dati nel computer del gateway.

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere l'articolo relativo alla [risoluzione dei problemi del gateway](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting).

## Esempio: Copiare dati da un archivio dati ODBC al BLOB di Azure

Questo esempio illustra come copiare dati da un archivio dati ODBC all'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Data factory di Azure.
 
L'esempio include le entità di Data factory seguenti:

1.	Un servizio collegato di tipo [OnPremisesOdbc](#odbc-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#odbc-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#odbc-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia i dati dai risultati della query in un archivio dati ODBC a un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Per prima cosa, impostare il Gateway di gestione dati in base alle istruzioni contenute nell'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato ODBC**: questo esempio usa l'autenticazione di base. Per i diversi tipi di autenticazione disponibili, vedere la sezione [Servizio collegato ODBC](#odbc-linked-service-properties).

	{
	    "name": "OnPremOdbcLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
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

**Set di dati di input ODBC**

L'esempio presuppone che sia stata creata una tabella "MyTable" in un archivio dati ODBC e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data factory che la tabella è esterna e non è prodotta da un'attività al suo interno.
	
	{
	    "name": "ODBCDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremOdbcLinkedService",
	        "typeProperties": {},
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



**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

	{
	    "name": "AzureBlobOdbcDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.
	
	{
	    "name": "CopyODBCToBlob",
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
	                        "name": "OdbcDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOdbcDataSet"
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
	                "name": "OdbcToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Proprietà del servizio collegato ODBC

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato ODBC.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| type | La proprietà type deve essere impostata su: **OnPremisesOdbc** | Sì |
| connectionString | La parte delle credenziali non di accesso della stringa di connessione, nonché una credenziale crittografata facoltativa. Vedere gli esempi seguenti. | Sì
| credential | La parte delle credenziali di accesso della stringa di connessione specificata nel formato di valore della proprietà specifico del driver, ad esempio: "Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;". | No
| authenticationType | Tipo di autenticazione usato per connettersi all'archivio dati ODBC. I valori possibili sono: anonima e di base. | Sì | 
| username | Specificare il nome utente se si usa l'autenticazione di base. | No | 
| password | Specificare la password per l'account utente specificato per il nome utente. | No | 
| gatewayName | Nome del gateway che il servizio Data factory deve usare per connettersi all'archivio dati ODBC. | Sì |


Per informazioni dettagliate sull'impostazione delle credenziali per un archivio dati ODBC locale, vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

### Uso dell'autenticazione di base

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
	        }
	    }
	}

### Uso dell'autenticazione di base con credenziali crittografate
È possibile crittografare le credenziali usando il cmdlet [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx), in Azure PowerShell versione 1.0, oppure [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx), in Azure PowerShell versione 0.9 o precedente.

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Uso dell'autenticazione anonima

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
	            "credential": "UID={uid};PWD={pwd}",
	            "gatewayName": "mygateway"
	        }
	    }
	}



## Proprietà del tipo di set di dati ODBC

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **RelationalTable**, che include il set di dati ODBC, presenta le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella nell'istanza dell'archivio dati ODBC a cui fa riferimento il servizio collegato. | Sì | 

## Proprietà del tipo di attività di copia ODBC

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia con origine di tipo **RelationalSource**, che include ODBC, sono disponibili le proprietà seguenti nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * da MyTable. | Sì | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per ODBC

Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da archivi dati ODBC, viene eseguito il mapping dei tipi di dati ODBC ai tipi .NET, come riportato nell'argomento [Mapping dei tipi di dati ODBC](https://msdn.microsoft.com/library/cc668763.aspx).


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Archivio GE Historian
Creare un servizio collegato ODBC per collegare un archivio dati [GE Proficy Historian (ora GE Historian)](http://www.geautomation.com/products/proficy-historian) a una data factory di Azure come illustrato nell'esempio seguente:

	{
	    "name": "HistorianLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
			    "connectionString": "DSN=<name of the GE Historian store>",
			    "gatewayName": "<gateway name>",
			    "authenticationType": "Basic",
			    "userName": "<user name>",
			    "password": "<password>"
	        }
	    }
	}

È necessario installare il Gateway di gestione dati in un computer locale e registrare il gateway con il portale. Il gateway installato nel computer locale usa il driver ODBC per GE Historian per la connessione all'archivio dati GE Historian, quindi è necessario installare il driver se non è già installato nel computer del gateway. Per i dettagli, vedere [Attivazione della connettività](#enabling-connectivity).

Prima di usare l'archivio GE Historian in una soluzione Data Factory, verificare se il gateway è in grado di connettersi all'archivio dati usando le istruzioni indicate nella sezione successiva.

Leggere l'articolo dall'inizio per una panoramica dettagliata dell'uso degli archivi dati ODBC come archivi dati di origine in un'operazione di copia.

[AZURE.INCLUDE [data-factory-troubleshoot-connectivity](../../includes/data-factory-troubleshoot-connectivity.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzarle, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0420_2016-->