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
	ms.date="12/16/2015"
	ms.author="jgao"/>

# Gestire cluster Hadoop in HDInsight tramite la CLI di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-portal-management-selector.md)]

Informazioni su come usare l'[Interfaccia della riga di comando di Azure](xplat-cli-install.md) per gestire i cluster Hadoop in Azure HDInsight. L'interfaccia della riga di comando di Azure viene implementata in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, incluse Windows, Mac e Linux.

In questo articolo viene illustrato solo l’uso dell’interfaccia della riga di comando di Azure con HDInsight. Per una guida generale sull'uso dell’interfaccia della riga di comando di Azure vedere [Installare e configurare l’interfaccia della riga di comando di Azure][azure-command-line-tools].

##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Interfaccia della riga di comando di Azure**: per informazioni sull’installazione e la configurazione vedere [Installare e configurare l’interfaccia della riga di comando di Azure](../xplat-cli-install.md).
- **Collegarsi ad Azure**, usando il comando seguente:

		azure login

	Per altre informazioni sull'autenticazione con un account aziendale o dell'istituto di istruzione, vedere l'argomento relativo alla [connessione a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md).
	
- **Passare alla modalità Gestione risorse di Azure**, usando il comando seguente:

		azure config mode arm

Per ottenere assistenza, utilizzare l’opzione **-h**. Ad esempio:

	azure hdinsight cluster create -h
	
##Creare i cluster

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

È necessario avere una Gestione delle risorse di Azure (ARM) e un account di archiviazione BLOB di Azure prima di poter creare un cluster HDInsight. Per creare un cluster HDInsight, è necessario specificare quanto segue:

- **Gruppo di risorse di Azure**: è necessario creare un account di Data Lake Analytics all'interno di un gruppo di risorse di Azure. Gestione risorse di Azure consente di lavorare con le risorse dell'applicazione come gruppo. È quindi possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione mediante un'unica operazione coordinata. 

	Per elencare i gruppi di risorse nella sottoscrizione:
	
		azure group list 
	
	Per creare un nuovo gruppo di risorse:
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome del cluster HDInsight**

- **Posizione**: uno dei data center di Azure che supporta cluster HDInsight. Per un elenco di percorsi supportati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Account di archiviazione predefinito**: HDInsight usa un contenitore di archiviazione BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure.

	Per creare un nuovo account di archiviazione di Azure:
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE]L'account di archiviazione deve trovarsi in HDInsight nel data center. Il tipo di account di archiviazione non può essere ZRS, perché ZRS non supporta la tabella.

	Per informazioni sulla creazione di un account di archiviazione di Azure tramite il portale di Azure, vedere [Creare, gestire o eliminare un account di archiviazione][azure-create-storageaccount].
	
	Se si dispone già di un account di archiviazione, ma non se ne conosce né il nome né la chiave, è possibile usare i comandi seguenti per recuperare tali informazioni:
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Per i dettagli sull'acquisizione delle informazioni mediante il portale di Azure, vedere la sezione "Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Creare, gestire o eliminare un account di archiviazione][azure-create-storageaccount].

- **(Facoltativo) Contenitore di BLOB predefinito**: il comando **azure hdinsight cluster create** crea il contenitore se non esiste. Se si sceglie di creare il contenitore prima, è possibile usare il comando seguente:

	azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

Dopo aver preparato l'account di archiviazione, è possibile creare un cluster.

	azure hdinsight cluster create --resource-group <Resource Group Name> --clusterName <Cluster Name> --location <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --storageAccountName <Default Storage Account Name> --storageAccountKey <Storage Account Key> --storageContainer <Default Storage Container> --username <HDInsight Cluster Username> --password <HDInsight Cluster Password> --sshUserName <SSH Username> --sshPassword <SSH User Password> --workerNodeCount <Number of Worker Nodes>


##Creare cluster mediante i file di configurazione
In genere, si crea un cluster HDInsight, vi si eseguono i processi e quindi si elimina il cluster per ridurre il costo. L'interfaccia della riga di comando consente di salvare le configurazioni in un file, per poterle riutilizzare ogni volta che si crea un cluster.

	azure hdinsight config create [options ] <Config File Path> <overwirte>
	azure hdinsight config add-config-values [options] <Config File Path>
	azure hdinsight config add-script-action [options] <Config File Path>

Esempio: creare un file di configurazione che contiene un'azione script per l'esecuzione durante la creazione di un cluster.

	hdinsight config create "C:\myFiles\configFile.config"
	hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"

##Creare cluster con un’azione di script

Di seguito è fornito un esempio:

	azure hdinsight cluster create -g mahirg001 -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01
	
Per informazioni generali sull'azione di script, vedere [Personalizzare cluster HDInsight mediante l'azione script (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

##Elencare i cluster e visualizzarne i dettagli
Utilizzare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

	azure hdinsight cluster list
	azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Eliminare cluster
Utilizzare il comando seguente per eliminare un cluster:

	azure hdinsight cluster delete <Cluster Name>

##Ridimensionare i cluster

Per modificare le dimensioni del cluster Hadoop:

	azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## Abilitare/disabilitare l'accesso HTTP per un cluster

	azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
	azure hdinsight cluster disable-http-access [options] <Cluster Name>

## Abilitare/disabilitare l'accesso RDP per un cluster

  	azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
  	azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##Passaggi successivi
In questo articolo si è appreso come eseguire diverse attività amministrative relative ai cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare cluster Hadoop in HDInsight tramite il portale di Azure][hdinsight-admin-portal]
* [Gestire cluster Hadoop in HDInsight tramite Azure PowerShell][hdinsight-admin-powershell]
* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Come utilizzare l’interfaccia della riga di comando di Azure][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli.md
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

<!---HONumber=AcomDC_1217_2015-->