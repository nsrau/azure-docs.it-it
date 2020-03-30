---
title: Formati di file supportati in Azure Data FactorySupported file formats in Azure Data Factory
description: Questo argomento descrive i formati di file e i codici di compressione supportati dai connettori basati su file in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439566"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Formati di file e codec di compressione supportati in Azure Data Factory

*Questo articolo si applica ai connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), Blob di [Azure](connector-azure-blob-storage.md), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md), Azure Data Lake Storage [Gen2](connector-azure-data-lake-storage.md), [Archiviazione file di Azure](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

È possibile utilizzare [l'attività Copy](copy-activity-overview.md) per copiare i file così come sono tra due archivi dati basati su file, nel qual caso i dati vengono copiati in modo efficiente senza alcuna serializzazione o deserializzazione. 

Inoltre, è anche possibile analizzare o generare file di un determinato formato. Ad esempio, è possibile eseguire le operazioni seguenti:For example, you can perform the following:

* Copiare i dati da un database di SQL Server locale e scrivere in Azure Data Lake Storage Gen2 in formato Parquet.Copy data from an on-premises SQL Server database and write to Azure Data Lake Storage Gen2 in Parquet format.
* Copiare i file in formato testo (CSV) da un file system locale e scrivere nell'archivio BLOB di Azure in formato Avro.Copy files in text (CSV) format from an on-premises file system and write to Azure Blob storage in Avro format.
* Copiare i file compressi da un file system locale, decomprimerli immediatamente e scrivere i file estratti in Azure Data Lake Storage Gen2.
* Copiare i dati in formato Gzip con testo compresso (CSV) dall'archivio BLOB di Azure e scriverlo nel database SQL di Azure.Copy data in Gzip compressed-text (CSV) format from Azure Blob storage and write it to Azure SQL Database.
* Molte altre attività che richiedono serializzazione/deserializzazione o compressione/decompressione.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Prestazioni dell'attività di copia](copy-activity-performance.md)
