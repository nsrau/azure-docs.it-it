---
title: Servizi di Azure che supportano Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sui servizi di Azure che si integrano con Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: cb68f1bc851a8573ddec01d1eee803135a11b067
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195359"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Servizi di Azure che supportano Azure Data Lake Storage Gen2

È possibile usare i servizi di Azure per inserire dati, eseguire analisi e creare rappresentazioni visive. Questo articolo fornisce un elenco dei servizi di Azure supportati, divulga il livello di supporto e fornisce collegamenti ad articoli che consentono di usare questi servizi con Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Servizi di Azure supportati

Questa tabella elenca i servizi di Azure che è possibile usare con Azure Data Lake Storage Gen2. Gli elementi visualizzati in queste tabelle verranno modificati nel corso del tempo poiché il supporto continua ad espandersi.

> [!NOTE]
> Il livello di supporto si riferisce solo al modo in cui il servizio è supportato con Data Lake Storage generazione 2.

|Servizio di Azure |Livello di supporto |Articoli correlati |
|---------------|-------------------|---|
|Data factory di Azure|Disponibile a livello generale|[Caricare dati in Azure Data Lake Storage Gen2 con Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponibile a livello generale|[Usare con Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Guida introduttiva: analizzare i dati in Azure Data Lake Storage Gen2 tramite Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Esercitazione: estrarre, trasformare e caricare i dati usando Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Esercitazione: accedere ai dati di Data Lake Storage Gen2 con Azure Databricks mediante Spark](data-lake-storage-use-databricks-spark.md)|
|Acquisizione di hub eventi di Azure| Disponibile a livello generale|[Acquisire eventi tramite hub eventi di Azure nell'archivio BLOB di Azure o Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|App per la logica di Azure|Disponibile a livello generale|[Panoramica: che cos'è app per la logica di Azure?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Disponibile a livello generale|[Accedere ai dati nei servizi di archiviazione di Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Analisi di flusso di Azure|Disponibile a livello generale|[Avvio rapido: creare un processo di Analisi di flusso di Azure tramite il portale di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Uscita per Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| Disponibile a livello generale|[Usare Azure Data Box per migrare i dati da un archivio HDFS locale ad archiviazione di Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | Disponibile a livello generale|[Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Uso dell'interfaccia della riga di comando di HDFS con Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Esercitazione: estrarre, trasformare e caricare i dati usando Apache Hive in Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Hub IoT | Disponibile a livello generale|[Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| Disponibile a livello generale|[Analizzare i dati in Data Lake Storage Gen2 usando Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Disponibile a livello generale|[Usare con Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Disponibile a livello generale|[Gestione connessione di archiviazione di Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Ricerca cognitiva di Azure| Anteprima|[Indicizzare e cercare Azure Data Lake Storage Gen2 documenti (anteprima)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Rete per la distribuzione di contenuti di Azure|Non ancora supportato|[Indicizzare e cercare Azure Data Lake Storage Gen2 documenti (anteprima)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Vedere anche

- [Problemi noti relativi a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)