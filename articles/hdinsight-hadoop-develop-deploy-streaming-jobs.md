<properties linkid="manage-services-hdinsight-develop-hadoop-streaming-programs-for-hdinsight" urlDisplayName="" pageTitle="Develop C# Hadoop streaming programs for HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="Learn how to develop Hadoop streaming MapReduce programs in C#, and how to deploy them to Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Develop C# Hadoop streaming programs for HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight

In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. In questa esercitazione è descritto in modo dettagliato uno scenario end-to-end, dallo sviluppo e il test di un programma MapReduce per lo streaming in Hadoop tramite C# in HDInsight Emulator all'esecuzione del processo MapReduce in Azure HDInsight e il recupero dei risultati.

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   Installare Azure HDInsight Emulator. Per istruzioni, vedere [Introduzione all'utilizzo di HDInsight Emulator][].
-   Installare Azure PowerShell nel computer dell'emulatore. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][]
-   Ottenere una sottoscrizione di Azure. Per istruzioni, vedere [Opzioni di acquisto][], [Offerte per i membri][] oppure [Versione di valutazione gratuita][].

## Contenuto dell'articolo

-   [Sviluppo di un programma per la creazione di flussi di Hadoop in C# per il conteggio delle parole][]
-   [Test del programma sull'emulatore][]
-   [Caricamento di dati e di applicazioni nell'archivio BLOB di Azure][]
-   [Esecuzione del programma MapReduce in Azure HDInsight][]
-   [Recupero dei risultati di MapReduce][]
-   [Passaggi successivi][]

## <a name="develop"></a>Sviluppo di un programma per la creazione di flussi di Hadoop in C# per il conteggio delle parole

La soluzione per il conteggio delle parole include due progetti di applicazione console: l'applicazione per il mapping e per la riduzione. L'applicazione per il mapping crea il flusso di ogni parola nella console e l'applicazione per la riduzione conta il numero di parole di cui viene creato il flusso da un documento. Sia il mapper che il reducer leggono i caratteri, riga per riga, dal flusso di input standard (stdin) e scrivono nel flusso di output standard (stdout).

**Per creare un'applicazione console in C#**

1.  Aprire Visual Studio 2013.
2.  Fare clic su **FILE**, **Nuovo** e quindi su **Progetto**.
3.  Digitare o selezionare i valori seguenti:

    |----------------|----------------------------------------|
    | Campo          | Valore                                 |
    | Modello        | Visual C#/Windows/Console Application |
    | Nome           | WordCountMapper                        |
    | Percorso       | C:\\Tutorials                          |
    | Nome soluzione | WordCount                              |

4.  Fare clic su **OK** per creare il progetto.

**Per creare il programma per il mapping**

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Program.cs** e quindi scegliere **Rinomina**.
2.  Rinominare il file in **WordCountMapper.cs** e quindi premere **INVIO**.
3.  Fare clic su **Sì** per confermare l'assegnazione del nuovo nome a tutti i riferimenti.
4.  Fare doppio clic su **WordCountMapper.cs** per aprirlo.
5.  Aggiungere l'istruzione using seguente:

        using System.IO;

6.  Sostituire la funzione Main() con il codice seguente:

        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            string[] words;

            while ((line = Console.ReadLine()) != null)
            {
                words = line.Split(' ');

                foreach (string word in words)
                    Console.WriteLine(word.ToLower());
            }
        }

7.  Fare clic su **COMPILA** e quindi su **Compila soluzione** per compilare il programma per il mapping.

**Per creare il programma per la riduzione**

1.  In Visual Studio 2013 fare clic su **FILE**, quindi su **Aggiungi** e infine su **Nuovo progetto**.
2.  Digitare o selezionare i valori seguenti:

    | Campo    | Valore                                 |
    |----------|----------------------------------------|
    | Modello  | Visual C#/Windows/Console Application |
    | Nome     | WordCountReducer                       |
    | Percorso | C:\\Tutorials\\WordCount               |

3.  Fare clic su **OK** per creare il progetto.
4.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Program.cs** e quindi scegliere **Rinomina**.
5.  Rinominare il file in **WordCountReducer.cs** e quindi premere **INVIO**.
6.  Fare clic su **Sì** per confermare l'assegnazione del nuovo nome a tutti i riferimenti.
7.  Fare doppio clic su **WordCountReducer.cs** per aprirlo.
8.  Aggiungere l'istruzione using seguente:

        using System.IO;

9.  Sostituire la funzione Main() con il codice seguente:

        static void Main(string[] args)
        {
            string word, lastWord = null;
            int count = 0;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            while ((word = Console.ReadLine()) != null)
            {
                if (word != lastWord)
                {
                    if(lastWord != null)
                        Console.WriteLine("{0}[{1}]", lastWord, count);

                    count = 1;
                    lastWord = word;
                }
                else
                {
                    count += 1; 
                }
            }
            Console.WriteLine(count);
        }

10. Fare clic su **COMPILA** e quindi su **Compila soluzione** per compilare la soluzione.

I file eseguibili per il programma di mapping e di riduzione si trovano nei percorsi seguenti:

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

## <a name="test"></a>Test del programma sull'emulatore

In questa sezione sono disponibili le procedure seguenti:

1.  Caricamento dei dati in HDFS nell'emulatore
2.  Caricamento dei programmi per il mapping e la riduzione in HDFS nell'emulatore
3.  Invio di un processo MapReduce per il conteggio delle parole
4.  Verifica dello stato del processo
5.  Recupero dei risultati del processo

Per impostazione predefinita, HDInsight Emulator utilizza HDFS come file system predefinito. Se lo si desidera, è possibile configurare HDInsight Emulator per l'utilizzo dell'archivio BLOB di Azure. Per informazioni dettagliate, vedere [Introduzione all'utilizzo di HDInsight Emulator][1]. In questa sezione verrà utilizzato il comando copyFromLocal di HDFS per il caricamento dei file. Nella sezione successiva verrà illustrato come caricare i file utilizzando Azure PowerShell. Per informazioni sugli altri metodi disponibili, vedere [Caricare i dati in HDInsight][].

In questa esercitazione viene utilizzata la struttura di cartelle seguente:

| Cartella                    | Nota                                                                    |
|-----------------------------|-------------------------------------------------------------------------|
| \\WordCount                 | Cartella radice per il progetto per il conteggio delle parole.          |
| \\WordCount\\Apps           | Cartella per i file eseguibili dei programmi di mapping e di riduzione. |
| \\WordCount\\Input          | Cartella di file di origine di MapReduce.                               |
| \\WordCount\\Output         | Cartella di file di output di MapReduce.                                |
| \\WordCount\\MRStatusOutput | Cartella di output del processo.                                        |

</br>

In questa esercitazione vengono utilizzati i file con estensione txt disponibili nella directory %hadoop\_home%.

> [WACOM.NOTE] I comandi HDFS di Hadoop rispettano la distinzione tra maiuscole e minuscole.

**Per copiare i file di testo in HDFS nell'emulatore**

1.  Nella finestra della riga di comando di Hadoop eseguire il comando seguente per creare una directory per i file di input:

        hadoop fs -mkdir /WordCount/Input

    Viene utilizzato il percorso relativo, che equivale al percorso seguente:

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  Eseguire il comando seguente per copiare alcuni file di testo nella cartella di input in HDFS:

        hadoop fs -copyFromLocal %hadoop_home%\*.txt \WordCount\Input

3.  Eseguire il comando seguente per ottenere un elenco dei file caricati:

        hadoop fs -ls \WordCount\Input

    Dovrebbero essere visualizzati circa otto file con estensione txt.

**Per distribuire i programmi per il mapping e la riduzione in HDFS nell'emulatore**

1.  Aprire la riga di comando di Hadoop dal desktop.
2.  Eseguire i comandi seguenti:

        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  Eseguire il comando seguente per ottenere un elenco dei file caricati:

        hadoop fs -lsr /WordCount/Apps

    Verranno visualizzati due file con estensione exe.

**Per eseguire il processo MapReduce tramite HDInsight PowerShell**

1.  Aprire Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][].
2.  Per impostare le variabili, eseguire i comandi seguenti.

        $clusterName = "http://localhost:50111"

        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "/WordCount/Input/"
        $mrOutput = "/WordCount/Output"
        $mrStatusOutput = "/WordCount/MRStatusOutput"

    Il nome del cluster di HDInsight Emulator è "<http://localhost:50111>".

3.  Eseguire i comandi seguenti per definire il processo di streaming:

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

4.  Eseguire il comando seguente per creare un oggetto Credential:

        $creds = Get-Credential -Message "Enter password" -UserName "hadoop"

    Verrà richiesta l'immissione della password. Per la password è possibile utilizzare qualsiasi stringa. Il nome utente deve essere "hadoop".

5.  Eseguire i comandi seguenti per inviare il processo MapReduce e attendere il completamento del processo:

        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
        Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

    Al termine, si otterrà un output analogo al seguente:

        StatusDirectory : /WordCount/MRStatusOutput
        ExitCode        : 
        Name            : mrWordCountStreamingJob
        Query           : 
        State           : Completed
        SubmissionTime  : 11/15/2013 7:18:16 PM
        Cluster         : http://localhost:50111
        PercentComplete : map 100%  reduce 100%
        JobId           : job_201311132317_0034

    Si saprà quindi, ad esempio, che l'ID del processo è job-201311132317-0034.

**Per verificare lo stato del processo**

1.  Sul desktop fare clic su **Hadoop MapReduce Status**, oppure passare a **<http://localhost:50030/jobtracker.jsp>**.
2.  Individuare il processo che utilizza l'ID del processo in una delle tre sezioni seguenti: **Completed Jobs**, **Running Jobs**, **Retired Jobs**.
3.  Se un processo ha avuto esito negativo, sarà possibile visualizzare i dettagli relativi al processo e trovare informazioni utili per il debug.

**Per visualizzare l'output da HDFS**

1.  Aprire la riga di comando di Hadoop.
2.  Eseguire il comando seguente per visualizzare l'output:

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    È possibile aggiungere "|more" alla fine del comando per ottenere la visualizzazione della pagina

## <span id="upload"></span></a>Caricamento di dati nell'archivio BLOB di Azure

Azure HDInsight utilizza l'archivio BLOB di Azure come file system predefinito. È possibile configurare un cluster HDInsight per l'utilizzo di archiviazione BLOB aggiuntiva per i file di dati. In questa sezione verrà creato un account di archiviazione e i file di dati verranno caricati nell'archiviazione BLOB. I file di dati sono file con estensione txt disponibili nella directory %hadoop\_home%.

**Per creare un archivio BLOB e un contenitore**

1.  Aprire Azure PowerShell.
2.  Impostare le variabili, quindi eseguire i comandi:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  Eseguire il comando seguente per creare un account di archiviazione e un contenitore di archiviazione BLOB nell'account.

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName -Context $destContext

4.  Eseguire i comandi seguenti per verificare l'account e il contenitore di archiviazione:

        Get-AzureStorageAccount -StorageAccountName $storageAccountName
        Get-AzureStorageContainer -Context $destContext

**Per caricare i file di dati**

1.  Aprire Azure PowerShell.
2.  Impostare le prime tre variabili, quindi eseguire i comandi seguenti:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
        $destFolder = "WordCount/Input"

    Si noti che la cartella di file di origine è **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** e la cartella di destinazione è **WordCount/Input**.

3.  Eseguire i comandi seguenti per ottenere un elenco dei file con estensione txt disponibili nella cartella di file di origine:

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Per creare un oggetto contesto archiviazione, eseguire i comandi seguenti:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5.  Eseguire i comandi seguenti per copiare i file:

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
        }

6.  Eseguire il comando seguente per ottenere un elenco dei file caricati:

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**Per caricare l'applicazione per il conteggio delle parole**

1.  Aprire Azure PowerShell.
2.  Impostare le prime tre variabili, quindi eseguire i comandi seguenti:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
        $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
        $blobFolder = "WordCount/Apps"

    Si noti che la cartella di destinazione è **WordCount/Apps**.

3.  Per creare un oggetto contesto archiviazione, eseguire i comandi seguenti:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

4.  Eseguire i comandi seguenti per copiare le applicazioni:

        Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
        Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

5.  Eseguire il comando seguente per ottenere un elenco dei file caricati:

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    Entrambi i file dovrebbero essere inclusi nell'elenco.

## <a name="run"></a>Esecuzione del processo MapReduce in Azure HDInsight

Lo script PowerShell riportato di seguito consente di eseguire le attività seguenti:

1.  Provisioning di un cluster HDInsight

    1.  Creazione di un account di archiviazione che verrà utilizzato come file system predefinito del cluster HDInsight
    2.  Creazione di un contenitore di archiviazione BLOB
    3.  Creazione di un cluster HDInsight

2.  Invio del processo MapReduce

    1.  Creazione di una definizione del processo MapReduce di streaming
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
        $subscriptionName = "<AzureSubscriptionName>"
        $serviceNameToken = "<ServiceNameTokenString>"
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = 1

        $clusterName = $serviceNameToken + "hdicluster"

        $storageAccountName_Default = $serviceNameToken + "hdistore"
        $containerName_Default =  $serviceNameToken + "hdicluster"

        # The streaming MapReduce job variables
        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        Select-AzureSubscription $subscriptionName

        #=============================
        # Create a storage account
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #=============================
        # Create a Blob storage container
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

        New-AzureHDInsightCluster -Subscription $subscriptionName  -Name $clusterName -Location $location -Config $config

        #=============================
        # Create a streaming MapReduce job definition
        Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

        #=============================
        # Run a streaming MapReduce job
        Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Subscription $subscriptionName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Subscription $subscriptionName -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardOutput

        #=============================
        # Delete the HDInsight cluster
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Remove-AzureHDInsightCluster -Name $clusterName -Subscription $subscriptionName 

        # Delete the storage account
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Impostare le prime quattro variabili nello script. $serviceNameToken verrà utilizzato per il nome del cluster HDInsight, il nome dell'account di archiviazione e il nome del contenitore di archiviazione BLOB. Poiché il nome del servizio deve avere una lunghezza compresa tra 3 e 24 caratteri e lo script aggiunge ai nomi una stringa di un massimo di 10 caratteri, è necessario limitare la stringa a un massimo di 14 caratteri. È inoltre necessario utilizzare lettere minuscole per $serviceNameToken. $storageAccountName\_Data e $containerName\_Data corrispondono all'account e al contenitore di archiviazione utilizzati per archiviare i file di dati e le applicazioni. $location deve corrispondere alla posizione dell'account di archiviazione dei dati.
4.  Esaminare le variabili rimanenti.
5.  Salvare il file di script.
6.  Aprire Azure PowerShell.
7.  Eseguire il comando seguente per impostare i criteri di esecuzione su remotesigned:

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Quando richiesto, immettere nome utente e password per il cluster HDInsight. Poiché il cluster verrà eliminato alla fine dello script e il nome utente e la password non saranno più necessari, è possibile specificare qualsiasi stringa come nome utente e password. Se non si desidera che venga richiesta l'immissione di credenziali, vedere [Utilizzo di password, stringhe sicure e credenziali in Windows PowerShell][]

Per un esempio di HDInsight .NET SDK sull'invio di processi di streaming di Hadoop, vedere [Inviare processi Hadoop a livello di codice][].

## <a name="retrieve"></a>Recupero dell'output del processo MapReduce

In questa sezione viene illustrato come scaricare e visualizzare l'output. Per informazioni sulla visualizzazione dei risultati in Excel, vedere [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][] e [Connettere Excel a HDInsight mediante Power Query][].

**Per recuperare l'output**

1.  Aprire una finestra di Azure PowerShell.
2.  Impostare i valori, quindi eseguire i comandi:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<TheDataStorageAccountName>"
        $containerName = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-00000"

3.  Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti:

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  Eseguire i comandi seguenti per scaricare e visualizzare l'output:

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

## <span id="nextsteps"></span></a>Passaggi successivi

In questa esercitazione si è appreso come sviluppare un processo MapReduce di streaming per Hadoop, come testare l'applicazione in HDInsight Emulator e come scrivere uno script di PowerShell per eseguire il provisioning di un cluster HDInsight ed eseguire un processo MapReduce sul cluster. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight][]
-   [Introduzione all'utilizzo di HDInsight Emulator][]
-   [Sviluppare programmi MapReduce Java per HDInsight][]
-   [Usare l'archivio BLOB di Azure con HDInsight][]
-   [Amministrazione di HDInsight tramite PowerShell][]
-   [Caricare i dati in HDInsight][]
-   [Usare Hive con HDInsight][]
-   [Usare Pig con HDInsight][]

  [Introduzione all'utilizzo di HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Opzioni di acquisto]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Sviluppo di un programma per la creazione di flussi di Hadoop in C# per il conteggio delle parole]: #develop
  [Test del programma sull'emulatore]: #test
  [Caricamento di dati e di applicazioni nell'archivio BLOB di Azure]: #upload
  [Esecuzione del programma MapReduce in Azure HDInsight]: #run
  [Recupero dei risultati di MapReduce]: #retrieve
  [Passaggi successivi]: #nextsteps
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [Caricare i dati in HDInsight]: ../hdinsight-upload-data/
  [Utilizzo di password, stringhe sicure e credenziali in Windows PowerShell]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Connettere Excel a HDInsight mediante Power Query]: ../hdinsight-connect-excel-power-query/
  [Introduzione all'utilizzo di Azure HDInsight]: ../hdinsight-get-started/
  [Sviluppare programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
