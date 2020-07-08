---
title: Ottimizzare la configurazione del cluster Apache Spark - Azure HDInsight
description: Informazioni su come configurare il cluster Apache Spark per ottimizzare la velocità effettiva in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 464f0dcab3debf92605d2f13be9b25ece63f4bf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737683"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Ottimizzazione della configurazione del cluster per Apache Spark

Questo articolo illustra come ottimizzare la configurazione del cluster di Apache Spark per ottenere prestazioni ottimali in Azure HDInsight.

## <a name="overview"></a>Panoramica

A seconda del carico di lavoro del cluster Spark, è possibile stabilire se una configurazione non predefinita per Spark darà origine a un'esecuzione del processo Spark maggiormente ottimizzata.  Eseguire test di benchmark con carichi di lavoro di esempio per convalidare tutte le configurazioni cluster non predefinite.

Ecco alcuni parametri comuni che è possibile modificare:

|Parametro |Descrizione |
|---|---|
|--num-executors|Imposta il numero appropriato di executor.|
|--executor-cores|Imposta il numero di core per ogni executor. Di norma si useranno degli executor di dimensioni medie, giacché parte della memoria disponibile è già usata da altri processi.|
|--executor-memory|Imposta le dimensioni della memoria per ogni executor, che controlla le dimensioni dell'heap in YARN. Lasciare una certa quantità di memoria per l'overhead di esecuzione.|

## <a name="select-the-correct-executor-size"></a>Selezionare le dimensioni corrette dell'executor

Quando si sceglie la configurazione dell'executor, prendere in considerazione l'overhead di Java Garbage Collection (GC).

* Fattori che riducono le dimensioni dell'executor:
    1. Ridurre le dimensioni heap al di sotto di 32 GB per mantenere un overhead di GC < 10%.
    2. Ridurre il numero di core per mantenere un overhead di GC < 10%.

* Fattori che aumentano le dimensioni dell'executor:
    1. Ridurre l'overhead di comunicazione tra executor.
    2. Ridurre il numero di connessioni aperte tra executor (N2) nei cluster di grandi dimensioni (> 100 executor).
    3. Aumentare le dimensioni dell'heap per consentire un uso intensivo della memoria.
    4. Facoltativo: ridurre l'overhead della memoria per ogni executor.
    5. Facoltativo: aumentare la concorrenza e l'uso mediante sovrascrittura della CPU.

Come regola generale quando si selezionano le dimensioni dell'executor:

1. Iniziare con 30 GB per executor e distribuire i core disponibili sul computer.
2. Aumentare il numero di core per executor per i cluster di grandi dimensioni (> 100 executor).
3. Modificare le dimensioni in base alle esecuzioni di prova e ai fattori precedenti, ad esempio l'overhead di Garbage Collection.

Durante l'esecuzione di query simultanee, considerare di:

1. Iniziare con 30 GB per ogni executor e per tutti i core del computer.
2. Creare più applicazioni Spark parallele sovrascrivendo la CPU (miglioramento della latenza di circa il 30%).
3. Distribuire le query tra applicazioni parallele.
4. Modificare le dimensioni in base alle esecuzioni di prova e ai fattori precedenti, ad esempio l'overhead di Garbage Collection.

Per altre informazioni sull'uso di Ambari per configurare gli Executor, vedere [Impostazioni di Apache Spark - Executor Spark](apache-spark-settings.md#configuring-spark-executors).

Monitorare le prestazioni della query per gli outlier o altri problemi di prestazioni osservando la visualizzazione della sequenza temporale, il grafico SQL, le statistiche dei processi e così via. Per informazioni sul debug di processi Spark con YARN e il server cronologia Spark, vedere [Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight](apache-spark-job-debugging.md). Per suggerimenti sull'uso del server di sequenza temporale YARN, vedere [Accedere ai log applicazioni di Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

In alcuni casi, uno o più executor possono rivelarsi più lenti rispetto ad altri e richiedere molto più tempo per eseguire le attività. Questa lentezza si verifica spesso nei cluster di dimensioni maggiori (> 30 nodi). In questo caso, suddividere il lavoro in un numero maggiore di attività, in modo che l'utilità di pianificazione riesca a compensare l'effetto di rallentamento delle attività. Ad esempio, prevedere almeno il doppio di attività rispetto al numero di core degli executor nell'applicazione. È anche possibile abilitare l'esecuzione speculativa delle attività con `conf: spark.speculation = true`.

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzare l'elaborazione dei dati per Apache Spark](optimize-cluster-configuration.md)
* [Ottimizzare l'archiviazione dei dati per Apache Spark](optimize-data-storage.md)
* [Ottimizzare l'utilizzo della memoria per Apache Spark](optimize-memory-usage.md)
