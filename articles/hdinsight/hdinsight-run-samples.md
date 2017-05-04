---
title: Eseguire gli esempi di Hadoop in HDInsight | Documentazione Microsoft
description: Informazioni su come iniziare a usare il servizio Azure HDInsight con gli esempi forniti. Usare script PowerShell che eseguono programmi MapReduce su cluster di dati.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bf76d452-abb4-4210-87bd-a2067778c6ed
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: e4042dfbf28e78d2fca5c3f6a93df751a12153f2
ms.lasthandoff: 04/27/2017


---
# <a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Eseguire esempi di Hadoop MapReduce in HDInsight basato su Windows
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Sono disponibili alcuni esempi utili per iniziare a eseguire processi MapReduce nei cluster Hadoop usando Azure HDInsight. Questi esempi sono disponibili in ogni cluster creato e gestito da HDInsight. L'esecuzione di questi esempi permette di acquisire familiarità con l'uso dei cmdlet di Azure PowerShell per l'esecuzione di processi nei cluster Hadoop.

* [**Conteggio delle parole**][hdinsight-sample-wordcount]: conta le occorrenze delle parole in un file di testo.
* [**Conteggio delle parole di un flusso in C#**][hdinsight-sample-csharp-streaming]: conta le occorrenze delle parole in un file di testo usando l'interfaccia di flusso Hadoop.
* [**Calcolo del Pi greco**][hdinsight-sample-pi-estimator]: per calcolare il valore del Pi greco viene usato un metodo statistico simile al metodo Monte Carlo.
* [**Graysort da 10 GB**][hdinsight-sample-10gb-graysort]: esegue un ordinamento GraySort per utilizzo generico in un file da 10 GB usando HDInsight. I processi da eseguire sono tre: Teragen per generare i dati, Terasort per ordinare i dati e Teravalidate per verificare che i dati siano stati ordinati correttamente.

> [!NOTE]
> Il codice sorgente è disponibile nell'appendice.

Sul Web sono disponibili molti altri documenti per le tecnologie relative a Hadoop, ad esempio il flusso e la programmazione MapReduce basati su Java, oltre alla documentazione sui cmdlet usati negli script di Windows PowerShell. Per altre informazioni su queste risorse, vedere:

* [Sviluppare programmi MapReduce Java per Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
* [Inviare processi Hadoop in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introduzione ad Azure HDInsight][hdinsight-introduction]

Molte persone preferiscono oggi Hive e Pig rispetto a MapReduce.  Per altre informazioni, vedere:

* [Usare Hive in HDInsight](hdinsight-use-hive.md)
* [Usare Pig in HDInsight](hdinsight-use-pig.md)

**Prerequisiti**:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un cluster HDInsight**. Per informazioni sui vari metodi disponibili per la creazione dei cluster, vedere [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* **Workstation con Azure PowerShell**.

    > [!IMPORTANT]
    > Il supporto di Azure PowerShell per la gestione delle risorse HDInsight tramite Azure Service Manager è **deprecato** e verrà rimosso dal 1° gennaio 2017. La procedura descritta in questo documento usa i nuovi cmdlet HDInsight, compatibili con Azure Resource Manager.
    >
    > Per installare la versione più recente, seguire la procedura descritta in [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs). Se sono presenti script che devono essere modificati per l'uso dei nuovi cmdlet compatibili con Azure Resource Manager, vedere [Migrazione a strumenti di sviluppo basati su Azure Resource Manager per i cluster HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="hdinsight-sample-wordcount"></a>Conteggio delle parole: Java
Per inviare un progetto MapReduce, è necessario prima creare una definizione del processo MapReduce. Nella definizione del processo, specificare il file con estensione jar del programma MapReduce e il relativo percorso, ovvero **wasbs:///example/jars/hadoop-mapreduce-examples.jar**, nonché il nome della classe e gli argomenti.  Il programma MapReduce per il conteggio delle parole accetta due argomenti: il file di origine usato per contare le parole e il percorso di output.

Il codice sorgente è disponibile nell' [Appendice A](#apendix-a---the-word-count-MapReduce-program-in-java).

Per la procedura di sviluppo di un programma MapReduce Java, vedere [Sviluppare programmi MapReduce Java per Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

**Per inviare un processo MapReduce per il conteggio delle parole**

1. Aprire **Windows PowerShell ISE**. Per istruzioni, vedere [Installare e configurare Azure PowerShell][powershell-install-configure].
2. Incollare lo script di PowerShell seguente:

    ```powershell
    $subscriptionName = "<Azure Subscription Name>"
    $resourceGroupName = "<Resource Group Name>"
    $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

    Select-AzureRmSubscription -SubscriptionName $subscriptionName

    # Define the MapReduce job
    $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "wordcount" `
                                -Arguments "wasbs:///example/data/gutenberg/davinci.txt", "wasbs:///example/data/WordCountOutput"

    # Submit the job and wait for job completion
    $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:"
    $mrJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $clusterName `
                        -HttpCredential $cred `
                        -JobDefinition $mrJobDefinition

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -JobId $mrJob.JobId

    # Get the job output
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -DefaultStorageAccountName $defaultStorageAccount `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultStorageContainer  `
        -JobId $mrJob.JobId `
        -DisplayOutputType StandardError

    # Download the job output to the workstation
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output file
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    ```

    Il processo MapReduce produce un file denominato *part-r-00000*, che include le parole e i conteggi. Lo script usa il comando **findstr** per elencare tutte le parole contenenti *"there"*.
3. Impostare le prime tre variabili, quindi eseguire lo script.

## <a name="hdinsight-sample-csharp-streaming"></a>Conteggio delle parole: flusso in C#
In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java.

> [!NOTE]
> La procedura descritta in questa esercitazione è valida solo per i cluster HDInsight basati su Windows. Per un esempio di flusso per cluster HDInsight basati su Linux, vedere [Sviluppare programmi di streaming Python per HDInsight](hdinsight-hadoop-streaming-python.md).

Nell'esempio il mapper e il reducer sono file eseguibili che leggono l'input da [stdin][stdin-stdout-stderr] (riga per riga) e inviano l'output a [stdout][stdin-stdout-stderr]. Il programma consente di contare tutte le parole del testo.

Quando si specifica un file eseguibile per **mapper**, ogni attività del mapper avvia il file eseguibile come processo distinto all'avvio del mapper. Durante l'esecuzione dell'attività del mapper, gli input vengono convertiti in righe che vengono quindi inviate al flusso [stdin][stdin-stdout-stderr] del processo.

Nel frattempo il mapper raccoglie gli output basati su riga dal flusso stdout del processo e converte ogni riga in una coppia chiave-valore che viene raccolta come output del mapper. Per impostazione predefinita, il prefisso di una riga fino al primo carattere di tabulazione costituisce la chiave, mentre la parte restante (escluso il carattere di tabulazione) costituisce il valore. Se la riga non contiene caratteri di tabulazione, come chiave viene considerata l'intera riga e il valore è Null.

Quando si specifica un file eseguibile per **reducer**, ogni attività del reducer avvia il file eseguibile come processo distinto all'avvio del reducer. Durante l'esecuzione dell'attività del reducer, le coppie chiave-valore di input vengono convertite in righe che vengono quindi inviate al flusso [stdin][stdin-stdout-stderr] del processo.

Nel frattempo il reducer raccoglie gli output basati su riga dal flusso [stdout][stdin-stdout-stderr] del processo e converte ogni riga in una coppia chiave-valore che viene raccolta come output del reducer. Per impostazione predefinita, il prefisso di una riga fino al primo carattere di tabulazione costituisce la chiave, mentre la parte restante (escluso il carattere di tabulazione) costituisce il valore.

**Per inviare un processo di conteggio delle parole di un flusso in C#**

* Seguire la procedura descritta in [Conteggio delle parole: Java](#word-count-java) sostituendo la definizione del processo con la riga seguente:

    ```powershell
    $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                            -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                            -Mapper "cat.exe" `
                            -Reducer "wc.exe" `
                            -InputPath "/example/data/gutenberg/davinci.txt" `
                            -OutputPath "/example/data/StreamingOutput/wc.txt"
    ```

    Il file di output avrà l'aspetto seguente:

        example/data/StreamingOutput/wc.txt/part-00000

## <a name="hdinsight-sample-pi-estimator"></a>Calcolo del Pi greco
Per calcolare il valore del Pi greco viene usato un metodo statistico simile al metodo Monte Carlo. I punti posizionati in modo casuale all'interno di un quadrato unitario rientrano anche in un cerchio incluso in tale quadrato con una probabilità uguale all'area del cerchio, Pi greco/4. Il valore di Pi greco può essere stimato in base a valore di 4R, dove R indica il rapporto tra il numero di punti che si trovano all’interno del cerchio e il numero totale di punti che si trovano all’interno del quadrato. La precisione del calcolo è direttamente proporzionale al numero di punti utilizzati.

Lo script fornito per questo esempio invia un processo jar Hadoop ed è configurato per l'esecuzione con un valore pari a 16 mappe, ognuna delle quali è necessaria per il calcolo di 10 milioni di punti campione da parte dei valori del parametro. Tali valori di parametro possono essere modificati per ottimizzare il valore stimato di Pi greco. A scopo di riferimento, si noti che le prime 10 cifre decimali di Pi greco sono 3,1415926535.

**Per inviare un processo di calcolo del Pi greco**

* Seguire la procedura descritta in [Conteggio delle parole: Java](#word-count-java) sostituendo la definizione del processo con la riga seguente:

    ```powershell
    $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "pi" `
                                -Arguments "16", "10000000"
    ```

## <a name="hdinsight-sample-10gb-graysort"></a>Graysort da 10 GB
In questo esempio vengono usati solo 10 GB di dati, in modo da consentire un'esecuzione relativamente rapida. Vengono usate le applicazioni MapReduce sviluppate da Owen O'Malley e Arun Murthy, vincitrici del benchmark annuale di ordinamento generico di terabyte ("daytona") nel 2009 con una velocità pari a 0,578 TB/min (100 TB in 173 minuti). Per ulteriori informazioni su questo e su altri benchmark di ordinamento, vedere il sito [Sortbenchmark](http://sortbenchmark.org/) .

In questo esempio vengono utilizzati tre set di programmi MapReduce:

1. **TeraGen** è un programma MapReduce utilizzabile per generare le righe di dati da ordinare.
2. **TeraSort**esegue il campionamento dei dati di input e usa MapReduce per organizzare i dati in un ordine totale. TeraSort è un ordinamento standard di funzioni MapReduce, ad eccezione di un partitioner personalizzato che usa un elenco ordinato di N-1 chiavi sottoposte a campionamento che definiscono l'intervallo di chiavi per ogni riduzione. In particolare, tutte le chiavi corrispondenti al criterio sample[i-1]<= chiave < sample[i] vengono inviate alla funzione reduce i. Ciò garantisce che tutti gli output di reduce i siano inferiori all'output della funzione reduce i+1.
3. **TeraValidate** è un programma MapReduce che convalida l'ordinamento globale dell'output. Crea una funzione map per ogni file nella directory di output e ogni funzione map assicura che ogni chiave sia inferiore o uguale alla precedente. La funzione map genera inoltre record della prima e dell'ultima chiave di ogni file, mentre la funzione reduce assicura che la prima chiave del file sia superiore all'ultima chiave di file i-1. Eventuali problemi vengono segnalati come output della funzione reduce insieme alle chiavi che non rispettano l'ordinamento.

Il formato di input e il formato di output, usati da tutte e tre le applicazioni, consentono di leggere e scrivere i file di testo nel formato corretto. Nell'output della funzione reduce la replica è impostata su 1, anziché sul valore predefinito 3, poiché il contesto del benchmark non richiede la replica dei dati di output in più nodi.

L'esempio richiede tre attività, ognuna delle quali corrisponde a uno dei programmi MapReduce illustrati nell'introduzione:

1. Generazione dei dati per l'ordinamento tramite l'esecuzione del processo MapReduce **TeraGen** .
2. Ordinamento dei dati tramite l'esecuzione del processo MapReduce **TeraSort** .
3. Conferma del corretto ordinamento dei dati tramite l'esecuzione del processo MapReduce **TeraValidate** .

**Per inviare i processi**

* Seguire la procedura descritta in [Conteggio delle parole: Java](#word-count-java) usando le definizioni di processo seguenti:

    ```powershell
    $teragen = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teragen" `
                                -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

    $terasort = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "terasort" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

    $teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teravalidate" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"
    ```

## <a name="next-steps"></a>Passaggi successivi
In questo articolo e negli articoli relativi ai singoli esempi, si è appreso come eseguire gli esempi inclusi nei cluster HDInsight usando Azure PowerShell. Per le esercitazioni sull'uso di Pig, Hive e MapReduce con HDInsight, vedere gli argomenti seguenti:

* [Introduzione all'uso di Hadoop con Hive in HDInsight per analizzare l'uso di telefoni cellulari][hdinsight-get-started]
* [Usare Pig con Hadoop in HDInsight][hdinsight-use-pig]
* [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive]
* [Inviare processi Hadoop in HDInsight][hdinsight-submit-jobs]
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation]
* [Eseguire il debug di Hadoop in HDInsight: messaggi di errore][hdinsight-errors]

## <a name="appendix-a---the-word-count-source-code"></a>Appendice A: Codice sorgente per il conteggio delle parole

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

## <a name="appendix-b---the-word-count-streaming-source-code"></a>Appendice B - Codice sorgente per il conteggio delle parole di un flusso
Nel programma MapReduce vengono usate l'applicazione cat.exe come interfaccia di mapping per trasmettere il testo nella console e l'applicazione wc.exe come interfaccia di riduzione per contare il numero di parole trasmesse da un documento. Sia il mapper che il reducer leggono i caratteri, riga per riga, dal flusso di input standard (stdin) e scrivono nel flusso di output standard (stdout).

```csharp
// The source code for the cat.exe (Mapper).

using System;
using System.IO;

namespace cat
{
    class cat
    {
        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            char[] separators = { ' ', '\n'};
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split(separators);
                foreach (var word in words)
                {
                    Console.WriteLine("{0}\t1", word);
                }
            }
        }
    }
}
```

Il codice del mapper nel file cat.cs usa un oggetto [StreamReader][streamreader] per leggere i caratteri del flusso in ingresso nella console. A sua volta, tale oggetto scrive il flusso nel flusso di output standard con il metodo statico [Console.Writeline][console-writeline].

```csharp
// The source code for wc.exe (Reducer) is:

using System;
using System.IO;
using System.Linq;
using System.Collections;

namespace wc
{
    class wc
    {
        static void Main(string[] args)
        {
            string line;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            Hashtable wordCount = new Hashtable();
            while ((line = Console.ReadLine()) != null)
            {
                string[] words = line.Split('\t');

                string key = words[0];

                if (wordCount.ContainsKey(key) == true)
                {
                    int n = Convert.ToInt32(wordCount[key]);
                    wordCount[key] = Convert.ToString(n + 1);
                }
                else
                {
                    wordCount[key] = words[1];
                }
            }
            foreach (var key in wordCount.Keys)
            {
                Console.WriteLine("{0} {1}", key, wordCount[key]);
            }
        }
    }
}
```

Il codice del reducer nel file wc.cs usa un oggetto [StreamReader][streamreader] per leggere dal flusso di input standard i caratteri che sono stati trasmessi dal mapper cat.exe. Durante la lettura dei caratteri con il metodo [Console.Writeline][console-writeline] viene eseguito il conteggio delle parole, inclusi gli spazi e i caratteri di fine riga alla fine di ogni parola. Il totale viene quindi scritto nel flusso di output standard con il metodo [Console.Writeline][console-writeline].

## <a name="appendix-c---the-pi-estimator-source-code"></a>Appendice C: Codice sorgente per il calcolo del Pi greco
Il codice Java Pi Estimator, contenente le funzioni per il mapping e per la riduzione, è disponibile di seguito per l'analisi. Il programma per il mapping consente di generare un numero specifico di punti posizionati in modo casuale all'interno di un quadrato unitario e quindi di contare il numero di tali punti inclusi all'interno del cerchio. Il programma per la riduzione consente di accumulare i punti contati dalle funzioni di mapping e quindi di calcolare il valore di Pi greco in base alla formula 4R, dove R indica il rapporto tra il numero di punti che si trovano all’interno del cerchio e il numero totale di punti che si trovano all’interno del quadrato.

```java
/**
* Licensed to the Apache Software Foundation (ASF) under one
* or more contributor license agreements. See the NOTICE file
* distributed with this work for additional information
* regarding copyright ownership. The ASF licenses this file
* to you under the Apache License, Version 2.0 (the
* "License"); you may not use this file except in compliance
* with the License. You may obtain a copy of the License at
*
* http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or     implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package org.apache.hadoop.examples;

import java.io.IOException;
import java.math.BigDecimal;
import java.util.Iterator;

import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BooleanWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.io.WritableComparable;
import org.apache.hadoop.io.SequenceFile.CompressionType;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.SequenceFileInputFormat;
import org.apache.hadoop.mapred.SequenceFileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

//A Map-reduce program to estimate the value of Pi
//using quasi-Monte Carlo method.
//
//Mapper:
//Generate points in a unit square
//and then count points inside/outside of the inscribed circle of the square.
//
//Reducer:
//Accumulate points inside/outside results from the mappers.
//Let numTotal = numInside + numOutside.
//The fraction numInside/numTotal is a rational approximation of
//the value (Area of the circle)/(Area of the square),
//where the area of the inscribed circle is Pi/4
//and the area of unit square is 1.
//Then, Pi is estimated value to be 4(numInside/numTotal).
//

public class PiEstimator extends Configured implements Tool {
//tmp directory for input/output
static private final Path TMP_DIR = new Path(
PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

//2-dimensional Halton sequence {H(i)},
//where H(i) is a 2-dimensional point and i >= 1 is the index.
//Halton sequence is used to generate sample points for Pi estimation.
private static class HaltonSequence {
// Bases
static final int[] P = {2, 3};
//Maximum number of digits allowed
static final int[] K = {63, 40};

private long index;
private double[] x;
private double[][] q;
private int[][] d;

//Initialize to H(startindex),
//so the sequence begins with H(startindex+1).
HaltonSequence(long startindex) {
index = startindex;
x = new double[K.length];
q = new double[K.length][];
d = new int[K.length][];
for(int i = 0; i < K.length; i++) {
q[i] = new double[K[i]];
d[i] = new int[K[i]];
}

for(int i = 0; i < K.length; i++) {
long k = index;
x[i] = 0;

for(int j = 0; j < K[i]; j++) {
q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
d[i][j] = (int)(k % P[i]);
k = (k - d[i][j])/P[i];
x[i] += d[i][j] * q[i][j];
}
}
}

//Compute next point.
//Assume the current point is H(index).
//Compute H(index+1).
//@return a 2-dimensional point with coordinates in [0,1)^2
double[] nextPoint() {
index++;
for(int i = 0; i < K.length; i++) {
for(int j = 0; j < K[i]; j++) {
d[i][j]++;
x[i] += q[i][j];
if (d[i][j] < P[i]) {
break;
}
d[i][j] = 0;
x[i] -= (j == 0? 1.0: q[i][j-1]);
}
}
return x;
}
}

//Mapper class for Pi estimation.
//Generate points in a unit square and then
//count points inside/outside of the inscribed circle of the square.
public static class PiMapper extends MapReduceBase
implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

//Map method.
//@param offset samples starting from the (offset+1)th sample.
//@param size the number of samples for this map
//@param out output {ture->numInside, false->numOutside}
//@param reporter
public void map(LongWritable offset,
LongWritable size,
OutputCollector<BooleanWritable, LongWritable> out,
Reporter reporter) throws IOException {

final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
long numInside = 0L;
long numOutside = 0L;

for(long i = 0; i < size.get(); ) {
//generate points in a unit square
final double[] point = haltonsequence.nextPoint();

//count points inside/outside of the inscribed circle of the square
final double x = point[0] - 0.5;
final double y = point[1] - 0.5;
if (x*x + y*y > 0.25) {
numOutside++;
} else {
numInside++;
}

//report status
i++;
if (i % 1000 == 0) {
reporter.setStatus("Generated " + i + " samples.");
}
}

//output map results
out.collect(new BooleanWritable(true), new LongWritable(numInside));
out.collect(new BooleanWritable(false), new LongWritable(numOutside));
}
}

//Reducer class for Pi estimation.
//Accumulate points inside/outside results from the mappers.
public static class PiReducer extends MapReduceBase
implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

private long numInside = 0;
private long numOutside = 0;
private JobConf conf; //configuration for accessing the file system

//Store job configuration.
@Override
public void configure(JobConf job) {
conf = job;
}

// Accumulate number of points inside/outside results from the mappers.
// @param isInside Is the points inside?
// @param values An iterator to a list of point counts
// @param output dummy, not used here.
// @param reporter

public void reduce(BooleanWritable isInside,
Iterator<LongWritable> values,
OutputCollector<WritableComparable<?>, Writable> output,
Reporter reporter) throws IOException {
if (isInside.get()) {
for(; values.hasNext(); numInside += values.next().get());
} else {
for(; values.hasNext(); numOutside += values.next().get());
}
}

//Reduce task done, write output to a file.
@Override
public void close() throws IOException {
//write output to a file
Path outDir = new Path(TMP_DIR, "out");
Path outFile = new Path(outDir, "reduce-out");
FileSystem fileSys = FileSystem.get(conf);
SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
outFile, LongWritable.class, LongWritable.class,
CompressionType.NONE);
writer.append(new LongWritable(numInside), new LongWritable(numOutside));
writer.close();
}
}

//Run a map/reduce job for estimating Pi.
//@return the estimated value of Pi.
public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
)
throws IOException {
//setup job conf
jobConf.setJobName(PiEstimator.class.getSimpleName());

jobConf.setInputFormat(SequenceFileInputFormat.class);

jobConf.setOutputKeyClass(BooleanWritable.class);
jobConf.setOutputValueClass(LongWritable.class);
jobConf.setOutputFormat(SequenceFileOutputFormat.class);

jobConf.setMapperClass(PiMapper.class);
jobConf.setNumMapTasks(numMaps);

jobConf.setReducerClass(PiReducer.class);
jobConf.setNumReduceTasks(1);

// turn off speculative execution, because DFS doesn't handle
// multiple writers to the same file.
jobConf.setSpeculativeExecution(false);

//setup input/output directories
final Path inDir = new Path(TMP_DIR, "in");
final Path outDir = new Path(TMP_DIR, "out");
FileInputFormat.setInputPaths(jobConf, inDir);
FileOutputFormat.setOutputPath(jobConf, outDir);

final FileSystem fs = FileSystem.get(jobConf);
if (fs.exists(TMP_DIR)) {
throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
+ " already exists. Remove it first.");
}
if (!fs.mkdirs(inDir)) {
throw new IOException("Cannot create input directory " + inDir);
}

//generate an input file for each map task
try {
for(int i=0; i < numMaps; ++i) {
final Path file = new Path(inDir, "part"+i);
final LongWritable offset = new LongWritable(i * numPoints);
final LongWritable size = new LongWritable(numPoints);
final SequenceFile.Writer writer = SequenceFile.createWriter(
fs, jobConf, file,
LongWritable.class, LongWritable.class, CompressionType.NONE);
try {
writer.append(offset, size);
} finally {
writer.close();
}
System.out.println("Wrote input for Map #"+i);
}

//start a map/reduce job
System.out.println("Starting Job");
final long startTime = System.currentTimeMillis();
JobClient.runJob(jobConf);
final double duration = (System.currentTimeMillis() - startTime)/1000.0;
System.out.println("Job Finished in " + duration + " seconds");

//read outputs
Path inFile = new Path(outDir, "reduce-out");
LongWritable numInside = new LongWritable();
LongWritable numOutside = new LongWritable();
SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
try {
reader.next(numInside, numOutside);
} finally {
reader.close();
}

//compute estimated value
return BigDecimal.valueOf(4).setScale(20)
.multiply(BigDecimal.valueOf(numInside.get()))
.divide(BigDecimal.valueOf(numMaps))
.divide(BigDecimal.valueOf(numPoints));
} finally {
fs.delete(TMP_DIR, true);
}
}

//Parse arguments and then runs a map/reduce job.
//Print output in standard out.
//@return a non-zero if there is an error. Otherwise, return 0.
public int run(String[] args) throws Exception {
if (args.length != 2) {
System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
ToolRunner.printGenericCommandUsage(System.err);
return -1;
}

final int nMaps = Integer.parseInt(args[0]);
final long nSamples = Long.parseLong(args[1]);

System.out.println("Number of Maps = " + nMaps);
System.out.println("Samples per Map = " + nSamples);

final JobConf jobConf = new JobConf(getConf(), getClass());
System.out.println("Estimated value of Pi is "
+ estimate(nMaps, nSamples, jobConf));
return 0;
}

//main method for running it as a stand alone command.
public static void main(String[] argv) throws Exception {
System.exit(ToolRunner.run(null, new PiEstimator(), argv));
}
}
```

## <a name="appendix-d---the-10gb-graysort-source-code"></a>Appendice D - Codice sorgente per il Graysort da 10 GB
Il codice per il programma MapReduce TeraSort viene presentato per la verifica in questa sezione.

```java
/**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements.  See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership.  The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License.  You may obtain a copy of the License at
    *
    *     http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

package org.apache.hadoop.examples.terasort;

import java.io.IOException;
import java.io.PrintStream;
import java.net.URI;
import java.util.ArrayList;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.filecache.DistributedCache;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.Partitioner;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
    * Generates the sampled split points, launches the job,
    * and waits for it to finish.
    * <p>
    * To run the program:
    * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
    */

public class TeraSort extends Configured implements Tool {
    private static final Log LOG = LogFactory.getLog(TeraSort.class);

    /**
    * A partitioner that splits text keys into roughly equal
    * partitions in a global sorted order.
    */

    static class TotalOrderPartitioner implements Partitioner<Text,Text>{
    private TrieNode trie;
    private Text[] splitPoints;

    /**
        * A generic trie node
        */
    static abstract class TrieNode {
        private int level;
        TrieNode(int level) {
        this.level = level;
        }
        abstract int findPartition(Text key);
        abstract void print(PrintStream strm) throws IOException;
        int getLevel() {
        return level;
        }
    }

    /**
        * An inner trie node that contains 256 children based on the next
        * character.
        */
    static class InnerTrieNode extends TrieNode {
        private TrieNode[] child = new TrieNode[256];

        InnerTrieNode(int level) {
        super(level);
        }
        int findPartition(Text key) {
        int level = getLevel();
        if (key.getLength() <= level) {
            return child[0].findPartition(key);
        }
        return child[key.getBytes()[level]].findPartition(key);
        }
        void setChild(int idx, TrieNode child) {
        this.child[idx] = child;
        }
        void print(PrintStream strm) throws IOException {
        for(int ch=0; ch < 255; ++ch) {
            for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
            }
            strm.print(ch);
            strm.println(" ->");
            if (child[ch] != null) {
            child[ch].print(strm);
            }
        }
        }
    }

    /**
        * A leaf trie node that does string compares to figure out where the given
        * key belongs between lower..upper.
        */
    static class LeafTrieNode extends TrieNode {
        int lower;
        int upper;
        Text[] splitPoints;
        LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
        super(level);
        this.splitPoints = splitPoints;
        this.lower = lower;
        this.upper = upper;
        }
        int findPartition(Text key) {
        for(int i=lower; i<upper; ++i) {
            if (splitPoints[i].compareTo(key) >= 0) {
            return i;
            }
        }
        return upper;
        }
        void print(PrintStream strm) throws IOException {
        for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
        }
        strm.print(lower);
        strm.print(", ");
        strm.println(upper);
        }
    }

    /**
        * Read the cut points from the given sequence file.
        * @param fs the file system
        * @param p the path to read
        * @param job the job config
        * @return the strings to split the partitions on
        * @throws IOException
        */
    private static Text[] readPartitions(FileSystem fs, Path p,
                                            JobConf job) throws IOException {
        SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
        List<Text> parts = new ArrayList<Text>();
        Text key = new Text();
        NullWritable value = NullWritable.get();
        while (reader.next(key, value)) {
        parts.add(key);
        key = new Text();
        }
        reader.close();
        return parts.toArray(new Text[parts.size()]);
    }

    /**
        * Given a sorted set of cut points, build a trie that will find the correct
        * partition quickly.
        * @param splits the list of cut points
        * @param lower the lower bound of partitions 0..numPartitions-1
        * @param upper the upper bound of partitions 0..numPartitions-1
        * @param prefix the prefix that we have already checked against
        * @param maxDepth the maximum depth we will build a trie for
        * @return the trie node that will divide the splits correctly
        */
    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                        Text prefix, int maxDepth) {
        int depth = prefix.getLength();
        if (depth >= maxDepth || lower == upper) {
        return new LeafTrieNode(depth, splits, lower, upper);
        }
        InnerTrieNode result = new InnerTrieNode(depth);
        Text trial = new Text(prefix);
        // append an extra byte on to the prefix
        trial.append(new byte[1], 0, 1);
        int currentBound = lower;
        for(int ch = 0; ch < 255; ++ch) {
        trial.getBytes()[depth] = (byte) (ch + 1);
        lower = currentBound;
        while (currentBound < upper) {
            if (splits[currentBound].compareTo(trial) >= 0) {
            break;
            }
            currentBound += 1;
        }
        trial.getBytes()[depth] = (byte) ch;
        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                        maxDepth);
        }
        // pick up the rest
        trial.getBytes()[depth] = 127;
        result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                    maxDepth);
        return result;
    }

    public void configure(JobConf job) {
        try {
        FileSystem fs = FileSystem.getLocal(job);
        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
        splitPoints = readPartitions(fs, partFile, job);
        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
        } catch (IOException ie) {
        throw new IllegalArgumentException("can't read paritions file", ie);
        }
    }

    public TotalOrderPartitioner() {
    }

    public int getPartition(Text key, Text value, int numPartitions) {
        return trie.findPartition(key);
    }

    }

    public int run(String[] args) throws Exception {
    LOG.info("starting");
    JobConf job = (JobConf) getConf();
    Path inputDir = new Path(args[0]);
    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
    URI partitionUri = new URI(partitionFile.toString() +
                                "#" + TeraInputFormat.PARTITION_FILENAME);
    TeraInputFormat.setInputPaths(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setJobName("TeraSort");
    job.setJarByClass(TeraSort.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(Text.class);
    job.setInputFormat(TeraInputFormat.class);
    job.setOutputFormat(TeraOutputFormat.class);
    job.setPartitionerClass(TotalOrderPartitioner.class);
    TeraInputFormat.writePartitionFile(job, partitionFile);
    DistributedCache.addCacheFile(partitionUri, job);
    DistributedCache.createSymlink(job);
    job.setInt("dfs.replication", 1);
    TeraOutputFormat.setFinalSync(job, true);
    JobClient.runJob(job);
    LOG.info("done");
    return 0;
    }

    /**
    * @param args
    */

    public static void main(String[] args) throws Exception {
    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
    System.exit(res);
    }
}
```

[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx

