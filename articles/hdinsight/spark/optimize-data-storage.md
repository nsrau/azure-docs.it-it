---
title: Ottimizzare l'archiviazione dei dati per Apache Spark - Azure HDInsight
description: Informazioni su come ottimizzare l'archiviazione dei dati con Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 7162e2e8c42f3e83a47c46d739f93cfc4cfcaac6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737632"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Ottimizzazione dell'archiviazione dei dati per Apache Spark

Questo articolo illustra le strategie per ottimizzare l'archiviazione dei dati per un'esecuzione efficiente dei processi di Apache Spark in Azure HDInsight.

## <a name="overview"></a>Panoramica

Spark supporta molti formati, come ad esempio csv, json, xml, parquet, orc e avro. Spark può essere esteso per supportare numerosi formati con origini dati esterne; per altre informazioni, vedere [Pacchetti Apache Spark](https://spark-packages.org).

Il formato migliore per le prestazioni è Parquet con *compressione Snappy*, ovvero l'impostazione predefinita in Spark 2.x. Parquet archivia i dati in formato a colonne ed è altamente ottimizzato in Spark.

## <a name="choose-data-abstraction"></a>Scegliere l'astrazione dei dati

Nelle versioni precedenti di Spark si usano RDD per estrapolare dati. In particolare, in Spark 1.3 e 1.6 sono stati introdotti rispettivamente frame di dati e set di dati. Valutare i seguenti vantaggi:

* **Frame di dati**
    * La soluzione ottimale nella maggior parte dei casi
    * Consentono l'ottimizzazione query tramite Catalyst
    * Generazione di codici whole-stage
    * Accesso diretto alla memoria
    * Sovraccarico ridotto di Garbage Collection (GC)
    * Per gli sviluppatori non risultano efficienti quanto i set di dati, perché non sono presenti controlli in fase di compilazione né alcuna programmazione degli oggetti di dominio
* **Set di dati**
    * Ideali nelle pipeline ETL complesse in cui l'impatto sulle prestazioni è accettabile
    * Sconsigliati nelle aggregazioni in cui l'impatto sulle prestazioni può essere considerevole
    * Consentono l'ottimizzazione query tramite Catalyst
    * Apprezzati dagli sviluppatori, offrono controlli in fase di compilazione e programmazione di oggetti di dominio
    * Comportano un sovraccarico di serializzazione/deserializzazione
    * Sovraccarico GC elevato
    * Interrompono la generazione di codici whole-stage
* **RDD**
    * Non è necessario usare RDD, a meno che non sia necessario creare un nuovo RDD personalizzato.
    * Nessuna ottimizzazione query tramite Catalyst
    * Nessuna generazione di codici whole-stage
    * Sovraccarico GC elevato
    * È necessario usare API legacy per Spark 1.x

## <a name="select-default-storage"></a>Selezionare la risorsa di archiviazione predefinita

Quando si crea un nuovo cluster Spark, è possibile selezionare Archiviazione BLOB di Azure o Azure Data Lake Storage come spazio di archiviazione predefinito del cluster. Entrambe le opzioni offrono il vantaggio di un'archiviazione a lungo termine per i cluster temporanei. Quando si elimina il cluster, i dati non vengono eliminati automaticamente. È possibile ricreare un cluster temporaneo e accedere comunque ai dati.

| Tipo di store | File system | speed | Temporaneo | Modalità di utilizzo comuni |
| --- | --- | --- | --- | --- |
| Archiviazione BLOB di Azure | **wasb:** //url/ | **Standard** | Sì | Cluster temporaneo |
| Archiviazione BLOB di Azure (sicuro) | **wasbs:** //url/ | **Standard** | Sì | Cluster temporaneo |
| Azure Data Lake Storage Gen 2| **abfs:** //url/ | **Più rapido** | Sì | Cluster temporaneo |
| Azure Data Lake Storage Gen 1| **adl:** //url/ | **Più rapido** | Sì | Cluster temporaneo |
| Hadoop Distributed File System locale | **hdfs:** //url/ | **Il più rapido** | No | Cluster interattivo 24/7 |

Per una descrizione completa delle opzioni di archiviazione, vedere [Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Usare la cache

Spark offre meccanismi di memorizzazione nella cache nativi che possono essere usati con metodi diversi, ad esempio `.persist()`, `.cache()` e `CACHE TABLE`. La memorizzazione nella cache nativa è efficace con set di dati di piccole dimensioni e con pipeline ETL in cui è necessario memorizzare nella cache i risultati intermedi. Tuttavia, la memorizzazione nella cache nativa in Spark attualmente non funziona in modo ottimale con il partizionamento, poiché le tabelle memorizzate nella cache non conservano i dati del partizionamento. Una tecnica di memorizzazione nella cache più generica e affidabile è la *memorizzazione nella cache a livello di archiviazione*.

* Memorizzazione nella cache nativa di Spark (non consigliata)
    * Adatta per set di dati di piccole dimensioni.
    * Non funziona con il partizionamento, ma potrebbe cambiare nelle versioni future di Spark.

* Memorizzazione nella cache a livello di archiviazione (consigliata)
    * Può essere implementata in HDInsight con la funzionalità [Cache IO](apache-spark-improve-performance-iocache.md).
    * Usa una memorizzazione nella cache di unità SSD e interna alla memoria.

* Hadoop Distributed File System locale (opzione consigliata)
    * Percorso `hdfs://mycluster`.
    * Usa la memorizzazione nella cache di unità SSD.
    * I dati memorizzati nella cache andranno persi quando si elimina il cluster, richiedendo così una ricompilazione della cache.

## <a name="optimize-data-serialization"></a>Ottimizzare la serializzazione dei dati

I processi Spark vengono distribuiti, per cui una serializzazione dei dati appropriata è importante per ottenere prestazioni ottimali.  Sono disponibili due opzioni di serializzazione per Spark:

* La serializzazione predefinita è quella in Java.
* La serializzazione `Kryo` è un formato più recente e può rivelarsi più veloce e più compatta rispetto a Java.  `Kryo` richiede la registrazione delle classi nel programma e non supporta ancora tutti i tipi serializzabili.

## <a name="use-bucketing"></a>Uso di bucket

L'uso di bucket è simile al partizionamento dei dati, ma ogni bucket può contenere un set di valori in colonna anziché uno solo. Questo metodo funziona anche per il partizionamento di valori più numerosi (nell'ordine di milioni o più), ad esempio per gli identificatori prodotto. Un bucket è determinato dall'hash affiancato alla chiave di bucket della riga. Le tabelle inserite in bucket offrono ottimizzazioni univoche perché archiviano i metadati relativi a come sono state inserite in bucket e ordinate.

Ecco alcune funzionalità bucket avanzate:

* Ottimizzazione query in base alla ripartizione in bucket di metainformazioni
* Aggregazioni ottimizzate
* Join ottimizzati

È possibile usare partizionamento e bucket contemporaneamente.

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzare l'elaborazione dei dati per Apache Spark](optimize-cluster-configuration.md)
* [Ottimizzare l'utilizzo della memoria per Apache Spark](optimize-memory-usage.md)
* [Ottimizzare la configurazione del cluster per Apache Spark](optimize-cluster-configuration.md)
