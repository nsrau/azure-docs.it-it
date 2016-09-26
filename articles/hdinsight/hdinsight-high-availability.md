<properties
	pageTitle="Disponibilità di cluster Hadoop in HDInsight | Microsoft Azure"
	description="HDInsight distribuisce cluster affidabili e ad alta disponibilità con un nodo head aggiuntivo."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jgao"/>


#Disponibilità e affidabilità di cluster Hadoop basati su Windows in HDInsight


>[AZURE.NOTE] I passaggi descritti in questo documento sono specifici per cluster HDInsight basati su Windows. Se si usa un cluster basato su Linux, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md) per informazioni specifiche su Linux.

HDInsight consente ai clienti di distribuire svariati tipi di cluster, per diversi carichi di lavoro di analisi di dati. I tipi di cluster attualmente disponibili sono i cluster Hadoop per i carichi di lavoro di query e di analisi, i cluster HBase per i carichi di lavoro NoSQL e i cluster Storm per i carichi di lavoro di elaborazione eventi in tempo reale. In un determinato tipo di cluster, esistono ruoli diversi per ogni nodo. ad esempio:



- I cluster Hadoop per HDInsight vengono distribuiti con due ruoli:
	- Nodo head (2 nodi)
	- Nodo dati (almeno 1 nodo)

- I cluster HBase per HDInsight vengono distribuiti con tre ruoli:
	- Server head (2 nodi)
	- Server area (almeno 1 nodo)
	- Nodi master/Zookeeper (3 nodi)

- I cluster Storm per HDInsight vengono distribuiti con tre ruoli:
	- Nodi Nimbus (2 nodi)
	- Server Supervisor (almeno 1 nodo)
	- Nodi Zookeeper (3 nodi)

Le implementazioni standard dei cluster Hadoop hanno in genere un singolo nodo head. HDInsight rimuove questo singolo punto di guasto con l'aggiunta di un nodo head/server head/nodo Nimbus secondario per aumentare la disponibilità e l'affidabilità del servizio necessario per gestire i carichi di lavoro. Questi nodi head/server head/nodi Nimbus hanno l'obiettivo di gestire semplicemente gli errori dei nodi di lavoro, ma eventuali interruzioni dei servizi principali in esecuzione sul nodo head potrebbero causare il mancato funzionamento del cluster.


Sono stati aggiunti i nodi [ZooKeeper](http://zookeeper.apache.org/) (ZK), usati per l'elezione del leader dei nodi head e per garantire che i nodi di lavoro e i gateway (GW) sappiano quando eseguire il failover sul nodo head secondario (Head Node1) nel momento in cui il nodo head attivo (Node0) diventa inattivo.

![Diagramma dei nodi head ad alta affidabilità nell'implementazione di HDInsight Hadoop.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## Controllare lo stato del servizio dei nodi head attivi
Per determinare quale nodo head è attivo e verificare lo stato dei servizi in esecuzione sullo stesso, è necessario connettersi al cluster Hadoop usando il protocollo RDP (Remote Desktop Protocol). Per istruzioni, vedere [Gestire i cluster Hadoop in HDInsight con il portale di Azure](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp). Dopo aver eseguito l'accesso in remoto al cluster, fare doppio clic sull'icona **Hadoop Service Available** sul desktop per ottenere informazioni sul nodo head nel quale sono in esecuzione i servizi Namenode, Jobtracker, Templeton, Oozieservice, Metastore e Hiveserver2 oppure HDI 3.0, Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore e Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

Nella schermata, il nodo head attivo è *headnode0*.

## Accedere ai file di log sul nodo head secondario

Per accedere ai log dei processi sul nodo head secondario nel caso in cui sia diventato il nodo head attivo, è possibile usare l'interfaccia utente di JobTracker esattamente come per il nodo attivo primario. Per accedere a JobTracker è necessario connettersi al cluster Hadoop usando il protocollo RDP, come descritto nella sezione precedente. Dopo aver eseguito l'accesso remoto al cluster, fare doppio clic sull'icona **Hadoop Name Node Status** sul desktop e quindi su **NameNode logs** per ottenere la directory dei log sul nodo head secondario.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## Configurare le dimensioni del nodo head
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

Per l'SDK, la procedura è simile. La creazione e il provisioning di un cluster con l'SDK sono documentati in [Uso di .NET SDK per HDInsight](hdinsight-provision-clusters.md#sdk). La configurazione di un nodo head molto grande richiede l'aggiunta del parametro `HeadNodeSize = NodeVMSize.ExtraLarge` al metodo `ClusterCreateParameters()` usato in questo codice.

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


## Passaggi successivi

- [Apache ZooKeeper](http://zookeeper.apache.org/)
- [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Uso di .NET SDK per HDInsight](hdinsight-provision-clusters.md#sdk)

<!---HONumber=AcomDC_0914_2016-->