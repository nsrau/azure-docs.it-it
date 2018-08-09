---
title: Versione di anteprima di Azure Data Lake Storage Gen2 - Introduzione
description: Offre una panoramica della creazione di un'anteprima di Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 51f38cf7ade01b58ad5ce7925af5546d1a4f1a0c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525383"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Anteprima di Azure Data Lake Storage Gen2 - Introduzione

Anteprima di Azure Data Lake Storage Gen2 è un set di funzionalità dedicate all'analisi dei Big Data, compilato sulla base di [archiviazione Blob di Azure](../blobs/storage-blobs-introduction.md). Consente di interagire con i dati approfittando dei paradigmi sia del file system che dell'archiviazione di oggetti. Ciò rende Azure Data Lake Storage Gen2 l'unico servizio di archiviazione multimodale basato sul cloud, grazie alla possibilità di sfruttare le potenzialità di analisi da tutti i dati.

Azure Data Lake Storage Gen2 è dotato di tutte le qualità necessarie per l'intero ciclo di vita dei dati di analisi. Ciò è dovuto alla convergenza delle funzionalità di due servizi di archiviazione esistenti. Le funzionalità di [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), come ad esempio la semantica dei file system, la sicurezza a livello di file e la scalabilità, vengono combinate con un'archiviazione a basso costo e a più livelli, elevata disponibilità/ripristino di emergenza e un SDK/ecosistema di strumenti di grandi dimensioni derivati dall'[archiviazione Blob di Azure](../blobs/storage-blobs-introduction.md). Data Lake Storage Gen2 offre non solo tutte le qualità dell'archiviazione di oggetti, ma anche i vantaggi di un'interfaccia di file system ottimizzata per i carichi di lavoro di analisi.

## <a name="designed-for-enterprise-big-data-analytics"></a>Progettato per l'analisi dei Big Data Enterprise

Data Lake Storage Gen2 è il servizio di archiviazione fondamentale per la compilazione di Enterprise Data Lake (EDL) in Azure. Progettato dall'inizio per servire più petabyte di informazioni supportando al contempo centinaia di Gigabit di velocità effettiva, Data Lake Storage Gen2 offre un modo semplice per gestire grandi quantità di dati.

Una funzionalità fondamentale di Data Lake Storage Gen2 consiste nell'aggiunta di uno [spazio dei nomi gerarchico](./namespace.md) al servizio di archiviazione Blob che organizza oggetti e file in una gerarchia di directory per un accesso ai dati ad alte prestazioni. Lo spazio dei nomi gerarchico consente anche a Data Lake Storage Gen2 di supportare contemporaneamente sia l'archiviazione di oggetti che i paradigmi del file system. Ad esempio, una convenzione di denominazione comune per l'archiviazione di oggetti prevede l'inserimento di slash nel nome per simulare una struttura di cartelle gerarchica. Questa struttura diventa effettiva con Data Lake Storage Gen2. Operazioni quali la ridenominazione o l'eliminazione di una directory diventano singole operazioni atomiche sui metadati della directory anziché operazioni di enumerazione ed elaborazione di tutti gli oggetti che condividono il prefisso del nome della directory.

In passato, le analisi basate sul cloud imponevano il raggiungimento di un compromesso tra prestazioni, gestione e sicurezza. Data Lake Storage Gen2 gestisce ognuno di questi aspetti nei modi seguenti:

- Le **prestazioni** sono ottimizzate perché non è necessario copiare o trasformare i dati come prerequisito per l'analisi. Lo spazio dei nomi gerarchico migliora notevolmente le prestazioni delle operazioni di gestione directory, consentendo di migliorare le prestazioni complessive del processo.

- La **gestione** risulta più semplice poiché è possibile organizzare e gestire i file nelle directory e sottodirectory.

- La **convenienza** è resa possibile dal fatto che Data Lake Storage Gen2 è compilato sulla base dell'[archiviazione Blob di Azure](../blobs/storage-blobs-introduction.md) a basso costo. Le funzionalità aggiuntive riducono ulteriormente il costo totale di proprietà per l'esecuzione di analisi dei Big Data in Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Funzionalità principali di Data Lake Storage Gen2

