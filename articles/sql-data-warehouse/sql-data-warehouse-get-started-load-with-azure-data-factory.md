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
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Caricare i dati con Azure Data Factory 

> [AZURE.SELECTOR]
- [Data Factory][]
- [PolyBase][]
- [BCP][]

 Questa esercitazione illustra come creare una pipeline in Azure Data Factory per lo spostamento di dati dai BLOB di Archiviazione di Azure in SQL Data Warehouse. I passaggi seguenti consentono di eseguire queste operazioni:

+ Configurare dati di esempio in un BLOB di Archiviazione di Azure.
+ Connettere le risorse a Data factory di Azure.
+ Creare una pipeline per spostare i dati dai BLOB di archiviazione a SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## Prima di iniziare

Per una panoramica di Azure Data Factory, vedere [Introduzione al servizio Azure Data Factory][].

### Creare o identificare le risorse

Prima di iniziare questa esercitazione, è necessario avere le risorse seguenti.

   + **BLOB del servizio di archiviazione di Azure**: questa esercitazione usa il BLOB del servizio di archiviazione di Azure come origine dati per la pipeline di Azure Data Factory e quindi è necessario che ne sia disponibile uno per archiviare i dati di esempio. Se non ne è già disponibile uno, vedere [Creare un account di archiviazione][].

   + **SQL Data Warehouse**: questa esercitazione sposta i dati dal BLOB di archiviazione di Azure in SQL Data Warehouse e quindi è necessario avere un data warehouse online che viene caricato con i dati di esempio AdventureWorksDW. Se non si ha già un data warehouse, vedere le informazioni su come [effettuare il provisioning][Create a SQL Data Warehouse]. Se è disponibile un data warehouse, ma non ne è stato effettuato il provisioning con i dati di esempio, è possibile [caricarli manualmente][Load sample data into SQL Data Warehouse].

   + **Azure Data Factory**: Azure Data Factory completerà il caricamento effettivo, quindi è necessario averne uno da usare per creare la pipeline per lo spostamento dei dati. Se non ne è già disponibile uno, vedere le informazioni su come crearlo nel passaggio 1 in [Introduzione ad Azure Data Factory (editor di Data Factory)][].

   + **AZCopy**: per copiare i dati di esempio dal client locale al BLOB di archiviazione di Azure, è necessario AZCopy. Per istruzioni di installazione, vedere la [documentazione di AZCopy][].

## Passaggio 1: Copiare i dati di esempio nel BLOB di archiviazione di Azure

Una volta che tutti i componenti sono pronti, è possibile copiare i dati di esempio nel BLOB di archiviazione di Azure.

1. [Scaricare i dati di esempio][]. Questi dati aggiungono altri tre anni di dati di vendita ai dati di esempio AdventureWorksDW.

2. Usare questo comando di AZCopy per copiare i tre anni di dati nel BLOB di archiviazione di Azure.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## Passaggio 2: Connettere le risorse a Azure Data Factory.

Dopo avere inserito i dati, è possibile creare la pipeline di Azure Data Factory per spostare i dati dall'archivio BLOB di Azure in SQL Data Warehouse.

Per iniziare, aprire il [portale di Azure][] e selezionare la data factory dal menu a sinistra.

### Passaggio 2.1: Creare servizi collegati

Collegare l'account di archiviazione di Azure e SQL Data Warehouse alla data factory.

1. Iniziare prima di tutto il processo di registrazione facendo clic sulla sezione "Servizi collegati" della data factory e quindi su "Nuovo archivio dati". Scegliere quindi un nome per registrare la risorsa di archiviazione di Azure, selezionare Archiviazione di Azure come tipo e immettere il nome dell'account e la chiave dell'account.

2. Per registrare SQL Data Warehouse passare alla sezione "Creare e distribuire", quindi selezionare "Nuovo archivio dati" e infine "Azure SQL Data Warehouse". Copiare e incollare in questo modello e quindi immettere le informazioni specifiche.

```JSON
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
```

### Passaggio 2.2: Definire il set di dati

Dopo avere creato i servizi collegati, sarà necessario definire i set di dati, ovvero definire la struttura dei dati da spostare dall'archiviazione al data warehouse. Altre informazioni sulla creazione

1. Avviare il processo passando alla sezione 'Creare e distribuire' della data factory.

2. Fare clic su 'Nuovo set di dati' e quindi su 'Archivio BLOB di Azure' per collegare la risorsa di archiviazione alla data factory. È possibile usare lo script seguente per definire i dati nell'archivio BLOB di Azure:

```JSON
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
```


3. È ora possibile definire anche il set di dati per SQL Data Warehouse. Si inizia sempre facendo clic su 'Nuovo set di dati' e quindi su 'Azure SQL Data Warehouse'.

```JSON
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
```

## Passaggio 3: Creare ed eseguire la pipeline

È infine possibile configurare ed eseguire la pipeline in Data factory di Azure. Questa operazione completerà lo spostamento effettivo dei dati. Per una visualizzazione completa delle operazioni che è possibile completare con SQL Data Warehouse e Data factory di Azure, vedere [qui][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Nella sezione 'Creare e distribuire' fare clic su 'Più comandi' e quindi 'Nuova pipeline'. Dopo la creazione della pipeline, è possibile usare il codice seguente per trasferire i dati al data warehouse:

```JSON
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
```

## Passaggi successivi

Per altre informazioni, vedere:

- [Percorso di apprendimento per Azure Data Factory][].
- [Connettore Azure SQL Data Warehouse][]. Questo è l'argomento di riferimento di base per l'uso di Azure Data Factory con Azure SQL Data Warehouse.


Questi argomenti forniscono informazioni dettagliate su Azure Data Factory. Illustrano il database SQL di Azure o HDinsight, ma le informazioni si applicano anche ad Azure SQL Data Warehouse.

- [Esercitazione: Creare la prima data factory][]\: questa è l'esercitazione di base per l'elaborazione dei dati con Azure Data Factory. In questa esercitazione si apprenderà come creare la prima pipeline che usa HDInsight per trasformare e analizzare i blog ogni mese. Si noti che questa esercitazione non prevede attività di copia.
- [Esercitazione: Copiare i dati dal BLOB di archiviazione di Azure al database SQL di Azure][]. In questa esercitazione si creerà una pipeline in Azure Data Factory per copiare i dati da un BLOB di archiviazione di Azure in un database SQL di Azure.

<!--Image references-->

<!--Article references-->
[documentazione di AZCopy]: ../storage/storage-use-azcopy.md
[Connettore Azure SQL Data Warehouse]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Creare un account di archiviazione]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Introduzione ad Azure Data Factory (editor di Data Factory)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introduzione al servizio Azure Data Factory]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Esercitazione: Copiare i dati dal BLOB di archiviazione di Azure al database SQL di Azure]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Esercitazione: Creare la prima data factory]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Percorso di apprendimento per Azure Data Factory]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[portale di Azure]: https://portal.azure.com
[Scaricare i dati di esempio]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv

<!---HONumber=AcomDC_0817_2016-->