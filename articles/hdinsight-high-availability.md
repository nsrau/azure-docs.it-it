<properties linkid="manage-services-hdinsight-high-availability" urlDisplayName="HDInsight High Availability" pageTitle="Availability of Hadoop clusters in HDInsight | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight deploys highly available and reliable clusters." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Availability of Hadoop clusters in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Disponibilità e affidabilità dei cluster Hadoop in HDInsight

## Introduzione

È stato aggiunto un secondo nodo head ai cluster Hadoop distribuiti da HDInsight al fine di aumentare la disponibilità e l'affidabilità del servizio necessario per gestire i carichi di lavoro. Le implementazioni standard dei cluster Hadoop hanno in genere un singolo nodo head. Questi cluster hanno l'obiettivo di gestire semplicemente gli errori dei nodi di lavoro, ma eventuali interruzioni dei servizi principali in esecuzione sul nodo head potrebbero causare il mancato funzionamento del cluster.

![][]

HDInsight elimina questo singolo punto di errore con l'aggiunta di un nodo head secondario (Head Node1). Sono stati aggiunti i nodi [ZooKeeper][] (ZK), usati per l'elezione del leader dei nodi head e per garantire che i nodi di lavoro e i gateway (GW) sappiano quando eseguire il failover sul nodo head secondario (Head Node1) quando il nodo head attivo (HeadNode0) diventa inattivo.

## Come controllare lo stato di servizio del nodo head attivo

Per determinare quale nodo head è attivo e verificare lo stato dei servizi in esecuzione sullo stesso è necessario connettersi al cluster Hadoop usando il protocollo RDP (Remote Desktop Protocol). La funzionalità per accedere al cluster in remoto è disattivata per impostazione predefinita in Azure, quindi è necessario prima attivarla. Per istruzioni su come eseguire questa operazione nel portale, vedere [Connessione a cluster HDInsight tramite RDP][]
. Dopo aver effettuato l'accesso in remoto al cluster, fare doppio clic sull'icona **Hadoop Service Available Status** sul desktop per ottenere informazioni sul nodo head nel quale sono in esecuzione i servizi Namenode, Jobtracker, Templeton, Oozieservice, Metastore e Hiveserver2 oppure HDI 3.0, Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore e Hiveserver2.

![][1]

## Come accedere ai file di log sul nodo head secondario

Per accedere ai log dei processi sul nodo head secondario nel caso sia diventato il nodo head attivo, è possibile esplorare l'interfaccia utente di JobTracker esattamente come nel nodo attivo primario. Per accedere a JobTracker è necessario connettersi al cluster Hadoop usando il protocollo RDP, come descritto nella sezione precedente. Dopo aver effettuato l'accesso remoto al cluster, fare doppio clic sull'icona **Nome nodo Hadoop** sul desktop e quindi su **Log NameNode** per ottenere la directory dei log sul nodo head secondario.

![][2]

## Come configurare le dimensioni del nodo head

I nodi head vengono allocati come macchine virtuali con dimensione grande per impostazione predefinita. Queste dimensioni sono adeguate per la gestione di gran parte dei processi Hadoop eseguiti sul cluster. Tuttavia, ci sono scenari in cui potrebbero essere necessarie macchine virtuali con dimensione molto grande. Un esempio si ha quando il cluster deve gestire un elevato numero di piccoli processi Oozie.

È possibile configurare le macchine virtuali con dimensione molto grande usando i cmdlet di Azure PowerShell oppure l'SDK per HDInsight.

La creazione e il provisioning di un cluster con PowerShell sono documentati in [Amministrazione di HDInsight tramite PowerShell][]. La configurazione di un nodo head molto grande richiede l'aggiunta del parametro `-HeadNodeVMSize ExtraLarge` al cmdlet `New-AzureHDInsightcluster` usato in questo codice.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge Headnode VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Per l'SDK, la procedura è simile: la creazione e il provisioning di un cluster con l'SDK sono documentati in [Utilizzo di .NET SDK per HDInsight][]. La configurazione di un nodo head molto grande richiede l'aggiunta del parametro `HeadNodeSize = NodeVMSize.ExtraLarge` al metodo `ClusterCreateParameters()` usato in questo codice.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge Headnode VM
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

-   [ZooKeeper][]
-   [Connessione a cluster HDInsight tramite RDP][]
-   [Utilizzo di .NET SDK per HDInsight][]

  []: http://i.imgur.com/jrUmrH4.png
  [ZooKeeper]: http://zookeeper.apache.org/
  [Connessione a cluster HDInsight tramite RDP]: ../hdinsight-administer-use-management-portal/#rdp
  [1]: http://i.imgur.com/MYTkCHW.png
  [2]: http://i.imgur.com/eL6jzgB.png
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Utilizzo di .NET SDK per HDInsight]: ../hdinsight-provision-clusters/#sdk
