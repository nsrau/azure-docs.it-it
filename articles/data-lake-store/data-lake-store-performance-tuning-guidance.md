---
title: Linee guida per l&quot;ottimizzazione delle prestazioni di Azure Data Lake Store | Documentazione Microsoft
description: Linee guida per l&quot;ottimizzazione delle prestazioni di Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: af11866fc812cd8a375557b7bf9df5cdc9bba610
ms.openlocfilehash: f0d0c05c08ce198e2702c76ad35b348107c664c7


---
# <a name="performance-tuning-guidance-for-azure-data-lake-store"></a>Linee guida per l'ottimizzazione delle prestazioni di Azure Data Lake Store

Questo articolo fornisce indicazioni su come ottimizzare le prestazioni di scrittura e lettura dei dati in Azure Data Lake Store. Questo articolo ha lo scopo di aiutare gli utenti a comprendere i parametri che possono essere configurati per comuni strumenti di caricamento/scaricamento di dati e carichi di lavoro di analisi dei dati. Le ottimizzazioni trattate in questa guida riguardano soprattutto i carichi di lavoro con uso intensivo di risorse caratterizzati da grandi quantità di dati da leggere o scrivere in Data Lake Store.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** con accesso a un account di Archivio Data Lake. Vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.


## <a name="guidelines-for-data-ingestion-tools"></a>Linee guida per gli strumenti di inserimento dati

Questa sezione fornisce indicazioni generali su come migliorare le prestazioni quando i dati vengano copiati o spostati in Data Lake Store. Questa sezione spiega i fattori che limitano le prestazioni e alcuni modi per superare tali limitazioni. Di seguito sono riportate alcune considerazioni di cui tenere conto.

* **Dati di origine**: molti vincoli possono derivare dalla provenienza dei dati di origine. La velocità effettiva può rappresentare un collo di bottiglia se i dati di origine si trovano su spindle lenti o risorse di archiviazione remote con ridotte capacità. Gli SSD, preferibilmente su disco locale, forniranno le migliori prestazioni grazie alla maggiore velocità effettiva del disco.

* **Rete**: se i dati di origine si trovano su VM, la connessione di rete tra la VM e Data Lake Store è importante. Usare le VM con la scheda di rete più grande disponibile per ottenere maggiore larghezza di banda di rete.

* **Copia tra aree**: l'I/O di dati fra aree comporta un costo di rete elevato, ad esempio l'esecuzione di uno strumento di inserimento dati in una VM nell'area Stati Uniti orientali 2 per scrivere dati in un account Data Lake Store nell'area Stati Uniti centrali. Quando si copiano dati fra aree, è possibile riscontrare un calo delle prestazioni. Per ottimizzare la velocità effettiva della rete si consiglia di usare processi di inserimento dati su VM nella stessa area dell'account Data Lake Store di destinazione.                                                                                                                                        

* **Cluster**: se si eseguono processi di inserimento dati tramite un cluster HDInsight (ad esempio per DistCp), si consiglia di usare VM di serie D per il cluster in quanto contengono più memoria. Un maggior numero di memorie centrali consentirà inoltre di aumentare la velocità effettiva.                                                                                                                                                                                                                                                                                                            

* **Concorrenza di thread**: se si usa un cluster HDInsight per copiare dati da un contenitore di archiviazione, esistono limitazioni sul numero di thread paralleli che possono essere usati in base alle dimensioni del cluster e del contenitore e alle impostazioni di thread. Uno dei modi più importanti per ottenere prestazioni migliori in Data Lake Store consiste nell'aumentare la concorrenza. È consigliabile ottimizzare le impostazioni per ottenere la quantità massima di concorrenza in modo da raggiungere una maggiore velocità effettiva. La tabella seguente riporta le impostazioni per ogni metodo di inserimento che è possibile configurare per ottenere maggiore concorrenza. Seguire i collegamenti nella tabella per accedere ad articoli che parlano di come usare lo strumento per inserire dati in Data Lake Store, nonché come ottimizzare le prestazioni dello strumento per la velocità effettiva massima.

    | Strumento               | Impostazione di concorrenza                                                                |
    |--------------------|------------------------------------------------------------------------------------|
    | [PowerShell](data-lake-store-get-started-powershell.md)       | PerFileThreadCount, ConcurrentFileCount |
    | [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)    | Unità Azure Data Lake Analytics         |
    | [DistCp](data-lake-store-copy-data-wasb-distcp.md)            | -m (mapper)                             |
    | [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)| parallelCopies                          |
    | [Sqoop](data-lake-store-data-transfer-sql-sqoop.md)           | fs.azure.block.size, -m (mapper)        |


## <a name="guidelines-while-working-with-hdinsight-workloads"></a>Linee guida per l'uso di carichi di lavoro HDInsight

