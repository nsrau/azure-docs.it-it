---
title: Note sulla versione dei componenti Hadoop in Azure HDInsight | Microsoft Docs
description: "Note sulla versione più recente e le versioni di componenti Hadoop per Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Spark, R Server, Hive e altro."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 96b2b4976729da5b7d8b75909dbe099090240c08
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Note sulla versione di componenti Hadoop in Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).


## <a name="notes-for-08012017-release-of-hdinsight"></a>Note per la versione di HDInsight rilasciata in data 01/08/2017

| Titolo | Descrizione | Area interessata  | Tipo di cluster  | 
| --- | --- | --- | --- | --- |
| Versione di Microsoft R Server 9.1 in HDInsight |HDInsight supporta ora il provisioning dei cluster R Server 9.1 in HDInsight. Per altre informazioni sulla versione di Microsoft R Server 9.1, vedere [questo blog](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Service |R Server |
| HDInsight 3.6 include ora le versioni più recenti dello stack di Hadoop|<ul><li>Per un elenco dettagliato delle versioni aggiornate, vedere [Componenti di Hadoop disponibili con diverse versioni di HDInsight](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>Per un elenco di bug risolti nelle versioni più recenti dello stack di Hadoop, vedere la pagina relativa alle [informazioni delle patch Apache](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Per un elenco delle modifiche importanti in HDP 2.6.1 (ora disponibile in HDInsight 3.6), vedere [https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Per un elenco dei problemi noti di HDP 2.6.1, vedere la pagina relativa ai [problemi noti](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Service |Tutti |N/D |
| Aggiornamenti ai cluster Interactive Hive (anteprima) |<ul><li><b>Miglioramento della funzionalità.</b> L'implementazione del metastore memorizzato nella cache riduce il carico sul database di back-end di SQL memorizzando nella cache i metadati e migliora le prestazioni per tutte le operazioni dei metadati.  Questo miglioramento è ora un'impostazione predefinita in tutti i cluster Interactive Hive. Per altre informazioni, vedere [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Miglioramento della funzionalità.</b> Il caricamento della partizione dinamica è ottimizzato. Per altre informazioni, vedere [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Miglioramento della funzionalità.</b> Ottimizzazioni di configurazione per HDInsight su Linux.</li><li><b>Correzione di bug.</b> `CredentialProviderFactory$getProviders` non è thread-safe. Questo problema ora è stato corretto. Per altre informazioni, vedere [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Correzione di bug.</b> L'utilizzo elevato della CPU con API `liststatus` del driver WASB genera prestazioni ATS non valide. Questo problema ora è stato corretto. Per altre informazioni, vedere [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Service |Interactive Hive (anteprima) |
| Aggiornamenti ai cluster Hadoop |L'affidabilità dell'operazione del processo Templeton è migliorata. Per altre informazioni, vedere [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947). |Service |Hadoop |
| Aggiornamenti di YARN | HDInsight ora crea un database Ambari da 250 GB (senza l'aumento dei costi), che comporta una migliore esperienza per i clienti. Questa modifica dovrebbe impedire il riempimento di ATS e migliorare le prestazioni. |Service |Tutti |
| Aggiornamenti di Spark | Versione di Spark 2.1.1. Per altre informazioni, vedere la pagina relativa alle [note sulla versione di Spark 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Service | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 - Disponibilità generale di HDInsight 3.6

* Con questa versione, Azure HDInsight aggiunge la versione 3.6, basata su HDP 2.6. Le note sulla versione di HDP 2.6 sono disponibili [qui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) e altre informazioni sulle versioni di HDInsight sono disponibili [qui](hdinsight-component-versioning.md). HDInsight 3.6 è disponibile per i carichi di lavoro seguenti:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Supporto per Hive View 2.0**. Questa vista migliora l'esperienza utente in Interactive Hive. Per altre informazioni, vedere la [documentazione di Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Miglioramenti delle prestazioni con Hive LLAP**. Per altre informazioni, vedere la [documentazione di Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nuove funzionalità di Hive**. Vedere la [documentazione di Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Interfaccia della riga di comandi di Hive deprecata**: l'interfaccia della riga di comando di Hive verrà deprecata e si invita gli utenti a usare Beeline al suo posto. Per altre informazioni, vedere la [documentazione Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Per istruzioni su come usare Beeline con HDInsight, vedere [Use Beeline with HDInsight Hadoop clusters](hdinsight-hadoop-use-hive-beeline.md) (Usare Beeline con i cluster Hadoop HDInsight).

* **Nuove funzionalità di Apache Phoenix e HBase**.
    * Supporto di quota di archiviazione: comunemente usato negli ambienti multi-tenant, consente uno spazio di archiviazione limitato a livello di tabella e di spazio dei nomi.
    * Miglioramenti di indicizzazione Phoenix: creazione dell'indice incrementale e ricompilazione o ripresa dell'indicizzazione da errori precedenti.
    * Strumento di integrità dei dati di Phoenix: supporta la convalida di schema, indice e altri metadati.


* **Problema con HBase**: durante l'esecuzione di un processo MapReduce di caricamento in massa di CSV, la sintassi seguente potrebbe causare un errore.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Usare invece la sintassi seguente:

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
*   Nei cluster R Server è ora possibile scegliere due versioni: R Server 9.0 in HDI 3.5 (Spark 2.0) e R Server 8.0 in HDI 3.4 (Spark 1.6).
*   R Server 9.0 in HDI 3.5 (Spark 2.0) si basa su R 3.3.2 e include nuove funzioni di origine dati per ScaleR, denominate RxHiveData e RxParquetData, per il caricamento diretto di dati da Hive e Parquet a frame di dati Spark per l'analisi in ScaleR. Per altre informazioni su queste funzioni, vedere la guida in linea di R usando i comandi **?RxHiveData** e **?RxParquetData**.
*   RStudio Server Community Edition è ora installato per impostazione predefinita (con possibilità di rifiuto esplicito) nel pannello Configurazione cluster, nell'ambito del flusso di provisioning.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - Versione di Spark 2.0 in HDInsight
* I cluster Spark 2.0 in HDInsight 3.5 supportano ora servizi Livy e Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10/26/2016 - Versione di R Server in HDInsight
* L'URI per l'accesso a nodi perimetrali è stato modificato in **nomecluster**-ed-ssh.azurehdinsight.net
* Il provisioning del cluster R Server in HDInsight è stato semplificato.
* R Server in HDInsight ora è disponibile come normale tipo di cluster "R Server" in HDInsight e non viene più installato come applicazione HDInsight separata. Il provisioning del nodo perimetrale e dei file binari di R Server ora viene effettuato durante la distribuzione del cluster R Server. In questo modo il provisioning è più veloce e affidabile. Il modello di determinazione prezzi per R Server viene aggiornato di conseguenza.
* Il prezzo del tipo di cluster R Server ora corrisponde al prezzo del piano Standard più la maggiorazione per R Server. Il piano Premium è riservato alle funzionalità Premium disponibili in tipi di cluster diversi e non viene usato per il tipo di cluster R Server. Questa modifica non ha effetto sul prezzo effettivo di R Server. Cambia solo il modo in cui gli addebiti vengono presentati nella fattura. Tutti i cluster R Server esistenti e i modelli di Azure Resource Manager continuano a funzionare fino all'avviso di funzionalità deprecata. **È tuttavia consigliabile aggiornare le distribuzioni con script per l'uso del nuovo modello di Resource Manager.**







