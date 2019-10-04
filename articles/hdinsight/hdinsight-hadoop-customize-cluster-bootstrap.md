---
title: Personalizzare le configurazioni del cluster HDInsight di Azure usando bootstrap
description: Informazioni su come personalizzare la configurazione del cluster HDInsight a livello di codice usando i modelli .NET, PowerShell e Gestione risorse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098679"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizzare cluster HDInsight tramite Bootstrap

Gli script bootstrap consentono di installare e configurare i componenti in Azure HDInsight a livello di codice.

Sono disponibili tre approcci per impostare le impostazioni del file di configurazione durante la creazione del cluster HDInsight:

* Usare Azure PowerShell
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

## <a name="prerequisites"></a>Prerequisiti

* Se si usa PowerShell, sarà necessario il [modulo AZ](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

Il codice PowerShell seguente personalizza una configurazione [Apache Hive](https://hive.apache.org/):

> [!IMPORTANT]  
> Potrebbe essere `Spark2Defaults` necessario usare il parametro con [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). È possibile passare valori vuoti al parametro, come illustrato nell'esempio di codice riportato di seguito.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

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

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Cluster HDInsight** dal menu di sinistra. Se non viene visualizzato, prima fare clic su **Tutti i servizi**.
3. Fare clic sul cluster appena creato usando lo script di PowerShell.
4. Fare clic su **Dashboard** nella parte superiore del pannello per aprire l'interfaccia utente di Ambari.
5. Fare clic su **Hive** nel menu di sinistra.
6. Fare clic su **HiveServer2** da **Riepilogo**.
7. Fare clic sulla scheda **Configurazioni** .
8. Fare clic su **Hive** nel menu di sinistra.
9. Fare clic sulla scheda **Avanzate** .
10. Scorrere verso il basso e quindi espandere le **impostazioni avanzate hive-site**.
11. Cercare **hive.metastore.client.socket.timeout** nella sezione.

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

* [Creare cluster Apache Hadoop in HDInsight][hdinsight-provision-cluster] fornisce istruzioni su come creare un cluster HDInsight usando altre opzioni personalizzate.
* [Sviluppare script di Azione di script per HDInsight][hdinsight-write-script]
* [Installare e usare Apache Spark nei cluster HDInsight][hdinsight-install-spark]
* [Installare e usare Apache Giraph in cluster Hadoop di HDInsight](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fasi durante la creazione di un cluster"

## <a name="appendix-powershell-sample"></a>Appendice: Esempio PowerShell

Questo script di PowerShell crea un cluster HDInsight e Personalizza un'impostazione hive. Assicurarsi di immettere i valori per `$nameToken`, `$httpPassword`e `$sshPassword`.

> [!WARNING]  
> Il tipo di account di archiviazione `BlobStorage` non può essere usato per cluster HDInsight.

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
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

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
