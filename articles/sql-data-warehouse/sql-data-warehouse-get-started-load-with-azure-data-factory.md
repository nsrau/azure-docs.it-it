<properties
	pageTitle="Caricare i dati con Data factory di Azure | Microsoft Azure"
	description="Informazioni su come caricare i dati con Data factory di Azure"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/19/2015"
   ms.author="lodipalm"/>

# Caricare i dati con Data factory di Azure

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 Questa esercitazione illustra come creare una pipeline in Data factory di Azure per lo spostamento di dati dai BLOB di Archiviazione di Azure a SQL Data Warehouse. I passaggi seguenti consentono di eseguire queste operazioni:

+ Configurare dati di esempio in un BLOB di Archiviazione di Azure.
+ Connettere le risorse a Data factory di Azure.
+ Creare una pipeline per spostare i dati dai BLOB di archiviazione a SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]

## Risorse

Per questa esercitazione sono necessarie le risorse seguenti:

   + **BLOB di Archiviazione di Azure**: il BLOB di Archiviazione di Azure sarà l'origine dei dati per la pipeline. È possibile usare un BLOB esistente o [eseguire il provisioning di un nuovo BLOB](../storage/storage-create-storage-account/).

   + **SQL Data Warehouse**: in questa esercitazione i dati verranno spostati in SQL Data Warehouse. Se non è già disponibile un'istanza configurata, vedere [qui](sql-data-warehouse-get-started-provision.md) per ottenere indicazioni. Sarà anche necessario configurare l'istanza con il set di dati AdventureWorks DW. Se non è stato effettuato il provisioning del data warehouse con i dati di esempio, è possibile [caricarlo manualmente](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Data factory di Azure**: Data factory di Azure completerà il caricamento effettivo. Se sono necessarie informazioni aggiuntive sulla configurazione di Data factory di Azure o sulla creazione di pipeline, vedere [qui](../data-factory/data-factory-build-your-first-pipeline-using-editor/).

Quando tutte le risorse sono disponibili, è possibile passare alla preparazione dei dati e alla creazione della pipeline di Data factory di Azure.

## Dati di esempio

Oltre alle diverse parti della pipeline, sono necessari anche dati di esempio da usare per esercitarsi con il caricamento di dati in Data factory di Azure.

1. Prima di tutto [scaricare i dati di esempio](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv). Questi dati vengono usati insieme ai dati di esempio già disponibili nei dati di esempio, fornendo altri tre anni di dati di vendita.

2. Dopo il download dei dati, è possibile spostarli nell'archivio BLOB eseguendo lo script seguente in AZCopy:

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	Per altre informazioni su come installare e usare AZCopy, vedere la [documentazione di AZCopy](../storage/storage-use-azcopy/).

Quando i dati si trovano in un'unica posizione, è possibile spostarli nella data factory per creare la pipeline per lo spostamento dei dati dall'account di archiviazione a SQL Data Warehouse.

## Uso di Data factory di Azure

Dopo avere configurato tutti gli elementi, è possibile iniziare a configurare la pipeline passando all'istanza di Data factory di Azure nel portale di anteprima di Azure. Per eseguire questa operazione, passare al [portale di Azure](portal.azure.com) e selezionare la data factory dal menu a sinistra.

Sarà quindi necessario eseguire tre passaggi per configurare la pipeline di Data factory di Azure per il trasferimento dei dati al data warehouse, ovvero collegamento dei servizi, definizione dei set di dati e creazione della pipeline.

### Creazione di servizi collegati

Il primo passaggio consiste nel collegare l'account di archiviazione di Azure e SQL Data Warehouse alla data factory.

1. Iniziare prima di tutto il processo di registrazione facendo clic sulla sezione 'Servizi collegati' della data factory e quindi su 'Nuovo archivio dati'. Scegliere quindi un nome per registrare la risorsa di archiviazione di Azure, selezionare Archiviazione di Azure come tipo e immettere il nome account e la chiave dell'account.

2. Per registrare SQL Data Warehouse sarà necessario passare alla sezione 'Creare e distribuire', quindi selezionare 'Nuovo archivio dati' e infine 'Azure SQL Data Warehouse'. Sarà quindi necessario compilare il modello seguente:

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### Registrazione di set di dati

Dopo avere creato i servizi collegati, sarà necessario definire i set di dati, ovvero definire la struttura dei dati da spostare dall'archiviazione al data warehouse. Altre informazioni sulla creazione

1. Avviare il processo passando alla sezione 'Creare e distribuire' della data factory.

2. Fare clic su 'Nuovo set di dati' e quindi su 'Archivio BLOB di Azure' per collegare la risorsa di archiviazione alla data factory. È possibile usare lo script seguente per definire i dati nell'archivio BLOB di Azure:

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
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



3. È ora possibile definire anche il set di dati per SQL Data Warehouse. Si inizia sempre facendo clic su 'Nuovo set di dati' e quindi su 'Azure SQL Data Warehouse'.

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### Configurazione della pipeline

È infine possibile configurare ed eseguire la pipeline in Data factory di Azure. Questa operazione completerà lo spostamento effettivo dei dati. Per una visualizzazione completa delle operazioni che è possibile completare con SQL Data Warehouse e Data factory di Azure, vedere [qui](../data-factory/data-factory-azure-sql-data-warehouse-connector/).

Nella sezione 'Creare e distribuire' fare clic su 'Più comandi' e quindi 'Nuova pipeline'. Dopo la creazione della pipeline, è possibile usare il codice seguente per trasferire i dati al data warehouse:

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
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
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=AcomDC_1125_2015-->