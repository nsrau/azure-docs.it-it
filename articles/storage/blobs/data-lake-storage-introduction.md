---
title: Introduzione ad Azure Data Lake Storage Gen2
description: Offre una panoramica di Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3dea4dfc58bf087b8f6bc0a3f45646da5cb597ad
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847221"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introduzione ad Azure Data Lake Storage Gen2

‎Azure Data Lake Storage Gen2 è un set di funzionalità dedicate all'analisi dei Big Data e integrate in [Archiviazione BLOB di Azure](storage-blobs-introduction.md). È il risultato della convergenza delle funzionalità di due servizi di archiviazione Microsoft esistenti, Archiviazione BLOB di Azure e Azure Data Lake Storage Gen1. Le funzionalità di [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), ad esempio la semantica dei file system, la sicurezza a livello di directory e file e la scalabilità, vengono combinate con le funzionalità di archiviazione a basso costo e a più livelli, elevata disponibilità/ripristino di emergenza di [Archiviazione BLOB di Azure](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Progettato per l'analisi dei Big Data Enterprise

Data Lake Storage Gen2 usa Archiviazione di Azure come base per la compilazione di Enterprise Data Lake (EDL) in Azure. Progettato dall'inizio per servire più petabyte di informazioni supportando al contempo centinaia di Gigabit di velocità effettiva, Data Lake Storage Gen2 consente di gestire facilmente grandi quantità di dati.

Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno [spazio dei nomi gerarchico](data-lake-storage-namespace.md) all'archivio BLOB. Lo spazio dei nomi gerarchico organizza gli oggetti e i file in una gerarchia di directory per un accesso ai dati efficiente. Una convenzione di denominazione comune per l'archiviazione di oggetti prevede l'inserimento di slash nel nome per simulare una struttura di directory gerarchica. Questa struttura diventa effettiva con Data Lake Storage Gen2. Operazioni quali la ridenominazione o l'eliminazione di una directory diventano singole operazioni atomiche sui metadati della directory anziché operazioni di enumerazione ed elaborazione di tutti gli oggetti che condividono il prefisso del nome della directory.

In passato, le analisi basate sul cloud imponevano il raggiungimento di un compromesso tra prestazioni, gestione e sicurezza. Data Lake Storage Gen2 gestisce ognuno di questi aspetti nei modi seguenti:

-   Le **prestazioni** sono ottimizzate perché non è necessario copiare o trasformare i dati come prerequisito per l'analisi. Lo spazio dei nomi gerarchico migliora notevolmente le prestazioni delle operazioni di gestione directory, consentendo di migliorare le prestazioni complessive del processo.

-   La **gestione** risulta più semplice poiché è possibile organizzare e gestire i file nelle directory e sottodirectory.

-   La **protezione** si può applicare perché è possibile definire le autorizzazioni POSIX delle directory o dei singoli file.

-   La **convenienza** è resa possibile dal fatto che Data Lake Storage Gen2 è compilato sulla base dell'[archiviazione Blob di Azure](storage-blobs-introduction.md) a basso costo. Le funzionalità aggiuntive riducono ulteriormente il costo totale di proprietà per l'esecuzione di analisi dei Big Data in Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Funzionalità principali di Data Lake Storage Gen2

