---
title: Ottimizzare l'utilizzo della memoria in Apache Spark - Azure HDInsight
description: Informazioni su come ottimizzare l'utilizzo della memoria in Apache Spark su Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 6992359bbef743bffba0ccbb7f5db3f865e7993b
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790748"
---
# <a name="memory-usage-optimization"></a>Ottimizzazione dell'utilizzo di memoria

Questo articolo illustra come ottimizzare la gestione della memoria del cluster di Apache Spark per ottenere prestazioni ottimali in Azure HDInsight.

## <a name="overview"></a>Panoramica

Spark funziona inserendo i dati in memoria. Pertanto, la gestione delle risorse di memoria è un aspetto essenziale per ottimizzare l'esecuzione dei processi Spark.  Esistono diverse tecniche che è possibile applicare per usare la memoria del cluster in modo efficiente.

* Prediligere partizioni di dati più piccole e tenere conto delle dimensioni, del tipo e della distribuzione dei dati nella strategia di partizionamento.
* Valutare la versione più recente e più efficiente di [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo), anziché la serializzazione Java predefinita.
* Prediligere l'uso di YARN, che consente la separazione `spark-submit` per batch.
* Monitorare e ottimizzare le impostazioni di configurazione di Spark.

La struttura della memoria Spark e alcuni parametri di memoria dell'executor chiave vengono visualizzati nella figura seguente per riferimento.

## <a name="spark-memory-considerations"></a>Considerazioni sulla memoria Spark

Se si usa Apache Hadoop YARN, questo controlla la memoria usata da tutti i contenitori in ogni nodo Spark.  Il diagramma seguente mostra gli oggetti chiave e le relative relazioni.

![Gestione della memoria Spark in YARN](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Per indirizzare messaggi di "memoria insufficiente", provare a:

* Esaminare le riproduzioni casuali con gestione DAG. Ridurre mediante riduzione lato mappa, pre-partizione (o assegnazione di bucket) dell'origine dati, ottimizzazione delle riproduzioni con sequenza casuale singole e riduzione della quantità di dati inviati.
* Prediligere `ReduceByKey` per il limite di memoria fissa rispetto a `GroupByKey`, che fornisce aggregazioni, windowing e altre funzioni, ma non ha limite di memoria.
* Prediligere `TreeReduce`, che esegue più operazioni su executor e partizioni rispetto a `Reduce`, che esegue tutto il lavoro sul driver.
* Usare i frame di dati, anziché gli oggetti RDD di livello inferiore.
* Creare tipi complessi in grado di incapsulare azioni, come ad esempio "Top N", diverse aggregazioni od operazioni di windowing.

Per altri passaggi per la risoluzione dei problemi, vedere [Eccezioni OutOfMemoryError per Apache Spark in Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzare l'elaborazione dei dati per Apache Spark](optimize-cluster-configuration.md)
* [Ottimizzare l'archiviazione dei dati per Apache Spark](optimize-data-storage.md)
* [Ottimizzare la configurazione del cluster per Apache Spark](optimize-cluster-configuration.md)