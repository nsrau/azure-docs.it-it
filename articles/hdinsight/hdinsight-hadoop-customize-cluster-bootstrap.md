---
title: Personalizzare le configurazioni cluster di Azure HDInsight usando il bootstrapCustomize Azure HDInsight cluster configurations using bootstrap
description: Informazioni su come personalizzare la configurazione del cluster HDInsight a livello di programmazione usando i modelli .NET, PowerShell e Resource Manager.Learn how to customize HDInsight cluster configuration programmatically using .NET, PowerShell, and Resource Manager templates.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 796dbc53d1adf310028e06dea319b9a60d5cf54b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529357"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizzare cluster HDInsight tramite Bootstrap

Gli script Bootstrap consentono di installare e configurare i componenti in Azure HDInsight a livello di codice.

Esistono tre approcci per impostare le impostazioni del file di configurazione durante la creazione del cluster HDInsight:There are three approaches to set configuration file settings as your HDInsight cluster is created:

* Usare Azure PowerShell
* Usare .NET SDK
* Usare un modello di Azure Resource Manager

Ad esempio, utilizzando questi metodi a livello di codice, è possibile configurare le opzioni nei file seguenti:For example, using these programmatic methods, you can configure options in these files:

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

Per informazioni sull'installazione di componenti aggiuntivi nel cluster HDInsight durante la fase di creazione, vedere [Personalizzare i cluster HDInsight usando Script Action (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Prerequisiti

* Se si usa PowerShell, è necessario il [modulo Az](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

Il codice PowerShell seguente personalizza una configurazione [Apache Hive](https://hive.apache.org/):

> [!IMPORTANT]  
> Potrebbe `Spark2Defaults` essere necessario utilizzare il parametro con [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). È possibile passare valori vuoti al parametro, come illustrato nell'esempio di codice riportato di seguito.

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

1. Passare `https://CLUSTERNAME.azurehdinsight.net/` a `CLUSTERNAME` where is the name del cluster.
1. Dal menu a sinistra, accedere a Hive**Configs** > Advanced (Configurazione **Hive** > **Advanced ).**
1. Espandere **Sito hive avanzato**.
1. Individuare **hive.metastore.client.socket.timeout** e verificare che il valore sia **90s**.

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

Vedere [Azure HDInsight SDK per .NET.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)

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

![Hadoop personalizza il modello di Azure Resource Manager per il bootstrap del clusterHadoop customizes cluster bootstrap Azure Resource Manager template](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

Frammento di modello di Resource Manager di esempio per cambiare la configurazione in spark2-defaults per pulire periodicamente i log eventi dall'archiviazione.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>Vedere anche

* [Creare cluster Apache Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) fornisce istruzioni relative alla creazione di un cluster HDInsight tramite l'uso di altre opzioni personalizzate.
* [Sviluppare script di azione di script per HDInsightDevelop Script Action scripts for HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installare e usare Apache Spark in cluster HDInsight](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Installare e utilizzare Apache Giraph sui cluster HDInsight](hdinsight-hadoop-giraph-install.md).

## <a name="appendix-powershell-sample"></a>Appendice: esempio di PowerShell

Questo script di PowerShell crea un cluster HDInsight e personalizza un'impostazione Hive.This PowerShell script creates an HDInsight cluster and customizes a Hive setting. Assicurarsi di immettere `$httpPassword`i `$sshPassword`valori per `$nameToken`, , e .

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
