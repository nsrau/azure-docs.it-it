---
title: Gestire i cluster di Apache Hadoop con PowerShell-Azure HDInsight
description: Informazioni su come eseguire attività amministrative per i cluster Apache Hadoop in HDInsight tramite Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e37571b0078b4966aab9f505ddf88c2edb353197
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435638"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gestire cluster Apache Hadoop in HDInsight usando Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questo articolo illustra come gestire i cluster di [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight usando il modulo Azure PowerShell AZ. Per l'elenco dei cmdlet di PowerShell per HDInsight, vedere il [riferimento AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il [modulo Az](https://docs.microsoft.com/powershell/azure/overview) di PowerShell installato.

## <a name="create-clusters"></a>Creare cluster

Vedere [Creare cluster basati su Linux in HDInsight tramite Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Elencare i cluster

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

## <a name="scale-clusters"></a>Scalare i cluster

La funzionalità di scalabilità del cluster consente di modificare il numero di nodi del ruolo di lavoro usati da un cluster in esecuzione in Azure HDInsight senza dover ricreare il cluster. Per modificare le dimensioni del cluster Hadoop mediante Azure PowerShell, eseguire il comando seguente da un computer client:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Per altre informazioni sul ridimensionamento dei cluster, vedere [ridimensionare i cluster HDInsight](./hdinsight-scaling-best-practices.md).

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
$hadoopUserPassword = '<Enter the Password>'
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

Si tratta della stessa procedura per concedere/revocare l'accesso HTTP. Se al cluster è stato concesso l'accesso HTTP, è necessario innanzitutto revocarlo.  E quindi concedere l'accesso con le nuove credenziali utente HTTP.

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

* [Cmdlet AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md)
* [Amministrare HDInsight tramite un'interfaccia della riga di comando](hdinsight-administer-use-command-line.md)
* [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Inviare processi Apache Hadoop a livello di codice](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
