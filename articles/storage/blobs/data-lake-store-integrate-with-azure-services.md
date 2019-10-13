---
title: Integrare Azure Data Lake Storage con i servizi di Azure | Microsoft Docs
description: Informazioni sui servizi di Azure che si integrano con Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.openlocfilehash: e073c02f19a9add37e684a76839a620af0d07ec0
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301321"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Integrare Azure Data Lake Storage con i servizi di Azure

È possibile usare i servizi di Azure per inserire dati, eseguire analisi e creare rappresentazioni visive. Questo articolo fornisce un elenco dei servizi di Azure supportati e fornisce collegamenti ad articoli che consentono di usare questi servizi con Azure Data Lake Storage Gen2.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Servizi di Azure che supportano Azure Data Lake Storage Gen2

La tabella seguente elenca i servizi di Azure che supportano Azure Data Lake Storage Gen2.

| Servizio di Azure |  Articoli correlati |
|---------------|-------------------|
|Data factory di Azure | [Caricare dati in Azure Data Lake Storage Gen2 con Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Usare con Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Avvio rapido: Analizzare i dati in Azure Data Lake Storage Gen2 con Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Esercitazione: Estrarre, trasformare e caricare dati con Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Esercitazione: Accedere ai dati di Data Lake Storage Gen2 con Azure Databricks mediante Spark @ no__t-0 |
|Acquisizione di hub eventi di Azure| [Acquisire eventi tramite hub eventi di Azure nell'archivio BLOB di Azure o Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|App per la logica di Azure | [Panoramica: che cos'è app per la logica di Azure?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Ricerca di Azure | [Ricerca nell'archivio BLOB tramite Ricerca di Azure](https://docs.microsoft.com/azure/search/search-blob-storage-integration)|
|Analisi di flusso di Azure| [Uscita per Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|DataBox|  [Usare Azure Data Box per migrare i dati da un archivio HDFS locale ad archiviazione di Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Uso dell'interfaccia della riga di comando di HDFS con Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Esercitazione: Estrarre, trasformare e caricare i dati usando Apache Hive in Azure HDInsight @ no__t-0 |
|Hub Internet delle cose | [Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Analizzare i dati in Data Lake Storage Gen2 usando Power BI](data-lake-storage-use-power-bi.md) |
|SQL Data Warehouse | [Usare con Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Gestione connessione di archiviazione di Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli strumenti che è possibile usare per elaborare i dati in data Lake. [Per i requisiti Big data, vedere uso di Azure Data Lake storage Gen2](data-lake-storage-data-scenarios.md).

- Scopri di più su Data Lake Storage Gen2 e su come iniziare a usarlo. Vedere [Introduzione a Azure Data Lake storage Gen2](data-lake-storage-introduction.md)