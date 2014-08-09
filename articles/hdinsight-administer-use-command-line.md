<properties linkid="manage-services-hdinsight-administer-hdinsight-using-command-line" urlDisplayName="HDInsight Administration" pageTitle="Administer HDInsight using using the Cross-Platform Command-Line Interface | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to use the Cross-Platform Command-Line Interface to manage HDInsight clusters on any platform that supports Node.js, including Windows, Mac, and Linux." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer HDInsight using the Cross-platform Command-line Interface" authors="jgao" />

Amministrazione di HDInsight tramite l'interfaccia della riga di comando multipiattaforma
=========================================================================================

In questo articolo viene illustrato come utilizzare l'interfaccia della riga di comando multipiattaforma per la gestione di cluster HDInsight. Lo strumento da riga di comando viene implementato in Node.js. Può essere utilizzato in tutte le piattaforme che supportano Node.js, inclusi Windows, Mac e Linux.

Lo strumento da riga di comando è open source. Il codice sorgente viene gestito GitHub all'indirizzo <https://github.com/WindowsAzure/azure-sdk-tools-xplat>.

In questo articolo viene descritto solo l'utilizzo dell'interfaccia della riga di comando da Windows. Per una guida generale sull'utilizzo dell'interfaccia della riga di comando, vedere [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/). Per la documentazione di riferimento completa, vedere [Strumento da riga di comando di Azure per Mac e Linux](/en-us/manage/linux/other-resources/command-line-tools/).

**Prerequisiti:**

Per eseguire le procedure descritte nell'articolo, è necessario disporre di quanto segue:

-   **Sottoscrizione di Azure**. Azure è una piattaforma basata su sottoscrizione. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Offerte per i membri](https://www.windowsazure.com/en-us/pricing/member-offers/) oppure [Versione di valutazione gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).

Contenuto dell'articolo
-----------------------

-   [Installazione](#installation)
-   [Download e importazione di impostazioni di pubblicazione dell'account Azure](#importsettings)
-   [Provisioning di un cluster](#provision)
-   [Provisioning di un cluster mediante un file di configurazione](#provisionconfigfile)
-   [Elenco e visualizzazione di cluster](#listshow)
-   [Eliminazione di un cluster](#delete)
-   [Passaggi successivi](#nextsteps)

Installazione
-------------

L'interfaccia della riga di comando può essere installata mediante *Node.js Package Manager (NPM)* o Windows Installer.

**Per installare l'interfaccia della riga di comando mediante NPM**

1.  Passare a **www.nodejs.org**.
2.  Fare clic su **INSTALL** e seguire le istruzioni utilizzando le impostazioni predefinite.
3.  Aprire **Command Prompt** (o *Azure Command Prompt* o *Developer Command Prompt for VS2012*) dalla workstation.
4.  Nella finestra del prompt dei comandi eseguire il comando seguente.

        npm install -g azure-cli

    > [WACOM.NOTE] Se viene visualizzato un errore che informa che il comando NPM non è stato trovato, verificare che i percorsi seguenti siano presenti nella variabile di ambiente PATH: *C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm* o *C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm*

5.  Eseguire il comando seguente per verificare l'installazione:

        azure hdinsight -h

    È possibile utilizzare l'opzione *-h* a livelli diversi per visualizzare le informazioni della Guida. Ad esempio:

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Per installare l'interfaccia della riga di comando mediante Windows Installer**

1.  Andare a **http://www.windowsazure.com/it-it/downloads/**.
2.  Scorrere verso il basso fino alla sezione **Strumenti da riga di comando** e quindi fare clic sul collegamento relativo all'**interfaccia della riga di comando multipiattaforma** e seguire l'Installazione guidata piattaforma Web.

Download e importazione di impostazioni di pubblicazione dell'account Azure
---------------------------------------------------------------------------

Prima di utilizzare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono utilizzate dall'interfaccia della riga di comando per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà utilizzata per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

> [WACOM.NOTE] Il file di impostazioni di pubblicazione contiene informazioni riservate. Si consiglia di eliminare il file o di eseguire ulteriori passaggi per crittografare la cartella utente contenente il file. In Windows, modificare le proprietà della cartella o utilizzare BitLocker.

**Per scaricare e importare impostazioni di pubblicazione**

1.  Aprire **Command Prompt**.
2.  Per scaricare il file di impostazioni di pubblicazione, eseguire il comando seguente.

        azure account download

    ![HDI.CLIAccountDownloadImport](./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png)

    Il comando mostra le istruzioni per scaricare il file, incluso un URL.

3.  Aprire **Internet Explorer** e andare all'URL elencato nella finestra del prompt dei comandi.
4.  Fare clic su **Save** per salvare il file nella workstation.
5.  Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

        azure account import <file>

    Nella schermata precedente, il file di impostazioni di pubblicazione è stato salvato nella cartella C:\\HDInsight sulla workstation.

Provisioning di un cluster HDInsight
------------------------------------

HDInsight utilizza un contenitore dell'archivio BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, è necessario un account di archiviazione di Azure.

Dopo avere importato il file publishsettings è possibile utilizzare il comando seguente per creare un account di archiviazione:

    azure account storage create [options] <StorageAccountName>

> [WACOM.NOTE] L'account di archiviazione deve trovarsi nello stesso data center. È attualmente possibile eseguire il provisioning di cluster HDInsight solo nei data center seguenti:
><ul>
<li>Asia sudorientale
<li>Europa settentrionale
<li>Europa occidentale
<li>Stati Uniti orientali
<li>Stati Uniti occidentali
</ul>

Per informazioni sulla creazione di un account di archiviazione di Azure mediante il portale di gestione di Azure, vedere [Come creare un account di archiviazione](/en-us/manage/services/storage/how-to-create-a-storage-account/).

Se si dispone già di un account di archiviazione, ma non si conosce il nome account e la chiave dell'account, è possibile utilizzare i comandi seguenti per recuperare le informazioni:

    -- lists storage accounts
    azure account storage list
    -- Shows a storage account
    azure account storage show <StorageAccountName>
    -- Lists the keys for a storage account
    azure account storage keys list <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di gestione, vedere la sezione *Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione* di [Come gestire gli account di archiviazione](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

Il comando *azure hdinsight cluster create* crea il contenitore se non esiste. Se si sceglie di creare il contenitore prima, è possibile utilizzare il comando seguente:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster:

    azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation](./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png)

Provisioning di un cluster HDInsight mediante un file di configurazione
-----------------------------------------------------------------------

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

![HDI.CLIClusterCreationConfig](./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png)

Elenco e visualizzazione di cluster
-----------------------------------

Utilizzare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster](./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Elenco e visualizzazione di cluster")

Eliminazione di un cluster
--------------------------

Utilizzare il comando seguente per eliminare un cluster:

    azure hdinsight cluster delete <ClusterName>

Passaggi successivi
-------------------

In questo articolo si è appreso come eseguire diverse attività amministrative relative ai cluster HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Amministrazione di HDInsight tramite il portale di gestione](/en-us/manage/services/hdinsight/howto-administer-hdinsight/)
-   [Amministrazione di HDInsight tramite PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/)
-   [Strumenti da riga di comando di Azure per Mac e Linux](/en-us/manage/linux/other-resources/command-line-tools/)

