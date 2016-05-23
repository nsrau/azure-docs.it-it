<properties
   pageTitle="MapReduce con Hadoop in HDInsight | Microsoft Azure"
   description="Informazioni su come eseguire processi MapReduce in un cluster Hadoop in HDInsight. Si eseguirà un'operazione di base di conteggio parole implementata come processo MapReduce Java."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/18/2016"
   ms.author="larryfr"/>

# Usare MapReduce in Hadoop su HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Questo articolo descrive come eseguire processi MapReduce in un cluster Hadoop in HDInsight. Si esegue un'operazione di base di conteggio parole implementata come processo MapReduce Java.

##<a id="whatis"></a>Che cos'è MapReduce?

Hadoop MapReduce è un framework software per la scrittura di processi in grado di elaborare grandi quantità di dati. I dati di input sono suddivisi in blocchi indipendenti, che vengono successivamente elaborati in parallelo tra i nodi del cluster. Un processo MapReduce è costituito da due funzioni:

* **Mapper**: usa i dati di input, li analizza (in genere mediante operazioni di filtro e ordinamento) e quindi genera tuple (coppie chiave-valore)
* **Reducer**: usa le tuple generate dal Mapper ed esegue un'operazione di riepilogo che crea un risultato combinato più piccolo a partire dai dati del Mapper

La figura seguente illustra un esempio di processo di base di conteggio parole in MapReduce.

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

L'output del processo consentirà di conoscere il numero totale di occorrenze di ogni parola presente nel testo analizzato.

* Il mapper accetta come input ciascuna riga del testo di input e la suddivide in parole. Emette quindi una coppia chiave-valore ogni volta che viene riscontrata un'occorrenza della parola seguita da 1. L'output viene ordinato e inviato al reducer.

* Il reducer somma i singoli conteggi relativi a ciascuna parola ed emette una sola coppia chiave-valore contenente la parola seguita dal numero di occorrenze.

MapReduce può essere implementato in vari tipi di linguaggio. A scopo dimostrativo, in questo esempio si userà l'implementazione più comune: Java.

### Hadoop Streaming

Mentre i linguaggi o i framework basati su Java e Java Virtual Machine (ad esempio, Scalding o Cascading) possono essere eseguiti direttamente come processo MapReduce, analogamente a un'applicazione Java, altri linguaggi, ad esempio C# o Python, o file eseguibili autonomi devono usare Hadoop Streaming.

Hadoop Streaming comunica con il mapper e il reducer tramite STDIN e STDOUT: il mapper e il reducer leggono i dati riga per riga da STDIN e scrivono l'output in STDOUT. Ogni riga letta o generata dal mapper e dal reducer deve essere in formato coppia chiave/valore, separate da un carattere di tabulazione.

    [key]/t[value]

Per altre informazioni, vedere l'argomento relativo a [Hadoop Streaming](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Per esempi di utilizzo di Hadoop Streaming con HDInsight, vedere gli argomenti seguenti:

* [Sviluppare processi Python MapReduce](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>Informazioni sui dati di esempio

In questo esempio si useranno i quaderni di Leonardo Da Vinci come dati di esempio, disponibili come documento di testo nel cluster HDInsight.

I dati di esempio vengono archiviati nell'archivio BLOB di Azure, usata da HDInsight come file system predefinito per i cluster Hadoop. HDInsight può accedere ai file nell'archivio BLOB tramite il prefisso **wasb**. Ad esempio, per accedere al file sample.log, usare la sintassi seguente:

	wasb:///example/data/gutenberg/davinci.txt

Poiché l'archivio BLOB di Azure è la risorsa di archiviazione predefinita per HDInsight, è anche possibile accedere al file usando **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] Nella sintassi precedente, ****wasb:///** consente di accedere ai file archiviati nel contenitore di archiviazione predefinito per il cluster HDInsight. Se durante il provisioning del cluster sono stati specificati account di archiviazione aggiuntivi e si desidera accedere ai file archiviati in tali account, è possibile accedere ai dati specificando il nome del contenitore e l'indirizzo dell'account di archiviazione. Ad esempio ****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>Informazioni sul processo MapReduce di esempio

Il processo MapReduce usato in questo esempio si trova in ****wasb://example/jars/hadoop-mapreduce-examples.jar** ed è disponibile nel cluster HDInsight. Contiene un esempio di conteggio parole e verrà eseguito su **davinci.txt**.

> [AZURE.NOTE] Nei cluster HDInsight 2.1 il percorso del file è ****wasb:///example/jars/hadoop-examples.jar**.

Di seguito viene fornito, come riferimento, il codice Java per il processo MapReduce di conteggio parole:

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

Per istruzioni sulla scrittura di un processo MapReduce personalizzato, vedere [Sviluppare programmi MapReduce Java per HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Eseguire il processo MapReduce

HDInsight è in grado di eseguire processi HiveQL in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo**... | **...per eseguire questa operazione** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) | Usare il comando Hadoop tramite **SSH** | Linux | Linux, Unix, Mac OS X o Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) | Inviare il processo in remoto tramite **REST** | Linux o Windows | Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Inviare il processo in remoto tramite **Windows PowerShell** | Linux o Windows | Windows |
| [Desktop remoto](hdinsight-hadoop-use-mapreduce-remote-desktop) | Usare il comando Hadoop tramite **Desktop remoto** | Windows | Windows |

##<a id="nextsteps"></a>Passaggi successivi

Pur includendo potenti funzionalità diagnostiche, MapReduce può presentare difficoltà di gestione. Esistono diversi framework basati su Java che semplificano la definizione di applicazioni MapReduce, nonché tecnologie come Pig e Hive, che consentono di gestire più facilmente i dati in HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Sviluppare programmi MapReduce Java per HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Sviluppare programmi MapReduce per la creazione di flussi Python per HDInsight](hdinsight-hadoop-streaming-python.md)

* [Sviluppare processi MapReduce in Scalding con Apache Hadoop in HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Usare Hive con HDInsight][hdinsight-use-hive]

* [Usare Pig con HDInsight][hdinsight-use-pig]

* [Eseguire esempi in HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

<!---HONumber=AcomDC_0511_2016-->