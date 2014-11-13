<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight-Hadoop" urlDisplayName="HDInsight Tutorials" pageTitle="Sviluppare programmi MapReduce Java per Hadoop in HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Informazioni su come sviluppare programmi MapReduce Java su HDInsight Emulator e implementarli in HDInsight." services="hdinsight" title="Sviluppare programmi MapReduce Java per Hadoop in HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="nitinme" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/10/2014" ms.author="nitinme" />

# Sviluppare programmi MapReduce Java per Hadoop in HDInsight

In questa esercitazione è illustrato in modo dettagliato uno scenario end-to-end per lo sviluppo di un processo MapReduce Hadoop in Java usando Apache Maven. L'esercitazione mostra anche come eseguire il test dell'applicazione su HDInsight Emulator e quindi implementarla ed eseguirla sul cluster di Azure HDInsight.

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario aver completato quanto segue:

-   Installare Azure HDInsight Emulator. Per istruzioni, vedere [Introduzione all'uso di HDInsight Emulator][Introduzione all'uso di HDInsight Emulator].
-   Installare Azure PowerShell nel computer dell'emulatore. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell].
-   Installare la piattaforma Java JDK 7 o versioni successive nel computer dell'emulatore. È già disponibile nel computer dell'emulatore.
-   Installare e configurare [Apache Maven][Apache Maven].
-   Ottenere una sottoscrizione di Azure. Per istruzioni, vedere [Opzioni di acquisto][Opzioni di acquisto], [Offerte per i membri][Offerte per i membri] oppure [Versione di valutazione gratuita][Versione di valutazione gratuita].

## Contenuto dell'articolo

-   [Uso di Apache Maven per la creazione di un programma MapReduce per il conteggio di parole in Java][Uso di Apache Maven per la creazione di un programma MapReduce per il conteggio di parole in Java]
-   [Test del programma sull'emulatore][Test del programma sull'emulatore]
-   [Caricamento di file di dati e dell'applicazione nell'archivio BLOB di Azure][Caricamento di file di dati e dell'applicazione nell'archivio BLOB di Azure]
-   [Esecuzione del programma MapReduce in Azure HDInsight][Esecuzione del programma MapReduce in Azure HDInsight]
-   [Recupero dei risultati di MapReduce][Recupero dei risultati di MapReduce]
-   [Passaggi successivi][Passaggi successivi]

## <a name="develop"></a>Uso di Apache Maven per la creazione di un programma MapReduce in Java

Creare un'applicazione MapReduce per il conteggio delle parole. È una semplice applicazione che conta le occorrenze di ogni parola in un set di input specifico. In questa sezione verranno eseguite le attività seguenti:

1.  Creare un progetto con Apache Maven
2.  Aggiornare il modello a oggetti dei progetti
3.  Creare l'applicazione MapReduce per il conteggio delle parole
4.  Compilare e creare il pacchetto dell'applicazione

**Per creare un progetto con Maven**

1.  Creare una directory \*\*C:\\Tutorials\\WordCountJava\*\*.
2.  Dalla riga di comando nell'ambiente di sviluppo, cambiare la directory specificando il percorso creato.
3.  Usare il comando **mvn**, che viene installato con Maven, per generare lo scaffolding per il progetto.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Verrà creata una nuova directory nella directory corrente, con il nome specificato dal parametro **artifactID** (in questo esempio, **wordcountjava**), che conterrà i seguenti elementi.

    -   **pom.xml**: il modello a oggetti dei progetti ([POM][POM], Project Object Model) contiene le informazioni e i dettagli di configurazione usati per compilare il progetto

    -   **src**: la directory che contiene la directory **main\\java\\org\\apache\\hadoop\\examples**, in cui verrà creata l'applicazione.

4.  Eliminare il file **src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java**, perché non verrà usato in questo esempio.

**Per aggiornare il modello a oggetti dei progetti**

