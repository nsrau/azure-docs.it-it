<properties urlDisplayName="HDInsight Administration" pageTitle="Gestire cluster Hadoop in HDInsight con Azure PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, Hadoop, administration, administer" description="Learn how to perform administrative tasks for the Hadoop clusters in HDInsight using Azure PowerShell." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Manage Hadoop clusters in HDInsight using Azure PowerShell" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="jgao" />

# Gestire cluster Hadoop in HDInsight tramite Azure PowerShell

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. In questo articolo sarà illustrato come gestire cluster Hadoop in HDInsight usando una console di Azure PowerShell tramite Windows PowerShell. Per l'elenco dei cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight][hdinsight-powershell-reference].

**Prerequisiti:**

Per eseguire le procedure descritte nell'articolo è necessario:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. I cmdlet di PowerShell HDInsight eseguono le attività con la sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].

- Workstation con Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Powershell-install-configure].

			
	

## Contenuto dell'articolo

* [Effettuare il provisioning di un cluster](#provision)
* [Elencare e visualizzare cluster](#listshow)
* [Eliminare un cluster](#delete)
* [Concedere/revocare l'accesso ai servizi HTTP](#httpservices)
* [Inviare processi MapReduce](#mapreduce)
* [Inviare processi Hive](#hive)
* [Caricare i dati nell'archiviazione BLOB](#upload)
* [Scaricare i dati di output di MapReduce dall'archiviazione BLOB](#download)


##<a id="provision"></a> Provisioning di un cluster HDInsight
HDInsight usa un contenitore dell'archiviazione BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, sono necessari un account di archiviazione di Azure e un contenitore dell'archiviazione. 

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Per creare un account di archiviazione di Azure**

Dopo avere importato il file publishsettings è possibile usare il comando seguente per creare un account di archiviazione:

	# Create an Azure storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] L'account di archiviazione deve trovarsi nello stesso data center che include il cluster HDInsight. È attualmente possibile eseguire il provisioning di cluster HDInsight solo nei data center seguenti:

><ul>
<li>Asia sudorientale</li>
<li>Europa settentrionale</li>
<li>Europa occidentale</li>
<li>Stati Uniti orientali</li>
<li>Stati Uniti occidentali</li>
</ul>



Per informazioni sulla creazione di un account di archiviazione di Azure mediante il portale di gestione, vedere [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/).

Se si dispone già di un account di archiviazione, ma non si conosce il nome account e la chiave dell'account, è possibile usare i comandi seguenti per recuperare le informazioni:

	# List storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a storage account
	Get-AzureStorageKey <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di gestione, vedere la sezione *Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso* di [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/).

**Per creare il contenitore dell'archiviazione di Azure**

PowerShell non è in grado di creare un contenitore BLOB durante il processo di provisioning di HDInsight. È possibile crearne uno usando lo script seguente:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
	                                       -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Per effettuare il provisioning di un cluster**

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.    
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


Nella schermata seguente è illustrata l'esecuzione dello script:

![HDI.PS.Provision][image-hdi-ps-provision]




##<a id="listshow"></a> Elenco e visualizzazione di cluster
Usare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

**Per visualizzare l'elenco di tutti i cluster nella sottoscrizione corrente**

	Get-AzureHDInsightCluster 

**Per visualizzare i dettagli del cluster specifico nella sottoscrizione corrente**

	Get-AzureHDInsightCluster -Name <ClusterName> 

##<a id="delete"></a> Eliminazione di un cluster
Usare il comando seguente per eliminare un cluster:

	Remove-AzureHDInsightCluster -Name <ClusterName> 

##<a id="httpservice"></a> Concessione/revoca dell'accesso ai servizi HTTP

Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Per impostazione predefinita, a questi servizi è concesso l'accesso. L'accesso può essere revocato/concesso,  come illustrato nell'esempio seguente:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

Nell'esempio <i>hdiv2</i> è il nome di un cluster HDInsight.

>[WACOM.NOTE] La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.

È possibile effettuare tale operazione anche tramite il portale di gestione di Azure. Vedere [Amministrare HDInsight tramite il portale di gestione][hdinsight-admin-portal].

##<a id="mapreduce"></a> Inviare processi MapReduce
Con la distribuzione dei cluster HDInsight vengono forniti alcuni esempi di MapReduce. Uno degli esempi è relativo al conteggio della frequenza delle parole nei file di origine.

**Per inviare un processo MapReduce**

Lo script di PowerShell seguente consente di inviare il processo di esempio relativo al conteggio delle parole: 
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
> [WACOM.NOTE] Il file *hadoop-examples.jar* è incluso nei cluster HDInsight della versione 2.1. Il file è stato rinominato in *hadoop-mapreduce.jar* nei cluster HDInsight della versione 3.0.

Per altre informazioni sul prefisso WASB, vedere [Usare l'archiviazione BLOB di Azure per HDInsight][hdinsight-storage].

**Per scaricare l'output del processo MapReduce**

Lo script di PowerShell seguente consente di recuperare l'output del processo MapReduce dall'ultima routine:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Per altre informazioni sullo sviluppo e l'esecuzione di processi MapReduce, vedere [Usare MapReduce con HDInsight][hdinsight-use-mapreduce].






































##<a id="hive"></a> Inviare processi Hive
La distribuzione di cluster HDInsight include una tabella Hive di esempio denominata *hivesampletable*. È possibile usare una query HiveQL "show tables;" per visualizzare l'elenco delle tabelle Hive in un cluster.

**Per inviare un processo Hive**

Lo script seguente consente di inviare un processo Hive per visualizzare l'elenco delle tabelle Hive:
	
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

Il processo Hive visualizzerà innanzitutto le tabelle Hive create nel cluster e i dati restituiti da hivesampletable.

Per altre informazioni sull'uso di Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].


##<a id="upload"></a>Caricare i dati nell'archiviazione BLOB
Vedere [Caricare i dati in HDInsight][hdinsight-upload-data].

##<a id="download"></a>Download dei dati di output di MapReduce dall'archiviazione BLOB
Vedere la sessione [Inviare processi MapReduce](#mapreduce) in questo articolo.

## Vedere anche
* [Documentazione di riferimento relativa ai cmdlet di HDInsight][hdinsight-powershell-reference]
* [Amministrare HDInsight tramite il portale di gestione][hdinsight-admin-portal]
* [Amministrare HDInsight tramite l'interfaccia della riga di comando][hdinsight-admin-cli]
* [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]
* [Caricare i dati in HDInsight][hdinsight-upload-data]
* [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs]
* [Introduzione ad Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/it-it/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/it-it/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/it-it/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/it-it/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png



<!--HONumber=35_1-->
