---
title: Personalizzare le configurazioni del cluster HDInsight di Azure usando bootstrap
description: Informazioni su come personalizzare la configurazione del cluster HDInsight a livello di codice usando i modelli .NET, PowerShell e Gestione risorse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: baef54fc5c8fd03ea190da2023dcba2e96abb982
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406287"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizzare cluster HDInsight tramite Bootstrap

Gli script bootstrap consentono di installare e configurare i componenti in Azure HDInsight a livello di codice.

Sono disponibili tre approcci per impostare le impostazioni del file di configurazione durante la creazione del cluster HDInsight:

* Uso di Azure PowerShell
* Usare .NET SDK
* Usare un modello di Azure Resource Manager

Usando questi metodi programmatici, ad esempio, è possibile configurare le opzioni nei file seguenti:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (configurazione kafka-broker)

Per informazioni sull'installazione di componenti aggiuntivi nel cluster HDInsight durante la fase di creazione, vedere [personalizzare i cluster HDInsight mediante l'azione script (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>prerequisiti

* Se si usa PowerShell, è necessario il [modulo AZ](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Uso di Azure PowerShell

Il codice PowerShell seguente personalizza una configurazione [Apache Hive](https://hive.apache.org/):

> [!IMPORTANT]  
> Potrebbe essere necessario usare il parametro `Spark2Defaults` con [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). È possibile passare valori vuoti al parametro, come illustrato nell'esempio di codice riportato di seguito.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Uno script di PowerShell completo funzionante è disponibile nell'[appendice](#appendix-powershell-sample).

**Per verificare la modifica:**

1. Passare a `https://CLUSTERNAME.azurehdinsight.net/` dove `CLUSTERNAME` è il nome del cluster.
1. Dal menu a sinistra passare a **Hive** > **configs** > **Advanced**.
1. Espandere **Advanced hive-site**.
1. Individuare **hive. Metastore. client. Socket. timeout** e verificare che il valore sia **90**.

Ecco altri esempi relativi alla personalizzazione di altri file di configurazione:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Usare .NET SDK

Vedere [Creare cluster basati su Linux in HDInsight tramite .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager

È possibile usare bootstrap in un modello di Resource Manager:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop Personalizza il modello di Azure Resource Manager bootstrap del cluster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Vedere anche

* [Creare cluster Apache Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) fornisce istruzioni su come creare un cluster HDInsight usando altre opzioni personalizzate.
* [Sviluppare script di Azione script per HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installare e usare Apache Spark nei cluster HDInsight](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Installare e usare Apache Giraph in cluster Hadoop di HDInsight](hdinsight-hadoop-giraph-install.md)

## <a name="appendix-powershell-sample"></a>Appendice: esempio di PowerShell

Questo script di PowerShell crea un cluster HDInsight e Personalizza un'impostazione hive. Assicurarsi di immettere i valori per `$nameToken`, `$httpPassword`e `$sshPassword`.

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
