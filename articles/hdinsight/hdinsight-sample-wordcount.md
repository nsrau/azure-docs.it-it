<properties
	pageTitle="Esempio MapReduce di Hadoop relativo al conteggio di parole in HDInsight | Microsoft Azure"
	description="Eseguire un esempio MapReduce relativo al conteggio di parole in un cluster Hadoop in HDInsight. Il programma, scritto in Java, conta le occorrenze delle parole in un file di testo."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2015" 
	ms.author="bradsev"/>

#Eseguire un esempio MapReduce relativo al conteggio di parole scritto in Java in un cluster Hadoop in HDInsight

Questa esercitazione mostra come eseguire un esempio MapReduce relativo al conteggio di parole in un cluster Hadoop in HDInsight. Il programma, scritto in Java, conta le occorrenze delle parole in un file di testo e fornisce come output un nuovo file di testo contenente ogni parola associata alla rispettiva frequenza. Il file di testo analizzato in questo esempio è l'edizione Project Gutenberg eBook di The Notebooks of Leonardo Da Vinci.

> [AZURE.NOTE]I passaggi descritti in questo documento richiedono un client Windows. Per i passaggi per l'utilizzo dell'esempio di conteggio di parole da un client Unix, Linux o OS con un cluster HDInsight basato su Linux, vedere [Usare MapReduce con Hadoop in HDInsight con SSH](hdinsight-hadoop-use-mapreduce-ssh.md) o [Usare MapReduce con Hadoop in HDInsight con Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Si acquisiranno le nozioni seguenti:**

* Usare Azure PowerShell per eseguire un programma MapReduce su un cluster HDInsight.
* Scrivere programmi MapReduce in Java.


**Prerequisiti**:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un cluster HDInsight**. Per istruzioni sui vari metodi di creazione di tali cluster, vedere [Introduzione all'uso di Azure HDInsight][hdinsight-get-started] o l'articolo [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate](hdinsight-provision-clusters.md).

- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Eseguire l'esempio con Azure PowerShell</h2>

**Per inviare il processo a MapReduce**

1.	Aprire la console di **Azure PowerShell**. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

3. Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. Per creare una definizione del processo MapReduce, eseguire i comandi seguenti:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]Il file *hadoop-examples.jar* è incluso nei cluster HDInsight della versione 2.1. Il file è stato rinominato *hadoop-mapreduce.jar* nella versione 3.0 dei cluster HDInsight.

	Il file hadoop-mapreduce-examples.jar è incluso nella distribuzione del cluster HDInsight. Per il processo MapReduce sono disponibili due argomenti. Il primo argomento è il nome del file di origine, il secondo è il percorso del file di output. Il file di origine è incluso nel cluster HDInsight e il percorso del file di output verrà creato in fase di esecuzione.

6. Per inviare il processo MapReduce, eseguire il comando seguente:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Oltre alla definizione del processo MapReduce, è necessario specificare il nome del cluster HDInsight in cui si desidera eseguire il processo MapReduce.

8. Per verificare se si sono verificati errori nel processo MapReduce, eseguire il comando seguente:

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**Per recuperare i risultati del processo MapReduce**

1. Aprire la console di **Azure PowerShell**.
2. Impostare le tre variabili necessarie nei comandi seguenti, quindi eseguirli:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	L'account di archiviazione di Azure corrisponde all'account creato in precedenza in questa esercitazione. L'account di archiviazione viene utilizzato per l'hosting del BLOB usato come file system predefinito del cluster HDInsight. Il nome del contenitore di archiviazione BLOB Azure corrisponde in genere al nome del cluster HDInsight, a meno che durante il provisioning del cluster. non venga specificato un nome diverso.

3. Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti:

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Il comando **Select-AzureSubscription** consente di impostare la sottoscrizione corrente, nel caso in cui siano disponibili più sottoscrizioni e non si desideri usare quella predefinita.

4. Per scaricare l'output del processo MapReduce dal BLOB alla workstation, eseguire il comando seguente:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	La cartella */example/data/WordCountOutput* è la cartella di output specificata durante l'esecuzione del processo MapReduce. Il valore *part-r-00000* corrisponde al nome file predefinito per l'output del processo MapReduce. Il file verrà scaricato nella stessa struttura di cartelle nella cartella locale. Ad esempio, nella schermata seguente la cartella corrente è la cartella radice C. Il file verrà scaricato nella cartella *C:\\example\\data\\WordCountOutput*.

5. Per stampare il file di output del processo MapReduce, eseguire il comando seguente:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	Il processo MapReduce produce un file denominato *part-r-00000*, che include le parole e i conteggi. Lo script usa il comando **findstr** per elencare tutte le parole contenenti *"there"*.

L'output dello script WordCount dovrebbe essere visualizzato nella finestra dei comandi:

![Risultati relativi alla frequenza delle parole in PowerShell dall'esempio del conteggio parole MapReduce di Hadoop in HDInsight.][image-hdi-sample-wordcount-output]

Si noti che i file di output di un processo MapReduce non sono modificabili. Se pertanto si esegue di nuovo l'esempio, è necessario cambiare il nome del file di output.

## <a id="java-code"></a>Codice Java per il programma MapReduce WordCount</h2>



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



In questa esercitazione è stato illustrato come eseguire un programma MapReduce per il conteggio delle occorrenze delle parole in un file di testo con HDInsight tramite Azure PowerShell.

## <a id="next-steps"></a>Passaggi successivi</h2>

Per esercitazioni in cui vengono eseguiti altri esempi e che includono istruzioni per l'uso di processi Pig, Hive e MapReduce in Azure HDInsight con Azure PowerShell, vedere gli argomenti seguenti:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Esempio GraySort da 10 GB][hdinsight-sample-10gb-graysort]
* [Esempio di calcolo del Pi greco][hdinsight-sample-pi-estimator]
* [Esempio di flusso C#][hdinsight-sample-cs-streaming]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
 

<!---HONumber=July15_HO2-->