<properties
   pageTitle="Usare Hive di Hadoop in HDInsight | Azure"
   description="Informazioni sull'uso di Hive di Hadoop in HDInsight tramite PowerShell."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags 
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Esecuzione di query Hive usando PowerShell

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Questo documento fornisce un esempio di come usare PowerShell per eseguire query Hive in un cluster Hadoop in HDInsight.

> [AZURE.NOTE] Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere <a href="../hdinsight-use-hive/" target="_blank">Usare Hive con Hadoop in HDInsight</a>.


## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Windows o su Linux)

* <a href="http://azure.microsoft.com/ documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


## <a id="powershell"></a>Eseguire query Hive usando PowerShell

Azure PowerShell fornisce *cmdlets* che consentono di eseguire in modalità remota query Hive in HDInsight. Internamente ciò avviene tramite chiamate REST a <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (precedentemente denominato Templeton) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di query Hive in un cluster HDInsight remoto, vengono usati i seguenti cmdlet.

* **Add-AzureAccount**: autentica PowerShell nella sottoscrizione Azure.

* **New-AzureHDInsightHiveJobDefinition**: crea una nuova *job definition* usando le istruzioni HiveQL specificate.

* **Start-AzureHDInsightJob**: invia la definizione del processo a HDInsight, avvia il processo e restituisce un oggetto *job* che può essere usato per verificare lo stato del processo.

* **Wait-AzureHDInsightJob**: usa l'oggetto job per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.

* **Get-AzureHDInsightJobOutput**: viene usato per recuperare l'output del processo.

* **Invoke-Hive**: viene usato per eseguire istruzioni HiveQL e bloccarne il completamento. Quindi restituisce i risultati.

* **Use-AzureHDInsightCluster**: imposta il cluster corrente per l'uso per il comando **Invoke-Hive**.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight. 

1. Usando un editor, salvare il seguente codice come **hivejob.ps1**. È necessario sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}
		
		#Specify the cluster name
		$clusterName = "CLUSTERNAME" 
		
		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString
		
		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. Aprire un nuovo prompt dei comandi di **Microsoft Azure PowerShell**. Passare al percorso del file **hivejob.ps1**, quindi usare quanto segue per eseguire lo script.

		.\hivejob.ps1

7. Quando viene completato, il processo dovrebbe restituire informazioni simili alle seguenti.

		Display the standard output...
		[ERROR]	3

4. Come accennato in precedenza, **Invoke-Hive** può essere usato per eseguire una query e attendere la risposta. Usare i seguenti comandi, sostituendo **CLUSTERNAME** con il nome del cluster.

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	L'output sarà simile al seguente:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id	
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id	
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] Per query HiveQL più lunghe, è possibile usare stringhe Here di PowerShell o un file di script HiveQL. Il frammento di codice seguente illustra come usare il cmdlet *Invoke-Hive* per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Per altre informazioni sulle stringhe Here, vedere l'articolo relativo all' <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">uso di stringhe Here di Windows PowerShell</a>.

## <a id="troubleshooting"></a>Risoluzione dei problemi

Se al termine del processo non vengono restituite informazioni, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni relative all'errore per questo processo, aggiungere quanto segue alla fine del file **hivejob.ps1**, salvare il file, quindi eseguirlo nuovamente.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

Verranno restituite le informazioni scritte in STDERR nel server durante l'esecuzione del processo. Tali informazioni possono essere utili per determinare la causa del problema.

## <a id="summary"></a>Riepilogo

Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight.

* [Usare Hive con Hadoop in HDInsight](../hdinsight-use-hive/)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Pig con Hadoop in HDInsight](../hdinsight-use-pig/)

* [Usare MapReduce con Hadoop in HDInsight](../hdinsight-use-mapreduce/)

<!--HONumber=45--> 
