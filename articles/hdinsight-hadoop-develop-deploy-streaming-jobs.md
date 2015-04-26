<properties 
	pageTitle="Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight | Azure" 
	description="Informazioni sullo sviluppo di programmi MapReduce per la creazione di flussi Hadoop in C# e sulla distribuzione in Azure HDInsight." 
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
	ms.date="09/25/2014" 
	ms.author="jgao"/>



# Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight

In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. In questa esercitazione viene descritto uno scenario di conteggio parole in cui si contano le occorrenze di una determinata parola nei dati di input forniti. La seguente figura mostra come il framework MapReduce esegue un conteggio parole.

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

Questo argomento descrive come:

- Sviluppare e testare un programma per MapReduce per la creazione di flussi Hadoop con C# in un emulatore HDInsight
- Eseguire lo stesso processo MapReduce in Azure HDInsight 
- Recuperare i risultati del processo MapReduce

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario eseguire le operazioni seguenti:

- Installare Azure HDInsight Emulator. Per le istruzioni, vedere [Introduzione all'uso di HDInsight Emulator][hdinsight-get-started-emulator].
- Installare Azure PowerShell nel computer dell'emulatore. Per le istruzioni, vedere [Installare e configurare Azure PowerShell][powershell-install-configure].
- Ottenere una sottoscrizione di Azure. Per le istruzioni, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].

##Contenuto dell'articolo

