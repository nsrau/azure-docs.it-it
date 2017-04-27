---
title: Note sulla versione dei componenti Hadoop in Azure HDInsight | Microsoft Docs
description: "Note sulla versione più recente e le versioni di componenti Hadoop per Azure HDInsight. È possibile ottenere suggerimenti per lo sviluppo e i dettagli per Hadoop, Apache Storm e HBase."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: e9bcb0cb33eefc743ed220cb2a874f9ae11b5fe7
ms.lasthandoff: 04/10/2017


---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Note sulla versione di componenti Hadoop in Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 - Disponibilità generale di HDInsight 3.6

* Con questa versione, Azure HDInsight aggiunge la versione 3.6, basata su HDP 2.6. Le note sulla versione di HDP 2.6 sono disponibili [qui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) e altre informazioni sulle versioni di HDInsight sono disponibili [qui](hdinsight-component-versioning.md). HDInsight 3.6 è disponibile per i carichi di lavoro seguenti.

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Supporto per Hive View 2.0**. Questa vista migliora l'esperienza utente in Interactive Hive. Per altre informazioni, vedere la [documentazione Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Miglioramenti delle prestazioni con Hive LLAP**. Per altre informazioni, vedere la [documentazione Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nuove funzionalità di Hive**. Per altre informazioni, vedere la [documentazione Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Interfaccia della riga di comandi di Hive deprecata**: l'interfaccia della riga di comando di Hive verrà deprecata e si invita gli utenti a usare Beeline al suo posto. Per altre informazioni, vedere la [documentazione Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Per istruzioni su come usare Beeline con HDInsight, vedere [Use Beeline with HDInsight Hadoop clusters](hdinsight-hadoop-use-hive-beeline.md) (Usare Beeline con i cluster Hadoop HDInsight).

* **Nuove funzionalità di Apache Phoenix e HBase**.
    * Supporto di quota di archiviazione: comunemente usato negli ambienti multi-tenant, consente uno spazio di archiviazione limitato a livello di tabella e di spazio dei nomi.
    * Miglioramenti di indicizzazione Phoenix: creazione dell'indice incrementale e ricompilazione o ripresa dell'indicizzazione da errori precedenti.
    * Strumento di integrità dei dati di Phoenix: supporta la convalida di schema, indice e altri metadati.


* **Problema con HBase**: durante l'esecuzione di un processo MapReduce di caricamento in massa di CSV, la sintassi seguente potrebbe causare un errore.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Usare la sintassi seguente al suo posto.

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 - Versione di Spark 2.1 in HDInsight 3.6 (Anteprima)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) risolve i molti problemi relativi a usabilità e stabilità riscontrati con le versioni precedenti. Offre anche nuove funzionalità in tutti i carichi di lavoro di Spark, ad esempio Spark Core, SQL, ML e Streaming.
* Streaming strutturati presentano una migliore scalabilità con il supporto per soglie di tempo dell'evento e con il connettore Kafka 0.10.
* Il partizionamento Spark SQL viene ora gestito usando il nuovo meccanismo di gestione di partizioni scalabili. Per altre informazioni su come eseguire l'aggiornamento, vedere [qui](http://spark.apache.org/releases/spark-release-2-1-0.html).
* Spark 2.1 in Azure HDInsight 3.6 Preview attualmente non supporta la connettività degli strumenti di Business Intelligence tramite il driver ODBC.
* L'accesso di Azure Data Lake Store dai cluster Spark 2.1 non è supportato in questa versione di anteprima.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016 - Versione di Spark 2.0.1 in HDInsight 3.5
Spark 2.0.1 è ora disponibile in cluster Spark (HDInsight versione 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 - Versione di R Server 9.0 in HDInsight 3.5 (Spark 2.0)
*    Nei cluster R Server è ora possibile scegliere due versioni: R Server 9.0 in HDI 3.5 (Spark 2.0) e R Server 8.0 in HDI 3.4 (Spark 1.6).
*    R Server 9.0 in HDI 3.5 (Spark 2.0) si basa su R 3.3.2 e include nuove funzioni di origine dati per ScaleR, denominate RxHiveData e RxParquetData, per il caricamento diretto di dati da Hive e Parquet a frame di dati Spark per l'analisi in ScaleR. Per altre informazioni su queste funzioni, vedere la guida in linea di R usando i comandi ?RxHiveData e ?RxParquetData.
*    RStudio Server Community Edition è ora installato per impostazione predefinita (con possibilità di rifiuto esplicito) nel pannello Configurazione cluster, nell'ambito del flusso di provisioning.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - Versione di Spark 2.0 in HDInsight
* I cluster Spark 2.0 in HDInsight 3.5 supportano ora servizi Livy e Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10/26/2016 - Versione di R Server in HDInsight
* L'URI per l'accesso a nodi perimetrali è stato modificato in **nomecluster**-ed-ssh.azurehdinsight.net
* Il provisioning del cluster R Server in HDInsight è stato semplificato.
* R Server in HDInsight ora è disponibile come normale tipo di cluster "R Server" in HDInsight e non viene più installato come applicazione HDInsight separata. Il provisioning del nodo perimetrale e dei file binari di R Server ora viene effettuato durante la distribuzione del cluster R Server. In questo modo il provisioning è più veloce e affidabile. Il modello di determinazione prezzi per R Server viene aggiornato di conseguenza.
* Il prezzo del tipo di cluster R Server ora corrisponde al prezzo del piano Standard più la maggiorazione per R Server. Il piano Premium ora sarà riservato alle funzionalità Premium disponibili in tipi di cluster diversi e non verrà usato per il tipo di cluster R Server. Questa modifica non ha effetto sul prezzo effettivo di R Server. Cambia solo il modo in cui gli addebiti vengono presentati nella fattura. Tutti i cluster R Server esistenti e i modelli di Azure Resource Manager continueranno a funzionare fino all'avviso di funzionalità deprecata. **È tuttavia consigliabile aggiornare le distribuzioni con script per usare il nuovo modello di Azure Resource Manager.**

## <a name="08302016---release-of-r-server-on-hdinsight"></a>08/30/2016 - Versione di R Server in HDInsight
Numeri di versione completi per i cluster HDInsight basati su Linux distribuiti con questa versione:

| HDI | Cluster versione HDI | HDP | Compilazione HDP | Compilazione Ambari |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8268980 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8268980 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8269383 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Numeri di versione completi per i cluster HDInsight basati su Windows distribuiti con questa versione:

| HDI | Cluster versione HDI | HDP | Compilazione HDP |
| --- | --- | --- | --- |
| 2.1 |2.1.10.1033.2559206 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1033.2559206 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1033.2559206 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1033.2559206 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1033.2559206 |2.3 |2.3.3.1-25 |






