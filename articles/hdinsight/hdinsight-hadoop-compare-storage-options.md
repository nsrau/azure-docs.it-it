---
title: Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight
description: Questo articolo fornisce una panoramica dei tipi di archiviazione e del relativo funzionamento con Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: ed93ba937a843618f36bac6e88b15ff77355ca75
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82610701"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight

È possibile scegliere tra alcuni servizi di archiviazione di Azure diversi quando si creano cluster HDInsight:

* [Archiviazione di Azure](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)

Questo articolo offre una panoramica di questi tipi di archiviazione e delle relative funzionalità univoche.

## <a name="storage-types-and-features"></a>Tipi e funzionalità di archiviazione

La tabella seguente riepiloga i servizi di archiviazione di Azure supportati con diverse versioni di HDInsight:

| Servizio di archiviazione | Tipo di account | Tipo di spazio dei nomi | Servizi supportati | Livelli di prestazioni supportati | Livelli di accesso supportati | HDInsight Version | Tipo di cluster |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Utilizzo generico v2 | Gerarchico (filesystem) | BLOB | Standard | Frequente, sporadico, archivio | 3.6+ | Tutti tranne Spark 2,1 e 2,2|
|Archiviazione di Azure| Utilizzo generico v2 | Oggetto | BLOB | Standard | Frequente, sporadico, archivio | 3.6+ | Tutti |
|Archiviazione di Azure| Utilizzo generico v1 | Oggetto | BLOB | Standard | N/D | Tutti | Tutti |
|Archiviazione di Azure| Archiviazione BLOB * * | Oggetto | BLOB in blocchi | Standard | Frequente, sporadico, archivio | Tutti | Tutti |
|Azure Data Lake Storage Gen1| N/D | Gerarchico (filesystem) | N/D | N/D | N/D | solo 3,6 | Tutti tranne HBase |

* * Per i cluster HDInsight, solo gli account di archiviazione secondari possono essere di tipo BlobStorage e il BLOB di pagine non è un'opzione di archiviazione supportata.

Per altre informazioni sui tipi di account di archiviazione di Azure, vedere [Panoramica dell'account di archiviazione di Azure](../storage/common/storage-account-overview.md)

Per altre informazioni sui livelli di accesso di archiviazione di Azure, vedere [archiviazione BLOB di Azure: livelli di archiviazione Premium (anteprima),](../storage/blobs/storage-blob-storage-tiers.md) ad accesso frequente, ad accesso sporadico e archivio

È possibile creare cluster usando combinazioni di servizi per l'archiviazione secondaria primaria e facoltativa. Nella tabella seguente sono riepilogate le configurazioni di archiviazione del cluster attualmente supportate in HDInsight:

| HDInsight Version | Archiviazione primaria | Archiviazione secondaria | Supportato |
|---|---|---|---|
| 3,6 & 4,0 | Per utilizzo generico V1, per utilizzo generico V2 | Per utilizzo generico V1, per utilizzo generico V2, BlobStorage (BLOB in blocchi) | Sì |
| 3,6 & 4,0 | Per utilizzo generico V1, per utilizzo generico V2 | Data Lake Storage Gen2 | No |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Sì |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Per utilizzo generico V1, per utilizzo generico V2, BlobStorage (BLOB in blocchi) | Sì |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | No |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Sì |
| 3.6 | Data Lake Storage Gen1 | Per utilizzo generico V1, per utilizzo generico V2, BlobStorage (BLOB in blocchi) | Sì |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | No |
| 4.0 | Data Lake Storage Gen1 | Qualsiasi | No |
| 4.0 | Per utilizzo generico V1, per utilizzo generico V2 | Data Lake Storage Gen1 | No |

* = Può trattarsi di uno o più account Data Lake Storage Gen2, purché siano tutti impostati per usare la stessa identità gestita per l'accesso al cluster.

> [!NOTE]
> Data Lake Storage Gen2 archiviazione primaria non è supportata per i cluster Spark 2,1 o 2,2.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Archiviazione di Azure](./overview-azure-storage.md)
* [Panoramica di Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Panoramica di Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md)