- [Sviluppo di un programma per la creazione di flussi di Hadoop in C# per il conteggio delle parole](#develop)
- [Testare il programma sull'emulatore](#test)
- [Caricamento di dati e di applicazioni nell'archivio BLOB di Azure](#upload)
- [Eseguire il programma MapReduce in Azure HDInsight](#run)
- [Recuperare i risultati di MapReduce](#retrieve)
- [Passaggi successivi](#nextsteps)

##<a name="develop"></a>Sviluppo di un programma per la creazione di flussi di Hadoop in C&#35; per il conteggio delle parole

La soluzione per il conteggio delle parole include due progetti di applicazione console: l'applicazione per il mapping e per la riduzione. L'applicazione per il mapping crea il flusso di ogni parola nella console e l'applicazione per la riduzione conta il numero di parole di cui viene creato il flusso da un documento. Sia il mapper che il reducer leggono i caratteri, riga per riga, dal flusso di input standard (stdin) e scrivono nel flusso di output standard (stdout).

**Per creare un'applicazione console in C#**

1. Aprire Visual Studio 2013.
2. Fare clic su **FILE**, **Nuovo** e quindi su **Progetto**.
3. Digitare o selezionare i valori seguenti:

	<table border="1">
	<tr><td>Campo</td><td>Valore</td></tr>
	<tr><td>Modello</td><td>Visual C#/Windows/Console Application</td></tr>
	<tr><td>Nome</td><td>WordCountMapper</td></tr>
	<tr><td>Percorso</td><td>C:\Tutorials</td></tr>
	<tr><td>Nome soluzione</td><td>WordCount</td></tr>
	</table>
	
4. Fare clic su **OK** per creare il progetto.

**Per creare il programma per il mapping**

5. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Program.cs** e quindi scegliere **Rinomina**.
6. Rinominare il file in **WordCountMapper.cs** e quindi premere **INVIO**.
7. Fare clic su **Sì** per confermare l'assegnazione del nuovo nome a tutti i riferimenti.
8. Fare doppio clic su **WordCountMapper.cs** per aprirlo.
9. Aggiungere l'istruzione using seguente:

		using System.IO;

10. Sostituire la funzione Main() con il codice seguente:

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

11. Fare clic su **COMPILA** e quindi su **Compila soluzione** per compilare il programma per il mapping.	


**Per creare il programma per la riduzione**

1. In Visual Studio 2013 fare clic su **FILE**, quindi su **Aggiungi** e infine su **Nuovo progetto**.
2. Digitare o selezionare i valori seguenti:

	<table border="1">
	<tr><td>Campo</td><td>Valore</td></tr>
	<tr><td>Modello</td><td>Visual C#/Windows/Console Application</td></tr>
	<tr><td>Nome</td><td>WordCountReducer</td></tr>
	<tr><td>Percorso</td><td>C:\Tutorials\WordCount</td></tr>
	</table>
3. Deselezionare la casella di controllo **Crea directory per soluzione** e quindi fare clic su **OK** per creare il progetto.
4. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Program.cs** e quindi scegliere **Rinomina**.
5. Rinominare il file in **WordCountReducer.cs** e quindi premere **INVIO**.
7. Fare clic su **Sì** per confermare l'assegnazione del nuovo nome a tutti i riferimenti.
8. Fare doppio clic su **WordCountReducer.cs** per aprirlo.
9. Aggiungere l'istruzione using seguente:

		using System.IO;

10. Sostituire la funzione Main() con il codice seguente:

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

11. Fare clic su **COMPILA** e quindi su **Compila soluzione** per compilare il programma di riduzione.	

I file eseguibili per il programma di mapping e di riduzione si trovano nei percorsi seguenti:

- C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe
- C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe


##<a name="test"></a>Test del programma sull'emulatore

Eseguire le operazioni seguenti per testare il programma nell'emulatore:

1. Caricamento dei dati in HDFS nell'emulatore
2. Caricamento delle applicazioni Mapper e Reducer in HDFS nell'emulatore
3. Invio di un processo MapReduce per il conteggio delle parole
4. Verifica dello stato del processo
5. Recupero dei risultati del processo

Per impostazione predefinita, HDInsight Emulator usa HDFS come file system predefinito.  Facoltativamente, è possibile configurare HDInsight Emulator per l'uso dell'archiviazione BLOB di Azure. Per informazioni dettagliate, vedere [Introduzione all'uso di HDInsight Emulator][hdinsight-emulator-wasb]. In questa sezione verrà usato il comando *copyFromLocal* di HDFS per il caricamento dei file. Nella sezione successiva verrà illustrato come caricare i file usando Azure PowerShell. Per informazioni sugli altri metodi disponibili, vedere [Caricare i dati in HDInsight][hdinsight-upload-data].

In questa esercitazione viene usata la struttura di cartelle seguente:

<table border="1">
<tr><td>Cartella</td><td>Nota</td></tr>
<tr><td>\WordCount</td><td>Cartella radice per il progetto per il conteggio delle parole. </td></tr>
<tr><td>\WordCount\Apps</td><td>Cartella per i file eseguibili dei programmi di mapping e di riduzione.</td></tr>
<tr><td>\WordCount\Input</td><td>Cartella di file di origine di MapReduce.</td></tr>
<tr><td>\WordCount\Output</td><td>Cartella di file di output di MapReduce.</td></tr>
<tr><td>\WordCount\MRStatusOutput</td><td>Cartella di output del processo.</td></tr>
</table></br>

In questa esercitazione vengono usati i file con estensione txt disponibili nella directory %hadoop_home%.

> [AZURE.NOTE] I comandi HDFS di Hadoop rispettano la distinzione tra maiuscole e minuscole.

**Per copiare i file di testo in HDFS nell'emulatore**

1. Nella finestra della riga di comando di Hadoop eseguire il comando seguente per creare una directory per i file di input:

		hadoop fs -mkdir /WordCount/
		hadoop fs -mkdir /WordCount/Input

	Viene usato il percorso relativo, che equivale al percorso seguente:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Eseguire il comando seguente per copiare alcuni file di testo nella cartella di input in HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\share\doc\hadoop\common\*.txt \WordCount\Input

3. Eseguire il comando seguente per ottenere un elenco dei file caricati:

		hadoop fs -ls \WordCount\Input

	


**Per distribuire i programmi per il mapping e la riduzione in HDFS nell'emulatore**

1. Aprire la riga di comando di Hadoop dal desktop e creare la cartella /Apps in HDFS
		hadoop fs -mkdir /WordCount/Apps

2. Eseguire i comandi seguenti:

		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3. Eseguire il comando seguente per ottenere un elenco dei file caricati:

		hadoop fs -ls /WordCount/Apps

	Verranno visualizzati due file con estensione exe.


**Per eseguire il processo MapReduce tramite HDInsight PowerShell**

1. Aprire Azure PowerShell. Per le istruzioni, vedere [Installare e configurare Azure PowerShell][powershell-install-configure]. 
3. Per impostare le variabili, eseguire i comandi seguenti.

		$clusterName = "http://localhost:50111"
		
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "/WordCount/Input/"
		$mrOutput = "/WordCount/Output"
		$mrStatusOutput = "/WordCount/MRStatusOutput"

	Il nome del cluster di HDInsight Emulator è "http://localhost:50111".  

4. Eseguire i comandi seguenti per definire il processo di streaming:

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

5. Eseguire il comando seguente per creare un oggetto Credential:

		$creds = Get-Credential -Message "Enter password" -UserName "hadoop"

	Verrà richiesta l'immissione della password.  Per la password è possibile usare qualsiasi stringa.  Il nome utente deve essere "hadoop".

6. Eseguire i comandi seguenti per inviare il processo MapReduce e attendere il completamento del processo:
		
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
		
	È possibile vedere l'ID processo nell'output, ad esempio, *job-201311132317-0034*. 

**Per verificare lo stato del processo**

1. Sul desktop fare clic su **Hadoop YARN Status** oppure passare a **http://localhost:50030/jobtracker.jsp**.
2. Per individuare il processo, usare l'ID processo nella categoria **RUNNING** o **FINISHED**. 
3. Un processo non completato sarà reperibile nella categoria **FAILED**. Sarà anche possibile visualizzare i dettagli relativi al processo e trovare informazioni utili per il debug.


**Per visualizzare l'output da HDFS**

1. Aprire la riga di comando di Hadoop.
2. Eseguire il comando seguente per visualizzare l'output:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	È possibile aggiungere "|more" alla fine del comando per ottenere la visualizzazione della pagina

##<a id="upload"></a>Caricamento di dati nell'archivio BLOB di Azure
Azure HDInsight usa l'archivio BLOB di Azure come file system predefinito. È possibile configurare un cluster HDInsight per l'utilizzo di archiviazione BLOB aggiuntiva per i file di dati. In questa sezione verrà creato un account di archiviazione e i file di dati verranno caricati nell'archiviazione BLOB. I file di dati sono file con estensione txt disponibili nella directory %hadoop_home%\share\doc\hadoop\common.


**Per creare un archivio BLOB e un contenitore**

1. Aprire Azure PowerShell.
2. Impostare le variabili, quindi eseguire i comandi:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Eseguire il comando seguente per creare un account di archiviazione e un contenitore di archiviazione BLOB nell'account.

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Eseguire i comandi seguenti per verificare l'account e il contenitore di archiviazione:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**Per caricare i file di dati**

1. Nella finestra di Azure PowerShell impostare i valori per la cartella locale e le cartelle di destinazione.

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Si noti che la cartella di file di origine locale è **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common** e la cartella di destinazione è **WordCount/Input**. Il percorso di origine è il percorso dei file txt nell'emulatore HDInsight. La destinazione è la struttura di cartelle che sarà rispecchiata nel contenitore BLOB di Azure.

3. Eseguire i comandi seguenti per ottenere un elenco dei file con estensione txt disponibili nella cartella di file di origine:

		# Get a list of the txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
5. Eseguire il frammento seguente per copiare i file:

		# Copy the file from local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Eseguire il comando seguente per ottenere un elenco dei file caricati:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**Per caricare l'applicazione per il conteggio delle parole**

1. Nella finestra di Azure PowerShell impostare le variabili seguenti:

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Si noti che la cartella di destinazione è **WordCount/Apps**, che è la struttura che sarà rispecchiata nel contenitore BLOB di Azure.

2. Eseguire i comandi seguenti per copiare le applicazioni:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. Eseguire il comando seguente per ottenere un elenco dei file caricati:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	Verranno elencati entrambi i file dell'applicazione.


##<a name="run"></a>Esecuzione del processo MapReduce in Azure HDInsight

Questa sezione contiene uno script di PowerShell che esegue tutte le attività correlate all'esecuzione di un MapReduce. L'elenco di attività include:

1. Provisioning di un cluster HDInsight
	
	1. Creazione di un account di archiviazione che verrà usato come file system predefinito del cluster HDInsight
	2. Creazione di un contenitore di archiviazione BLOB 
	3. Creazione di un cluster HDInsight

2. Invio del processo MapReduce

	1. Creazione di una definizione del processo MapReduce di streaming
	2. Invio di un processo MapReduce
	3. Attesa del completamento del processo
	4. Visualizzazione degli errori standard
	5. Visualizzazione dell'output standard

3. Eliminazione del cluster

	1. Eliminazione del cluster HDInsight
	2. Eliminazione dell'account di archiviazione usato come file system predefinito del cluster HDInsight


**Per eseguire lo script di PowerShell**

1. Aprire il Blocco note.
2. Copiare e incollare il codice seguente:
		
		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### prefix to cluster, storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### must match the data storage account location
		$clusterNodes = 1
		
		$clusterName = $stringPrefix + "hdicluster"
		
		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		Select-AzureSubscription $subscriptionName
		
		#====== CREATE A STORAGE ACCOUNT ======
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#====== CREATE A BLOB STORAGE CONTAINER ======
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#====== CREATE AN HDINSIGHT CLUSTER ======
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		Select-AzureSubscription $subscriptionName
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config
		
		#====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green
		
		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)
		
		#====== RUN A STREAMING MAPREDUCE JOB ======
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
		
		#====== DELETE THE HDINSIGHT CLUSTER ======
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		Remove-AzureHDInsightCluster -Name $clusterName 
		
		#====== DELETE THE STORAGE ACCOUNT ======
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Impostare le prime quattro variabili nello script. **$stringPrefix** verrà usato per aggiungere la stringa specificata come prefisso del nome del cluster HDInsight, il nome dell'account di archiviazione e il nome del contenitore di archiviazione BLOB. Poiché la lunghezza di questi nomi deve essere compresa tra 3 e 24 caratteri, assicurarsi che la stringa specificata e i nomi usati dallo script combinati non superino il limite di caratteri fissato per il nome. È necessario usare lettere minuscole per $stringPrefix.

	 **$storageAccountName_Data** e **$containerName_Data** corrispondono all'account e al contenitore di archiviazione già creati nei passaggi precedenti, quindi è necessario specificarne i nomi. Vengono usati per archiviare i file di dati e le applicazioni. $location deve corrispondere alla posizione dell'account di archiviazione dei dati.
4. Esaminare le variabili rimanenti.
5. Salvare il file di script.
6. Aprire Azure PowerShell.
7. Eseguire il comando seguente per impostare i criteri di esecuzione su remotesigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Quando richiesto, immettere nome utente e password per il cluster HDInsight. Assicurarsi che la password sia almeno di 10 caratteri, inclusi una lettera maiuscola, una lettera minuscola, un numero e un carattere speciale. Se non si vuole che venga richiesta l'immissione di credenziali, vedere [Uso di password, stringhe sicure e credenziali in Windows PowerShell][powershell-PSCredential].

Per un esempio di HDInsight .NET SDK sull'invio di processi di streaming di Hadoop, vedere [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs].


##<a name="retrieve"></a>Recupero dell'output del processo MapReduce
In questa sezione viene illustrato come scaricare e visualizzare l'output.  Per informazioni sulla visualizzazione dei risultati in Excel, vedere [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][hdinsight-ODBC] e [Connettere Excel a HDInsight mediante Power Query][hdinsight-power-query].


**Per recuperare l'output**

1. Aprire una finestra di Azure PowerShell.
2. Impostare i valori, quindi eseguire i comandi:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"
	
3. Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

4. Eseguire i comandi seguenti per scaricare e visualizzare l'output:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

	

##<a id="nextsteps"></a>Passaggi successivi
In questa esercitazione si è appreso come sviluppare un processo MapReduce di streaming per Hadoop, come testare l'applicazione in HDInsight Emulator e come scrivere uno script di PowerShell per eseguire il provisioning di un cluster HDInsight ed eseguire un processo MapReduce sul cluster. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione all'uso di Azure HDInsight](hdinsight-get-started.md)
- [Introduzione all'uso di HDInsight Emulator][hdinsight-get-started-emulator]
- [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]
- [Usare ll'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]
- [Amministrare HDInsight tramite PowerShell][hdinsight-admin-powershell]
- [Caricare i dati in HDInsight][hdinsight-upload-data]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Usare Pig con HDInsight][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator/#blobstorage
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[p	owershell-install-configure]: ../install-configure-powershell/

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "MapReduce wordcount application flow"







<!--HONumber=42-->
