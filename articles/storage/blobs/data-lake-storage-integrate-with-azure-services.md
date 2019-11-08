---
title: Integrare Azure Data Lake Storage con i servizi di Azure | Microsoft Docs
description: Informazioni sui servizi di Azure che si integrano con Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: c7223274417ef4c911c32acbcde1511682d6d9e0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796077"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Integrare Azure Data Lake Storage con i servizi di Azure

È possibile usare i servizi di Azure per inserire dati, eseguire analisi e creare rappresentazioni visive. Questo articolo fornisce un elenco dei servizi di Azure supportati e fornisce collegamenti ad articoli che consentono di usare questi servizi con Azure Data Lake Storage Gen2.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Servizi di Azure che supportano Azure Data Lake Storage Gen2

La tabella seguente elenca i servizi di Azure che supportano Azure Data Lake Storage Gen2.

| Servizio di Azure |  Articoli correlati |
|---------------|-------------------|
|Data factory di Azure | [Caricare dati in Azure Data Lake Storage Gen2 con Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Usare con Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Guida introduttiva: analizzare i dati in Azure Data Lake Storage Gen2 tramite Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Esercitazione: estrarre, trasformare e caricare i dati usando Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Esercitazione: accedere ai dati di Data Lake Storage Gen2 con Azure Databricks mediante Spark](data-lake-storage-use-databricks-spark.md) |
|Acquisizione di hub eventi di Azure| [Acquisire eventi tramite hub eventi di Azure nell'archivio BLOB di Azure o Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|App per la logica di Azure | [Panoramica: che cos'è app per la logica di Azure?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[Accedere ai dati nei servizi di archiviazione di Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data)|
|Ricerca cognitiva di Azure | [Indicizzare e cercare Azure Data Lake Storage Gen2 documenti (anteprima)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Analisi di flusso di Azure| [Guida introduttiva: Creare un processo di Analisi di flusso di Azure tramite il portale di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Uscita per Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Usare Azure Data Box per migrare i dati da un archivio HDFS locale ad archiviazione di Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Uso dell'interfaccia della riga di comando di HDFS con Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Esercitazione: estrarre, trasformare e caricare i dati usando Apache Hive in Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|Hub IoT | [Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Analizzare i dati in Data Lake Storage Gen2 usando Power BI](data-lake-storage-use-power-bi.md) |
|SQL Data Warehouse | [Usare con Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Gestione connessione di archiviazione di Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli strumenti che è possibile usare per elaborare i dati in data Lake. [Per i requisiti Big data, vedere uso di Azure Data Lake storage Gen2](data-lake-storage-data-scenarios.md).

- Scopri di più su Data Lake Storage Gen2 e su come iniziare a usarlo. Vedere [Introduzione a Azure Data Lake storage Gen2](data-lake-storage-introduction.md)