-   **Accesso compatibile con Hadoop**: Data Lake Storage Gen2 consente di gestire i dati e accedervi esattamente come si farebbe con [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Il nuovo [driver ABFS](data-lake-storage-abfs-driver.md) è disponibile all'interno di tutti gli ambienti di Apache Hadoop, tra cui [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) e [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/), per accedere all'archivio dati di Data Lake Storage Gen2.

-   **Un superset di autorizzazioni POSIX**: il modello di sicurezza per Data Lake Gen2 supporta l'elenco di controllo di accesso e le autorizzazioni POSIX oltre a una granularità aggiuntiva specifica di Data Lake Storage Gen2. È possibile configurare le impostazioni tramite Storage Explorer o framework come Hive e Spark.

-   **Conveniente**: Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. Con il passaggio di dati tramite il relativo ciclo di vita completo, le tariffe di fatturazione cambiano mantenendo i costi al minimo tramite funzionalità incorporate, come ad esempio il [ciclo di vita di archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).

-   **Driver ottimizzato**: il driver ABFS è [ottimizzato appositamente](data-lake-storage-abfs-driver.md) per l'analisi dei Big Data. Le API REST corrispondenti vengono rilevate tramite l'endpoint `dfs.core.windows.net`.

### <a name="scalability"></a>Scalabilità

Archiviazione di Azure è scalabile per impostazione predefinita, sia che si acceda da Data Lake Storage Gen2 o dalle interfacce di archiviazione BLOB. È in grado di archiviare e servire *molti esabyte di dati*. Questa quantità di archiviazione è disponibile con la velocità effettiva misurata in Gigabit al secondo (Gbps) con un elevato livello di operazioni di I/O al secondo (IOPS). Oltre alla persistenza, l'elaborazione viene eseguita a una latenza per richiesta quasi costante, misurata a livello di servizio, account e file.

### <a name="cost-effectiveness"></a>Convenienza

Uno dei numerosi vantaggi della compilazione di Data Lake Storage Gen2 sulla base di Archiviazione BLOB di Azure è il basso costo delle transazioni e della capacità di archiviazione. A differenza di altri servizi di archiviazione cloud, i dati archiviati in Data Lake Storage Gen2 non devono essere spostati o trasformati prima dell'analisi. Per altre informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage).

Funzionalità quali lo [spazio dei nomi gerarchico](data-lake-storage-namespace.md) migliorano significativamente anche le prestazioni complessive di molti processi di analisi. Questo miglioramento nelle prestazioni implica una minore capacità di calcolo per elaborare la stessa quantità di dati, con conseguente riduzione del costo totale di proprietà (TCO) per il processo di analisi end-to-end.

### <a name="one-service-multiple-concepts"></a>Un servizio, più concetti

Data Lake Storage Gen2 è una funzionalità aggiuntiva per l'analisi dei Big Data, basata sull'archivio BLOB di Azure. Sfruttare i componenti della piattaforma esistente dei BLOB per creare e usare i Data Lake per l'analisi offre numerosi vantaggi, ma genera più concetti che descrivono gli stessi elementi condivisi.

Di seguito sono elencate le entità equivalenti, descritte da concetti diversi. Se non diversamente specificato, queste entità sono chiaramente sinonimi:

| Concetto                                | Organizzazione di livello superiore | Organizzazione di livello inferiore                                            | Contenitore dati |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOB: archivio di oggetti per utilizzo generico | Contenitore              | Directory virtuale (solo SDK, non fornisce la manipolazione atomica) | BLOB           |
| ADLS Gen2: archivio di analisi          | File system             | Directory                                                           | File           |

## <a name="supported-open-source-platforms"></a>Piattaforme open source supportate

Diverse piattaforme open source supportano Data Lake Storage Gen2. Tali piattaforme sono visualizzate nella tabella seguente.

> [!NOTE]
> Sono supportate solo le versioni visualizzate in questa tabella.

| Piattaforma |  Versioni supportate | Altre informazioni |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6+ | [Componenti e versioni di Apache Hadoop disponibili in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2+ | [Archivio di versioni di Apache Hadoop](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1+ | [Note sulla versione di Cloudera Enterprise 6.x](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1+ | [Versioni del runtime di Databricks](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1.x++ | [Configurazione dell'accesso ai dati del cloud](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="next-steps"></a>Passaggi successivi

Negli articoli seguenti vengono descritte alcune delle principali nozioni di Data Lake Storage Gen2 e i dettagli su come archiviare, accedere, gestire e ottenere informazioni significative dai dati:

-   [Spazio dei nomi gerarchico](data-lake-storage-namespace.md)
-   [Creare un account di archiviazione](data-lake-storage-quickstart-create-account.md)
-   [Usare un account di Data Lake Storage Gen2 in Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md)
