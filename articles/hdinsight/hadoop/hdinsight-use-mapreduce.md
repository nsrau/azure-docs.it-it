---
title: MapReduce con Apache Hadoop in HDInsight
description: Informazioni su come eseguire i processi Apache MapReduce in Apache Hadoop nei cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 8b683f12a17d6aabbcdce3190a34675951567f64
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86075842"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Usare MapReduce in Apache Hadoop in HDInsight

Informazioni su come eseguire i processi MapReduce nei cluster di HDInsight.

## <a name="example-data"></a>Dati di esempio

HDInsight offre diversi set di dati di esempio, archiviati nelle directory `/example/data` e `/HdiSamples`. Queste directory si trovano nella risorsa di archiviazione predefinita per il cluster. In questo documento, viene usato il file `/example/data/gutenberg/davinci.txt`. Questo file contiene i notebook di Leonardo da Vinci.

## <a name="example-mapreduce"></a>MapReduce di esempio

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

Per istruzioni sulla scrittura di applicazioni MapReduce personalizzate, vedere [sviluppare applicazioni Java MapReduce per HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Eseguire il processo MapReduce

HDInsight è in grado di eseguire processi HiveQL in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questa**... | **... per eseguire questa operazione** |  ...da questo **sistema operativo client** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Usare il comando Hadoop tramite **SSH** |Linux, Unix, Mac OS X o Windows |
| [CURL](apache-hadoop-use-mapreduce-curl.md) |Inviare il processo in remoto tramite **REST** |Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Inviare il processo in remoto tramite **Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare i dati in HDInsight, vedere i documenti seguenti:

* [Sviluppare programmi MapReduce Java per HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Usare Apache Hive con HDInsight](./hdinsight-use-hive.md)
