<properties
	pageTitle="Personalizzare cluster HDInsight tramite Bootstrap | Microsoft Azure"
	description="Informazioni su come personalizzare cluster HDInsight tramite Bootstrap."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>  

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="jgao"/>  

# Personalizzare cluster HDInsight tramite Bootstrap

A volte può essere necessario modificare i file di configurazione che includono:

- clusterIdentity.xml
- core-site.xml
- gateway.xml
- hbase-env.xml
- hbase-site.xml
- hdfs-site.xml
- hive-env.xml
- hive-site.xml
- mapred-site
- oozie-site.xml
- oozie-env.xml
- storm-site.xml
- tez-site.xml
- webhcat-site.xml
- yarn-site.xml

I cluster non possono conservare le modifiche a causa della creazione di nuove immagini. Per altre informazioni sulla creazione di nuove immagini, vedere il blog relativo ai [riavvii delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Per mantenere le modifiche per l'intero ciclo di vita dei cluster, è possibile usare la personalizzazione dei cluster HDInsight durante il processo di creazione. Questo è il metodo consigliato per modificare le configurazioni di un cluster e mantenere questi eventi di riavvio Azure per la ricreazione dell'immagine. Queste modifiche alla configurazione vengono applicate prima dell'avvio del servizio, pertanto non sarà necessario riavviare i servizi.

Sono disponibili 3 metodi per usare Bootstrap:

- Uso di Azure PowerShell

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
- Usare .NET SDK
- Usare un modello di Azure Resource Manager

Per informazioni sull'installazione di componenti aggiuntivi nel cluster HDInsight durante la creazione, vedere:

- [Personalizzare cluster HDInsight mediante Azione di script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [Personalizzare cluster HDInsight mediante Azione di script (Windows)](hdinsight-hadoop-customize-cluster.md)

## Uso di Azure PowerShell

Il codice PowerShell seguente personalizza una configurazione Hive:

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

Uno script di PowerShell completo funzionante è disponibile nell'[appendice A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample).

**Per verificare la modifica:**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro di sinistra fare clic su **Sfoglia** e quindi su **Cluster HDInsight**.
3. Fare clic sul cluster appena creato usando lo script di PowerShell.
4. Fare clic su **Dashboard** nella parte superiore del pannello per aprire l'interfaccia utente di Ambari.
5. Fare clic su **Hive** nel menu di sinistra.
6. Fare clic su **HiveServer2** da **Riepilogo**.
7. Fare clic sulla scheda **Configurazioni**.
8. Fare clic su **Hive** nel menu di sinistra.
9. Fare clic sulla scheda **Avanzate**.
10. Scorrere verso il basso e quindi espandere le **impostazioni avanzate hive-site**.
11. Cercare **hive.metastore.client.socket.timeout** nella sezione.

Ecco altri esempi relativi alla personalizzazione di altri file di configurazione:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Per altre informazioni, vedere il blog di Azim Uddin relativo alla [personalizzazione della creazione di cluster HDInsight ](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## Usare .NET SDK

Vedere [Creare cluster basati su Linux in HDInsight tramite .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## Usare i modelli di Resource Manager

È possibile usare bootstrap in un modello di Resource Manager:

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop personalizzare cluster bootstrap modello di azure resource manager](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)  



## Vedere anche

- [Creare cluster Hadoop in HDInsight][hdinsight-provision-cluster] contiene istruzioni relative alla creazione di un cluster HDInsight con altre opzioni personalizzate.
- [Sviluppare script di Azione di script per HDInsight][hdinsight-write-script]
- [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark]
- [Installare e usare R nei cluster HDInsight][hdinsight-install-r]
- [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install.md).
- [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fasi durante la creazione di un cluster"

## Appendice A: Esempio di PowerShell

Questo script di PowerShell crea un cluster HDInsight e personalizza un'impostazione Hive:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
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

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion

<!---HONumber=AcomDC_0914_2016-->