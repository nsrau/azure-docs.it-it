<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

Sviluppo di programmi MapReduce Java per HDInsight
==================================================

In questa esercitazione viene illustrato in modo dettagliato uno scenario end-to-end, dallo sviluppo e il test di un processo MapReduce per il conteggio di parole in HDInsight Emulator fino alla distribuzione e all'esecuzione di tale processo in Azure HDInsight.

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   Installare Azure HDInsight Emulator. Per istruzioni, vedere [Introduzione all'utilizzo di HDInsight Emulator](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).
-   Installare Azure PowerShell nel computer dell'emulatore. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).
-   Ottenere una sottoscrizione di Azure. Per istruzioni, vedere [Opzioni di acquisto](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Offerte per i membri](https://www.windowsazure.com/en-us/pricing/member-offers/) oppure [Versione di valutazione gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).

Contenuto dell'articolo
-----------------------

-   [Sviluppo di un programma MapReduce per il conteggio di parole in Java](#develop)
-   [Test del programma sull'emulatore](#test)
-   [Caricamento di file di dati e dell'applicazione nell'archivio BLOB di Azure](#upload)
-   [Esecuzione del programma MapReduce in Azure HDInsight](#run)
-   [Recupero dei risultati di MapReduce](#retrieve)
-   [Passaggi successivi](#nextsteps)

Sviluppo di un programma MapReduce per il conteggio di parole in Java
---------------------------------------------------------------------

Il conteggio delle parole viene eseguito da una semplice applicazione che conta le occorrenze di ogni parola in un set di input specifico.

**Per scrivere il processo MapReduce per il conteggio delle parole in Java**

1.  Aprire il Blocco note.
2.  Copiare e incollare il programma seguente nel Blocco note.

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
             System.exit(job.waitForCompletion(true) 
         0 : 1);
           }
         }

    Si noti che il nome del pacchetto è **org.apache.hadoop.examples** e il nome della classe è **WordCount**. Tali nomi verranno utilizzati per l'invio del processo MapReduce.

3.  Salvare il file come **c:\\Tutorials\\WordCountJava\\WordCount.java**. Creare la struttura di cartelle, se non esiste già.

HDInsight Emulator include il compilatore *javac*.

**Per compilare il programma MapReduce**

1.  Aprire il prompt dei comandi.
2.  Cambiare la directory in **c:\\Tutorials\\WordCountJava**. Questa è la cartella per il programma MapReduce per il conteggio delle parole.
3.  Eseguire il comando seguente per verificare l'esistenza dei due file con estensione jar:

         dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
         dir %hadoop_home%\lib\commons-cli-1.2.jar

4.  Eseguire il comando seguente per compilare il programma:

         C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

    javac si trova nella cartella C:\\Hadoop\\java\\bin. L'ultimo parametro corrisponde al programma Java disponibile nella cartella corrente. Il compilatore crea tre file di classe nella cartella corrente.

5.  Eseguire il comando seguente per creare un file con estensione jar:

         C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

    Questo comando consente di creare un file WordCount.jar nella cartella corrente.

    ![HDI.EMulator.WordCount.Compile](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png)

Test del programma sull'emulatore
---------------------------------

Per testare il processo MapReduce nell'emulatore, è necessario eseguire le procedure seguenti:

1.  Caricamento dei file di dati in HDFS nell'emulatore
2.  Invio di un processo MapReduce per il conteggio delle parole
3.  Verifica dello stato del processo
4.  Recupero dei risultati del processo

Per impostazione predefinita, HDInsight Emulator utilizza HDFS come file system predefinito. Se lo si desidera, è possibile configurare HDInsight Emulator per l'utilizzo dell'archivio BLOB di Azure. Per informazioni dettagliate, vedere [Introduzione a HDInsight Emulator](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/#blobstorage).

In questa esercitazione verrà utilizzato il comando *copyFromLocal* di HDFS per il caricamento dei file di dati in HDFS. Nella sezione successiva verrà illustrato come caricare i file utilizzando Azure PowerShell nell'archivio BLOB di Azure. Per altri metodi per il caricamento di file nell'archivio BLOB di Azure, vedere [Caricamento di dati in HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

In questa esercitazione viene utilizzata la struttura di cartelle HDFS seguente:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><td data-morhtml="true">Cartella</td><td data-morhtml="true">Nota</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount</td><td data-morhtml="true">Cartella radice per il progetto per il conteggio delle parole. </td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Apps</td><td data-morhtml="true">Cartella per i file eseguibili dei programmi di mapping e di riduzione.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Input</td><td data-morhtml="true">Cartella di file di origine di MapReduce.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Output</td><td data-morhtml="true">Cartella di file di output di MapReduce.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/MRStatusOutput</td><td data-morhtml="true">Cartella di output del processo.</td></tr>
</table>

In questa esercitazione vengono utilizzati come file di dati i file con estensione txt disponibili nella directory %hadoop\_home%.

> [WACOM.NOTE] I comandi HDFS di Hadoop rispettano la distinzione tra maiuscole e minuscole.

**Per copiare i file di dati in HDFS nell'emulatore**

1.  Aprire la riga di comando di Hadoop dal desktop. La riga di comando di Hadoop viene installata dal programma di installazione dell'emulatore.
2.  Nella finestra della riga di comando di Hadoop eseguire il comando seguente per creare una directory per i file di input:

         hadoop fs -mkdir /WordCount/Input

    Viene utilizzato il percorso relativo, che equivale al percorso seguente:

         hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  Eseguire il comando seguente per copiare alcuni file di testo nella cartella di input in HDFS:

         hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

    Il processo MapReduce conterà le parole incluse nei file.

4.  Eseguire il comando seguente per elencare e verificare i file caricati:

         hadoop fs -ls /WordCount/Input

    Dovrebbero essere visualizzati circa otto file con estensione txt.

**Per eseguire il processo MapReduce tramite la riga di comando di Hadoop**

1.  Aprire la riga di comando di Hadoop dal desktop.
2.  Eseguire il comando seguente per eliminare la struttura di cartelle /WordCount/Output da HDFS. /WordCount/Output è la cartella di output del processo MapReduce per il conteggio delle parole. Se tale cartella esiste già, il processo MapReduce avrà esito negativo. Questo passaggio è necessario se si sta eseguendo il processo per la seconda volta.

         hadoop fs -rmr /WordCount/Output

3.  Eseguire il comando seguente:

         hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    Se il processo viene completato correttamente, l'output ottenuto dovrebbe essere analogo alla schermata seguente:

    ![HDI.EMulator.WordCount.Run](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png)

    Come si può vedere nella schermata, il mapping e la riduzione sono stati completati al 100%. Viene elencato anche l'ID del processo, job\_201312092021\_0002. È possibile recuperare lo stesso report aprendo il collegamento **Hadoop MapReduce status** dal desktop e cercando l'ID del processo.

In alternativa, per eseguire il processo MapReduce, è possibile utilizzare Azure PowerShell. Per istruzioni, vedere [Introduzione all'utilizzo di HDInsight Emulator](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).

**Per visualizzare l'output da HDFS**

1.  Aprire la riga di comando di Hadoop.
2.  Eseguire il comando seguente per visualizzare l'output:

         hadoop fs -ls /WordCount/Output/
         hadoop fs -cat /WordCount/Output/part-00000

    È possibile aggiungere "|more" alla fine del comando per ottenere la visualizzazione della pagina oppure utilizzare il comando findstr per trovare un modello di stringa:

         hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

Il processo MapReduce per il conteggio delle parole è stato sviluppato e testato correttamente sull'emulatore. Il passaggio successivo consiste nella distribuzione e nell'esecuzione del processo in Azure HDInsight.

Caricamento di dati nell'archivio BLOB di Azure
-----------------------------------------------

Azure HDInsight utilizza l'archivio BLOB di Azure per l'archiviazione dei dati. Quando viene eseguito il provisioning di un cluster HDInsight, viene utilizzato un contenitore dell'archivio BLOB di Azure per archiviare i file di sistema. Per l'archiviazione dei file di dati è possibile utilizzare tale contenitore predefinito o un contenitore diverso nello stesso account di archiviazione di Azure oppure in un account di archiviazione diverso disponibile nello stesso data center del cluster.

In questa esercitazione verrà creato un contenitore in un account di archiviazione separato per i file di dati e l'applicazione MapReduce. I file di dati sono file di testo disponibili nella directory %hadoop\_home% nella workstation.

**Per creare un archivio BLOB e un contenitore**

1.  Aprire Azure PowerShell.
2.  Impostare le variabili, quindi eseguire i comandi:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"
         $location = "<MicrosoftDataCenter>"  # For example, "East US"

    Il valore **$subscripionName** è associato alla sottoscrizione di Azure in uso. È necessario specificare un nome per **$storageAccountName\_Data** e **$containerName\_Data**. Per informazioni sulle limitazioni previste per i nomi, vedere [Assegnazione di nome e riferimento a contenitori, BLOB e metadati](http://msdn.microsoft.com/it-it/library/windowsazure/dd135715.aspx).

3.  Eseguire il comando seguente per creare un account di archiviazione e un contenitore di archiviazione BLOB nell'account.

         # Select Azure subscription
         Select-AzureSubscription $subscriptionName
            
         # Create a storage account
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
                    
         # Create a Blob storage container
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
         New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  Eseguire i comandi seguenti per verificare l'account e il contenitore di archiviazione:

         Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
         Get-AzureStorageContainer -Context $destContext

**Per caricare i file di dati**

1.  Aprire Azure PowerShell.
2.  Impostare le prime tre variabili, quindi eseguire i comandi seguenti:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
         $destFolder = "WordCount/Input"

    I valori di **$storageAccountName\_Data** e **$containerName\_Data** corrispondono ai valori definiti nella procedura precedente.

    Si noti che la cartella di file di origine è **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** e la cartella di destinazione è **WordCount/Input**.

3.  Eseguire i comandi seguenti per ottenere un elenco dei file con estensione txt disponibili nella cartella di file di origine:

         # Get a list of the txt files
         $filesAll = Get-ChildItem $localFolder
         $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Per creare un oggetto contesto archiviazione, eseguire i comandi seguenti:

         # Create a storage context object
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  Eseguire i comandi seguenti per copiare i file:

         # Copy the file from local workstation to the Blob container        
         foreach ($file in $filesTxt){
             
             $fileName = "$localFolder\$file"
             $blobName = "$destFolder/$file"
            
             write-host "Copying $fileName to $blobName"
            
             Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
         }

6.  Eseguire il comando seguente per ottenere un elenco dei file caricati:

         # List the uploaded files in the Blob storage container
         Write-Host "The Uploaded data files:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    Dovrebbero essere visualizzati circa 8 file di dati di testo.

**Per caricare l'applicazione per il conteggio delle parole**

1.  Aprire Azure PowerShell.
2.  Impostare le prime tre variabili, quindi eseguire i comandi seguenti:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
         $blobFolder = "WordCount/jars"

    I valori di **$storageAccountName\_Data** e **$containerName\_Data** corrispondono ai valori definiti nella procedura precedente. I file di dati e l'applicazione verranno pertanto caricati nello stesso contenitore sullo stesso account di archiviazione.

    Si noti che la cartella di destinazione è **WordCount/jars**.

3.  Per creare un oggetto contesto archiviazione, eseguire i comandi seguenti:

         # Create a storage context object
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  Eseguire i comandi seguenti per copiare le applicazioni:

         Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  Eseguire il comando seguente per ottenere un elenco dei file caricati:

         # List the uploaded files in the Blob storage container
         Write-Host "The Uploaded application file:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    Il file con estensione jar dovrebbe essere incluso nell'elenco visualizzato.

Esecuzione del processo MapReduce in Azure HDInsight
----------------------------------------------------

Lo script PowerShell riportato di seguito consente di eseguire le attività seguenti:

1.  Provisioning di un cluster HDInsight

    1.  Creazione di un account di archiviazione che verrà utilizzato come file system predefinito del cluster HDInsight
    2.  Creazione di un contenitore di archiviazione BLOB
    3.  Creazione di un cluster HDInsight

2.  Invio del processo MapReduce

    1.  Creazione di una definizione del processo MapReduce
    2.  Invio di un processo MapReduce
    3.  Attesa del completamento del processo
    4.  Visualizzazione degli errori standard
    5.  Visualizzazione dell'output standard

3.  Eliminazione del cluster

    1.  Eliminazione del cluster HDInsight
    2.  Eliminazione dell'account di archiviazione utilizzato come file system predefinito del cluster HDInsight

**Per eseguire lo script di PowerShell**

1.  Aprire il Blocco note.
2.  Copiare e incollare il codice seguente:

         # The storage account and the HDInsight cluster variables
         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $serviceNameToken = "<ServiceNameTokenString>"
         $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
         $clusterNodes = <NumberOFNodesInTheCluster>

         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
            
         $clusterName = $serviceNameToken + "hdicluster"
            
         $storageAccountName_Default = $serviceNameToken + "hdistore"
         $containerName_Default =  $serviceNameToken + "hdicluster"

         # The MapReduce job variables
         $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
         $className = "org.apache.hadoop.examples.WordCount"
         $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
         $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
         $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
            
         # Create a PSCredential object. Il nome utente e la password sono hardcoded.  Se lo si desidera, è possibile modificarli.
         $password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
         $creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
            
         Select-AzureSubscription $subscriptionName
            
         #=============================
         # Create a storage account used as the default file system
         Write-Host "Create a storage account" -ForegroundColor Green
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
            
         #=============================
         # Create a Blob storage container used as teh default file system
         Write-Host "Create a Blob storage container" -ForegroundColor Green
         $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
            
         New-AzureStorageContainer -Name $containerName_Default -Context $destContext
            
         #=============================
         # Create an HDInsight cluster
         Write-Host "Create an HDInsight cluster" -ForegroundColor Green
         $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
            
         $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
             Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
             Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
            
         New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
            
         #=============================
         # Create a MapReduce job definition
         Write-Host "Create a MapReduce job definition" -ForegroundColor Green
         $mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
            
         #=============================
         # Run the MapReduce job
         Write-Host "Run the MapReduce job" -ForegroundColor Green
         $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
         Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
            
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
                    
         #=============================
         # Delete the HDInsight cluster
         Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
         Remove-AzureHDInsightCluster -Name $clusterName  
            
         # Delete the default file system storage account
         Write-Host "Delete the storage account" -ForegroundColor Green
         Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Impostare le prime sei variabili nello script. **$serviceNameToken** verrà utilizzato per il nome del cluster HDInsight, il nome dell'account di archiviazione predefinito e il nome del contenitore di archiviazione BLOB predefinito. Poiché il nome del servizio deve avere una lunghezza compresa tra 3 e 24 caratteri e lo script aggiunge ai nomi una stringa di un massimo di 10 caratteri, è necessario limitare la stringa a un massimo di 14 caratteri. È inoltre necessario utilizzare lettere minuscole per \$serviceNameToken. **$storageAccountName\_Data** e **$containerName\_Data** corrispondono all'account e al contenitore di archiviazione utilizzati per archiviare i file di dati e l'applicazione. **$location** deve corrispondere alla posizione dell'account di archiviazione dei dati.
4.  Esaminare le variabili rimanenti.
5.  Salvare il file di script.
6.  Aprire Azure PowerShell.
7.  Eseguire il comando seguente per impostare i criteri di esecuzione su remotesigned:

         PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Quando richiesto, immettere nome utente e password per il cluster HDInsight. Poiché il cluster verrà eliminato alla fine dello script e il nome utente e la password non saranno più necessari, è possibile specificare qualsiasi stringa come nome utente e password. Se non si desidera che venga richiesta l'immissione di credenziali, vedere [Utilizzo di password, stringhe sicure e credenziali in Windows PowerShell](http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx).

Recupero dell'output del processo MapReduce
-------------------------------------------

In questa sezione viene illustrato come scaricare e visualizzare l'output. Per informazioni sulla visualizzazione dei risultati in Excel, vedere [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/) e [Connessione di Excel a HDInsight mediante Power Query](/en-us/manage/services/hdinsight/connect-excel-with-power-query/).

**Per recuperare l'output**

1.  Aprire una finestra di Azure PowerShell.
2.  Cambiare la directory in **C:\\Tutorials\\WordCountJava**. La cartella predefinita per Azure PowerShell è **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. I cmdlet eseguiti consentiranno di scaricare il file di output nella cartella corrente. Non si dispone di autorizzazioni per il download dei file nelle cartelle di sistema.
3.  Per impostare i valori, eseguire i comandi seguenti.

         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
         $blobName = "WordCount/Output/part-r-00000"

4.  Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti:

         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

5.  Eseguire i comandi seguenti per scaricare e visualizzare l'output:

         Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
         cat "./$blobName" | findstr "there"

Al termine del processo sarà possibile esportare i dati in SQL Server o nel database SQL di Azure tramite [Sqoop](../hdinsight-use-sqoop/) oppure esportare i dati in Excel.

Passaggi successivi
-------------------

In questa esercitazione si è appreso come sviluppare un processo MapReduce Java, come testare l'applicazione in HDInsight Emulator e come scrivere uno script di PowerShell per eseguire il provisioning di un cluster HDInsight ed eseguire un processo MapReduce sul cluster. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Sviluppo di programmi MapReduce di streaming Hadoop in C\# per HDInsight](/it-it/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Introduzione a HDInsight Emulator](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/)
-   [Utilizzo dell'archivio BLOB di Azure con HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/)
-   [Amministrazione di HDInsight tramite PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Caricamento di dati in HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Utilizzo di Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Utilizzo di Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Connessione di Excel a HDInsight mediante Power Query](/it-it/documentation/articles/hdinsight-connect-excel-power-query/)
-   [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver](../hdinsight-connect-excel-hive-ODBC-driver/)

