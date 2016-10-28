<properties
   	pageTitle="Creare cluster Hadoop, HBase, Storm o Spark con Linux in HDInsight tramite Azure PowerShell | Microsoft Azure"
   	description="Informazioni su come creare cluster Hadoop, HBase, Storm o Spark su Linux per HDInsight tramite Azure PowerShell."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="07/08/2016"
   	ms.author="nitinme"/>

# Creare cluster basati su Linux in HDInsight tramite Azure PowerShell

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Microsoft Azure. Questo documento offre informazioni su come creare un cluster HDInsight basato su Linux usando Azure PowerShell. Include anche uno script di esempio.

> [AZURE.NOTE] Azure PowerShell è disponibile solo su client Windows. Se si usa un client Mac OS X, Linux o Unix, vedere [Creare cluster basati su Linux in HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) per informazioni sull'uso dell'interfaccia della riga di comando di Azure per creare un cluster.

## Prerequisiti
Prima di iniziare questa procedura è necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- Azure PowerShell. Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet di Windows PowerShell per HDInsight, vedere la [documentazione di riferimento dei cmdlet per HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## Creare i cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

È necessario completare le procedure seguenti per creare un cluster HDInsight con Azure PowerShell:

- Creare un gruppo di risorse di Azure
- Creare un account di Archiviazione di Azure
- Creazione di un contenitore BLOB di Azure
- Creazione di un cluster HDInsight

I due parametri più importanti da impostare per la creazione dei cluster Linux sono quelli in cui vengono specificati il tipo di sistema operativo e i dettagli dell'utente SSH:

- Assicurarsi di specificare il parametro **- OSType** come **Linux**.
- Per usare SSH per le sessioni remote nei cluster, è possibile specificare la password utente SSH oppure la chiave pubblica SSH. Se si specificano sia la password utente SSH e la chiave pubblica SSH, la chiave verrà ignorata. Per usare la chiave SSH per le sessioni remote, specificare una password SSH vuota quando viene richiesta. Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Lo script seguente illustra come creare un nuovo cluster:

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

I valori specificati per **$clusterCredentials** vengono usati per creare l'account utente Hadoop per il cluster. Usare questo account per connettersi al cluster.

I valori specificati per **$sshCredentials** vengono usati per creare l'utente SSH per il cluster. Usare questo account per avviare una sessione SSH remota nel cluster ed eseguire i processi.

> [AZURE.IMPORTANT] In questo script, è necessario specificare il numero di nodi di lavoro che verranno inclusi nel cluster. Se si prevede di usare più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario specificare anche una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
> Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

La creazione di un cluster può richiedere fino a 20 minuti.

Nell'esempio seguente viene illustrato come aggiungere un account di archiviazione aggiuntivo:

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## Personalizzare i cluster

- Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Vedere [Personalizzare cluster HDInsight basati su Windows tramite Azione script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## Eliminazione del cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Passaggi successivi

Dopo aver creato un cluster HDInsight, usare le risorse seguenti per acquisire familiarità con il cluster.

### Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### Cluster Spark

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire i processi in modalità remota in un cluster Spark utilizzando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0914_2016-->