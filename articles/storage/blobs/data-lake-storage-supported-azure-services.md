---
title: Servizi di Azure che supportano Azure Data Lake Storage Gen2 Documenti Microsoft
description: Informazioni sui servizi di Azure che si integrano con Azure Data Lake Storage Gen2Learn about which Azure services integrate with Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878324"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure services that support Azure Data Lake Storage Gen2

È possibile usare i servizi di Azure per l'inserimento di dati, l'esecuzione di analisi e la creazione di rappresentazioni visive. Questo articolo fornisce un elenco dei servizi di Azure supportati, ne rivela il livello di supporto e fornisce collegamenti ad articoli che consentono di usare questi servizi con Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Servizi di Azure supportati

Questa tabella elenca i servizi di Azure che è possibile usare con Archiviazione dati di Azure2.This table lists the Azure services that you can use with Azure Data Lake Storage Gen2. Gli elementi visualizzati in queste tabelle cambieranno nel tempo man mano che il supporto continua a espandersi.

> [!NOTE]
> Il livello di supporto si riferisce solo al modo in cui il servizio è supportato con Data Lake Storage Gen 2.Support level refers only to how the service is supported with Data Lake Storage Gen 2.

|Servizio di Azure |Livello di supporto |Articoli correlati |
|---------------|-------------------|---|
|Data factory di Azure|Disponibile a livello generale|[Caricare dati in Azure Data Lake Storage Gen2 con Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponibile a livello generale|[Uso con Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Guida introduttiva: Analizzare i dati in Azure Data Lake Storage Gen2 usando Azure DatabricksQuickstart: Analyze data in Azure Data Lake Storage Gen2 by using Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Esercitazione: Estrarre, trasformare e caricare dati usando Azure DatabricksTutorial: Extract, transform, and load data by using Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Esercitazione: Accedere ai dati di Data Lake Storage Gen2 con Azure Databricks tramite SparkTutorial: Access Data Lake Storage Gen2 data with Azure Databricks using Spark](data-lake-storage-use-databricks-spark.md)|
|Hub eventi di Azure|Disponibile a livello generale|[Acquisire eventi tramite Hub eventi di Azure in Archiviazione BLOB di Azure o Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Griglia di eventi di Azure|Disponibile a livello generale|[Esercitazione: Implementare il modello di acquisizione del data lake per aggiornare una tabella Delta di DatabricksTutorial: Implement the data lake capture pattern to update a Databricks Delta table](data-lake-storage-events.md)|
|App per la logica di Azure|Disponibile a livello generale|[Panoramica - Informazioni su App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Disponibile a livello generale|[Accedere ai dati nei servizi di archiviazione di AzureAccess data in Azure storage services](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Analisi di flusso di Azure|Disponibile a livello generale|[Avvio rapido: creare un processo di Analisi di flusso di Azure tramite il portale di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Uscita da Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Disponibile a livello generale|[Usare Azure Data Box per eseguire la migrazione dei dati da un archivio HDFS locale ad Archiviazione di AzureUse Azure Data Box to migrate data from an on-premises HDFS store to Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Disponibile a livello generale|[Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Uso dell'interfaccia della riga di comando di Hadoop Distributed File System con Anteprima di Azure Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Esercitazione: Estrarre, trasformare e caricare dati usando Apache Hive in Azure HDInsightTutorial: Extract, transform, and load data by using Apache Hive on Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Hub IoT |Disponibile a livello generale|[Usare il routing dei messaggi dell'hub IoT per inviare messaggi da dispositivo a cloud a endpoint diversiUse IoT Hub message routing to send device-to-cloud messages to different endpoints](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Disponibile a livello generale|[Analizzare i dati in Data Lake Storage Gen2 usando Power BIAnalyze data in Data Lake Storage Gen2 using Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Disponibile a livello generale|[Uso con Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Disponibile a livello generale|[Gestione connessione Archiviazione di AzureAzure Storage connection manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Ricerca cognitiva di Azure|Anteprima|[Indicizzazione e ricerca di documenti Gen2 di Azure Data Lake Storage (anteprima)Index and search Azure Data Lake Storage Gen2 documents (preview)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Esplora dati di Azure|Anteprima|[Query data in Azure Data Lake using Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Rete per la distribuzione di contenuti di Azure|Non ancora supportato|[Indicizzazione e ricerca di documenti Gen2 di Azure Data Lake Storage (anteprima)Index and search Azure Data Lake Storage Gen2 documents (preview)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Vedere anche

- [Problemi noti con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Blob storage features available in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Open source platforms that support Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)