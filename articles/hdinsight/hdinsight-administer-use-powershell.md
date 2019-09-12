---
title: Gestire cluster Apache Hadoop in HDInsight con PowerShell - Azure
description: Informazioni su come eseguire attività amministrative per i cluster Apache Hadoop in HDInsight tramite Azure PowerShell.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tyfox
ms.openlocfilehash: 3f4ccd8de1f26ea898b0e7ec4bb57aa20b1be209
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885345"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gestire cluster Apache Hadoop in HDInsight usando Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questo articolo illustra come gestire i cluster di [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight usando il modulo Azure PowerShell AZ. Per l'elenco dei cmdlet di PowerShell per HDInsight, vedere il [riferimento AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Il [modulo Az](https://docs.microsoft.com/powershell/azure/overview) di PowerShell installato.

## <a name="create-clusters"></a>Creare i cluster
Vedere [Creare cluster basati su Linux in HDInsight tramite Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Elencare cluster
Usare il comando seguente per visualizzare l'elenco di tutti i cluster nella sottoscrizione corrente:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Mostrare cluster
Usare il comando seguente per visualizzare i dettagli di un cluster specifico nella sottoscrizione corrente:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Eliminare cluster
Utilizzare il comando seguente per eliminare un cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

È inoltre possibile eliminare un cluster rimuovendo il gruppo di risorse che lo contiene. L'eliminazione di un gruppo di risorse eliminerà tutte le risorse nel gruppo, compreso l’account di archiviazione predefinito.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Ridimensionare i cluster
La funzionalità di scalabilità del cluster consente di modificare il numero di nodi del ruolo di lavoro usati da un cluster in esecuzione in Azure HDInsight senza dover ricreare il cluster.

Questa sezione descrive l'impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Apache Hadoop

    È possibile aumentare facilmente il numero di nodi del ruolo di lavoro in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

    Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. A causa del riavvio dei servizi, tutti i processi in esecuzione e in attesa daranno esito negativo dopo il completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.
* Apache HBase

    È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.

    A tale scopo, è possibile scegliere tra due opzioni:

  * Interfaccia utente Web di Storm
  * Interfaccia della riga di comando (CLI)

    Per altri dettagli, vedere la [documentazione di Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

    ![Ribilanciamento scala di HDInsight Storm](./media/hdinsight-administer-use-powershell/portal-scale-cluster.png)

    Di seguito viene fornito un esempio d'uso del comando CLI per ribilanciare la topologia di Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Per modificare le dimensioni del cluster Hadoop mediante Azure PowerShell, eseguire il comando seguente da un computer client:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Concedere/Revocare l'accesso
Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. L'accesso può essere revocato/concesso, Per revocare:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Per concedere:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.

La concessione e la revoca dell'accesso possono essere eseguite anche tramite il portale. Vedere [gestire i cluster Apache Hadoop in HDInsight usando il portale di Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aggiornare le credenziali utente HTTP
È la stessa procedura di Concessione/revoca dell'accesso HTTP. Se al cluster è stato concesso l'accesso HTTP, è necessario innanzitutto revocarlo.  E quindi concedere l'accesso con le nuove credenziali utente HTTP.

## <a name="find-the-default-storage-account"></a>Trovare l'account di archiviazione predefinito
Lo script di PowerShell seguente dimostra come ottenere il nome dell'account di archiviazione predefinito e le informazioni correlate:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Trovare il gruppo di risorse
Nella modalità Resource Manager ogni cluster HDInsight appartiene a un gruppo di risorse di Azure.  Trovare il gruppo di risorse:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Inviare i processi
**Inviare processi MapReduce**

Vedere [Eseguire gli esempi di MapReduce inclusi in HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Inviare i processi Apache Hive**

Vedere [Eseguire query Apache Hive usando PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Inviare i processi Apache Sqoop**

Vedere [Usare Apache Sqoop con HDInsight](hadoop/hdinsight-use-sqoop.md).

**Inviare i processi Apache Oozie**

Vedere [Usare Apache Oozie con Apache Hadoop per definire ed eseguire un flusso di lavoro in HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Caricare dati nell'archivio BLOB di Azure

Vedere [caricare dati in HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento dei cmdlet di HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md)
* [Amministrare HDInsight tramite un'interfaccia della riga di comando](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Inviare processi Apache Hadoop a livello di codice](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
