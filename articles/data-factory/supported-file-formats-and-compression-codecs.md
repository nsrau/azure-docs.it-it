---
title: Formati di file supportati dall'attività di copia in Azure Data Factory
description: Questo argomento descrive i formati di file e i codici di compressione supportati dall'attività di copia in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: c044208699bf5bebb6383cfef00bf53b744369d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86522441"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Formati di file supportati e codec di compressione per attività di copia in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Questo articolo si applica ai connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

È possibile usare l' [attività di copia](copy-activity-overview.md) per copiare i file così come sono tra due archivi dati basati su file, nel qual caso i dati vengono copiati in modo efficiente senza serializzazione o deserializzazione. 

Inoltre, è possibile analizzare o generare file con un formato specifico. Ad esempio, è possibile eseguire le operazioni seguenti:

* Copiare i dati da un database di SQL Server e scrivere in Azure Data Lake Storage Gen2 in formato parquet.
* Copiare i file in formato testo (CSV) da un file system locale e scrivere nell'archiviazione BLOB di Azure nel formato avro.
* Copiare i file compressi da un file system locale, decomprimerli in tempo reale e scrivere i file estratti in Azure Data Lake Storage Gen2.
* Copiare i dati nel formato di testo compresso gzip dall'archiviazione BLOB di Azure e scriverli nel database SQL di Azure.
* Molte altre attività che richiedono la serializzazione/deserializzazione o la compressione/decompressione.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Prestazioni dell'attività di copia](copy-activity-performance.md)
