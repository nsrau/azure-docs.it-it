<properties 
	pageTitle="Spostare i dati da Sybase | Data factory di Azure" 
	description="Informazioni su come spostare i dati dal Database di Sybase utilizzando Data factory di Azure." 
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
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# Spostare i dati da Sybase utilizzando Data factory di Azure 

Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da Sybase a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data factory supporta la connessione a origini Sybase locali tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway.

**Nota:** è necessario usare il gateway per connettersi a Sybase anche se è ospitato in macchine virtuali IaaS di Azure. Se si sta cercando di connettersi a un'istanza di Sybase ospitata nel cloud è anche possibile installare l'istanza del gateway nella macchina virtuale IaaS.

Data factory supporta attualmente solo lo spostamento di dati da Sybase ad altri archivi dati, non da altri archivi dati a Sybase.

## Installazione

Perché Gateway di gestione dati si connetta al database Sybase, è necessario installare il [provider di dati per Sybase](http://go.microsoft.com/fwlink/?linkid=324846) nello stesso sistema del Gateway di gestione dati.

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere l'articolo relativo alla [risoluzione dei problemi del gateway](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting).

## Esempio: Copiare i dati da Sybase a BLOB di Azure

L'esempio seguente mostra:

1.	Un servizio collegato di tipo [OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia i dati dai risultati della query nel database Sybase in un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Per prima cosa, impostare il Gateway di gestione dati in base alle istruzioni contenute nell'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato Sybase:**

	{
	    "name": "OnPremSybaseLinkedService",
	    "properties": {
	        "type": "OnPremisesSybase",
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
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}


**Set di dati di input Sybase:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in Sybase e che contenga una colonna denominata "timestamp" per i dati di una serie temporale.

Impostando "external" su true e specificando i criteri externalData si comunica alla data factory che la tabella è esterna e non è prodotta da un'attività al suo interno. Si noti che il valore **type** del servizio collegato è impostato su: **RelationalTable**.
	
	{
	    "name": "SybaseDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremSybaseLinkedService",
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

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

	{
	    "name": "AzureBlobSybaseDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **RelationalSource** e il tipo **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** seleziona i dati dalla tabella DBA.Orders nel database.


	{
	    "name": "CopySybaseToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from DBA.Orders"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "SybaseDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobSybaseDataSet"
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
	                "name": "SybaseToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Proprietà del servizio collegato Sybase

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Sybase.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
type | La proprietà del tipo deve essere impostata su: **OnPremisesSybase** | Sì
server | Nome del server Sybase. | Sì
database | Nome del database Sybase. | Sì 
schema | Nome dello schema nel database. | No
authenticationType | Tipo di autenticazione usato per connettersi al database Sybase. I valori possibili sono: anonima, di base e Windows. | Sì
username | Specificare il nome utente se si usa l'autenticazione di base o Windows. | No
password | Specificare la password per l'account utente specificato per il nome utente. | No
gatewayName | Nome del gateway che il servizio Data factory deve usare per connettersi al database Sybase locale. | Sì 

Vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) per informazioni dettagliate sull'impostazione delle credenziali per un'origine dei dati Sybase locale.

## Proprietà del tipo del set di dati Sybase

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **RelationalTable** (che comprende il set di dati Sybase) presenta le proprietà seguenti.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
tableName | Nome della tabella nell'istanza del database Sybase a cui fa riferimento il servizio collegato. | No (se la **query** di **RelationalSource** è specificata)

## Proprietà del tipo di attività di copia Sybase 

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia con origine di tipo **RelationalSource** (che comprende Sybase), sono disponibili le proprietà seguenti nella sezione **typeProperties**:

Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
query | Usare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * da MyTable. | No (se **tableName** di **set di dati** è specificato)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Mapping dei tipi per Sybase

Come accennato nell'articolo Attività di spostamento dei dati, l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Sybase supporta i tipi T-SQL e T-SQL. Per una tabella di mapping dai tipi SQL al tipo .NET, vedere l'articolo [Connettore SQL Azure](data-factory-azure-sql-connector.md).

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0128_2016-->