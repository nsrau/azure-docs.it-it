---
title: "Disponibilità di cluster Hadoop in HDInsight | Documentazione Microsoft"
description: "HDInsight distribuisce cluster affidabili e ad alta disponibilità con un nodo head aggiuntivo."
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: mumian
documentationcenter: 
ms.assetid: ccab792d-60d6-4287-96c2-479e5d0cf358
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/21/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: 1f40d6119d5fefc48c1e12d510423f996239fe14


---
# <a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>Disponibilità e affidabilità di cluster Hadoop basati su Windows in HDInsight
> [!NOTE]
> I passaggi descritti in questo documento sono specifici per cluster HDInsight basati su Windows. Se si usa un cluster basato su Linux, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md) per informazioni specifiche su Linux.
>
>

HDInsight consente ai clienti di distribuire svariati tipi di cluster, per diversi carichi di lavoro di analisi di dati. I tipi di cluster attualmente disponibili sono i cluster Hadoop per i carichi di lavoro di query e di analisi, i cluster HBase per i carichi di lavoro NoSQL e i cluster Storm per i carichi di lavoro di elaborazione eventi in tempo reale. In un determinato tipo di cluster, esistono ruoli diversi per ogni nodo. ad esempio:

* I cluster Hadoop per HDInsight vengono distribuiti con due ruoli:

  * Nodo head (2 nodi)
  * Nodo dati (almeno 1 nodo)
* I cluster HBase per HDInsight vengono distribuiti con tre ruoli:

  * Server head (2 nodi)
  * Server area (almeno 1 nodo)
  * Nodi master/Zookeeper (3 nodi)
* I cluster Storm per HDInsight vengono distribuiti con tre ruoli:

  * Nodi Nimbus (2 nodi)
  * Server Supervisor (almeno 1 nodo)
  * Nodi Zookeeper (3 nodi)

Le implementazioni standard dei cluster Hadoop hanno in genere un singolo nodo head. HDInsight rimuove questo singolo punto di guasto con l'aggiunta di un nodo head/server head/nodo Nimbus secondario per aumentare la disponibilità e l'affidabilità del servizio necessario per gestire i carichi di lavoro. Questi nodi head/server head/nodi Nimbus hanno l'obiettivo di gestire semplicemente gli errori dei nodi di lavoro, ma eventuali interruzioni dei servizi principali in esecuzione sul nodo head potrebbero causare il mancato funzionamento del cluster.

Sono stati aggiunti i nodi [ZooKeeper](http://zookeeper.apache.org/) (ZK), usati per l'algoritmo di elezione dei nodi head e per garantire che i nodi di lavoro e i gateway (GW) sappiano quando eseguire il failover sul nodo head secondario (Head Node1) nel momento in cui il nodo head attivo (Node0) diventa inattivo.

![Diagramma dei nodi head ad alta affidabilità nell'implementazione di HDInsight Hadoop.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)

## <a name="check-active-head-node-service-status"></a>Controllare lo stato del servizio dei nodi head attivi
Per determinare quale nodo head è attivo e verificare lo stato dei servizi in esecuzione sullo stesso, è necessario connettersi al cluster Hadoop usando il protocollo RDP (Remote Desktop Protocol). Per istruzioni su RDP, vedere [Gestire i cluster Hadoop in HDInsight con il portale di Azure](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Dopo essersi connessi al cluster, fare doppio clic sull'icona **Hadoop Service Available** (Servizio Hadoop disponibile) sul desktop per ottenere informazioni sul nodo head nel quale sono in esecuzione i servizi Namenode, Jobtracker, Templeton, Oozieservice, Metastore e Hiveserver2 oppure per HDI 3.0, i servizi Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore e Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

Nella schermata, il nodo head attivo è *headnode0*.

## <a name="access-log-files-on-the-secondary-head-node"></a>Accedere ai file di log sul nodo head secondario
Per accedere ai log dei processi sul nodo head secondario nel caso in cui sia diventato il nodo head attivo, è possibile usare l'interfaccia utente di JobTracker esattamente come per il nodo attivo primario. Per accedere a JobTracker è necessario connettersi al cluster Hadoop usando il protocollo RDP, come descritto nella sezione precedente. Dopo aver eseguito l'accesso remoto al cluster, fare doppio clic sull'icona **Hadoop Name Node Status** (Stato Hadoop Name Node) sul desktop e quindi su **NameNode logs** (Log NameNode) per ottenere la directory dei log sul nodo head secondario.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)

## <a name="configure-head-node-size"></a>Configurare le dimensioni del nodo head
I nodi head vengono allocati per impostazione predefinita come macchine virtuali (VM) di grandi dimensioni. Queste dimensioni sono adeguate per la gestione di gran parte dei processi Hadoop eseguiti sul cluster. Tuttavia, esistono scenari in cui potrebbero essere necessarie VM di dimensioni molto grandi. Un esempio si ha quando il cluster deve gestire un elevato numero di piccoli processi Oozie.

È possibile configurare VM di dimensioni molto grandi usando i cmdlet di Azure PowerShell oppure HDInsight SDK.

La creazione e il provisioning di un cluster con Azure PowerShell sono documentati in [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). La configurazione di un nodo head molto grande richiede l'aggiunta del parametro `-HeadNodeVMSize ExtraLarge` al cmdlet `New-AzureRmHDInsightcluster` usato in questo codice.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName `
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

Per l'SDK, la procedura è simile. La creazione e il provisioning di un cluster con l'SDK sono documentati in [Uso di .NET SDK per HDInsight](hdinsight-provision-clusters.md). La configurazione di un nodo head molto grande richiede l'aggiunta del parametro `HeadNodeSize = NodeVMSize.ExtraLarge` al metodo `ClusterCreateParameters()` usato in questo codice.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>Passaggi successivi
* [Apache ZooKeeper](http://zookeeper.apache.org/)
* [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* [Uso di .NET SDK per HDInsight](hdinsight-provision-clusters.md)



<!--HONumber=Nov16_HO3-->


