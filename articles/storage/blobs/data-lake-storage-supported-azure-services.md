---
title: Servizi di Azure che supportano Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sui servizi di Azure che si integrano con Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e96fa932185a6689c360fe86d519a45581059118
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912638"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Servizi di Azure che supportano Azure Data Lake Storage Gen2

È possibile usare i servizi di Azure per inserire dati, eseguire analisi e creare rappresentazioni visive. Questo articolo fornisce un elenco dei servizi di Azure supportati, divulga il livello di supporto e fornisce collegamenti ad articoli che consentono di usare questi servizi con Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Servizi di Azure supportati

Questa tabella elenca i servizi di Azure che è possibile usare con Azure Data Lake Storage Gen2. Gli elementi visualizzati in queste tabelle verranno modificati nel corso del tempo man mano che il supporto continua ad espandersi.

> [!NOTE]
> Il livello di supporto si riferisce solo al modo in cui il servizio è supportato con Data Lake Storage generazione 2.

|Servizio di Azure |Livello di supporto |Azure AD |Chiave condivisa| Articoli correlati |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Disponibile a livello generale|Sì|Sì|[Caricare dati in Azure Data Lake Storage Gen2 con Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponibile a livello generale|Sì|Sì|[Uso con Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Guida introduttiva: analizzare i dati in Azure Data Lake Storage Gen2 tramite Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Esercitazione: Estrarre, trasformare e caricare dati tramite Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse) <br>[Esercitazione: accedere ai dati di Data Lake Storage Gen2 con Azure Databricks mediante Spark](data-lake-storage-use-databricks-spark.md)|
|Hub eventi di Azure|Disponibile a livello generale|No|Sì|[Acquisire eventi tramite Hub eventi di Azure in Archiviazione BLOB di Azure o Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md)|
|Griglia di eventi di Azure|Disponibile a livello generale|Sì|Sì|[Esercitazione: Implementare il modello di acquisizione data lake per aggiornare una tabella di Databricks Delta](data-lake-storage-events.md)|
|App per la logica di Azure|Disponibile a livello generale|No|Sì|[Panoramica - Informazioni su App per la logica di Azure](../../logic-apps/logic-apps-overview.md)|
|Azure Machine Learning|Disponibile a livello generale|Sì|Sì|[Accedere ai dati nei servizi di archiviazione di Azure](../../machine-learning/how-to-access-data.md)|
|Analisi di flusso di Azure|Disponibile a livello generale|Sì|Sì|[Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Uscita per Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Data Box|Disponibile a livello generale|No|Sì|[Usare Azure Data Box per migrare i dati da un archivio HDFS locale ad archiviazione di Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Disponibile a livello generale|Sì|Sì|[Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Uso dell'interfaccia della riga di comando di Hadoop Distributed File System con Anteprima di Azure Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Esercitazione: estrarre, trasformare e caricare i dati usando Apache Hive in Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Hub IoT |Disponibile a livello generale|No|Sì|[Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi](../../iot-hub/iot-hub-devguide-messages-d2c.md)|
|Power BI|Disponibile a livello generale|Sì|Sì|[Analizzare i dati in Data Lake Storage Gen2 usando Power BI](/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (in precedenza SQL Data Warehouse)|Disponibile a livello generale|Sì|Sì|[Uso con Azure sinapsi Analytics (in precedenza SQL Data Warehouse)](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|SQL Server Integration Services (SSIS)|Disponibile a livello generale|Sì|Sì|[Gestione connessione di archiviazione di Azure](/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Esplora dati di Azure|Disponibile a livello generale|Sì|Sì|[Eseguire query sui dati in Azure Data Lake usando Azure Esplora dati](/azure/data-explorer/data-lake-query-data)|
|Ricerca cognitiva di Azure|Anteprima|Sì|Sì|[Indicizzare e cercare Azure Data Lake Storage Gen2 documenti (anteprima)](../../search/search-howto-index-azure-data-lake-storage.md)|
|Rete per la distribuzione di contenuti di Azure|Non ancora supportato|Non applicabile|Non applicabile|[Indicizzare e cercare Azure Data Lake Storage Gen2 documenti (anteprima)](../../cdn/cdn-overview.md)|

## <a name="see-also"></a>Vedere anche

- [Problemi noti di Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)