Durante l'esecuzione di carichi di lavoro analitici per l'elaborazione dei dati in Data Lake Store si consiglia di usare versioni cluster HDInsight 3.5 per ottenere prestazioni ottimali con Data Lake Store. Quando il processo prevede un I/O più intensivo, è possibile configurare alcuni parametri per migliorare le prestazioni. Ad esempio, se il processo è costituito principalmente da lettura o scrittura, l'aumento della concorrenza di I/O da e verso Azure Data Lake Store potrebbe migliorare le prestazioni.

Azure Data Lake Store consente di ottenere prestazioni migliori quando la concorrenza è maggiore. Esistono alcuni modi generali per aumentare la concorrenza per i processi con I/O intensivo.

1. **Eseguire un numero maggiore di contenitori YARN di calcolo anziché un numero minore di contenitori YARN grandi**: un numero maggiore di contenitori incrementerà la concorrenza per le operazioni di input e output in modo da sfruttare al meglio le funzionalità di Data Lake Store.

    Si immagini, ad esempio, di avere 2 nodi D3v2 nel cluster HDInsight. Ogni nodo D3v2 ha 12 GB di memoria YARN perciò 2 macchine D3v2 avranno 24 GB di memoria YARN. Si supponga inoltre di aver impostato la dimensione del contenitore YARN su 6 GB. Questo significa che è possibile avere 4 contenitori da 6 GB ciascuno. Pertanto si possono avere 4 attività simultanee in esecuzione in parallelo. Per aumentare la concorrenza è possibile ridurre la dimensione dei contenitori a 3 GB in modo da avere 8 contenitori da 3 GB. Questo consente di avere 8 attività concorrenti eseguite in parallelo. Di seguito è illustrato questo concetto.

    ![Prestazioni di Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-1.png)

    Una domanda comune è perché non ridurre le dimensioni del contenitore a 1GB di memoria in modo da poter ottenere 24 contenitori e aumentare ulteriormente la concorrenza. Dipende se l'attività richiede 3 GB di memoria o è sufficiente 1 GB.  L'operazione eseguita nel contenitore potrebbe essere semplice e richiedere solo 1 GB di memoria oppure complessa e richiedere 3 GB di memoria.  Se si riduce troppo la dimensione del contenitore, è possibile ottenere un'eccezione di memoria esaurita.  In questo caso sarà necessario aumentare la dimensione dei contenitori.  Oltre alla memoria, anche il numero di memorie centrali virtuali può influenzare il parallelismo.

    ![Prestazioni di Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-2.png)

2. **Aumentare la memoria del cluster per ottenere maggiore concorrenza**: è possibile aumentare la memoria del cluster mediante l'aumento della dimensione del cluster o scegliendo un tipo di VM con più memoria. Questo aumenterà la quantità di memoria YARN disponibile, in modo da poter creare più contenitori, aumentando la concorrenza.  

    Si immagini, ad esempio, di avere un singolo nodo D3v2 nel cluster HDInsight con 12 GB di memoria YARN e contenitori da 3 GB.  Quindi si scala il cluster a 2 D3v2, aumentando la memoria YARN a 24 GB.  In questo modo la concorrenza aumenta da 4 a 8.

    ![Prestazioni di Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-3.png)

3. **Iniziare impostando il numero di attività sul numero di concorrenza**: a questo punto si è già impostata la dimensione del contenitore in modo appropriato per ottenere la quantità massima di concorrenza. Ora si deve impostare il numero di attività per usare tutti i contenitori. Ci sono nomi di attività diversi in ogni carico di lavoro.

    Può inoltre essere utile prendere in considerazione la dimensione del processo. Se la dimensione del processo è grande, ogni attività può presentare una grande quantità di dati da elaborare. È possibile usare più attività in modo che ogni attività non elaborerà troppi dati.

    Si immagini, ad esempio, di avere 6 contenitori. In questo caso si deve impostare le attività su 6 come punto di partenza. È possibile provare con un numero più elevato di attività per vedere se le prestazioni migliorano. Impostare un numero maggiore di attività non aumenta la concorrenza. Se si impostano le attività su un numero maggiore di 6, l'attività non verrà eseguita fino alla successiva ondata. Se si impostano le attività su un numero minore di 6, la concorrenza non sarà utilizzata pienamente.

    Ogni carico di lavoro ha diversi parametri disponibili per impostare il numero di attività. La tabella seguente ne elenca alcuni.

    | Carico di lavoro               | Parametro per impostare le attività                                                         |
    |--------------------|------------------------------------------------------------------------------------|
    | [Spark in HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
    | [Hive in HDInsight](data-lake-store-performance-tuning-hive.md)    | hive.tez.container.size         |
    | [MapReduce in HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
    | [Storm in HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Numero di processi del ruolo di lavoro</li><li>Numero di istanze di spout executor</li><li>Numero di istanze di bolt executor </li><li>Numero di attività spout</li><li>Numero di attività bolt</li></ul>|

## <a name="see-also"></a>Vedere anche
* [Panoramica dell’Archivio Data Lake di Azure](data-lake-store-overview.md)
* [Introduzione all’analisi dei dati di Data Lake di Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



<!--HONumber=Jan17_HO3-->


