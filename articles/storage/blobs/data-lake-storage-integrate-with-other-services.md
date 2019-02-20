---
title: Integrazione di Azure Data Lake Storage Gen2 con altri servizi di Azure | Microsoft Docs
description: Informazioni sulle modalità di integrazione di Azure Data Lake Storage Gen2 con altri servizi di Azure
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885542"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>Integrare Azure Data Lake Storage Gen2 con altri servizi di Azure

È possibile usare i servizi di Azure per inserire, analizzare e visualizzare i dati nell'account di archiviazione di Azure Data Lake Storage Gen2. Scegliere i servizi più adatti alle attività che si sta tentando di eseguire.

## <a name="ingest-data-into-your-data-lake"></a>Inserire i dati in Data Lake

Questi servizi consentono di popolare Data Lake con i dati appropriati.

### <a name="azure-data-factory"></a>Data factory di Azure

È possibile usare [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) per inserire i dati da queste origini:

* Tabelle di Azure
* Database SQL di Azure
* Azure SQL DataWarehouse
* BLOB del servizio di archiviazione di Azure
* Database locali

Vedere [Spostare dati da e in Data Lake Storage Gen2 usando Data Factory](../../data-factory/connector-azure-data-lake-store.md).

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>Analizzare e visualizzare i dati in Data Lake

Questi servizi possono usare Data Lake Storage Gen2 come endpoint di archiviazione.

### <a name="azure-hdinsight"></a>HDInsight di Azure

È possibile eseguire il provisioning di un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) che usa Data Lake Storage Gen2 come risorsa di archiviazione conforme a HDFS. Per questa versione, per i cluster Hadoop e Storm in Windows e Linux è possibile usare Data Lake Storage Gen2 solo come risorsa di archiviazione aggiuntiva. Tali cluster continuano a usare l'archiviazione di Azure (WASB) come risorsa di archiviazione predefinita. Per i cluster HBase in Windows e Linux, tuttavia, è possibile usare Data Lake Storage Gen2 come risorsa di archiviazione predefinita o come risorsa di archiviazione aggiuntiva.

Vedere [Usare Archiviazione Azure Data Lake Storage Gen2 con cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.

[Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) consente di usare i Big Data a livello di cloud. Il servizio effettua il provisioning dinamico delle risorse e permette di analizzare exabyte di dati. Data Lake Analytics è ottimizzato per usare Data Lake Storage Gen2 come origine dati. 

Vedere [Introduzione ad Azure Data Lake Analytics con Data Lake Storage Gen2](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="copy-data-to-other-repositories"></a>Copiare i dati in altri repository

Usare questi servizi per copiare dati da Data Lake e inserirli in altri repository.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

È possibile usare PolyBase per caricare dati da Data Lake Storage Gen2 in SQL Data Warehouse. Per altre informazioni, vedere [Usare Data Lake Storage Gen2 con SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="see-also"></a>Vedere anche 

* [Panoramica di Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Uso di Azure Data Lake Storage Gen2 per i requisiti di Big Data](data-lake-storage-data-scenarios.md)
