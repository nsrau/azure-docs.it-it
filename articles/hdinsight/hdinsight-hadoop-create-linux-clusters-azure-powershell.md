---
title: Creare cluster Azure HDInsight (Hadoop) usando PowerShell | Documentazione Microsoft
description: Informazioni su come creare cluster Hadoop, HBase, Storm o Spark su Linux per HDInsight tramite Azure PowerShell.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 44e418e52fc18dd22820331f7d921e789da62832
ms.lasthandoff: 03/25/2017


---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Creare cluster basati su Linux in HDInsight tramite Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Microsoft Azure. Questo documento offre informazioni su come creare un cluster HDInsight basato su Linux usando Azure PowerShell. Include anche uno script di esempio.

> [!NOTE]
> Azure PowerShell è disponibile solo su client Windows. Se si usa un client Mac OS X, Linux o Unix, vedere [Creare cluster basati su Linux in HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) per informazioni sull'uso dell'interfaccia della riga di comando di Azure per creare un cluster.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa procedura è necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.
    Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet di Windows PowerShell per HDInsight, vedere la [documentazione di riferimento dei cmdlet per HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).
  
    > [!IMPORTANT]
    > Il supporto di Azure PowerShell per la gestione delle risorse HDInsight tramite Azure Service Manager è **deprecato** ed è stato rimosso dal 1° gennaio 2017. La procedura descritta in questo documento usa i nuovi cmdlet HDInsight, compatibili con Azure Resource Manager.
    > 
    > Per installare la versione più recente, seguire la procedura descritta in [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Se sono presenti script che devono essere modificati per l'uso dei nuovi cmdlet compatibili con Azure Resource Manager, per altre informazioni vedere [Migrazione a strumenti di sviluppo basati su Azure Resource Manager per i cluster HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) .
    > 
    > 

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creare i cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

È necessario completare le procedure seguenti per creare un cluster HDInsight con Azure PowerShell:

* Creare un gruppo di risorse di Azure
* Creare un account di Archiviazione di Azure
* Creazione di un contenitore BLOB di Azure
* Creazione di un cluster HDInsight

I due parametri più importanti da impostare per la creazione dei cluster Linux sono quelli in cui vengono specificati il tipo di sistema operativo e i dettagli dell'utente SSH:

* Specificare il parametro **-OSType** come **Linux**.
* Per usare SSH per le sessioni remote nei cluster, è possibile specificare la password utente SSH oppure la chiave pubblica SSH. Se si specificano sia la password utente SSH e la chiave pubblica SSH, la chiave verrà ignorata. Per usare la chiave SSH per le sessioni remote, specificare una password SSH vuota quando viene richiesta. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Lo script seguente illustra come creare un nuovo cluster:

    $token ="<SpecifyAnUniqueString>"
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value
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

> [!IMPORTANT]
> In questo script, è necessario specificare il numero di nodi di lavoro che verranno inclusi nel cluster. Se si prevede di usare più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario specificare anche una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
> 
> Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
> 
> 

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

## <a name="customize-clusters"></a>Personalizzare i cluster
* Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Vedere [Personalizzare cluster HDInsight usando l'azione script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminazione del cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un cluster HDInsight, usare le risorse seguenti per acquisire familiarità con il cluster.

### <a name="hadoop-clusters"></a>Cluster Hadoop
* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Cluster HBase
* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Cluster Storm
* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Cluster Spark
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire i processi in modalità remota in un cluster Spark utilizzando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)


