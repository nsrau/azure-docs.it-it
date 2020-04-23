---
title: Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight
description: Questo articolo fornisce una panoramica dei tipi di archiviazione e del relativo funzionamento con Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869850"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight

È possibile scegliere tra alcuni servizi di archiviazione di Azure diversi durante la creazione di cluster HDInsight:You can choose between a few different Azure storage services when creating HDInsight clusters:

* [Archiviazione di AzureAzure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

Questo articolo offre una panoramica di questi tipi di archiviazione e delle relative funzionalità univoche.

## <a name="storage-types-and-features"></a>Tipi di storage e caratteristiche

The following table summarizes the Azure Storage services that are supported with different versions of HDInsight:

| Servizio di archiviazione | Tipo di account | Tipo di spazio dei nomiNamespace Type | Servizi supportati | Livelli di prestazioni supportati | Livelli di accesso supportati | HDInsight Version | Tipo di cluster |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Utilizzo generico v2 | Gerarchico (file system) | BLOB | Standard | Caldo, Fresco, Archivio | 3.6+ | Tutti tranne Spark 2.1 e 2.2|
|Archiviazione di Azure| Utilizzo generico v2 | Oggetto | BLOB | Standard | Caldo, Fresco, Archivio | 3.6+ | Tutti |
|Archiviazione di Azure| Utilizzo generico v1 | Oggetto | BLOB | Standard | N/D | Tutti | Tutti |
|Archiviazione di Azure| Archiviazione BLOB | Oggetto | BLOB in blocchi | Standard | Caldo, Fresco, Archivio | Tutti | Tutti |
|Azure Data Lake Storage Gen1| N/D | Gerarchico (file system) | N/D | N/D | N/D | 3.6 Solo | Tutti tranne HBase |

Per i cluster HDInsight, solo gli account di archiviazione secondari possono essere di tipo BlobStorage e Il BLOB di pagine non è un'opzione di archiviazione supportata.

Per altre informazioni sui tipi di account di Archiviazione di Azure, vedere [Panoramica dell'account di archiviazione](../storage/common/storage-account-overview.md) di AzureFor more information on Azure Storage account types, see Azure storage account overview

Per altre informazioni sui livelli di accesso di Archiviazione di Azure, vedere Archiviazione BLOB di Azure: Premium (anteprima), Hot, Cool e livelli di archiviazione di archiviazioneFor more information on Azure Storage [tiers, see Azure Blob storage: Premium (preview), Hot, Cool, and Archive storage tiers](../storage/blobs/storage-blob-storage-tiers.md)

È possibile creare cluster usando combinazioni di servizi per l'archiviazione secondaria e facoltativa. The following table summarizes the cluster storage configurations that are currently supported in HDInsight:

| HDInsight Version | Archiviazione primaria | Archiviazione secondaria | Supportato |
|---|---|---|---|
| 3.6 & 4,0 | Scopo generale V1, Scopo generale V2 | Scopo generale V1, scopo generale V2, BlobStorage(Block Blobs) | Sì |
| 3.6 & 4,0 | Scopo generale V1, Scopo generale V2 | Data Lake Storage Gen2 | No |
| 3.6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen2 | Sì |
| 3.6 & 4,0 | Data Lake Storage Gen2 | Scopo generale V1, scopo generale V2, BlobStorage(Block Blobs) | Sì |
| 3.6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | No |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Sì |
| 3.6 | Data Lake Storage Gen1 | Scopo generale V1, scopo generale V2, BlobStorage(Block Blobs) | Sì |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | No |
| 4.0 | Data Lake Storage Gen1 | Qualsiasi | No |
| 4.0 | Scopo generale V1, Scopo generale V2 | Data Lake Storage Gen1 | No |

Questo può essere uno o più account Data Lake Storage Gen2, purché siano tutti configurato per utilizzare la stessa identità gestita per l'accesso al cluster.

> [!NOTE]
> L'archiviazione primaria di Data Lake Storage Gen2 non è supportata per i cluster Spark 2.1 o 2.2.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Archiviazione di AzureAzure Storage overview](./overview-azure-storage.md)
* [Panoramica di Azure Data Lake Storage Gen1Azure Data Lake Storage Gen1 overview](./overview-data-lake-storage-gen1.md)
* [Panoramica di Azure Data Lake Storage Gen2Azure Data Lake Storage Gen2 overview](./overview-data-lake-storage-gen2.md)
* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md)
