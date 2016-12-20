---
title: Gestire cluster Hadoop in HDInsight con PowerShell | Microsoft Docs
description: "Informazioni su come eseguire attività amministrative per i cluster Hadoop in HDInsight tramite Azure PowerShell."
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: 
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0890076fb1f80489ec9ec75dddb8cbd4cfaa44f3


---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gestire cluster Hadoop in HDInsight tramite Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. In questo articolo verrà illustrato come gestire cluster Hadoop in Azure HDInsight usando una console di Azure PowerShell locale tramite Windows PowerShell. Per l'elenco dei cmdlet PowerShell per HDInsight, vedere [HDInsight cmdlet reference][hdinsight-powershell-reference] (Documentazione di riferimento relativa ai cmdlet di HDInsight).

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Installare Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Se è installato Azure PowerShell versione 0.9x, è necessario disinstallarlo prima di installare una versione più recente.

Per controllare la versione di PowerShell installata:

    Get-Module *azure*

Per disinstallare la versione precedente, eseguire Programmi e Funzionalità nel Pannello di controllo. 

## <a name="create-clusters"></a>Creare i cluster
Vedere [Creare cluster basati su Linux in HDInsight tramite Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Elencare cluster
Usare il comando seguente per visualizzare l'elenco di tutti i cluster nella sottoscrizione corrente:

    Get-AzureRmHDInsightCluster

## <a name="show-cluster"></a>Mostrare cluster
Usare il comando seguente per visualizzare i dettagli di un cluster specifico nella sottoscrizione corrente:

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

## <a name="delete-clusters"></a>Eliminare cluster
Utilizzare il comando seguente per eliminare un cluster:

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

È inoltre possibile eliminare un cluster rimuovendo il gruppo di risorse che lo contiene. Tenere presente che questa operazione eliminerà tutte le risorse nel gruppo, compreso l’account di archiviazione predefinito.

    Remove-AzureRmResourceGroup -Name <Resource Group Name>

## <a name="scale-clusters"></a>Ridimensionare i cluster
La funzionalità di scalabilità del cluster consente di modificare il numero di nodi del ruolo di lavoro usati da un cluster in esecuzione in Azure HDInsight senza dover ricreare il cluster.

> [!NOTE]
> Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà.  Vedere [Elencare e visualizzare i cluster](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
> 
> 

Questa sezione descrive l'impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Hadoop
  
    È possibile aumentare facilmente il numero di nodi del ruolo di lavoro in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.
  
    Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. In questo modo, tutti i processi in esecuzione e in attesa daranno esito negativo dopo il completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.
* HBase
  
    È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:
  
        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer
* Storm
  
    È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.
  
    A tale scopo, è possibile scegliere tra due opzioni:
  
  * Interfaccia utente Web di Storm
  * Interfaccia della riga di comando (CLI)
    
    Per altre informazioni, fare riferimento alla [documentazione su Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .
    
    L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:
    
    ![Ribilanciamento scala di HDInsight Storm](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)
    
    Di seguito viene fornito un esempio d'uso del comando CLI per ribilanciare la topologia di Storm:
    
    ## <a name="reconfigure-the-topology-mytopology-to-use-5-worker-processes"></a>Riconfigurare la topologia "mytopology" in modo da usare 5 processi di lavoro,
    ## <a name="the-spout-blue-spout-to-use-3-executors-and"></a>lo spout "blu-spout" in modo da usare 3 esecutori e
    ## <a name="the-bolt-yellow-bolt-to-use-10-executors"></a>il bolt "giallo-bolt" in modo da usare 10 esecutori
      $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Per modificare le dimensioni del cluster Hadoop mediante Azure PowerShell, eseguire il comando seguente da un computer client:

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>


## <a name="grantrevoke-access"></a>Concedere/Revocare l'accesso
Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. L'accesso può essere revocato/concesso, Per revocare:

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Per concedere:

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

> [!NOTE]
> La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.
> 
> 

Questa operazione può essere eseguita anche tramite il portale. Vedere [Amministrare HDInsight tramite il Portale di Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Aggiornare le credenziali utente HTTP
È la stessa procedura di [Concedere/revocare l'accesso HTTP](#grant/revoke-access). Se al cluster è stato concesso l'accesso HTTP, è necessario prima revocarlo.  E quindi concedere l'accesso con le nuove credenziali utente HTTP.

## <a name="find-the-default-storage-account"></a>Trovare l'account di archiviazione predefinito
Il seguente script di Powershell dimostra come ottenere il nome dell’account di archiviazione predefinito e la chiave dell’account di archiviazione predefinita per un cluster.

    $clusterName = "<HDInsight Cluster Name>"

    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

## <a name="find-the-resource-group"></a>Trovare il gruppo di risorse
Nella modalità Resource Manager ogni cluster HDInsight appartiene a un gruppo di risorse di Azure.  Trovare il gruppo di risorse:

    $clusterName = "<HDInsight Cluster Name>"

    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


## <a name="submit-jobs"></a>Inviare i processi
**Inviare processi MapReduce**

Vedere [esempi di MapReduce di Hadoop in HDInsight basato su Windows](hdinsight-run-samples.md).

**Inviare processi Hive** 

Vedere [Esecuzione di query Hive tramite PowerShell](hdinsight-hadoop-use-hive-powershell.md)

**Inviare processi Pig**

Vedere [Eseguire processi Pig mediante PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Inviare processi Sqoop**

Vedere [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop.md).

**Inviare processi Oozie**

Vedere [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Caricare dati nell'archivio BLOB di Azure
Vedere [Caricare dati in HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Vedere anche
* [HDInsight cmdlet reference documentation][hdinsight-powershell-reference] (Documentazione di riferimento relativa ai cmdlet di HDInsight)
* [Amministrare HDInsight tramite il Portale di Azure][hdinsight-admin-portal]
* [Amministrare HDInsight tramite l'interfaccia della riga di comando][hdinsight-admin-cli]
* [Creare cluster HDInsight][hdinsight-provision]
* [Caricare dati in HDInsight][hdinsight-upload-data]
* [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs]
* [Introduzione ad Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png



<!--HONumber=Nov16_HO3-->


