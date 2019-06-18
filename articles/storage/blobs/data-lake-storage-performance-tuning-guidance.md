---
title: Linee guida per l'ottimizzazione delle prestazioni di Azure Data Lake Storage Gen2 | Microsoft Docs
description: Linee guida per l'ottimizzazione delle prestazioni di Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 6f831dd0cde4641eb48f3c23e010f8c5e8aa3fa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939365"
---
# <a name="tuning-azure-data-lake-storage-gen2-for-performance"></a>Ottimizzazione delle prestazioni di Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 supporta la velocità effettiva elevata per l'analisi con uso intensivo dell'I/O e lo spostamento dei dati.  In Data Lake Storage Gen2 è importante poter usare tutta la velocità effettiva disponibile, ovvero la quantità di dati che possono essere letti o scritti al secondo, per ottenere prestazioni ottimali.  Questo risultato viene ottenuto eseguendo il numero massimo possibile di operazioni di lettura e scrittura in parallelo.

![Prestazioni di Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 può essere ridimensionato per offrire la velocità effettiva necessaria per qualsiasi scenario di analisi. Per impostazione predefinita, un account Data Lake Storage Gen2 offre automaticamente la velocità effettiva sufficiente per soddisfare le esigenze di un'ampia categoria di casi d'uso. Per i casi in cui i clienti raggiungono il limite predefinito, è possibile contattare l'[assistenza Azure](https://azure.microsoft.com/support/faq/) per configurare l'account Data Lake Storage Gen2 in modo da ottenere maggiore velocità effettiva.

## <a name="data-ingestion"></a>Inserimento di dati

Durante l'inserimento di dati da un sistema di origine a Data Lake Storage Gen2, è importante tenere presente che l'hardware di origine, l'hardware di rete di origine e la connettività di rete a Data Lake Storage Gen2 possono costituire il collo di bottiglia.  

![Prestazioni di Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

È importante verificare che questi fattori non influiscano sullo spostamento dei dati.

### <a name="source-hardware"></a>Hardware di origine

Indipendentemente dall'uso di computer locali o macchine virtuali in Azure, è necessario scegliere con attenzione l'hardware appropriato. Per l'hardware del disco di origine, preferire le unità SSD alle HDD e scegliere hardware con spindle più veloci. Per l'hardware di rete di origine, usare le schede di interfaccia di rete più veloci possibili.  In Azure è consigliabile usare macchine virtuali Azure D14 che sono dotate di hardware di rete e del disco sufficientemente potente.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Connettività di rete a Data Lake Storage Gen2

La connettività di rete tra i dati di origine e Data Lake Storage Gen2 può talvolta costituire il collo di bottiglia. Quando i dati di origine sono in locale, valutare l'opportunità di usare un collegamento dedicato con [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Se i dati di origine sono in Azure, si ottengono prestazioni ottimali quando i dati si trovano nella stessa area di Azure dell'account Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configurare gli strumenti di inserimento dei dati per la massima parallelizzazione

Dopo aver risolto i colli di bottiglia provocati dall'hardware di origine e dalla connettività di rete, si è pronti per configurare gli strumenti di inserimento. La tabella seguente presenta un riepilogo delle impostazioni delle chiavi per diversi strumenti di inserimento comuni e include collegamenti ad articoli di approfondimento sull'ottimizzazione delle prestazioni.  Per altre informazioni sullo strumento da usare per uno scenario specifico, vedere questo [articolo](data-lake-storage-data-scenarios.md).

| Strumento               | Impostazioni     | Altre informazioni                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapper)   | [Collegamento](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Data factory di Azure| parallelCopies    | [Collegamento](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Collegamento](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Strutturare il set di dati

Quando i dati vengono archiviati in Data Lake Storage Gen2, le dimensioni dei file, il numero di file e la struttura della cartelle influiscono sulle prestazioni.  La sezione seguente descrive le procedure consigliate in queste aree.  

### <a name="file-size"></a>Dimensioni complete

I motori di analisi come HDInsight e Azure Data Lake Analytics in genere gestiscono il sovraccarico a livello di singolo file. Se quindi si archiviano i dati in molti file di piccole dimensioni, questo può influire negativamente sulle prestazioni. Per ottenere prestazioni migliori, è in genere opportuno organizzare i dati in file di dimensioni più grandi (da 256 MB a 100 GB). Alcuni motori e applicazioni potrebbero riscontrare problemi nell'elaborazione dei file di dimensioni superiori a 100 GB.

Le pipeline di dati hanno talvolta un controllo limitato sui dati non elaborati costituiti da molti file di piccole dimensioni. È pertanto consigliabile prevedere l'esecuzione di un processo che genera file di maggiori dimensioni destinati all'uso da parte delle applicazioni downstream.

### <a name="organizing-time-series-data-in-folders"></a>Organizzazione dei dati di serie temporali in cartelle

Per i carichi di lavoro Hive, l'eliminazione delle partizioni dei dati di serie temporali consente ad alcune query di limitare la lettura a un subset di dati, migliorando così le prestazioni.    

Queste pipeline che inseriscono dati di serie temporali, posizionano spesso i file usando un sistema di denominazione molto strutturato per file e cartelle. Di seguito è riportato un esempio molto comune in cui i dati sono strutturati per data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Si noti che le informazioni di data/ora vengono visualizzate sia come cartelle sia nel nome del file.

Per la data e l'ora, di seguito è riportato un modello comune

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Anche in questo caso, la scelta relativa all'organizzazione di file e cartelle deve prevedere una gestione ottimizzata dei file di maggiori dimensioni e l'inclusione di un numero ragionevole di file in ogni cartella.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Ottimizzazione dei processi con uso intensivo dell'I/O nei carichi di lavoro Hadoop e Spark in HDInsight

I processi sono classificabili in una delle tre categorie seguenti:

* **Uso intensivo della CPU.**  Questi processi hanno tempi di elaborazione lunghi e tempi di I/O minimi,  come i processi di machine learning e quelli di elaborazione del linguaggio naturale.  
* **Uso intensivo della memoria.**  Questi processi usano grandi quantità di memoria,  come i processi di PageRank e quelli di analisi in tempo reale.  
* **Uso intensivo dell'I/O.**  Questi processi impiegano la maggior parte del tempo a eseguire operazioni di I/O.  Un esempio comune è rappresentato da un processo di copia che esegue solo operazioni di lettura e scrittura.  Altri esempi includono i processi di preparazione dei dati che leggono una grande quantità di dati, eseguono alcune trasformazioni e quindi scrivono nuovamente i dati nell'archivio.  

Le linee guida seguenti sono applicabili solo ai processi con uso intensivo dell'I/O.

## <a name="general-considerations"></a>Considerazioni generali

È possibile che un processo legga o scriva fino a 100 MB in un'unica operazione, ma un buffer di tali dimensioni rischia di compromettere le prestazioni.
Per ottimizzare le prestazioni, cercare di mantenere le dimensioni dell'operazione di I/O tra 4 MB e 16 MB.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerazioni generali per un cluster HDInsight

* **Versioni di HDInsight.** Per prestazioni ottimali, usare la versione più recente di HDInsight.
* **Aree.** Inserire l'account Data Lake Storage Gen2 nella stessa area del cluster HDInsight.  

Un cluster HDInsight è composto da due nodi head e da alcuni nodi di ruolo di lavoro. Ogni nodo di ruolo di lavoro fornisce un numero specifico di core e memoria, in base al tipo di macchina virtuale.  Quando si esegue un processo, YARN è il negoziatore di risorse che alloca la memoria e i core disponibili per creare contenitori.  Ogni contenitore esegue le attività necessarie per completare il processo.  I contenitori vengono eseguiti in parallelo per l'elaborazione rapida delle attività. È quindi possibile ottenere un miglioramento delle prestazioni eseguendo la quantità massima possibile di contenitori paralleli.

All'interno di un cluster HDInsight sono presenti tre livelli che possono essere ottimizzati per aumentare il numero di contenitori e sfruttare tutta la velocità effettiva disponibile.  

* **Livello fisico**
* **Livello YARN**
* **Livello del carico di lavoro**

### <a name="physical-layer"></a>Livello fisico

**Eseguire il cluster con più nodi e/o macchine virtuali di dimensioni maggiori.**  Un cluster di dimensioni maggiori consentirà di eseguire più contenitori YARN, come illustrato nell'immagine seguente.

![Prestazioni di Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Usare macchine virtuali con maggiore larghezza di banda di rete.**  La larghezza di banda di rete può costituire un collo di bottiglia se la larghezza di banda di rete disponibile è inferiore alla velocità effettiva di Data Lake Storage Gen2.  Macchine virtuali differenti avranno dimensioni variabili della larghezza di banda di rete.  Scegliere un tipo di macchina virtuale con la massima larghezza di banda di rete possibile.

### <a name="yarn-layer"></a>Livello YARN

**Usare contenitori YARN di dimensioni inferiori.**  Ridurre le dimensioni di ogni contenitore YARN per creare altri contenitori con la stessa quantità di risorse.

![Prestazioni di Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

A seconda del carico di lavoro, sarà sempre necessaria una dimensione minima per i contenitori YARN. Se si sceglie un contenitore troppo piccolo, si verificheranno problemi di memoria insufficiente per i processi. In genere, la dimensione dei contenitori YARN non deve essere inferiore a 1 GB. I contenitori YARN hanno spesso una dimensione di 3 GB. Per alcuni carichi di lavoro, possono essere necessari contenitori YARN più grandi.  

**Aumentare il numero di core per contenitore YARN.**  Aumentare il numero di core allocati a ogni contenitore per aumentare il numero di attività parallele eseguite in ogni contenitore.  Questa soluzione è valida per le applicazioni, come Spark, che eseguono più attività per contenitore.  Per le applicazioni, come Hive, che eseguono un singolo thread in ogni contenitore, è preferibile avere più contenitori anziché più core per contenitore.

### <a name="workload-layer"></a>Livello del carico di lavoro

**Usare tutti i contenitori disponibili.**  Impostare un numero di attività uguale o maggiore del numero di contenitori disponibili, in modo da usare tutte le risorse.

![Prestazioni di Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Le attività che non vengono eseguite correttamente sono dispendiose.** Se ogni attività deve elaborare una grande quantità di dati, l'esito negativo di un'attività ha come risultato l'esecuzione di un nuovo tentativo impegnativo in termini di risorse.  È quindi preferibile creare un numero maggiore di attività, ognuna delle quali elabora una piccola quantità di dati.

Oltre alle linee guida generali sopra illustrate, ogni applicazione dispone di diversi parametri che è possibile ottimizzare. La tabella seguente elenca alcuni parametri e collegamenti per ottimizzare con facilità le prestazioni di ogni applicazione.

| Carico di lavoro | Parametro per impostare le attività |
|----------|------------------------|
| [Spark in HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
| [Hive in HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce in HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm in HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Numero di processi del ruolo di lavoro</li><li>Numero di istanze di spout executor</li><li>Numero di istanze di bolt executor </li><li>Numero di attività spout</li><li>Numero di attività bolt</li></ul>|

## <a name="see-also"></a>Vedere anche
* [Panoramica di Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
