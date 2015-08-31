<properties
	pageTitle="Gestire cluster Hadoop in HDInsight con PowerShell | Microsoft Azure"
	description="Informazioni su come eseguire attività amministrative per i cluster Hadoop in HDInsight tramite Azure PowerShell."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>

# Gestire cluster Hadoop in HDInsight tramite Azure PowerShell

[AZURE.INCLUDE [selettore](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. In questo articolo verrà illustrato come gestire cluster Hadoop in Azure HDInsight usando una console di Azure PowerShell locale tramite Windows PowerShell. Per l'elenco dei cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight][hdinsight-powershell-reference].



**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	> [AZURE.NOTE]Gli script di PowerShell forniti in questo articolo utilizza la modalità di gestione risorse di Azure. Affinché gli esempi siano funzionali, scaricare il più recente PowerShell di Azure utilizzando l'installazione guidata piattaforma Web Microsoft.

##Provisioning di cluster HDInsight

I cluster HDInsight utilizzano un gruppo di risorse di Azure e un contenitore di BLOB in un account di archiviazione di Azure:

- Il gruppo di risorse di Azure è un contenitore logico per le risorse di Azure. Il gruppo di risorse di Azure e il cluster HDInsight non devono essere nello stesso percorso. Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](powershell-azure-resource-manager.md).
- HDInsight usa un contenitore BLOB di un account di archiviazione di Azure come file system predefinito. Per creare un cluster HDInsight, sono necessari un account di archiviazione di Azure e un contenitore di archiviazione. L'account di archiviazione predefinito deve trovarsi nello stesso percorso del cluster HDInsight.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Per creare un gruppo di risorse di Azure**

1. Verificare di essere in modalità risorse di Azure:

		Switch-AzureMode -Name AzureResourceManager

2. Connettersi all'account Azure e selezionare una sottoscrizione (nel caso in cui si dispone di più sottoscrizioni).

		Add-AzureAccount
		Select-AzureSubscription

3. Creare un nuovo gruppo di risorse

	New-AzureResourceGroup -name <AzureResourceGroupName> -Location <AzureDataCente> # Per esempio, “Stati Uniti occidentali”

	[AZURE.INCLUDE [Elenco di data center](../../includes/hdinsight-pricing-data-centers-clusters.md)]

**Per creare un account di archiviazione di Azure**

	New-AzureStorageAccount -ResourceGroupName <AzureResourceGroupName> -Name <AzureStorageAccountName> -Location <AzureDataCneter> -Type <AccountType> # account type example: Standard_ZRS for zero redundancy storage

	For a full list of the storage account types, see [https://msdn.microsoft.com/it-it/library/azure/hh264518.aspx](https://msdn.microsoft.com/it-it/library/azure/hh264518.aspx).


Per informazioni sulla creazione di un account di archiviazione di Azure tramite il portale di anteprima di Azure, vedere [Creare, gestire o eliminare un account di archiviazione](storage-create-storage-account.md).

Se si ha già di un account di archiviazione, ma non si conosce il nome dell'account e la chiave dell'account, è possibile usare i comandi seguenti per recuperare le informazioni:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageAccountKey -ResourceGroupName <AzureResourceGroupName> -name $storageAccountName <AzureStorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di Azure, vedere la sezione "Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Creare, gestire o eliminare un account di archiviazione](storage-create-storage-account.md).

**Per creare un contenitore di archiviazione di Azure**

Azure PowerShell non è in grado di creare un contenitore BLOB durante il processo di provisioning di HDInsight. È possibile crearne uno usando lo script seguente:

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<AzureStorageAccountName>"
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Per effettuare il provisioning di un cluster**

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Dettagli dell'elenco di cluster
Usare il comando seguente per visualizzare l'elenco di tutti i cluster nella sottoscrizione corrente:

	Get-AzureHDInsightCluster

Usare il comando seguente per visualizzare i dettagli di un cluster specifico nella sottoscrizione corrente:

	Get-AzureHDInsightCluster -ResourceGroupName <ResouceGroupName> -ClusterName <ClusterName>

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

Questa operazione può essere eseguita anche tramite il portale di anteprima di Azure. Vedere [Amministrare HDInsight tramite il portale di anteprima di Azure][hdinsight-admin-portal].

##Ridimensionare i cluster
La funzionalità di scalabilità del cluster consente di modificare il numero di nodi del ruolo di lavoro usati da un cluster in esecuzione in Azure HDInsight senza dover ricreare il cluster.

>[AZURE.NOTE]Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà. Vedere [Acquisire familiarità con l'interfaccia del portale cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

Questa sezione descrive l'impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

- Hadoop

	È possibile aumentare facilmente il numero di nodi del ruolo di lavoro in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

	Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. In questo modo, tutti i processi in esecuzione e in attesa daranno esito negativo dopo il completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.

- HBase

	È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Per altre informazioni sull'uso della shell HBase, vedere
- Storm

	È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.

	A tale scopo, è possibile scegliere tra due opzioni:

	* Interfaccia utente Web di Storm
	* Interfaccia della riga di comando (CLI)

	Per altre informazioni, fare riferimento alla [documentazione su Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

	![hdinsight scala ribilanciamento di storm](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Di seguito viene fornito un esempio d'uso del comando CLI per ribilanciare la topologia di Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Per modificare le dimensioni del cluster Hadoop mediante Azure PowerShell, eseguire il comando seguente da un computer client:

	Set-AzureHDInsightClusterSize -Name <ClusterName> -ClusterSizeInNodes <NewSize>



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
* [Amministrare HDInsight tramite il portale di anteprima di Azure][hdinsight-admin-portal]
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

<!---HONumber=August15_HO8-->