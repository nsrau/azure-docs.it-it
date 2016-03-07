<properties 
	pageTitle="Spostare dati da Oracle | Data factory di Azure" 
	description="Informazioni su come spostare i dati da e verso database Oracle in locale mediante Data factory di Azure." 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Spostare i dati da Oracle locale mediante Data factory di Azure 

Questo articolo illustra come usare l'attività di copia della data factory per spostare dati da Oracle a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

## Installazione 
Per rendere il servizio di Azure Data Factory in grado di connettersi al database Oracle in locale, è necessario installare quanto segue:

- Gateway di gestione dati nello stesso computer che ospita il database o in un computer separato per evitare che competa per le risorse con il database. Il Gateway di gestione dati è un software che connette le origini dati locali ai servizi cloud in modo sicuro e gestito. Vedere l’articolo [Spostare dati tra cloud e locale](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sui Gateway di Gestione dati. 
- [Oracle Data Access Components (ODAC) per Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/index.html). Deve essere installato nel computer host in cui è installato il gateway.

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere l'articolo relativo alla [risoluzione dei problemi del gateway](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting).

## Esempio: Copiare i dati da Oracle a BLOB di Azure
Questo esempio illustra come copiare dati da un database Oracle locale a un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Data factory di Azure.
 
L'esempio include le entità di Data factory seguenti:

1.	Un servizio collegato di tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.	La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) come origine e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) come sink.

L'esempio copia i dati da una tabella nel database Oracle locale a un BLOB ogni ora. Per altre informazioni sulle varie proprietà usate nell'esempio riportato di seguito, fare riferimento alla documentazione sulle diverse proprietà nelle sezioni che seguono gli esempi.

**Servizio collegato Oracle:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Servizio collegato di archiviazione BLOB di Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Set di dati di input Oracle:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in Oracle e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" e specificando i criteri externalData si comunica al servizio Data factory che la tabella è esterna e non è prodotta da un'attività al suo interno.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.
	
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


**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata con la proprietà **oracleReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


È necessario modificare la stringa di query in base alla configurazione delle date nel database Oracle. Se viene visualizzato il messaggio di errore seguente:

	Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

potrebbe essere necessario modificare la query come illustrato di seguito (usando la funzione to\_date):

	"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## Proprietà del servizio collegato Oracle

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Oracle.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà type deve essere impostata su: **OnPremisesOracle** | Sì
connectionString | Specificare le informazioni necessarie per connettersi all'istanza del database Oracle per la proprietà connectionString. | Sì 
gatewayName | Nome del gateway che verrà usato per connettersi al server Oracle locale | Sì

Per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati Oracle locale, vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).
## Proprietà del tipo di set di dati Oracle

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, fare riferimento all'articolo [Creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (Oracle, BLOB di Azure, tabelle di Azure e così via).
 
La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo OracleTable presenta le proprietà seguenti.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
tableName | Nome della tabella nell'istanza del database Oracle a cui fa riferimento il servizio collegato. | No (se **oracleReaderQuery** di **SqlSource** è specificato)

## Proprietà del tipo di attività di copia Oracle

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

**Nota:** l'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia con origine di tipo **OracleSource**, sono disponibili le proprietà seguenti nella sezione **typeProperties**:

Proprietà | Descrizione |Valori consentiti | Obbligatorio
-------- | ----------- | ------------- | --------
oracleReaderQuery | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. 
Ad esempio: selezionare * da MyTable <p>Se non specificata, l'istruzione SQL eseguita: selezionare * da MyTable</p> | No (se **tableName** di **set di dati** è specificato)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapping dei tipi per Oracle

Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipo automatico da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da Oracle, verranno usati i mapping seguenti dal tipo di dati Oracle al tipo .NET e viceversa.

Tipo di dati Oracle | Tipo di dati .NET Framework
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | Decimal
INTEGER | Decimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Decimal
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | String
XML | String

## Suggerimenti per la risoluzione dei problemi

**** Problema: ** viene visualizzato il seguente **messaggio di errore**: l’attività di copia ha trovato parametri non validi: 'UnknownParameterName', messaggio dettagliato: Impossibile trovare il Provider di dati Framework .NET richiesto. Potrebbe non essere installato".

**Possibili cause**

1. Non è stato installato il Provider di dati Framework .NET per Oracle.
2. Il Provider di dati Framework .NET per Oracle è stato installato nel Framework .NET 2.0 e non è disponibile nelle cartelle del Framework.NET 4.0. 

**Risoluzione/Soluzione alternativa**

1. Se non è stato installato il Provider .NET per Oracle, [installarlo](http://www.oracle.com/technetwork/topics/dotnet/utilsoft-086879.html) e ripetere lo scenario. 
2. Se viene visualizzato il messaggio di errore anche dopo l'installazione del provider, eseguire le operazioni seguenti: 
	1. Aprire la configurazione del computer di .NET 2.0 dalla cartella: <system disk>: \\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config.
	2. Cercare il **Provider di dati Oracle per .NET**, si dovrebbe poter trovare una voce simile a quella sottostante in **system.data** -> **DbProviderFactories**: “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.	Copiare questa voce nel file .config del computer nella seguente cartella v4.0: <system disk>: \\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config e modificare la versione a 4.xxx.x.x.
3.	Installare “<ODP.NET Installed Path>\\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll” nella global assembly cache (GAC) eseguendo “gacutil /i [percorso del provider]".



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0224_2016-->