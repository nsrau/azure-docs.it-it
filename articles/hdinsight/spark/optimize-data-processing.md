---
title: Ottimizzare l'elaborazione dei dati per Apache Spark - Azure HDInsight
description: Informazioni su come scegliere le operazioni più efficienti per elaborare i dati in Apache Spark con Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 1e48573c2b73c10f10f665b5b91759d54d79acdd
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790788"
---
# <a name="data-processing-optimization"></a>Ottimizzazione dell'elaborazione dei dati

Questo articolo illustra come ottimizzare la configurazione del cluster di Apache Spark per ottenere prestazioni ottimali in Azure HDInsight.

## <a name="overview"></a>Panoramica

Se sono presenti processi lenti in un'operazione Join o di riproduzione casuale, la causa è probabilmente dovuta a un'*asimmetria dei dati*, più precisamente nei dati del processo. Ad esempio, un processo di mapping può richiedere 20 secondi, mentre l'esecuzione di un processo in cui i dati vengono uniti in join o aggiunti o selezionati in ordine casuale richiede ore. Per correggere l'asimmetria dei dati è consigliabile archiviare in valori salt l'intera chiave o usare un *salt isolato* soltanto per alcuni subset di chiavi. Se si usa un salting isolato, è necessario filtrare ulteriormente per isolare il subset di chiavi archiviate con salting nei join di mapping. È anche possibile introdurre una colonna di bucket ed eseguire una prima aggregazione in bucket.

Un altro fattore che può causare join lenti è il tipo di join. Per impostazione predefinita, Spark usa il tipo di join `SortMerge`. Questo tipo di join è ideale per grandi set di dati, mentre in caso contrario risulta oneroso in termini di calcolo, perché è necessario innanzitutto ordinare i lati sinistro e destro dei dati prima di unirli.

Un join `Broadcast` è particolarmente appropriato per i set di dati più piccoli o laddove un lato del join sia nettamente inferiore all'altro. Questo tipo di join trasmette un solo lato a tutti gli executor e pertanto richiede più memoria per le trasmissioni in generale.

È possibile modificare il tipo di join nella configurazione impostando `spark.sql.autoBroadcastJoinThreshold`; in alternativa, è possibile impostare un join hint usando le API del frame di dati (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Se si usano tabelle inserite in bucket, è possibile usare un terzo tipo di join, ovvero `Merge`. Un set di dati correttamente pre-partizionato e preordinato ignorerà la fase costosa di ordinamento da un join `SortMerge`.

L'ordine dei join è importante, in particolare nelle query più complesse. Iniziare con i join più selettivi. Laddove possibile, spostare anche i join che aumentano il numero di righe dopo le aggregazioni.

Per gestire il parallelismo per i join cartesiani, è possibile aggiungere strutture annidate, windowing e talvolta anche ignorare uno o più passaggi nel processo Spark.

## <a name="optimize-job-execution"></a>Ottimizzare l'esecuzione del processo

* Memorizzare nella cache secondo necessità; ad esempio, se si usano i dati due volte, memorizzarli nella cache.
* Trasmissione di variabili a tutti gli executor. Le variabili vengono serializzate solo una volta, con conseguente snellimento delle ricerche.
* Usare il pool di thread sul driver, determinando operazioni più veloci per molte attività.

Monitorare regolarmente i processi in esecuzione per rilevare eventuali problemi di prestazioni. Se è necessario approfondire alcuni problemi, è possibile usare uno degli strumenti di profilatura delle prestazioni seguenti:

* [Intel PAL Tool](https://github.com/intel-hadoop/PAT) monitora l'uso della CPU, delle risorse di archiviazione, della rete e della larghezza di banda.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profila il codice dell'executor e di Spark.

La chiave per le prestazioni delle query di Spark 2.x è il motore al tungsteno, che dipende dalla generazione di codici whole-stage. In alcuni casi, la generazione di codici whole-stage potrebbe essere disabilitata. Ad esempio, se si usa un tipo non modificabile (`string`) nell'espressione di aggregazione, viene visualizzato `SortAggregate` al posto di `HashAggregate`. Ad esempio, per ottenere prestazioni migliori, eseguire le operazioni seguenti, quindi abilitare nuovamente la generazione di codici:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzare l'archiviazione dei dati per Apache Spark](optimize-data-storage.md)
* [Ottimizzare l'utilizzo della memoria per Apache Spark](optimize-memory-usage.md)
* [Ottimizzare la configurazione del cluster per Apache Spark](optimize-cluster-configuration.md)