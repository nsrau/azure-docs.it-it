<properties
	pageTitle="Gestire i cluster Hadoop tramite l’interfaccia della riga di comando di Azure | Microsoft Azure"
	description="Come utilizzare l’interfaccia della riga di comando di Azure per gestire i cluster Hadoop in HDInsight"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/17/2015"
	ms.author="jgao"/>

# Gestire cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-portal-management-selector.md)]

Informazioni su come utilizzare l’interfaccia della riga di comando di Azure per gestire i cluster Hadoop in Azure HDInsight. L'interfaccia della riga di comando di Azure viene implementata in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, incluse Windows, Mac e Linux.

L'interfaccia della riga di comando di Azure è open source. Il codice sorgente viene gestito in GitHub all'indirizzo <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>.

In questo articolo viene illustrato solo l’uso dell’interfaccia della riga di comando di Azure con HDInsight. Per una guida generale sull'uso dell’interfaccia della riga di comando di Azure vedere [Come utilizzare l’interfaccia della riga di comando di Azure][azure-command-line-tools].


##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Interfaccia della riga di comando di Azure**: per informazioni sull’installazione e la configurazione vedere [Installare e configurare l’interfaccia della riga di comando di Azure](../xplat-cli-install.md).

##Installare

Se non lo si è già fatto, usare il documento [Installare e configurare l’interfaccia della riga di comando di Azure](../xplat-cli-install.md) per installare e configurare l’interfaccia della riga di comando di Azure.

##Effettuare il provisioning di un cluster HDInsight

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


HDInsight usa un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure.

Dopo avere importato il file di impostazioni di pubblicazione è possibile usare il comando seguente per creare un account di archiviazione:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]L'account di archiviazione deve trovarsi in HDInsight nel data center.


Per informazioni sulla creazione di un account di archiviazione di Azure tramite il portale di anteprima di Azure, vedere [Creare, gestire o eliminare un account di archiviazione][azure-create-storageaccount].

Se si dispone già di un account di archiviazione, ma non se ne conosce né il nome né la chiave, è possibile usare i comandi seguenti per recuperare tali informazioni:

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di anteprima di Azure, vedere la sezione "Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Creare, gestire o eliminare un account di archiviazione][azure-create-storageaccount].


Il comando **azure hdinsight cluster create** crea il contenitore se non esiste. Se si sceglie di creare il contenitore prima, è possibile usare il comando seguente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster:

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Per effettuare il provisioning di un cluster HDInsight tramite un file di configurazione
In genere, si effettua il provisioning di un cluster HDInsight, vi si eseguono i processi e quindi si elimina il cluster per ridurre il costo. L'interfaccia della riga di comando consente di salvare le configurazioni in un file, per poterle riutilizzare ogni volta che si effettua il provisioning di un cluster.

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"

	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

	azure hdinsight cluster create --config <file>



![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##Elencare i cluster e visualizzarne i dettagli
Utilizzare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Eliminazione di un cluster
Usare il comando seguente per eliminare un cluster:

	azure hdinsight cluster delete <ClusterName>

##<a name="scaling"></a>Scalare un cluster

Per modificare le dimensioni del cluster Hadoop mediante Azure PowerShell, eseguire il comando seguente da un computer client:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>

##Passaggi successivi
In questo articolo si è appreso come eseguire diverse attività amministrative relative ai cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare in HDInsight tramite il portale di anteprima di Azure][hdinsight-admin-portal]
* [Gestire cluster Hadoop in HDInsight tramite Azure PowerShell][hdinsight-admin-powershell]
* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Come utilizzare l’interfaccia della riga di comando di Azure][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Elenco e visualizzazione di cluster"

<!---HONumber=Oct15_HO3-->