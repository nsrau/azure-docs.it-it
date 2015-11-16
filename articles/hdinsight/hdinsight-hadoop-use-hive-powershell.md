<properties
   pageTitle="Usare Hive di Hadoop con PowerShell in HDInsight | Microsoft Azure"
   description="Usare PowerShell per eseguire query Hive in Hadoop in HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/02/2015"
   ms.author="larryfr"/>

#Esecuzione di query Hive usando PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Questo documento fornisce un esempio di come usare Azure PowerShell nel gruppo di risorse Azure per eseguire query Hive in un cluster Hadoop in HDInsight. Per l'utilizzo di Azure PowerShell nella modalità di servizio di Azure, vedere [Eseguire query Hive utilizzando la modalità ASM di PowerShell ASM](hdinsight-hadoop-use-hive-powershell-v1.md).

> [AZURE.NOTE]Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).


**Prerequisiti**

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

- **Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Windows o basato su Linux)**
- **Una workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

##Eseguire query Hive usando Azure PowerShell

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota query Hive in HDInsight. Questo risultato si ottiene internamente usando chiamate REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (chiamato in precedenza Templeton) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di query Hive in un cluster HDInsight remoto, vengono usati i seguenti cmdlet:

* **Add-AzureRmAccount**: autentica Azure PowerShell nella sottoscrizione di Azure.

* **New-AzureRmHDInsightHiveJobDefinition**: crea una nuova *definizione del processo* usando le istruzioni HiveQL specificate.

* **Start-AzureRmHDInsightJob**: invia la definizione del processo a HDInsight, avvia il processo e restituisce un oggetto *job* che può essere usato per verificare lo stato del processo.

* **Wait-AzureRmHDInsightJob**: usa l'oggetto job per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.

* **Get-AzureRmHDInsightJobOutput**: viene usato per recuperare l'output del processo.

* **Invoke-AzureRmHDInsightHiveJob**: viene usato per eseguire istruzioni HiveQL. Questo blocca il completamento della query, quindi restituisce i risultati.

* **Use-AzureRmHDInsightCluster**: imposta il cluster corrente per l'uso per il comando **Invoke-AzureRmHDInsightHiveJob**.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight:

1. Usando un editor, salvare il seguente codice come **hivejob.ps1**. È necessario sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

		#Specify the values
		$clusterName = "CLUSTERNAME"
		$creds=Get-Credential
        		
		# Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureRmAccount
		}

		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green

		$hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds


		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Quindi, aprire un nuovo prompt dei comandi di **Azure PowerShell**. Passare al percorso del file **hivejob.ps1**, quindi usare il seguente comando per eseguire lo script:

		.\hivejob.ps1

    Quando viene eseguito lo script, verrà richiesto di immettere le credenziali dell'account HTTPS/Admin per il cluster. Si può anche richiedere di effettuare l’accesso alla sottoscrizione di Azure.
    
7. Quando viene completato, il processo dovrebbe restituire informazioni simili alle seguenti:

		Display the standard output...
		[ERROR]	3

4. Come accennato in precedenza, **Invoke-Hive** può essere usato per eseguire una query e attendere la risposta. Usare i seguenti comandi e sostituire **CLUSTERNAME** con il nome del cluster:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasb:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query @"
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
        SELECT * FROM errorLogs;
        "@

	L'output sarà simile al seguente:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE]Per query HiveQL più lunghe, è possibile usare il cmdlet **Here-Strings** di Azure PowerShell o un file di script HiveQL. Nel frammento di codice seguente viene illustrato come usare il cmdlet **Invoke-Hive** per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in wasb://.
	>
	> `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Per altre informazioni su **Here-Strings**, vedere <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Uso di Here-Strings di Windows PowerShell</a>.

##Risoluzione dei problemi

Se al termine del processo non vengono restituite informazioni, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni relative all'errore per questo processo, aggiungere quanto segue alla fine del file **hivejob.ps1**, salvare il file, quindi eseguirlo nuovamente.

	# Print the output of the Hive job.
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Vengono restituite le informazioni scritte in STDERR nel server durante l'esecuzione del processo. Tali informazioni possono essere utili per determinare la causa del problema.

##Riepilogo

Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

##Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Nov15_HO2-->