<properties 
	pageTitle="Usare Hadoop MapReduce in HDInsight | Azure" 
	description="Informazioni su come usare HDInsight per eseguire un semplice processo Hadoop MapReduce." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>



# Usare Hadoop MapReduce in HDInsight

Hadoop MapReduce è un framework software per la scrittura di applicazioni in grado di elaborare quantità elevate di dati. In questa esercitazione verrà usato Azure PowerShell dalla workstation per inviare a un cluster HDInsight un programma MapReduce che conta le occorrenze delle parole di un testo. Il programma di conteggio delle parole è scritto in Java ed è incluso nel cluster HDInsight.


**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- Un cluster HDInsight. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Effettuare il provisioning di cluster HDInsight][hdinsight-provision].

- Una workstation in cui sia stato installato e configurato Azure PowerShell. Per le istruzioni, vedere la pagina relativa a [installazione e configurazione di Azure PowerShell][powershell-install-configure].

## Contenuto dell'esercitazione:
1. [Informazioni sullo scenario](#scenario)
2. [Eseguire l'esempio con Azure PowerShell](#run-sample)	
3. [Codice Java per il programma di conteggio delle parole MapReduce](#java-code)
4. [Passaggi successivi](#next-steps)	

## <a id="scenario"></a>Informazioni sullo scenario

Nel diagramma seguente viene illustrato il funzionamento di MapReduce per lo scenario di conteggio delle parole:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]



L'output del processo MapReduce è un insieme di coppie chiave-valore. La chiave è una stringa che specifica una parola, mentre il valore è un intero che specifica il numero totale di occorrenze di tale parola nel testo. Questa operazione viene effettuata in due fasi: 

* Il mapper accetta come input ciascuna riga del testo di input e la suddivide in parole. Emette quindi una coppia chiave-valore ogni volta che viene riscontrata un'occorrenza della parola seguita da 1. L'output verrà ordinato prima dell'invio al reducer. 

* Il reducer somma i singoli conteggi relativi a ciascuna parola ed emette una sola coppia chiave-valore contenente la parola seguita dal numero delle occorrenze.

Per eseguire un processo MapReduce è necessario disporre degli elementi seguenti:

* Un programma MapReduce. In questa esercitazione verrà usato il processo di esempio WordCount, disponibile nella distribuzione dei cluster HDInsight. Non sarà pertanto necessario scrivere un processo personalizzato. Il processo di esempio è disponibile in */example/jars/hadoop-examples.jar*. Nella versione 3.0 dei cluster HDInsight il nome del file è *hadoop-mapreduce-examples.jar*. Per istruzioni sulla scrittura di un processo MapReduce personalizzato, vedere [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-MapReduce-jobs].
* Un file di input. Come file di input verrà usato il file */example/data/gutenberg/davinci.txt*. Per informazioni sul caricamento di file, vedere [Caricare i dati in HDInsight][hdinsight-upload-data].
* Una cartella di file di output. Come cartella di file di output verrà usata la cartella */example/data/WordCountOutput*. Se non esiste, tale cartella verrà creata dal sistema. Se tale cartella esiste già, il processo MapReduce avrà esito negativo.  Se si desidera eseguire il processo MapReduce una seconda volta, assicurarsi di eliminare la cartella di output o di specificarne un'altra.

	
## <a id="run-sample"></a>Eseguire l'esempio con Azure PowerShell

1.	Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

3. Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
4. Eseguire il comando seguente e specificare le informazioni relative all'account Azure:

		Add-AzureAccount
		
5. Per creare una definizione del processo MapReduce, eseguire i comandi seguenti:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

	> [AZURE.NOTE] Il file *hadoop-examples.jar* è incluso nei cluster HDInsight della versione 2.1. Il file è stato rinominato in *hadoop-mapreduce.jar* nei cluster HDInsight della versione 3.0.
	
	Il file hadoop-examples.jar è incluso nella distribuzione del cluster HDInsight. Per il processo MapReduce sono disponibili due argomenti. Il primo argomento è il nome del file di origine, il secondo è il percorso del file di output. Il file di origine è incluso nella distribuzione del cluster HDInsight e il percorso del file di output verrà creato in fase di esecuzione.

6. Per inviare il processo MapReduce, eseguire il comando seguente:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Oltre alla definizione del processo MapReduce, è necessario specificare anche il nome del cluster HDInsight in cui si desidera eseguire il processo MapReduce e le credenziali. Start-AzureHDInsightJob è una chiamata non sincronizzata. Per verificare il completamento del processo, usare il cmdlet *Wait-AzureHDInsightJob*.

7. Per verificare il completamento del processo MapReduce, eseguire il comando seguente:

		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

8. Per verificare se si sono verificati errori nel processo MapReduce, eseguire il comando seguente:	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
		
**Per recuperare i risultati del processo MapReduce**

1. Aprire **Azure PowerShell**.
2. Eseguire il comando seguente per passare alla directory C:\ radice:

		cd \

	La directory predefinita di Azure Powershell è *C:\Windows\System32\WindowsPowerShell\v1.0*. Per impostazione predefinita, non si dispone di autorizzazioni di scrittura per tale cartella. È necessario cambiare la directory, specificando la directory C:\ radice o una cartella per cui si disponga di autorizzazioni di scrittura.

2. Impostare le tre variabili necessarie nei comandi seguenti, quindi eseguirli:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

		The Azure Storage account is the one you created earlier in the tutorial. The storage account is used to host the Blob container that is used as the default HDInsight cluster file system.  The Blob storage container name usually share the same name as the HDInsight cluster unless you specify a different name when you provision the cluster.

3. Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti:
		
		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Il comando *Select-AzureSubscription* consente di impostare la sottoscrizione corrente, nel caso in cui siano disponibili più sottoscrizioni e non si desideri usare la sottoscrizione predefinita. 

4. Per scaricare l'output del processo MapReduce dal contenitore BLOB alla workstation, eseguire il comando seguente:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	La cartella */example/data/WordCountOutput* è la cartella di output specificata durante l'esecuzione del processo MapReduce. Il valore *part-r-00000* corrisponde al nome file predefinito per l'output del processo MapReduce.  Il file verrà scaricato nella stessa struttura di cartelle nella cartella locale. Ad esempio, nella schermata seguente la cartella corrente è la cartella radice C.  Il file verrà scaricato nella cartella *C:\example\data\WordCountOutput\*. 

5. Per stampare il file di output del processo MapReduce, eseguire il comando seguente:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	Il processo MapReduce consente di ottenere un file denominato *part-r-00000*, che include le parole e i conteggi.  Lo script usa il comando findstr per elencare tutte le parole contenenti *there*.


Si noti che i file di output di un processo MapReduce non sono modificabili. Se pertanto si esegue di nuovo l'esempio, sarà necessario cambiare il nome del file di output.

## <a id="java-code"></a>Codice Java per il programma di conteggio delle parole MapReduce

Di seguito è riportato il codice sorgente del programma di conteggio delle parole MapReduce Java:
 
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
 


## <a id="nextsteps"></a>Passaggi successivi
Pur includendo potenti funzionalità diagnostiche, MapReduce può presentare difficoltà di gestione. Altri linguaggi, come Pig e Hive, costituiscono una soluzione più agevole per gestire i dati archiviati in HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-MapReduce-jobs]
* [Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight][hdinsight-develop-streaming]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig] 
* [Eseguire gli esempi relativi a HDInsight][hdinsight-samples]


[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-MapReduce-jobs]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[powershell-install-configure]: ../install-and-configure-powershell/

[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif





<!--HONumber=42-->
