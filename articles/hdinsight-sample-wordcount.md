<properties linkid="manage-services-hdinsight-sample-wordcount" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight WordCount sample | Azure" metaKeywords="hdinsight, hdinsight sample, mapreduce" description="Learn how to run a simple MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight WordCount sample" authors="bradsev" />

Esempio WordCount di HDInsight
==============================

In questo argomento di esempio viene illustrato come eseguire un programma MapReduce per il conteggio delle occorrenze di parole in un file di testo con Azure HDInsight tramite Azure PowerShell. Il programma MapReduce WordCount è scritto in Java e viene eseguito in un cluster HDInsight. Il file di testo analizzato è l'edizione Project Gutenberg eBook di The Notebooks of Leonardo Da Vinci.

Il programma MapReduce Hadoop legge il file di testo e conta le occorrenze di ogni parola. L'output è un nuovo file di testo costituito da righe, ognuna delle quali include una parola e il conteggio (una coppia chiave/valore separati da tabulazione) del numero di occorrenze di tale parola nel documento. Il processo viene eseguito in due fasi. Il mapper accetta come input ciascuna riga del testo di input e la suddivide in parole. Per ogni occorrenza della parola emette una coppia chiave/valore, costituita dalla parola seguita da 1. La funzione per la riduzione somma quindi tali singoli conteggi per ogni parola ed emette una singola coppia chiave/valore, contenente la parola seguita dalla somma delle rispettive occorrenze.

**Si apprenderà come:**

-   Utilizzare Azure PowerShell per eseguire un programma MapReduce su un cluster HDInsight.
-   Scrivere programmi MapReduce in Java.

**Prerequisiti**:

-   È necessario disporre di un account Azure. Per le opzioni di iscrizione per ottenere un account, vedere la pagina [Abbonamento di prova gratuito di un mese](http://www.windowsazure.com/it-it/pricing/free-trial/).

-   È necessario avere completato il provisioning di un cluster HDInsight. Per informazioni sui vari metodi di creazione di tali cluster e per le relative istruzioni, vedere [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) o [Provisioning di cluster HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)

-   È necessario che Azure PowerShell sia installato e configurato per l'utilizzo con l'account utente. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

Contenuto dell'articolo
-----------------------

In questo argomento viene illustrato come eseguire l'esempio, viene presentato il codice Java per il programma MapReduce, vengono riepilogate le nozioni acquisite e vengono descritti alcuni passaggi successivi. L'argomento include le sezioni seguenti:

1.  [Esecuzione dell'esempio con Azure PowerShell](#run-sample)
2.  [Codice Java per il programma MapReduce WordCount](#java-code)
3.  [Riepilogo](#summary)
4.  [Passaggi successivi](#next-steps)

Esecuzione dell'esempio con Azure PowerShell
--------------------------------------------

**Per inviare il processo MapReduce**

1.  Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

2.  Impostare le due variabili necessarie nei comandi seguenti, quindi eseguirli:

         $subscriptionName = "<SubscriptionName>"   # Azure subscription name
         $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Per creare una definizione del processo MapReduce, eseguire i comandi seguenti:

         # Define the MapReduce job
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] Il file *hadoop-examples.jar* è incluso nei cluster HDInsight della versione 2.1. Il file è stato rinominato in *hadoop-mapreduce.jar* nei cluster HDInsight della versione 3.0.

    Il file hadoop-examples.jar è incluso nel cluster HDInsight. Per il processo MapReduce sono disponibili due argomenti. Il primo argomento è il nome del file di origine, il secondo è il percorso del file di output. Il file di origine è incluso nel cluster HDInsight e il percorso del file di output verrà creato in fase di esecuzione.

4.  Per inviare il processo MapReduce, eseguire il comando seguente:

         # Submit the job
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Oltre alla definizione del processo MapReduce, è necessario specificare anche il nome del cluster HDInsight in cui si desidera eseguire il processo MapReduce.

5.  Per verificare se si sono verificati errori nel processo MapReduce, eseguire il comando seguente:

         # Get the job output
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**Per recuperare i risultati del processo MapReduce**

1.  Aprire **Azure PowerShell**.
2.  Impostare le tre variabili necessarie nei comandi seguenti, quindi eseguirli:

         $subscriptionName = "<SubscriptionName>"       # Azure subscription name
            
         $storageAccountName = "<StorageAccountName>"   # Azure storage account name
         $containerName = "<ContainerName>"              # Blob storage container name

    L'account di archiviazione di Azure corrisponde all'account creato in precedenza in questa esercitazione. L'account di archiviazione viene utilizzato per l'hosting del contenitore BLOB utilizzato come file system predefinito per il cluster HDInsight. Il nome del contenitore di archiviazione BLOB corrisponde in genere al nome del cluster HDInsight, a meno che non venga specificato un nome diverso durante il provisioning del cluster.

3.  Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti:

         # Select the current subscription
         Select-AzureSubscription $subscriptionName

         # Create the storage account context object
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Il comando *Select-AzureSubscription* consente di impostare la sottoscrizione corrente, nel caso in cui siano disponibili più sottoscrizioni e non si desideri utilizzare la sottoscrizione predefinita.

4.  Per scaricare l'output del processo MapReduce dal contenitore BLOB alla workstation, eseguire il comando seguente:

         # Download the job output to the workstation
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    La cartella */example/data/WordCountOutput* è la cartella di output specificata durante l'esecuzione del processo MapReduce. Il valore *part-r-00000* corrisponde al nome file predefinito per l'output del processo MapReduce. Il file verrà scaricato nella stessa struttura di cartelle nella cartella locale. Ad esempio, nella schermata seguente la cartella corrente è la cartella radice C. Il file verrà scaricato nella cartella *C:\\example\\data\\WordCountOutput*.

5.  Per stampare il file di output del processo MapReduce, eseguire il comando seguente:

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    Il processo MapReduce produce un file denominato *part-r-00000*, che include le parole e i conteggi. Lo script utilizza il comando findstr per elencare tutte le parole contenenti *"there"*.

L'output dello script WordCount dovrebbe essere visualizzato nella finestra dei comandi:

![HDI.Sample.WordCount.Output](./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png)

Si noti che i file di output di un processo MapReduce non sono modificabili. Se pertanto si esegue di nuovo l'esempio, sarà necessario cambiare il nome del file di output.

Codice Java per il programma MapReduce WordCount
------------------------------------------------

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

Riepilogo
---------

In questa esercitazione è stato illustrato come eseguire un programma MapReduce per il conteggio delle occorrenze delle parole in un file di testo con HDInsight tramite Azure PowerShell.

Passaggi successivi
-------------------

Per esercitazioni in cui vengono eseguiti altri esempi e che includono istruzioni per l'utilizzo di Pig, Hive e processi MapReduce in Azure HDInsight con Azure PowerShell, vedere gli argomenti seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Esempio: GraySort da 10 GB](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Esempio: Calcolo del Pi greco](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Esempio: streaming C\#](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Utilizzo di Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilizzo di Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Documentazione di Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

