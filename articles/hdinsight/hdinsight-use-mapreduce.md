---
title: MapReduce con Hadoop in HDInsight | Documentazione Microsoft
description: Informazioni su come eseguire processi MapReduce in un cluster Hadoop in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 136de1c5599fa3c66f90c32c2f0599b166197928
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Usare MapReduce in Hadoop su HDInsight

Informazioni su come eseguire i processi MapReduce nei cluster di HDInsight. Usare la tabella seguente per individuare i vari modi in cui MapReduce può essere usato con HDInsight:

| **Usare questo**... | **...per eseguire questa operazione** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:--- |:--- |:--- |:--- |
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) |Usare il comando Hadoop tramite **SSH** |Linux |Linux, Unix, Mac OS X o Windows |
| [REST](hdinsight-hadoop-use-mapreduce-curl.md) |Inviare il processo in remoto tramite **REST** (negli esempi viene usato cURL) |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) |Inviare il processo in remoto tramite **Windows PowerShell** |Linux o Windows |Windows |
| [Desktop remoto](hdinsight-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2 e 3.3) |Usare il comando Hadoop tramite **Desktop remoto** |Windows |Windows |

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a id="whatis"></a>Definizione di MapReduce

Hadoop MapReduce è un framework software per la scrittura di processi in grado di elaborare grandi quantità di dati. I dati di input sono suddivisi in blocchi indipendenti, che vengono successivamente elaborati in parallelo tra i nodi del cluster. Un processo MapReduce è costituito da due funzioni:

* **Mapper**: usa i dati di input, li analizza (in genere mediante operazioni di filtro e ordinamento) e quindi genera tuple (coppie chiave-valore)

* **Reducer**: usa le tuple generate dal Mapper ed esegue un'operazione di riepilogo che crea un risultato combinato più piccolo a partire dai dati del Mapper

La figura seguente illustra un esempio di processo di base di conteggio parole in MapReduce.

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

L'output del processo consentirà di conoscere il numero totale di occorrenze di ogni parola presente nel testo analizzato.

* Il mapper accetta come input ciascuna riga del testo di input e la suddivide in parole. Emette quindi una coppia chiave-valore ogni volta che viene riscontrata un'occorrenza della parola seguita da 1. L'output viene ordinato e inviato al reducer.
* Il reducer somma i singoli conteggi relativi a ciascuna parola ed emette una sola coppia chiave-valore contenente la parola seguita dal numero di occorrenze.

MapReduce può essere implementato in vari tipi di linguaggio. A scopo dimostrativo, in questo esempio si userà l'implementazione più comune: Java.

## <a name="development-languages"></a>Linguaggi di sviluppo

I linguaggi o i framework basati su Java e Java Virtual Machine possono essere eseguiti direttamente come processo MapReduce. Nell'esempio in questo documento viene usata un'applicazione Java MapReduce. I linguaggi diversi da Java, ad esempio C# o Python, o file eseguibili autonomi devono usare lo streaming di Hadoop.

Lo streaming di Hadoop comunica con il mapper e il riduttore tramite STDIN e STDOUT. Il mapper e il riduttore leggono i dati di una riga alla volta da STDIN e scrivono l'output in STDOUT. Ogni riga letta o generata dal mapper e dal riduttore deve essere in formato coppia chiave/valore, separata da un carattere di tabulazione:

    [key]/t[value]

Per altre informazioni, vedere l'argomento relativo a [Hadoop Streaming](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Per esempi di uso dello streaming di Hadoop con HDInsight, vedere i documenti seguenti:

* [Sviluppare processi C# MapReduce](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Sviluppare processi Python MapReduce](hdinsight-hadoop-streaming-python.md)

## <a id="data"></a>Dati di esempio

HDInsight offre diversi set di dati di esempio, archiviati nelle directory `/example/data` e `/HdiSamples`. Queste directory si trovano nella risorsa di archiviazione predefinita per il cluster. In questo documento, viene usato il file `/example/data/gutenberg/davinci.txt`. Questo file contiene i quaderni di Leonardo Da Vinci.

## <a id="job"></a>MapReduce di esempio

Un'applicazione di esempio per il conteggio parole di MapReduce è inclusa nel cluster HDInsight. Questo esempio si trova in `/example/jars/hadoop-mapreduce-examples.jar` nello spazio di archiviazione predefinito del cluster.

Il codice Java seguente è l'origine dell'applicazione MapReduce contenuta nel file `hadoop-mapreduce-examples.jar`:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Per istruzioni sulla scrittura di applicazioni MapReduce, vedere i seguenti documenti:

* [Sviluppare applicazioni Java MapReduce per HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Sviluppare applicazioni Python MapReduce per HDInsight](hdinsight-hadoop-streaming-python.md)

## <a id="run"></a>Eseguire il processo MapReduce

HDInsight è in grado di eseguire processi HiveQL in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo**... | **...per eseguire questa operazione** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:--- |:--- |:--- |:--- |
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) |Usare il comando Hadoop tramite **SSH** |Linux |Linux, Unix, Mac OS X o Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) |Inviare il processo in remoto tramite **REST** |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) |Inviare il processo in remoto tramite **Windows PowerShell** |Linux o Windows |Windows |
| [Desktop remoto](hdinsight-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2 e 3.3) |Usare il comando Hadoop tramite **Desktop remoto** |Windows |Windows |

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a id="nextsteps"></a>Passaggi successivi

Per altre informazioni su come usare i dati in HDInsight, vedere i documenti seguenti:

* [Sviluppare programmi MapReduce Java per HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Sviluppare programmi MapReduce per la creazione di flussi Python per HDInsight](hdinsight-hadoop-streaming-python.md)

* [Sviluppare processi MapReduce in Scalding con Apache Hadoop in HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Usare Hive con HDInsight][hdinsight-use-hive]

* [Usare Pig con HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