1.  Modificare il file **pom.xml** aggiungere il codice seguente nella sezione `<dependencies>`.

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
        </dependency>
        <dependency>                                                                                     
          <groupId>org.apache.hadoop</groupId>                                                                                                       
          <artifactId>hadoop-common</artifactId>                                                                                                         
          <version>2.5.1</version>                                                                                            
        </dependency>

    Ciò indica a Maven che il progetto richiede le librerie (elencate entro <artifactid\>) con la versione specifica (elencata entro <version\>). In fase di compilazione, quest'ultimo verrà scaricato dal repository Maven predefinito. È possibile usare la [ricerca nel repository Maven][ricerca nel repository Maven] per visualizzare più informazioni.

2.  Aggiungere il codice seguente al file **pom.xml**. Il codice deve essere incluso tra tag `<project>...</project>` nel file; ad esempio, tra `</dependencies>` e `</project>`.

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>       
          </plugins>
        </build>

    Verrà configurato il plug-in [maven-shade-plugin][maven-shade-plugin], usato per prevenire la duplicazione della licenza nel file JAR compilato da Maven. Il motivo per cui viene usato questo plug-in è che i file di licenza duplicati causano un errore in fase di esecuzione sul cluster HDInsight. L'utilizzo del plug-in maven-shade-plugin con l'implementazione di `ApacheLicenseResourceTransformer` previene il verificarsi di questo errore.

    Il plug-in maven-shade-plugin produrrà anche un file uberjar (o fatjar), che contiene tutte le dipendenze richieste dall'applicazione.

3.  Salvare il file **pom.xml**.

**Per creare l'applicazione per il conteggio delle parole**

1.  Passare alla directory **wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples** e rinominare il file **app.java** in **WordCount.java**.
2.  Aprire il Blocco note.
3.  Copiare e incollare il programma seguente nel Blocco note.

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

    Si noti che il nome del pacchetto è **org.apache.hadoop.examples** e il nome della classe è **WordCount**. Tali nomi verranno usati per l'invio del processo MapReduce.

4.  Salvare il file.

**Per compilare e creare il pacchetto dell'applicazione**

1.  Aprire un prompt dei comandi e passare alla directory **wordcountjava**.

2.  Usare il comando seguente per compilare un file JAR contenente l'applicazione.

        mvn clean package

    In tal modo, verranno eliminati eventuali artefatti di compilazione precedenti, scaricate le dipendenze non ancora installate e compilato e creato il pacchetto dell'applicazione.

3.  Quando il comando viene completato, la directory **wordcountjava\\target** conterrà un file denominato **wordcountjava-1.0-SNAPSHOT.jar**.

    > [WACOM.NOTE] Il file **wordcountjava-1.0-SNAPSHOT.jar** è un file uberjar (talvolta chiamato fatjar,) che contiene tutte le dipendenze richieste per eseguire l'applicazione.

## <a name="test"></a>Test del programma sull'emulatore

Per testare il processo MapReduce su HDInsight Emulator, è necessario eseguire le procedure seguenti:

1.  Caricamento dei file di dati in HDFS nell'emulatore
2.  Creazione di un gruppo di utenti locale
3.  Esecuzione di un processo MapReduce per il conteggio delle parole
4.  Recupero dei risultati del processo

