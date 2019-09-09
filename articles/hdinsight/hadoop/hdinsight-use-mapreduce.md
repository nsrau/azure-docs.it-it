---
title: MapReduce con Apache Hadoop in HDInsight
description: Informazioni su come eseguire i processi Apache MapReduce in Apache Hadoop nei cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 36413a4b7ba4dcb7e8e2af736a7dab6718f84799
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810490"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Usare MapReduce in Apache Hadoop in HDInsight

Informazioni su come eseguire i processi MapReduce nei cluster di HDInsight.

## <a id="data"></a>Dati di esempio

HDInsight offre diversi set di dati di esempio, archiviati nelle directory `/example/data` e `/HdiSamples`. Queste directory si trovano nella risorsa di archiviazione predefinita per il cluster. In questo documento, viene usato il file `/example/data/gutenberg/davinci.txt`. Questo file contiene i notebook di Leonardo da Vinci.

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

Per istruzioni sulla scrittura di applicazioni MapReduce personalizzate, vedere il documento seguente:

* [Sviluppare applicazioni Java MapReduce per HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>Eseguire il processo MapReduce

HDInsight è in grado di eseguire processi HiveQL in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo**... | **...per eseguire questa operazione** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Usare il comando Hadoop tramite **SSH** |Linux |Linux, Unix, Mac OS X o Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Inviare il processo in remoto tramite **REST** |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Inviare il processo in remoto tramite **Windows PowerShell** |Linux o Windows |Windows |

## <a id="nextsteps"></a>Passaggi successivi

Per altre informazioni su come usare i dati in HDInsight, vedere i documenti seguenti:

* [Sviluppare programmi MapReduce Java per HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Usare Apache Hive con HDInsight][hdinsight-use-hive]

* [Usare Pig con Hadoop in HDInsight][hdinsight-use-pig]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs
