<properties 
	pageTitle="Gestire cluster Hadoop in HDInsight con PowerShell | Microsoft Azure" 
	description="Informazioni su come eseguire attività amministrative per i cluster Hadoop in HDInsight tramite Azure PowerShell." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="jgao"/>

# Gestire cluster Hadoop in HDInsight tramite Azure PowerShell


Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. In questo articolo verrà illustrato come gestire cluster Hadoop in Azure HDInsight usando una console di Azure PowerShell locale tramite Windows PowerShell. Per l'elenco dei cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight][hdinsight-powershell-reference].

##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##Provisioning di cluster HDInsight
HDInsight usa un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, sono necessari un account di archiviazione di Azure e un contenitore di archiviazione.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Per creare un account di archiviazione di Azure**

Dopo avere importato il file di impostazioni di pubblicazione è possibile usare il comando seguente per creare un account di archiviazione:

	# Create an Azure Storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location


[AZURE.INCLUDE [Elenco di data center](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Per informazioni sulla creazione di un account di archiviazione di Azure tramite il portale di Azure, vedere [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/).

Se si ha già di un account di archiviazione, ma non si conosce il nome dell'account e la chiave dell'account, è possibile usare i comandi seguenti per recuperare le informazioni:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageKey <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di Azure, vedere la sezione "Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/).

**Per creare un contenitore di archiviazione di Azure**

Azure PowerShell non è in grado di creare un contenitore BLOB durante il processo di provisioning di HDInsight. È possibile crearne uno usando lo script seguente:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Per effettuare il provisioning di un cluster**

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


Nella schermata seguente è illustrata l'esecuzione dello script:

![HDI.PS.Provision][image-hdi-ps-provision]




##Dettagli dell'elenco di cluster
Usare il comando seguente per visualizzare l'elenco di tutti i cluster nella sottoscrizione corrente:

	Get-AzureHDInsightCluster 

Usare il comando seguente per visualizzare i dettagli di un cluster specifico nella sottoscrizione corrente:

	Get-AzureHDInsightCluster -Name <ClusterName> 

##Eliminare cluster
Utilizzare il comando seguente per eliminare un cluster:

	Remove-AzureHDInsightCluster -Name <ClusterName> 



##Concedere/revocare l'accesso ai servizi HTTP

Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Per impostazione predefinita, a questi servizi è concesso l'accesso. L'accesso può essere revocato/concesso, come illustrato nell'esempio seguente:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

In questo esempio <i>hdiv2</i> è il nome di un cluster HDInsight.

>[AZURE.NOTE]La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.

Questa operazione può essere eseguita anche tramite il portale di Azure. Vedere [Amministrare cluster Hadoop in HDInsight tramite il portale di Azure][hdinsight-admin-portal]

##Ridimensionare i cluster
Vedere [Scalabilità del cluster in HDInsight](hdinsight-hadoop-cluster-scaling.md)

##Inviare processi MapReduce
Con la distribuzione dei cluster HDInsight vengono forniti alcuni esempi di MapReduce. Uno degli esempi è relativo al conteggio della frequenza delle parole nei file di origine.

**Per inviare un processo MapReduce**

Lo script di PowerShell seguente consente di inviare il processo di esempio relativo al conteggio delle parole:
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
Per informazioni sul prefisso **wasb**, vedere [Usare l'archivio BLOB di Azure per HDInsight][hdinsight-storage].

**Per scaricare l'output del processo MapReduce**

Lo script di PowerShell seguente recupera l'output del processo MapReduce dall'ultima routine:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Per altre informazioni sullo sviluppo e l'esecuzione di processi MapReduce, vedere [Utilizzo di MapReduce con HDInsight][hdinsight-use-mapreduce].






































##Invio di processi Hive
La distribuzione di cluster HDInsight include una tabella Hive di esempio denominata *hivesampletable*. È possibile usare il comando HiveQL **SHOW TABLES** per visualizzare l'elenco delle tabelle Hive in un cluster.

**Per inviare un processo Hive**

Lo script seguente invia un processo Hive per visualizzare l'elenco delle tabelle Hive:
	
	$clusterName = "<HDInsightClusterName>"               
	
	# HiveQL query
	$querystring = @"
		SHOW TABLES;
		SELECT * FROM hivesampletable 
			WHERE Country='United Kingdom'
			LIMIT 10;
	"@

	Use-AzureHDInsightCluster -Name $clusterName
	Invoke-Hive $querystring

Il processo Hive visualizzerà per prime le tabelle Hive create nel cluster e i dati restituiti dalla tabella hivesampletable.

Per altre informazioni sull'uso di Hive, vedere [Uso di Hive con HDInsight][hdinsight-use-hive].


##Caricare dati nell'archivio BLOB di Azure
Vedere [Caricare dati in HDInsight][hdinsight-upload-data].

##Scaricare l'output del processo dall'archivio BLOB di Azure
Vedere la sezione [Inviare processi MapReduce](#mapreduce) in questo articolo.

## Vedere anche
* [Documentazione di riferimento dei cmdlet di HDInsight][hdinsight-powershell-reference]
* [Amministrare cluster Hadoop in HDInsight tramite il portale di Azure][hdinsight-admin-portal]
* [Amministrare cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando][hdinsight-admin-cli]
* [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]
* [Caricare dati in HDInsight][hdinsight-upload-data]
* [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs]
* [Introduzione ad Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png


 

<!---HONumber=August15_HO6-->