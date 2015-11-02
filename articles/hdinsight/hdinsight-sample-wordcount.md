<properties
	pageTitle="Esempio MapReduce di Hadoop relativo al conteggio di parole in HDInsight | Microsoft Azure"
	description="Eseguire un esempio MapReduce relativo al conteggio di parole in un cluster Hadoop in HDInsight. Il programma, scritto in Java, conta le occorrenze delle parole in un file di testo."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="jgao"/>

#Eseguire il programma MapReduce per il conteggio delle parole nel cluster Hadoop in HDInsight

Informazioni su come eseguire un programma MapReduce su un cluster Hadoop in HDInsight tramite Azure PowerShell. Il programma viene scritto in Java, conta le occorrenze delle parole in un file di testo e fornisce come output un nuovo file di testo contenente ogni parola associata alla rispettiva frequenza.

Il programma è installato nel cluster. Il file di testo analizzato in questa esercitazione è l'edizione eBook del Project Gutenberg di The Notebooks of Leonardo Da Vinci.

> [AZURE.NOTE]I passaggi descritti in questo documento richiedono un client Windows. Per i passaggi per l'utilizzo dell'esempio di conteggio di parole da un client Unix, Linux o OS con un cluster HDInsight basato su Linux, vedere [Usare MapReduce con Hadoop in HDInsight con SSH](hdinsight-hadoop-use-mapreduce-ssh.md) o [Usare MapReduce con Hadoop in HDInsight con Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Altri articoli correlati:**

* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Sviluppare programmi MapReduce Java per Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Inviare processi Hadoop in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Esempio GraySort da 10 GB][hdinsight-sample-10gb-graysort]
* [Esempio di calcolo del Pi greco][hdinsight-sample-pi-estimator]
* [Esempio di flusso C#][hdinsight-sample-cs-streaming]

**Prerequisiti**:

- **Un cluster HDInsight**. Per istruzioni sui vari metodi di creazione di tali cluster, vedere [Introduzione all'uso di Azure HDInsight][hdinsight-get-started] o l'articolo [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate](hdinsight-provision-clusters.md).
- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Eseguire l'esempio tramite Azure PowerShell

**Per inviare il processo a MapReduce**

1. Aprire **Windows PowerShell ISE**. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].
2. Incollare lo script di PowerShell seguente:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. Impostare le prime 3 variabili, ed eseguire lo script.
		
**Per recuperare i risultati del processo MapReduce**

1. Aprire **Windows PowerShell ISE**. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].
2. Incollare lo script di PowerShell seguente:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	Il processo MapReduce produce un file denominato *part-r-00000*, che include le parole e i conteggi. Lo script usa il comando **findstr** per elencare tutte le parole contenenti *"there"*.

L'output dello script WordCount dovrebbe essere visualizzato nella finestra dei comandi:

![Risultati relativi alla frequenza delle parole in PowerShell dall'esempio del conteggio parole MapReduce di Hadoop in HDInsight.][image-hdi-sample-wordcount-output]

Si noti che i file di output di un processo MapReduce non sono modificabili. Se pertanto si esegue di nuovo l'esempio, è necessario cambiare il nome del file di output.

##Codice sorgente Java

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

## Passaggi successivi

* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Sviluppare programmi MapReduce Java per Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Inviare processi Hadoop in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Esempio GraySort da 10 GB][hdinsight-sample-10gb-graysort]
* [Esempio di calcolo del Pi greco][hdinsight-sample-pi-estimator]
* [Esempio di flusso C#][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->