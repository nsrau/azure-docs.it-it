<properties 
	pageTitle="Gestire cluster Hadoop tramite un'interfaccia della riga di comando | Microsoft Azure" 
	description="Informazioni su come usare l'interfaccia della riga di comando multipiattaforma per la gestione di cluster Hadoop in HDInsight su qualsiasi piattaforma che supporti Node.js, incluse Windows, Mac e Linux." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>

# Gestire cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando multipiattaforma

Informazioni su come usare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows per gestire i cluster Hadoop in Azure HDInsight. L'interfaccia della riga di comando di Azure viene implementata in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, incluse Windows, Mac e Linux.

L'interfaccia della riga di comando di Azure è open source. Il codice sorgente viene gestito in GitHub all'indirizzo <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>.

In questo articolo viene descritto solo l'uso dell'interfaccia della riga di comando da Windows. Per una guida generale sull'uso dell'interfaccia della riga di comando, vedere [Come usare gli strumenti da riga di comando di Azure per Mac e Linux][azure-command-line-tools].


##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Sottoscrizione di Azure**: Azure è una piattaforma basata su sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].

##Installazione
L'interfaccia della riga di comando può essere installata tramite *Node.js Package Manager (NPM)* o Windows Installer.

**Per installare l'interfaccia della riga di comando tramite NPM**

1.	Passare a **www.nodejs.org**.
2.	Fare clic su **INSTALL** e seguire le istruzioni usando le impostazioni predefinite.
3.	Aprire **Prompt dei comandi** (o il **prompt dei comandi di Azure** o **Prompt dei comandi per gli sviluppatori per VS2012**) dalla workstation.
4.	Nella finestra del prompt dei comandi eseguire il comando seguente:

		npm install -g azure-cli

	> [AZURE.NOTE]Se viene visualizzato un errore che informa che il comando NPM non è stato trovato, verificare che i percorsi seguenti siano presenti nella variabile di ambiente **PATH**: <i>C:\\Programmi (x86)\\nodejs;C:\\Utenti[nomeutente]\\AppData\\Roaming\\npm</i> or <i>C:\\Programmi\\nodejs;C:\\Utenti[nomeutente]\\AppData\\Roaming\\npm</i>


5.	Eseguire il comando seguente per verificare l'installazione:

		azure hdinsight -h

	È possibile usare l'opzione **-h** a livelli diversi per visualizzare le informazioni della Guida. ad esempio:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Per installare l'interfaccia della riga di comando tramite Windows Installer**

1.	Passare a **http://azure.microsoft.com/downloads/**. 
2.	Scorrere verso il basso fino alla sezione **Strumenti da riga di comando** e quindi fare clic sul collegamento relativo all'**interfaccia della riga di comando multipiattaforma** e seguire l'Installazione guidata piattaforma Web.

##Scaricare e importare un file di impostazioni di pubblicazione dell'account Azure

Prima di usare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono usate dall'interfaccia della riga di comando per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà usata per le operazioni successive. Sarà necessario importare il file di impostazioni di pubblicazione una sola volta.

> [AZURE.NOTE]Il file di impostazioni di pubblicazione contiene informazioni riservate. Si consiglia di eliminare il file o di eseguire altri passaggi per crittografare la cartella utente contenente il file. In Windows modificare le proprietà della cartella o usare Crittografia unità BitLocker.


**Per scaricare e importare il file publishsettings**

1.	Aprire un prompt dei comandi.
2.	Per scaricare il file publishsettings, eseguire il comando seguente:

		azure account download
 
	![Download dell'account Azure tramite l'interfaccia della riga di comando.][image-cli-account-download-import]

	Il comando mostra le istruzioni per scaricare il file, incluso un URL.

3.	Aprire Internet Explorer e andare all'URL elencato nella finestra del prompt dei comandi.
4.	Fare clic su **Save** per salvare il file nella workstation.
5.	Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

		azure account import <file>

	Nella schermata precedente il file di impostazioni di pubblicazione è stato salvato nella cartella C:\\HDInsight sulla workstation.


##Provisioning di un cluster HDInsight

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


HDInsight usa un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure.

Dopo avere importato il file di impostazioni di pubblicazione è possibile usare il comando seguente per creare un account di archiviazione:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]L'account di archiviazione deve trovarsi in HDInsight nel data center.


Per informazioni sulla creazione di un account di archiviazione di Azure tramite il portale di Azure, vedere [Creare, gestire o eliminare un account di archiviazione][azure-create-storageaccount].

Se si ha già un account di archiviazione, ma non si conoscono il nome dell'account e la chiave dell'account, è possibile usare i comandi seguenti per recuperare le informazioni:

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di Azure, vedere la sezione "Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Creare, gestire o eliminare un account di archiviazione][azure-create-storageaccount].


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
Utilizzare il comando seguente per eliminare un cluster:

	azure hdinsight cluster delete <ClusterName>




##Passaggi successivi
In questo articolo si è appreso come eseguire diverse attività amministrative relative ai cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare cluster Hadoop in HDInsight tramite il portale di Azure][hdinsight-admin-portal]
* [Amministrare cluster Hadoop in HDInsight tramite Azure PowerShell][hdinsight-admin-powershell]
* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Come usare l'interfaccia della riga di comando multipiattaforma di Azure per Mac, Linux e Windows][azure-command-line-tools]


[azure-command-line-tools]: xplat-cli.md
[azure-create-storageaccount]: storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Elenco e visualizzazione di cluster"

<!--HONumber=54-->