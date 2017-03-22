---
redirect_url: /azure/sql-data-warehouse/sql-data-warehouse-load-with-data-factory
title: Caricare i dati con Data factory di Azure | Documentazione Microsoft
description: Informazioni su come caricare i dati con Data factory di Azure
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: ac7ddaa7-a3a5-4e15-b3cf-c696d2d105df
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: mausher;barbkess
ms.custom: loading
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 2f0aa3ab44813529525108758785ea3ceb65311b
ms.lasthandoff: 01/30/2017


---
# <a name="load-data-with-azure-data-factory"></a>Caricare i dati con Azure Data Factory
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)  
> 
> 

Questa esercitazione illustra come creare una pipeline in Azure Data Factory per lo spostamento di dati da un BLOB del servizio di archiviazione di Azure ad Azure SQL Data Warehouse. I passaggi seguenti consentono di eseguire queste operazioni:

* Configurare dati di esempio in un BLOB del servizio di archiviazione di Azure.
* Connettere le risorse a Data factory di Azure.
* Creare una pipeline per spostare i dati dai BLOB di archiviazione a SQL Data Warehouse.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-Azure-SQL-Data-Warehouse-with-Azure-Data-Factory/player]
> 
> 

## <a name="before-you-begin"></a>Prima di iniziare
Per una panoramica di Azure Data Factory, vedere [Introduzione al servizio Azure Data Factory][Introduction to Azure Data Factory].

### <a name="create-or-identify-resources"></a>Creare o identificare le risorse
Prima di iniziare questa esercitazione, è necessario avere le risorse seguenti.

* **BLOB del servizio di archiviazione di Azure**: questa esercitazione usa il BLOB del servizio di archiviazione di Azure come origine dati per la pipeline di Azure Data Factory e quindi è necessario che ne sia disponibile uno per archiviare i dati di esempio. Se non ne è già disponibile uno, vedere [Creare un account di archiviazione][Create a storage account].
* **SQL Data Warehouse**: questa esercitazione sposta i dati da un BLOB del servizio di archiviazione di Azure a SQL Data Warehouse ed è quindi necessario avere un data warehouse online caricato con i dati di esempio AdventureWorksDW. Se non si ha già un data warehouse, vedere le informazioni su come [effettuare il provisioning][Create a SQL Data Warehouse]. Se è disponibile un data warehouse, ma non ne è stato effettuato il provisioning con i dati di esempio, è possibile [caricarli manualmente][Load sample data into SQL Data Warehouse].
* **Azure Data Factory**: Azure Data Factory completa il caricamento effettivo ed è quindi necessario avere una data factory da poter usare per creare la pipeline per lo spostamento dei dati. Se non si ha una data factory, per informazioni su come crearne una vedere il passaggio 1 dell'[introduzione ad Azure Data Factory (editor di Data Factory)][Get started with Azure Data Factory (Data Factory Editor)].
* **AZCopy**: per copiare i dati di esempio dal client locale al BLOB di archiviazione di Azure, è necessario AZCopy. Per istruzioni di installazione, vedere la [documentazione di AZCopy][AZCopy documentation].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Passaggio 1: Copiare i dati di esempio nel BLOB di archiviazione di Azure
Quando tutti i componenti sono pronti, è possibile copiare i dati di esempio nel BLOB del servizio di archiviazione di Azure.

1. [Scaricare i dati di esempio][Download sample data]. Questi dati aggiungono altri tre anni di dati di vendita ai dati di esempio AdventureWorksDW.
2. Usare questo comando di AZCopy per copiare i tre anni di dati nel BLOB di archiviazione di Azure.
   
    ````
    AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
    ````

## <a name="step-2-connect-resources-to-azure-data-factory"></a>Passaggio 2: Connettere le risorse a Azure Data Factory.
Dopo avere inserito i dati, è possibile creare la pipeline di Azure Data Factory per spostare i dati dall'archivio BLOB di Azure in SQL Data Warehouse.

Per iniziare, aprire il [portale di Azure][Azure portal] e selezionare la data factory dal menu a sinistra.

### <a name="step-21-create-linked-service"></a>Passaggio 2.1: Creare servizi collegati
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

### <a name="step-22-define-the-dataset"></a>Passaggio 2.2: Definire il set di dati
Dopo avere creato i servizi collegati, sarà necessario definire i set di dati,  ovvero definire la struttura dei dati da spostare dall'archiviazione al data warehouse.  Altre informazioni sulla creazione

1. Avviare il processo passando alla sezione 'Creare e distribuire' della data factory.
2. Fare clic su 'Nuovo set di dati' e quindi su 'Archivio BLOB di Azure' per collegare la risorsa di archiviazione alla data factory.  È possibile usare lo script seguente per definire i dati nell'archivio BLOB di Azure:
   
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
3. Verrà ora definito il set di dati per SQL Data Warehouse. Si inizia sempre facendo clic su 'Nuovo set di dati' e quindi su 'Azure SQL Data Warehouse'.
   
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

## <a name="step-3-create-and-run-your-pipeline"></a>Passaggio 3: Creare ed eseguire la pipeline
Viene infine configurata ed eseguita la pipeline in Azure Data Factory.  Questa operazione completa lo spostamento effettivo dei dati.  Per una visualizzazione completa delle operazioni che è possibile completare con SQL Data Warehouse e Azure Data Factory, vedere [qui][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Nella sezione "Creare e distribuire" fare clic su "Più comandi" e quindi su "Nuova pipeline".  Dopo la creazione della pipeline, è possibile usare il codice seguente per trasferire i dati al data warehouse:

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

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere:

* [Percorso di apprendimento per Azure Data Factory][Azure Data Factory learning path].
* [Connettore Azure SQL Data Warehouse][Azure SQL Data Warehouse Connector]. Questo è l'argomento di riferimento di base per l'uso di Azure Data Factory con Azure SQL Data Warehouse.

Questi argomenti forniscono informazioni dettagliate su Azure Data Factory. Illustrano il database SQL di Azure o HDInsight, ma le informazioni si applicano anche ad Azure SQL Data Warehouse.

* [Esercitazione: Creare la prima data factory][Tutorial: Get started with Azure Data Factory]: questa è l'esercitazione di base per l'elaborazione dei dati con Azure Data Factory. In questa esercitazione verrà creata una prima pipeline che usa HDInsight per trasformare e analizzare i blog ogni mese. Si noti che questa esercitazione non prevede attività di copia.
* [Esercitazione: Copiare i dati dal BLOB di archiviazione di Azure al database SQL di Azure][Tutorial: Copy data from Azure Storage Blob to Azure SQL Database]. In questa esercitazione verrà creata una pipeline in Azure Data Factory per copiare i dati da un BLOB del servizio di archiviazione di Azure a un database SQL di Azure.

<!--Image references-->

<!--Article references-->
[AZCopy documentation]: ../storage/storage-use-azcopy.md
[Azure SQL Data Warehouse Connector]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Create a storage account]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Get started with Azure Data Factory (Data Factory Editor)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introduction to Azure Data Factory]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Tutorial: Copy data from Azure Storage Blob to Azure SQL Database]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Tutorial: Get started with Azure Data Factory]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory learning path]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure portal]: https://portal.azure.com
[Download sample data]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv

