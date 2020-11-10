---
title: Informazioni su Apache Hadoop e MapReduce - Azure HDInsight
description: Introduzione a HDInsight, oltre che allo stack di tecnologie e ai componenti di Apache Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: ef1914499765beff9913f9735cf55736135f9d96
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899622"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Informazioni su Apache Hadoop in Azure HDInsight

[Apache Hadoop](https://hadoop.apache.org/) era il framework open source originale per l'elaborazione distribuita e l'analisi di set di Big Data in cluster. L'ecosistema Hadoop include software e utilità correlate, tra cui Apache Hive, Apache HBase, Spark, Kafka e molti altri.

Azure HDInsight è un servizio di analisi open source completamente gestito e ad ampio spettro nel cloud per le aziende. Il tipo di cluster Apache Hadoop in Azure HDInsight consente di usare [Apache HDFS (Hadoop Distributed File System)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), la gestione delle risorse [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) e un semplice modello di programmazione [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) per elaborare e analizzare i dati batch in parallelo.  I cluster Hadoop in HDInsight sono compatibili con [Archiviazione BLOB di Azure](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) o [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

Per i componenti dello stack di tecnologie Hadoop disponibili in HDInsight, vedere [Componenti e versioni disponibili con HDInsight](../hdinsight-component-versioning.md). Per altre informazioni su Hadoop in HDInsight, vedere la pagina delle [funzionalità di Azure per HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Informazioni su MapReduce

[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) è un framework software per la scrittura di processi in grado di elaborare grandi quantità di dati. I dati di input sono suddivisi in blocchi indipendenti. Ogni blocco viene elaborato in parallelo attraverso i nodi del cluster. Un processo MapReduce è costituito da due funzioni:

* **Mapper** : usa i dati di input, li analizza (in genere mediante operazioni di filtro e ordinamento) e quindi genera tuple (coppie chiave-valore)

* **Reducer** : usa le tuple generate dal Mapper ed esegue un'operazione di riepilogo che crea un risultato combinato più piccolo a partire dai dati del Mapper

La figura seguente illustra un esempio di processo di base di conteggio parole in MapReduce.

 ![HDI.WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

L'output del processo consentirà di conoscere il numero totale di occorrenze di ogni parola presente nel testo.

* Il mapper accetta come input ciascuna riga del testo di input e la suddivide in parole. Emette quindi una coppia chiave-valore ogni volta che viene riscontrata un'occorrenza della parola seguita da 1. L'output viene ordinato e inviato al reducer.
* Il reducer somma i singoli conteggi relativi a ciascuna parola ed emette una sola coppia chiave-valore contenente la parola seguita dal numero di occorrenze.

MapReduce può essere implementato in vari tipi di linguaggio. A scopo dimostrativo, in questo esempio si userà l'implementazione più comune: Java.

## <a name="development-languages"></a>Linguaggi di sviluppo

I linguaggi o i framework basati su Java e Java Virtual Machine possono essere eseguiti direttamente come [processo MapReduce](..//hadoop/submit-apache-hadoop-jobs-programmatically.md). Nell'esempio in questo documento viene usata un'applicazione Java MapReduce. I linguaggi diversi da Java, ad esempio C# o Python, o file eseguibili autonomi devono usare lo **streaming di Hadoop**.

Lo streaming di Hadoop comunica con il mapper e il riduttore tramite STDIN e STDOUT. Il mapper e il riduttore leggono i dati di una riga alla volta da STDIN e scrivono l'output in STDOUT. Ogni riga letta o generata dal mapper e dal riduttore deve essere in formato coppia chiave/valore, separata da un carattere di tabulazione:

`[key]\t[value]`

Per altre informazioni, vedere l'argomento relativo a [Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Per alcuni esempi sull'uso dello streaming di Hadoop con HDInsight, vedere il documento seguente:

* [Sviluppare processi C# MapReduce](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Da dove iniziare

* [Guida introduttiva: Creare cluster Apache Hadoop in Azure HDInsight usando il portale di Azure](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Esercitazione: Inviare processi Apache Hadoop in HDInsight](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Sviluppare programmi Java MapReduce per Apache Hadoop in HDInsight](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Usare Apache Hive come strumento per estrazione, trasformazione e caricamento (ETL, Extract, Transform, and Load)](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Estrarre, trasformare e caricare (ETL) su larga scala](../hadoop/apache-hadoop-etl-at-scale.md)
* [Rendere operativa una pipeline di analisi dei dati](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un cluster Apache Hadoop in HDInsight usando il portale](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Creare un cluster Apache Hadoop in HDInsight usando un modello di Resource Manager](../hadoop/apache-hadoop-linux-tutorial-get-started.md)