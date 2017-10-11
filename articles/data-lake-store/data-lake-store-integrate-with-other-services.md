---
title: Integrazione di Data Lake Store con altri servizi di Azure | Documentazione Microsoft
description: "Informazioni sulle modalità di integrazione di Data Lake Store con altri servizi di Azure"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: de7aff6b31d937576da65498c5fcce2ae9abdbf1
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrazione di Data Lake Store con altri servizi di Azure
Azure Data Lake Store può essere usato in combinazione con altri servizi di Azure per abilitare il supporto di una gamma più ampia di scenari. L'articolo seguente elenca i servizi con cui Data Lake Store può essere integrato.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Usare Archivio Data Lake con Azure HDInsight
È possibile eseguire il provisioning di un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) che usa Data Lake Store come archivio conforme a HDFS. Per questa versione, per i cluster Hadoop e Storm in Windows e Linux è possibile usare Data Lake Store solo come un'ulteriore risorsa di archiviazione. Tali cluster continuano a usare l'archiviazione di Azure (WASB) come risorsa di archiviazione predefinita. Tuttavia, per i cluster HBase in Windows e Linux è possibile usare Data Lake Store come risorsa di archiviazione predefinita o come risorsa di archiviazione aggiuntiva oppure in entrambe le modalità.

Per istruzioni su come eseguire il provisioning di un cluster HDInsight con Data Lake Store, vedere:

* [Effettuare il provisioning di un cluster HDInsight con Archivio Data Lake tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Eseguire il provisioning di un cluster HDInsight con Data Lake Store come archivio predefinito mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Eseguire il provisioning di un cluster HDInsight con Data Lake Store come archivio aggiuntivo mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Usare Archivio Data Lake con Analisi Data Lake di Azure
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) consente di usare i Big Data a livello di cloud. Il servizio effettua il provisioning dinamico delle risorse e permette di svolgere analisi su terabyte e addirittura exabyte di dati che possono essere archiviati nelle numerose origini dati supportate, ad esempio Data Lake Store. Data Lake Analytics è ottimizzato in modo specifico per usare Azure Data Lake Store in modo da fornire il più elevato livello di prestazioni, velocità effettiva e parallelizzazione dei carichi di lavoro dei Big Data.

Per istruzioni su come usare Data Lake Analytics con Data Lake Store, vedere [Introduzione a Data Lake Analytics mediante Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Usare Archivio Data Lake con Data factory di Azure
È possibile usare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per inserire dati da tabelle di Azure, database SQL di Azure, data warehouse SQL di Azure, BLOB di archiviazione di Azure e database locali. Essendo un elemento di primo piano nell'ecosistema Azure, Data factory di Azure può essere usato per gestire l'inserimento di dati da queste origini nel servizio Data Lake Store di Azure.

Per istruzioni su come usare Data factory di Azure con Data Lake Store, vedere [Spostare dati da e verso Data Lake Store mediante Data factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake
Archivio Data Lake di Azure fornisce uno strumento da riga di comando, AdlCopy, che consente di copiare dati da Archiviazione BLOB di Azure a un account di Archivio Data Lake. Per altre informazioni, vedere [Copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copiare i dati dal database SQL di Azure nell'Archivio Data Lake
È possibile usare Apache Sqoop per importare ed esportare dati da un database SQL di Azure in un Archivio Data Lake. Per altre informazioni, vedere [Copiare i dati tra Archivio Data Lake e un database SQL di Azure usando Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Uso di Archivio Data Lake con l'analisi di flusso
È possibile usare Archivio Data Lake come uno degli output per archiviare dati di flusso usando l'analisi di flusso di Azure. Per altre informazioni, vedere [Trasmettere i dati dal BLOB di archiviazione di Azure ad Archivio Data Lake usando l'analisi di flusso di Azure](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Uso di Archivio Data Lake con Power BI
È possibile usare Power BI per importare dati da un account Archivio Data Lake per analizzare e visualizzare i dati stessi. Per altre informazioni, vedere [Analisi dei dati in Archivio Data Lake con Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Uso di Archivio Data Lake con Catalogo dati di Azure
È possibile registrare dati da Archivio Data Lake nel Catalogo dati di Azure per rendere i dati individuabili nell'intera organizzazione. Per altre informazioni, vedere [Registrazione i dati da Archivio Data Lake in Catalogo dati di Azure](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Uso di Data Lake Store con SQL Server Integration Services (SSIS)
È possibile usare la gestione connessioni di Azure Data Lake Store in SSIS per connettere un pacchetto SSIS con Azure Data Lake Store. Per altre informazioni, vedere [Usare Data Lake Store con SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Uso di Data Lake Store con SQL Data Warehouse
È possibile usare PolyBase per caricare dati da Azure Data Lake Store in SQL Data Warehouse. Per altre informazioni, vedere [Usare Data Lake Store con SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Usare Data Lake Store con Hub eventi di Azure
È possibile usare Azure Data Lake Store per archiviare e acquisire i dati ricevuti da Hub eventi di Azure. Per altre informazioni, vedere [Usare Data Lake Store con Hub eventi di Azure](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Vedere anche
* [Panoramica di Data Lake Store di Azure](data-lake-store-overview.md)
* [Introduzione a Data Lake Store mediante il portale](data-lake-store-get-started-portal.md)
* [Introduzione a Data Lake Store mediante PowerShell](data-lake-store-get-started-powershell.md)  