> [!NOTE]
> Durante l'anteprima pubblica di Data Lake Storage Gen2, alcune delle funzionalità elencate di seguito possono variare in termini di disponibilità. Poiché le aree e le nuove funzionalità vengono rilasciate durante il programma di anteprima, queste informazioni verranno comunicate più avanti.
> [Iscriversi](https://aka.ms/adlsgen2signup) per l'anteprima pubblica di Data Lake Storage Gen2.  

- **Accesso compatibile con Hadoop**: Data Lake Storage Gen2 consente di gestire i dati e accedervi esattamente come si farebbe con un [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Il nuovo [driver ABFS](./abfs-driver.md) è disponibile all'interno di tutti gli ambienti di Apache Hadoop, tra cui [Azure HDInsight](../../hdinsight/index.yml) e [Azure Databricks](../../azure-databricks/index.yml), per accedere all'archivio dati di Data Lake Storage Gen2.

- **Accesso ai dati multiprotocollo e multimodale**: Data Lake Storage Gen2 viene considerato un servizio di archiviazione **multi-modale** perché fornisce interfacce sia per l'archiviazione di oggetti che per il file system agli stessi dati **contemporaneamente**. Ciò è reso possibile da più endpoint di protocollo che sono in grado di accedere agli stessi dati. 

    Diversamente da altre soluzioni di analisi, i dati archiviati in Data Lake Storage Gen2 non devono essere spostati o trasformati prima di poter eseguire un'ampia gamma di strumenti di analisi. È possibile accedere ai dati tramite [API di archiviazione Blob](../blobs/storage-blobs-introduction.md) tradizionali (come ad esempio l'inserimento di dati tramite l'[acquisizione di hub eventi](../../event-hubs/event-hubs-capture-enable-through-portal.md)) ed elaborare tali dati tramite HDInsight o Azure Databricks contemporaneamente. 

- **Economicamente conveniente**: Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. Con il passaggio di dati tramite il relativo ciclo di vita completo, le tariffe di fatturazione cambiano mantenendo i costi al minimo tramite funzionalità incorporate, come ad esempio il [ciclo di vita di archiviazione BLOB di Azure](../common/storage-lifecycle-managment-concepts.md).

- **Funziona con strumenti di archiviazione BLOB, framework e app**: Data Lake Storage Gen2 continua a funzionare con l'ampia gamma di strumenti, framework e app attualmente esistenti per l'archiviazione BLOB di Azure.

- **Driver ottimizzato**: il driver `abfs` è [ottimizzato appositamente](./abfs-driver.md) per l'analisi dei Big Data. Le API REST corrispondenti vengono rilevate tramite l'`dfs`endpoint, `dfs.core.windows.net`.

## <a name="scalability"></a>Scalabilità

Archiviazione di Azure è scalabile per impostazione predefinita, sia che si acceda da Data Lake Storage Gen2 o dalle interfacce di archiviazione BLOB. È in grado di archiviare e servire *molti esabyte di dati*. Questa quantità di archiviazione è disponibile con la velocità effettiva misurata in Gigabit al secondo (Gbps) con un elevato livello di operazioni di I/O al secondo (IOPS). Oltre alla persistenza, l'elaborazione viene eseguita a una latenza per richiesta quasi costante, misurata a livello di servizio, account e file.

## <a name="cost-effectiveness"></a>Convenienza

Uno dei numerosi vantaggi della compilazione di Data Lake Storage Gen2 sulla base di Archiviazione BLOB di Azure è il [basso costo](https://azure.microsoft.com/pricing/details/storage) delle transazioni e della capacità di archiviazione. A differenza di altri servizi di archiviazione cloud, Data Lake Storage Gen2 riduce i costi poiché i dati non devono essere spostati o trasformati prima dell'analisi.

Funzionalità quali lo [spazio dei nomi gerarchico](./namespace.md) migliorano significativamente anche le prestazioni complessive di molti processi di analisi. Questo miglioramento nelle prestazioni implica una minore capacità di calcolo per elaborare la stessa quantità di dati, con conseguente riduzione del costo totale di proprietà (TCO) per il processo di analisi end-to-end.

## <a name="next-steps"></a>Passaggi successivi

Negli articoli seguenti vengono descritte alcune delle principali nozioni di Data Lake Storage Gen2 e i dettagli su come archiviare, accedere, gestire e ottenere informazioni significative dai dati:

* [Spazio dei nomi gerarchico](./namespace.md)
* [Creare un account di archiviazione](./quickstart-create-account.md)
* [Creare un cluster HDInsight con Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Usare un account di Azure Data Lake Storage Gen2 in Azure Databricks](./quickstart-create-databricks-account.md) 