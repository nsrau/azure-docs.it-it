<properties
   pageTitle="Usare MapReduce e PowerShell con Hadoop | Microsoft Azure"
   description="Informazioni su come usare PowerShell per eseguire in modalità remota processi MapReduce con Hadoop in HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Esecuzione di query Hive con Hadoop in HDInsight mediante PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Questo documento fornisce un esempio di come usare Azure PowerShell per eseguire un processo MapReduce in un cluster Hadoop in HDInsight.

##<a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

- **Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Windows o basato su Linux)**

- **Una workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

##<a id="powershell"></a>Eseguire un processo MapReduce con Azure PowerShell

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota processi MapReduce in HDInsight. Questo risultato viene ottenuto internamente usando chiamate REST a <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (chiamato in precedenza Templeton) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di processi MapReduce in un cluster HDInsight remoto, vengono usati i seguenti cmdlet.

* **Add-AzureAccount**: autentica Azure PowerShell nella sottoscrizione di Azure.

* **New-AzureHDInsightMapReduceJobDefinition**: crea una nuova *definizione di processo* usando le informazioni MapReduce specificate.

* **Start-AzureHDInsightJob**: invia la definizione del processo a HDInsight, avvia il processo e restituisce un oggetto *job* che può essere usato per verificare lo stato del processo.

* **Wait-AzureHDInsightJob**: usa l'oggetto job per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.

* **Get-AzureHDInsightJobOutput**: viene usato per recuperare l'output del processo.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight.

1. Usando un editor, salvare il seguente codice come **mapreducejob.ps1**. È necessario sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
		                          -ClassName "wordcount" `
		                          -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardOutput

2. Quindi, aprire un nuovo prompt dei comandi di **Azure PowerShell**. Passare al percorso del file **mapreducejob.ps1**, quindi usare il seguente comando per eseguire lo script.

		.\mapreducejob.ps1

3. Al termine del processo, viene visualizzato un output simile al seguente:

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	Questo output indica che il processo è stato completato correttamente.
	
	> [AZURE.NOTE]Se **ExitCode** è un valore diverso da 0, vedere [Risoluzione dei problemi](#troubleshooting).

##<a id="results"></a>Visualizzare l'output del processo

Il processo MapReduce archivia i risultati dell'operazione nell'archivio BLOB di Azure, nel percorso **wasb:///example/data/WordCountOutput** specificato come argomento del processo. L'archivio BLOB di Azure è accessibile tramite Azure PowerShell, tuttavia per accedere direttamente ai file è necessario conoscere il nome e la chiave dell'account di archiviazione e il contenitore usato dal cluster HDInsight.

È possibile ottenere queste informazioni usando i seguenti cmdlet di Azure PowerShell:

* **Get-AzureHDInsightCluster**: restituisce informazioni su un cluster HDInsight, inclusi eventuali account di archiviazione associati. Sarà sempre presente un account di archiviazione predefinito associato al cluster.
* **New-AzureStorageContext**: dati il nome e la chiave dell'account di archiviazione ottenuti mediante **Get-AzureHDInsightCluster**, restituisce un oggetto di contesto che può essere usato per accedere all'account di archiviazione.
* **Get-AzureStorageBlob**: dati un oggetto di contesto e un nome contenitore, restituisce un elenco di BLOB nel contenitore.
* **Get-AzureStorageBlobContent**: dati un oggetto di contesto, un nome e un percorso di file e un nome contenitore (restituiti da **Get-AzureHDinsightCluster**), scarica un file dall'archivio BLOB di Azure.

Il seguente esempio recupera le informazioni di archiviazione, quindi scarica l'output da **wasb:///example/data/WordCountOutput**. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Retrieve the cluster information
		$clusterInfo = Get-AzureHDInsightCluster -ClusterName $clusterName

		#Get the storage account information
		$storageAccountName = $clusterInfo.DefaultStorageAccount.StorageAccountName
		$storageAccountKey = $clusterInfo.DefaultStorageAccount.StorageAccountKey
		$storageContainer = $clusterInfo.DefaultStorageAccount.StorageContainerName

		#Create the context object
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		#Download the files from wasb:///example/data/WordCountOutput
		#Use the -blob switch to filter only blobs contained in example/data/WordCountOutput
		Get-AzureStorageBlob -Container $storageContainer -Blob example/data/WordCountOutput/* -Context $context | Get-AzureStorageBlobContent -Context $context

> [AZURE.NOTE]Questo esempio permette di archiviare i file scaricati nella cartella **example/data/WordCountOutput** della directory da cui è stato eseguito lo script.

L'output del processo MapReduce è archiviato nei file con il nome *part-r-#####*. Per vedere le parole e i numeri generati dal processo, aprire il file **example/data/WordCountOutput/part-r-00000** in un editor di testo.

> [AZURE.NOTE]I file di output di un processo MapReduce non sono modificabili. Se pertanto si esegue di nuovo l'esempio, è necessario cambiare il nome del file di output.

##<a id="troubleshooting"></a>Risoluzione dei problemi

Se al termine del processo non vengono restituite informazioni, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni relative all'errore per questo processo, aggiungere il seguente comando alla fine del file **mapreducejob.ps1**, salvare il file, quindi eseguirlo nuovamente.

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

Vengono restituite le informazioni scritte in STDERR nel server durante l'esecuzione del processo. Tali informazioni possono essere utili per determinare la causa del problema.

##<a id="summary"></a>Riepilogo

Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire processi MapReduce in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce in HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
 

<!---HONumber=62-->