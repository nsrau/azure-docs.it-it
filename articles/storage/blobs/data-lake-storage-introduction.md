---
title: Introduzione ad Azure Data Lake Storage Gen2
description: Introduzione ad Azure Data Lake Storage Gen2. Informazioni sulle funzionalità principali e descrizione delle piattaforme, delle integrazioni di servizi di Azure e delle funzionalità di archiviazione BLOB supportate.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 5f2f3cfc5ccbdd6a3d3d3ede5bb39a3f6f548b19
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913097"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introduzione ad Azure Data Lake Storage Gen2

‎Azure Data Lake Storage Gen2 è un set di funzionalità dedicate all'analisi dei Big Data e integrate in [Archiviazione BLOB di Azure](storage-blobs-introduction.md). 

Data Lake Storage Gen2 riunisce le funzionalità di [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) e dell'archiviazione BLOB di Azure. Offre ad esempio semantica dei file system, sicurezza a livello di file e scalabilità. Poiché queste funzionalità sono basate sull'archiviazione BLOB, si ottiene anche un'archiviazione a più livelli a costi contenuti, con funzionalità di disponibilità elevata e ripristino di emergenza.

## <a name="designed-for-enterprise-big-data-analytics"></a>Progettato per l'analisi dei Big Data Enterprise

Data Lake Storage Gen2 usa Archiviazione di Azure come base per la compilazione di Enterprise Data Lake (EDL) in Azure. Progettato dall'inizio per servire più petabyte di informazioni supportando al contempo centinaia di Gigabit di velocità effettiva, Data Lake Storage Gen2 consente di gestire facilmente grandi quantità di dati.

Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno [spazio dei nomi gerarchico](data-lake-storage-namespace.md) all'archivio BLOB. Lo spazio dei nomi gerarchico organizza gli oggetti e i file in una gerarchia di directory per un accesso ai dati efficiente. Una convenzione di denominazione comune per l'archiviazione di oggetti prevede l'inserimento di slash nel nome per simulare una struttura di directory gerarchica. Questa struttura diventa effettiva con Data Lake Storage Gen2. Operazioni come la ridenominazione o l'eliminazione di una directory diventano singole operazioni atomiche sui metadati della directory. Non è necessario enumerare ed elaborare tutti gli oggetti che condividono il prefisso del nome della directory.

Data Lake Storage Gen2 si basa su archiviazione BLOB e migliora le prestazioni, la gestione e la sicurezza nei modi seguenti:

-   Le **prestazioni** sono ottimizzate perché non è necessario copiare o trasformare i dati come prerequisito per l'analisi. Rispetto allo spazio dei nomi flat in archiviazione BLOB, lo spazio dei nomi gerarchico migliora notevolmente le prestazioni delle operazioni di gestione directory, consentendo di aumentare le prestazioni complessive del processo.

-   La **gestione** risulta più semplice poiché è possibile organizzare e gestire i file nelle directory e sottodirectory.

-   La **protezione** si può applicare perché è possibile definire le autorizzazioni POSIX delle directory o dei singoli file.

Inoltre, Data Lake Storage Gen2 è particolarmente conveniente perché si basa sull'[archiviazione BLOB di Azure](storage-blobs-introduction.md) a basso costo. Le funzionalità aggiuntive riducono ulteriormente il costo totale di proprietà per l'esecuzione di analisi dei Big Data in Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Funzionalità principali di Data Lake Storage Gen2

-   **Accesso compatibile con Hadoop**: Data Lake Storage Gen2 consente di gestire i dati e accedervi esattamente come si farebbe con [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Il nuovo [driver ABFS](data-lake-storage-abfs-driver.md) (usato per accedere ai dati) è disponibile in tutti gli ambienti Apache Hadoop. Questi ambienti includono [Azure HDInsight](../../hdinsight/index.yml) *,* [Azure Databricks](/azure/databricks/) e [Azure Synapse Analytics](/azure/synapse-analytics).

-   **Un superset di autorizzazioni POSIX**: il modello di sicurezza per Data Lake Gen2 supporta l'elenco di controllo di accesso e le autorizzazioni POSIX oltre a una granularità aggiuntiva specifica di Data Lake Storage Gen2. È possibile configurare le impostazioni tramite Storage Explorer o framework come Hive e Spark.

-   **Conveniente**: Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. Funzionalità come il [ciclo di vita dell'archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md) ottimizzano i costi man mano che i dati si spostano lungo il ciclo di vita.

-   **Driver ottimizzato**: il driver ABFS è [ottimizzato appositamente](data-lake-storage-abfs-driver.md) per l'analisi dei Big Data. Le API REST corrispondenti vengono rilevate tramite l'endpoint `dfs.core.windows.net`.

### <a name="scalability"></a>Scalabilità

Archiviazione di Azure è scalabile per impostazione predefinita, sia che si acceda da Data Lake Storage Gen2 o dalle interfacce di archiviazione BLOB. È in grado di archiviare e servire *molti esabyte di dati*. Questa quantità di archiviazione è disponibile con la velocità effettiva misurata in Gigabit al secondo (Gbps) con un elevato livello di operazioni di I/O al secondo (IOPS). L'elaborazione viene eseguita a una latenza per richiesta quasi costante, misurata a livello di servizio, account e file.

### <a name="cost-effectiveness"></a>Convenienza

Poiché Data Lake Storage Gen2 si basa sull'archiviazione BLOB di Azure, i costi di transazione e capacità di archiviazione sono inferiori. A differenza di altri servizi di archiviazione cloud, non è necessario spostare o trasformare i dati per poterli analizzare. Per altre informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage).

Funzionalità quali lo [spazio dei nomi gerarchico](data-lake-storage-namespace.md) migliorano significativamente anche le prestazioni complessive di molti processi di analisi. Questo miglioramento nelle prestazioni implica una minore capacità di calcolo per elaborare la stessa quantità di dati, con conseguente riduzione del costo totale di proprietà (TCO) per il processo di analisi end-to-end.

### <a name="one-service-multiple-concepts"></a>Un servizio, più concetti

Poiché Data Lake Storage Gen2 si basa sull'archiviazione BLOB di Azure, possono essere usati più concetti per descrivere gli stessi elementi comuni.

Di seguito sono elencate le entità equivalenti, descritte da concetti diversi. Se non diversamente specificato, queste entità sono chiaramente sinonimi:

| Concetto                                | Organizzazione di livello superiore | Organizzazione di livello inferiore                                            | Contenitore dati |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOB: archivio di oggetti per utilizzo generico | Contenitore              | Directory virtuale (solo SDK, non fornisce la manipolazione atomica) | BLOB           |
| Azure Data Lake Storage Gen2 – Analisi sui dati archiviati          | Contenitore            | Directory                                                           | File           |

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

Le funzionalità di archiviazione BLOB, ad esempio [registrazione diagnostica](../common/storage-analytics-logging.md), [livelli di accesso](storage-blob-storage-tiers.md) e [criteri di gestione del ciclo di vita dell'archiviazione BLOB](storage-lifecycle-management-concepts.md), sono disponibili nel proprio account. 

Per un elenco delle funzionalità di archiviazione BLOB supportate, vedere [Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrazioni di servizi di Azure supportate

Data Lake Storage Gen2 supporta diversi servizi di Azure, che possono essere usati per inserire dati, eseguire analisi e creare rappresentazioni visive. Per un elenco dei servizi di Azure supportati, vedere [Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Piattaforme open source supportate

Diverse piattaforme open source supportano Data Lake Storage Gen2. Per un elenco completo, vedere [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Vedere anche

- [Problemi noti di Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)