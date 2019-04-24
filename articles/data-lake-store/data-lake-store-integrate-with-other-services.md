---
title: Integrazione di Azure Data Lake Storage Gen1 con altri servizi di Azure | Microsoft Docs
description: Informazioni sulle modalità di integrazione di Data Lake Storage Gen1 con altri servizi di Azure
documentationcenter: ''
services: data-lake-store
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: e28863f9980d6403bef1f88de01b7a9b5271b444
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197086"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrazione di Azure Data Lake Storage Gen1 con altri servizi di Azure
Azure Data Lake Storage Gen1 può essere usato in combinazione con altri servizi di Azure per abilitare il supporto di una gamma più ampia di scenari. L'articolo seguente elenca i servizi con cui Data Lake Storage Gen1 può essere integrato.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Usare Data Lake Storage Gen1 con Azure HDInsight
È possibile eseguire il provisioning di un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) che usa Data Lake Storage Gen1 come risorsa di archiviazione conforme a HDFS. Per questa versione, per i cluster Hadoop e Storm in Windows e Linux è possibile usare Data Lake Storage Gen1 solo come risorsa di archiviazione aggiuntiva. Tali cluster continuano a usare l'archiviazione di Azure (WASB) come risorsa di archiviazione predefinita. Tuttavia, per i cluster HBase in Windows e Linux è possibile usare Data Lake Storage Gen1 come risorsa di archiviazione predefinita o come risorsa di archiviazione aggiuntiva oppure in entrambe le modalità.

Per istruzioni su come eseguire il provisioning di un cluster HDInsight con Data Lake Storage Gen1, vedere:

* [Effettuare il provisioning di un cluster HDInsight con Data Lake Storage Gen1 tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Effettuare il provisioning di un cluster HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione predefinita usando Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Effettuare il provisioning di un cluster HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva usando Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Usare Data Lake Storage Gen1 con Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) consente di usare i Big Data a livello di cloud. Il servizio effettua il provisioning dinamico delle risorse e permette di svolgere analisi su terabyte e addirittura exabyte di dati che possono essere archiviati nelle numerose origini dati supportate, ad esempio Data Lake Storage Gen1. Data Lake Analytics è ottimizzato in modo specifico per usare Data Lake Storage Gen1 in modo da offrire il più elevato livello di prestazioni, velocità effettiva e parallelizzazione per i carichi di lavoro dei Big Data.

Per istruzioni su come usare Data Lake Analytics con Data Lake Storage Gen1, vedere [Introduzione a Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Usare Data Lake Storage Gen1 con Azure Data Factory
È possibile usare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per inserire dati da tabelle di Azure, database SQL di Azure, data warehouse SQL di Azure, BLOB di archiviazione di Azure e database locali. Essendo un elemento di primo piano nell'ecosistema Azure, Azure Data Factory può essere usato per gestire l'inserimento di dati da queste origini a Data Lake Storage Gen1.

Per istruzioni su come usare Azure Data Factory con Data Lake Storage Gen1, vedere [Spostare dati da e verso Data Lake Storage Gen1 usando Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Copiare i dati da BLOB di archiviazione di Azure a Data Lake Storage Gen1
Azure Data Lake Storage Gen1 offre uno strumento da riga di comando, AdlCopy, che consente di copiare dati da Archiviazione BLOB di Azure a un account di Data Lake Storage Gen1. Per altre informazioni, vedere [Copiare i dati da BLOB di archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Copiare i dati tra un database SQL di Azure e Data Lake Storage Gen1
È possibile usare Apache Sqoop per importare ed esportare dati tra un database SQL di Azure e Data Lake Storage Gen1. Per altre informazioni, vedere [Copiare i dati tra Data Lake Storage Gen1 e un database SQL di Azure usando Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Usare Data Lake Storage Gen1 con l'analisi di flusso
È possibile usare Data Lake Storage Gen1 come uno degli output per archiviare dati di flusso usando l'analisi di flusso di Azure. Per altre informazioni, vedere [Trasmettere i dati dal BLOB di archiviazione di Azure a Data Lake Storage Gen1 usando l'analisi di flusso di Azure](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Usare Data Lake Storage Gen1 con Power BI
È possibile usare Power BI per importare dati da un account Data Lake Storage Gen1 per analizzare e visualizzare i dati. Per altre informazioni, vedere [Analisi dei dati in Data Lake Storage Gen1 con Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Usare Data Lake Storage Gen1 con Data Catalog
È possibile registrare dati di Data Lake Storage Gen1 in Azure Data Catalog per rendere i dati individuabili nell'intera organizzazione. Per altre informazioni, vedere [Registrare i dati di Data Lake Storage Gen1 in Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Usare Data Lake Storage Gen1 con SQL Server Integration Services (SSIS)
È possibile usare la gestione connessione di Data Lake Storage Gen1 in SSIS per connettere un pacchetto SSIS a Data Lake Storage Gen1. Per altre informazioni, vedere [Usare Data Lake Storage Gen1 con SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Usare Data Lake Storage Gen1 con SQL Data Warehouse
È possibile usare PolyBase per caricare dati di Data Lake Storage Gen1 in SQL Data Warehouse. Per altre informazioni, vedere [Usare Data Lake Storage Gen1 con SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Usare Data Lake Storage Gen1 con Hub eventi di Azure
È possibile usare Azure Data Lake Storage Gen1 per archiviare e acquisire i dati ricevuti da Hub eventi di Azure. Per altre informazioni, vedere [Usare Data Lake Storage Gen1 con Hub eventi di Azure](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Vedere anche 
* [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introduzione a Data Lake Storage Gen1 con il portale](data-lake-store-get-started-portal.md)
* [Iniziare a usare Data Lake Storage Gen1 tramite PowerShell](data-lake-store-get-started-powershell.md)  

