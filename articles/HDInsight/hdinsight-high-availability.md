<properties 
	pageTitle="Disponibilità di cluster Hadoop in HDInsight | Microsoft Azure" 
	description="HDInsight distribuisce cluster affidabili e ad alta disponibilità con un nodo head aggiuntivo." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/19/2014" 
	ms.author="bradsev"/>


#Disponibilità e affidabilità dei cluster Hadoop in HDInsight


È stato aggiunto un secondo nodo head ai cluster Hadoop distribuiti da Azure HDInsight al fine di aumentare la disponibilità e l'affidabilità del servizio necessario per gestire i carichi di lavoro. Le implementazioni standard dei cluster Hadoop hanno in genere un singolo nodo head. Questi cluster hanno l'obiettivo di gestire semplicemente gli errori dei nodi di lavoro, ma eventuali interruzioni dei servizi principali in esecuzione sul nodo head potrebbero causare il mancato funzionamento del cluster.

![Diagramma dei nodi head ad alta affidabilità nell'implementazione di HDInsight Hadoop.](http://i.imgur.com/jrUmrH4.png)

HDInsight elimina questo singolo punto di errore con l'aggiunta di un nodo head secondario (Head Node1). Sono stati aggiunti i nodi [ZooKeeper](http://zookeeper.apache.org/) (ZK), usati per l'elezione del leader dei nodi head e per garantire che i nodi di lavoro e i gateway (GW) sappiano quando eseguire il failover sul nodo head secondario (Head Node1) nel momento in cui il nodo head attivo (Node0) diventa inattivo.


## Come controllare lo stato dei servizi del nodo head attivo ##
Per determinare quale nodo head è attivo e verificare lo stato dei servizi in esecuzione sullo stesso, è necessario connettersi al cluster Hadoop usando il protocollo RDP (Remote Desktop Protocol). La funzionalità per accedere al cluster in remoto è disattivata per impostazione predefinita in Azure e, pertanto, è necessario prima attivarla. Per istruzioni su come eseguire questa operazione nel portale, vedere [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#rdp). Dopo aver eseguito l'accesso in remoto al cluster, fare doppio clic sull'icona **Hadoop Service Available Status** sul desktop per ottenere informazioni sul nodo head nel quale sono in esecuzione i servizi Namenode, Jobtracker, Templeton, Oozieservice, Metastore e Hiveserver2 oppure HDI 3.0, Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore e Hiveserver2.

![](http://i.imgur.com/MYTkCHW.png)


## Come accedere ai file di log sul nodo head secondario \

Per accedere ai log dei processi sul nodo head secondario nel caso in cui sia diventato il nodo head attivo, è possibile usare l'interfaccia utente di JobTracker esattamente come per il nodo attivo primario. Per accedere a JobTracker è necessario connettersi al cluster Hadoop usando il protocollo RDP, come descritto nella sezione precedente. Dopo aver eseguito l'accesso remoto al cluster, fare doppio clic sull'icona **Hadoop Name Node** sul desktop e quindi su **NameNode logs** per ottenere la directory dei log sul nodo head secondario.

![](http://i.imgur.com/eL6jzgB.png)


## Come configurare le dimensioni del nodo head ##
I nodi head vengono allocati per impostazione predefinita come macchine virtuali di grandi dimensioni. Queste dimensioni sono adeguate per la gestione di gran parte dei processi Hadoop eseguiti sul cluster. Tuttavia, esistono scenari in cui potrebbero essere necessarie macchine virtuali con dimensioni molto grandi. Un esempio si ha quando il cluster deve gestire un elevato numero di piccoli processi Oozie.

È possibile configurare macchine virtuali di dimensioni molto grandi usando i cmdlet di Azure PowerShell oppure l'SDK di HDInsight.

La creazione e il provisioning di un cluster con Azure PowerShell sono documentati in [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). La configurazione di un nodo head molto grande richiede l'aggiunta del parametro `-HeadNodeVMSize ExtraLarge` al cmdlet `New-AzureHDInsightcluster` usato in questo codice.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

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


**Riferimenti**

- [ZooKeeper](http://zookeeper.apache.org/)
- [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Uso di .NET SDK per HDInsight](hdinsight-provision-clusters.md#sdk) 







 

<!---HONumber=62-->