Per impostazione predefinita, HDInsight Emulator usa HDFS come file system predefinito. Se si desidera, è possibile configurare HDInsight Emulator per l'uso dell'archivio BLOB di Azure. Per informazioni dettagliate, vedere [Introduzione all'uso di HDInsight Emulator][1].

In questa esercitazione verrà usato il comando *copyFromLocal* di HDFS per il caricamento dei file di dati in HDFS. Nella sezione successiva verrà illustrato come caricare i file usando Azure PowerShell nell'archivio BLOB di Azure. Per altri metodi per il caricamento di file nell'archivio BLOB di Azure, vedere [Caricare i dati in HDInsight][Caricare i dati in HDInsight].

In questa esercitazione viene usata la struttura di cartelle HDFS seguente:

|---------------------------|-------------------------------------------------------------------------|
| Cartella                  | Nota                                                                    |
| /WordCount                | Cartella radice per il progetto per il conteggio delle parole.          |
| /WordCount/Apps           | Cartella per i file eseguibili dei programmi di mapping e di riduzione. |
| /WordCount/Input          | Cartella di file di origine di MapReduce.                               |
| /WordCount/Output         | Cartella di file di output di MapReduce.                                |
| /WordCount/MRStatusOutput | Cartella di output del processo.                                        |

In questa esercitazione vengono usati come file di dati i file con estensione txt disponibili nella directory %hadoop\_home%.

> [WACOM.NOTE] I comandi HDFS di Hadoop rispettano la distinzione tra maiuscole e minuscole.

**Per copiare i file di dati in HDFS nell'emulatore**

1.  Aprire la riga di comando di Hadoop dal desktop. La riga di comando di Hadoop viene installata dal programma di installazione dell'emulatore.
2.  Nella finestra della riga di comando di Hadoop eseguire il comando seguente per creare una directory per i file di input:

        hadoop fs -mkdir /WordCount/Input

    Viene usato il percorso relativo, che equivale al percorso seguente:

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  Eseguire il comando seguente per copiare alcuni file di testo nella cartella di input in HDFS:

        hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /WordCount/Input

    Il processo MapReduce conterà le parole incluse nei file.

4.  Eseguire il comando seguente per elencare e verificare i file caricati:

        hadoop fs -ls /WordCount/Input

**Per creare un gruppo di utenti locale**

Per la corretta esecuzione del processo MapReduce sul cluster è necessario creare un gruppo di utenti denominato hdfs, al quale aggiungere anche un utente Hadoop e l'utente locale con il quale eseguire l'accesso all'emulatore. Usare i comandi riportati di seguito da un prompt dei comandi con privilegi elevati:

        # Add a user group called hdfs      
        net localgroup hdfs /add

        # Adds a user called hadoop to the group
        net localgroup hdfs hadoop /add

        # Adds the local user to the group
        net localgroup hdfs <username> /add

**Per eseguire il processo MapReduce tramite la riga di comando di Hadoop**

1.  Aprire la riga di comando di Hadoop dal desktop.
2.  Eseguire il comando seguente per eliminare la struttura di cartelle /WordCount/Output da HDFS. /WordCount/Output è la cartella di output del processo MapReduce per il conteggio delle parole. Se tale cartella esiste già, il processo MapReduce avrà esito negativo. Questo passaggio è necessario se si sta eseguendo il processo per la seconda volta.

        hadoop fs -rm - r /WordCount/Output

3.  Eseguire il comando seguente:

        hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    Se il processo viene completato correttamente, l'output ottenuto dovrebbe essere analogo alla schermata seguente:

    ![HDI.EMulator.WordCount.Run][HDI.EMulator.WordCount.Run]

    Come si può vedere nella schermata, il mapping e la riduzione sono stati completati al 100%. Viene elencato anche l'ID del processo. È possibile recuperare lo stesso report aprendo il collegamento **Hadoop MapReduce status** dal desktop e cercando l'ID dello stesso processo.

In alternativa, per eseguire il processo MapReduce, è possibile usare Azure PowerShell. Per istruzioni, vedere [Introduzione all'uso di HDInsight Emulator][Introduzione all'uso di HDInsight Emulator].

**Per visualizzare l'output da HDFS**

1.  Aprire la riga di comando di Hadoop.
2.  Eseguire il comando seguente per visualizzare l'output:

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-r-00000

    È possibile aggiungere "|more" alla fine del comando per ottenere la visualizzazione della pagina oppure usare il comando findstr per trovare un modello di stringa:

        hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

Il processo MapReduce per il conteggio delle parole è stato sviluppato e testato correttamente sull'emulatore. Il passaggio successivo consiste nella distribuzione e nell'esecuzione del processo in Azure HDInsight.

## <span id="upload"></span></a>Caricamento di dati e di applicazioni nell'archivio BLOB di Azure

Azure HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. Quando viene eseguito il provisioning di un cluster HDInsight, viene usato un contenitore dell'archivio BLOB di Azure per archiviare i file di sistema. Per l'archiviazione dei file di dati è possibile usare tale contenitore predefinito o un contenitore diverso nello stesso account di archiviazione di Azure oppure in un account di archiviazione diverso disponibile nello stesso data center del cluster.

In questa esercitazione verrà creato un contenitore in un account di archiviazione separato per i file di dati e l'applicazione MapReduce. I file di dati sono file di testo disponibili nella directory **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** nella workstation dell'emulatore.

**Per creare un archivio BLOB e un contenitore**

1.  Aprire Azure PowerShell.
2.  Impostare le variabili, quindi eseguire i comandi:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

    Il valore **$subscripionName** è associato alla sottoscrizione di Azure in uso. È necessario specificare un nome per **$storageAccountName\_Data** e **$containerName\_Data**. Per informazioni sulle limitazioni previste per i nomi, vedere [Assegnazione di nome e riferimento a contenitori, BLOB e metadati][Assegnazione di nome e riferimento a contenitori, BLOB e metadati].

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

        $localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\"
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

    Dovrebbero essere visualizzati i file di dati di testo caricati.

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

## <a name="run"></a>Esecuzione del processo MapReduce in Azure HDInsight

In questa sezione verrà creato uno script PowerShell che consente di eseguire le attività seguenti:

1.  Provisioning di un cluster HDInsight

    1.  Creazione di un account di archiviazione che verrà usato come file system predefinito del cluster HDInsight
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
    2.  Eliminazione dell'account di archiviazione usato come file system predefinito del cluster HDInsight

**Per eseguire lo script di PowerShell**

1.  Aprire il Blocco note.
2.  Copiare e incollare il codice seguente:

        # The storage account and the HDInsight cluster variables
        $subscriptionName = "<AzureSubscriptionName>"
        $stringPrefix = "<StringForPrefix>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = <NumberOFNodesInTheCluster>

        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"

        $clusterName = $stringPrefix + "hdicluster"

        $storageAccountName_Default = $stringPrefix + "hdistore"
        $containerName_Default =  $stringPrefix + "hdicluster"

        # The MapReduce job variables
        $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
        $className = "org.apache.hadoop.examples.WordCount"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        # Create a PSCredential object. The username and password are hardcoded here.  You can change them if you want.
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

3.  Impostare le prime sei variabili nello script. **$stringPrefix** verrà usato per aggiungere la stringa specificata come prefisso del nome del cluster HDInsight, il nome dell'account di archiviazione e il nome del contenitore di archiviazione BLOB. Poiché la lunghezza di questi nomi deve essere compresa tra 3 e 24 caratteri, assicurarsi che la stringa specificata e i nomi usati dallo script combinati non superino il limite di caratteri fissato per il nome. È necessario usare lettere minuscole per **$stringPrefix**.

    **$storageAccountName\_Data** e **$containerName\_Data** corrispondono all'account e al contenitore di archiviazione usati per archiviare i file di dati e l'applicazione. **$location** deve corrispondere alla posizione dell'account di archiviazione dei dati.

4.  Esaminare le variabili rimanenti.
5.  Salvare il file di script.
6.  Aprire Azure PowerShell.
7.  Eseguire il comando seguente per impostare i criteri di esecuzione su remotesigned:

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Quando richiesto, immettere nome utente e password per il cluster HDInsight. Poiché il cluster verrà eliminato alla fine dello script e il nome utente e la password non saranno più necessari, è possibile specificare qualsiasi stringa come nome utente e password. Se non si desidera che venga richiesta l'immissione di credenziali, vedere [Uso di password, stringhe sicure e credenziali in Windows PowerShell][Uso di password, stringhe sicure e credenziali in Windows PowerShell]

## <a name="retrieve"></a>Recupero dell'output del processo MapReduce

In questa sezione viene illustrato come scaricare e visualizzare l'output. Per informazioni sulla visualizzazione dei risultati in Excel, vedere [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver] e [Connettere Excel a HDInsight mediante Power Query][Connettere Excel a HDInsight mediante Power Query].

**Per recuperare l'output**

1.  Aprire una finestra di Azure PowerShell.
2.  Cambiare la directory in **C:\\Tutorials\\WordCountJava**. La cartella predefinita per Azure PowerShell è **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. I cmdlet eseguiti consentiranno di scaricare il file di output nella cartella corrente. Non si dispone di autorizzazioni per il download dei file nelle cartelle di sistema.
3.  Per impostare i valori, eseguire i comandi seguenti.

        $subscriptionName = "<AzureSubscriptionName>"
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

Al termine del processo sarà possibile esportare i dati in SQL Server o nel database SQL di Azure tramite [Sqoop][Sqoop] oppure esportare i dati in Excel.

## <span id="nextsteps"></span></a>Passaggi successivi

In questa esercitazione si è appreso come sviluppare un processo MapReduce Java, come testare l'applicazione in HDInsight Emulator e come scrivere uno script di PowerShell per eseguire il provisioning di un cluster HDInsight ed eseguire un processo MapReduce sul cluster. Per altre informazioni, vedere gli articoli seguenti:

-   [Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight][Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight]
-   [Introduzione all'utilizzo di Azure HDInsight][Introduzione all'utilizzo di Azure HDInsight]
-   [Introduzione all'uso di HDInsight Emulator][Introduzione all'uso di HDInsight Emulator]
-   [Uso dell'archiviazione BLOB di Azure con HDInsight][Uso dell'archiviazione BLOB di Azure con HDInsight]
-   [Amministrazione di HDInsight tramite PowerShell][Amministrazione di HDInsight tramite PowerShell]
-   [Caricare i dati in HDInsight][Caricare i dati in HDInsight]
-   [Usare Hive con HDInsight][Usare Hive con HDInsight]
-   [Usare Pig con HDInsight][Usare Pig con HDInsight]
-   [Connettere Excel a HDInsight mediante Power Query][Connettere Excel a HDInsight mediante Power Query]
-   [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver]

  [Introduzione all'uso di HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Apache Maven]: http://maven.apache.org/
  [Opzioni di acquisto]: http://azure.microsoft.com/it-it/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/it-it/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [Uso di Apache Maven per la creazione di un programma MapReduce per il conteggio di parole in Java]: #develop
  [Test del programma sull'emulatore]: #test
  [Caricamento di file di dati e dell'applicazione nell'archivio BLOB di Azure]: #upload
  [Esecuzione del programma MapReduce in Azure HDInsight]: #run
  [Recupero dei risultati di MapReduce]: #retrieve
  [Passaggi successivi]: #nextsteps
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [ricerca nel repository Maven]: http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [Caricare i dati in HDInsight]: ../hdinsight-upload-data/
  [HDI.EMulator.WordCount.Run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png
  [Assegnazione di nome e riferimento a contenitori, BLOB e metadati]: http://msdn.microsoft.com/it-it/library/windowsazure/dd135715.aspx
  [Uso di password, stringhe sicure e credenziali in Windows PowerShell]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Connettere Excel a HDInsight mediante Power Query]: ../hdinsight-connect-excel-power-query/
  [Sqoop]: ../hdinsight-use-sqoop/
  [Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Introduzione all'utilizzo di Azure HDInsight]: ../hdinsight-get-started/
  [Uso dell'archiviazione BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
