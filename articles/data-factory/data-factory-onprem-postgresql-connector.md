<properties 
	pageTitle="Spostare i dati da PostgreSQL | Data factory di Azure" 
	description="Informazioni su come spostare i dati dal database di PostgreSQL mediante Data factory di Azure." 
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
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# Spostare i dati da PostgreSQL mediante Data factory di Azure

Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da PostgreSQL a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data factory supporta la connessione a origini PostgreSQL locali tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway.

**Nota:** è necessario usare il gateway per connettersi a PostgreSQL anche se è ospitato in macchine virtuali IaaS di Azure. Se si sta cercando di connettersi a un'istanza di PostgreSQL ospitata nel cloud è anche possibile installare l'istanza del gateway nella macchina virtuale IaaS.

Data factory supporta solo lo spostamento di dati da PostgreSQL ad altri archivi dati, non da altri archivi dati a PostgreSQL.

## Installazione 

Perché Gateway di gestione dati si connetta al database PostgreSQL, è necessario installare il [provider di dati Ngpsql per PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) nello stesso sistema del Gateway di gestione dati.

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere l'articolo relativo alla [risoluzione dei problemi del gateway](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting).

## Esempio: Copiare i dati da PostgreSQL a BLOB di Azure

Questo esempio illustra come copiare dati da un database PostgreSQL locale a un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Data factory di Azure.
 
L'esempio include le entità di Data factory seguenti:

1.	Un servizio collegato di tipo [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#postgresql-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](data-factory-onprem-postgresql-connector.md#postgresql-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](data-factory-onprem-postgresql-connector.md#postgresql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

L'esempio copia i dati dai risultati della query nel database PostgreSQL in un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Innanzitutto, impostare il Gateway di gestione dati in base alle istruzioni contenute nell'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato PostgreSQL:**

	{
	    "name": "OnPremPostgreSqlLinkedService",
	    "properties": {
	        "type": "OnPremisesPostgreSql",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}

**Servizio collegato di archiviazione BLOB di Azure:**
		
	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
	    }
	  }
	}

**Set di dati di input PostgreSQL:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in PostgreSQL e che contenga una colonna denominata "timestamp" per i dati di una serie temporale.

Impostando "external" su true e specificando i criteri externalData si comunica al servizio Data factory che la tabella è esterna e non è prodotta da un'attività al suo interno.

	{
	    "name": "PostgreSqlDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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


**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

	{
	    "name": "AzureBlobPostgreSqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** seleziona i dati dalla tabella public.usstates nel database PostgreSQL.
	
	{
	    "name": "CopyPostgreSqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "public"."usstates""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "PostgreSqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobPostgreSqlDataSet"
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
	                "name": "PostgreSqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Proprietà del servizio collegato PostgreSQL

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato PostgreSQL.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà type deve essere impostata su: **OnPremisesPostgreSql** | Sì
server | Nome del server PostgreSQL. | Sì 
database | Nome del database PostgreSQL. | Sì 
schema | Nome dello schema nel database. Per il nome dello schema viene fatta distinzione tra maiuscole e minuscole. | No 
authenticationType | Tipo di autenticazione usato per connettersi al database PostgreSQL. I valori possibili sono: anonima, di base e Windows. | Sì 
username | Specificare il nome utente se si usa l'autenticazione di base o Windows. | No 
password | Specificare la password per l'account utente specificato per il nome utente. | No 
gatewayName | Nome del gateway che il servizio Data factory deve usare per connettersi al database PostgreSQL locale. | Sì 

Per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati PostgreSQL locale, vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

## Proprietà del tipo di set di dati PostgreSQL

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **RelationalTable** (che comprende il set di dati PostgreSQL) presenta le proprietà seguenti.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
tableName | Nome della tabella nell'istanza del database PostgreSQL a cui fa riferimento il servizio collegato. La proprietà tableName fa distinzione tra maiuscole e minuscole. | No (se la **query** di **RelationalSource** è specificata) 

## Proprietà del tipo di attività di copia PostgreSQL

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia con origine di tipo **RelationalSource** (che comprende PostgreSQL), sono disponibili le proprietà seguenti nella sezione typeProperties:

Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
query | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: "query": "select * from "Schema"."Tabella"". | No (se **tableName** di **set di dati** è specificato)

> [AZURE.NOTE] I nomi di schema e di tabella fanno distinzione tra maiuscole e minuscole e devono essere racchiusi tra virgolette doppie ("") nella query.

**Esempio:**

 "query": "select * from "Schema"."Tabella""

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Mapping dei tipi per PostgreSQL

Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipo automatico da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
1. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano i dati in PostgreSQL verranno usati i mapping seguenti dal tipo PostgreSQL al tipo .NET.

Tipo di database PostgreSQL |	Alias PostgresSQL | Tipo di .NET Framework
------------------------ | -------------------- | ---------------------
abstime | | Datetime
bigint | int8 | Int64
bigserial | serial8 | Int64
bit [ (n) ] | | Byte, String
bit varying [ (n) ] | varbit | Byte, String
boolean | bool | Boolean
box | | Byte, String
bytea | | Byte, String
character [ (n) ] | char [ (n) ] | String
character varying [ (n) ] | varchar [ (n) ] | String
cid | | String
cidr | | String
circle | | Byte, String
date | | Datetime
daterange | | String
double precision| float8 | Double
inet | | Byte, String
intarry | | String
int4range | | String
int8range | | String
integer | int, int4 | Int32
interval [ fields ] [ (p) ] | | TimeSpan
json | | String
jsonb | | Byte
line | | Byte, String
lseg | | Byte, String
macaddr | | Byte, String
money | | Decimal
numeric [ (p, s) ] | decimal [ (p, s) ] | Decimal
numrange | | String
oid | | Int32
path | | Byte, String
pg\_lsn | | Int64
point | | Byte, String
polygon | | Byte, String
real | float4 | Single
smallint | int2 | Int16
smallserial | serial2 | Int16
serial | serial4 | Int32
text | | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzarle, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0420_2016-->