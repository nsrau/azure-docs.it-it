<properties linkid="manage-services-hdinsight-provision-hdinsight-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision HDInsight clusters | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision HDInsight clusters" authors="jgao" />

Provisioning di cluster HDInsight
=================================

In questo articolo si apprenderanno diversi modi per eseguire il provisioning del cluster HDInsight.

**Prerequisiti:**

Per eseguire le procedure descritte nell'articolo, è necessario disporre di quanto segue:

-   Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. I cmdlet di PowerShell HDInsight eseguono le attività con la sottoscrizione. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Offerte per i membri](https://www.windowsazure.com/en-us/pricing/member-offers/) oppure [Versione di valutazione gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).

Contenuto dell'articolo
-----------------------

-   [Utilizzo del portale di gestione di Azure](#portal)
-   [Utilizzo di Azure PowerShell](#powershell)
-   [Utilizzo della riga di comando multipiattaforma](#cli)
-   [Utilizzo di .NET SDK per HDInsight](#sdk)
-   [Passaggi successivi](#nextsteps)

Utilizzo del portale di gestione di Azure
-----------------------------------------

I cluster HDInsight utilizzano un contenitore dell'archivio BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione](/en-us/manage/services/storage/how-to-create-a-storage-account/).

> [WACOM.NOTE] Attualmente i cluster HDInsight possono essere ospitati solo nelle aree seguenti: Asia sudorientale, Europa settentrionale, Europa occidentale, Stati Uniti orientali e Stati Uniti occidentali.

In questa sessione viene descritta la procedura per creare un cluster HDInsight mediante l'opzione di creazione personalizzata. Per informazioni sull'utilizzo dell'opzione di creazione rapida, vedere [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/).

**Per creare un cluster HDInsight mediante l'opzione di creazione personalizzata**

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).
2.  Fare clic su **+ NEW** nella parte inferiore della pagina, quindi su **DATA SERVICES**, **HDINSIGHT** e infine su **CUSTOM CREATE**.
3.  Nella pagina Cluster Details digitare o scegliere i valori seguenti:

    Proprietà

    Valore

    CLUSTER NAME

    Assegnare un nome al cluster.

    -   Il nome DNS deve iniziare e finire con un carattere alfanumerico e può contenere trattini.
    -   Il campo deve essere una stringa contenente da 3 a 63 caratteri.

    DATA NODES

    Specificare il numero di nodi nel cluster. Il valore predefinito è 4.

    HDINSIGHT VERSION

    Scegliere la versione. Il valore predefinito è 2.0. Questa versione esegue cluster Hadoop 1.2. La versione 3.0 utilizza cluster Hadoop 2.2. Per ulteriori informazioni, vedere [Versione di Hadoop inclusa in Azure HDInsight](http://www.windowsazure.com/en-us/manage/services/hdinsight/versioning-in-hdinsight/).

    REGION

    Specificare il data center in cui è installato il cluster. La posizione deve essere la stessa dell'archivio BLOB di Azure che verrà utilizzata come file system predefinito. Attualmente è possibile scegliere solo \*Southeast Asia\*, \*North Europe\*, \*West Europe\*, \*East US\* o \*West US\*.

    ![HDI.CustomProvision.Page1](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png)

4.  Fare clic sulla freccia destra nell'angolo inferiore destro della pagina.
5.  Nella pagina Configure Cluster User digitare o scegliere il valore seguente:

    Proprietà

    Valore

    USER NAME

    Specificare il nome utente del cluster HDInsight.

    PASSWORD

    CONFIRM PASSWORD

    Specificare la password utente del cluster HDInsight.

    Enter Hive/Oozie Metastore

    Specificare un database SQL nello stesso data center da utilizzare come metastore Hive/Oozie.

    HIVE META/OOZIESTORE DATABASE

    Specificare il database SQL di Azure che verrà utilizzato come metastore per Hive/OOzie. Questo database SQL deve trovarsi nello stesso data center del cluster HDInsight. Nella casella di riepilogo vengono elencati solo i database SQL nello stesso data center specificato nella pagina Cluster Details.

    DATABASE USER

    Specificare il database SQL che verrà utilizzato per connettersi al database.

    DATABASE USER PASSWORD

    Specificare la password utente del database SQL.

    Per il cluster HDInsight versione 2.0, le credenziali qui specificate consentono di accedere solo ai servizi nel cluster. Dopo la creazione del cluster, è possibile attivare Desktop remoto.

    ![HDI.CustomProvision.Page2](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

6.  Fare clic sulla freccia destra nell'angolo inferiore destro della pagina.
7.  Nella pagina Storage Account digitare o scegliere il valore seguente:

    Proprietà

    Valore

    STORAGE ACCOUNT

    Specificare l'account di archiviazione di Azure che verrà utilizzato come file system predefinito per il cluster HDInsight. È possibile scegliere una delle tre opzioni seguenti:

    -   Use Existing Storage
    -   Create New Storage
    -   Use Storage From Another Subscription

    ACCOUNT NAME

    Quando si sceglie **Use Existing Storage**, nella casella di riepilogo vengono elencati solo gli account di archiviazione che si trovano nello stesso data center.

    ACCOUNT KEY

    Questo campo è disponibile solo quando si sceglie **Use Storage From Another Subscription** nel campo STORAGE ACCOUNT.

    DEFAULT CONTAINER

    Il contenitore predefinito nell'account di archiviazione verrà utilizzato come file system predefinito per il cluster HDInsight. Quando si sceglie **Use Existing Storage** nel campo STORAGE ACCOUNT e **Create default container** nel campo DEFAULT CONTAINER, il nome del contenitore predefinito è lo stesso del cluster. Se esiste già un contenitore con il nome del cluster, al nome del contenitore verrà aggiunto un numero di sequenza. Ad esempio, mycontainer1, mycontainer2 e così via.

    ADDITIONAL STORAGE ACCOUNTS

    HDInsight supporta più account di archiviazione. Un cluster può utilizzare un numero illimitato di account di archiviazione aggiuntivi. Se tuttavia si crea un cluster mediante il portale di gestione, esiste un limite di sette dovuto ai vincoli dell'interfaccia utente. Per ogni account di archiviazione aggiuntivo specificato in questo campo viene aggiunta un'ulteriore pagina Storage Account in cui è possibile specificare le informazioni account. Nella schermata seguente, ad esempio, è selezionato 1 account di archiviazione aggiuntivo e la pagina 4 viene aggiunta alla finestra di dialogo.

    ![HDI.CustomProvision.Page3](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

8.  Fare clic sulla freccia destra nell'angolo inferiore destro della pagina.
9.  Nella pagina Storage Account immettere le informazioni per l'account di archiviazione aggiuntivo:

    ![HDI.CustomProvision.Page4](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

10. Fare clic sul pulsante Complete (icona del segno di spunta) nell'angolo inferiore destro per avviare il processo di provisioning del cluster HDInsight.

Il provisioning di un cluster può richiedere alcuni minuti. Al termine del processo di provisioning, nella colonna dello stato del cluster verrà visualizzato **Running**.

> [WACOM.NOTE] Dopo aver scelto un account di archiviazione di Azure per il cluster HDInsight, non è possibile eliminare l'account né sostituirlo con un altro account.

Utilizzo di Azure PowerShell
----------------------------

Azure PowerShell è un ambiente di scripting potente che può essere utilizzato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/). Per ulteriori informazioni sull'utilizzo di PowerShell con HDInsight, vedere [Amministrazione di HDInsight tramite PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/). Per l'elenco dei cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx).

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight utilizzando PowerShell:

-   Creazione di un account di archiviazione di Azure
-   Creazione di un contenitore BLOB di Azure
-   Creazione di un cluster HDInsight

HDInsight utilizza un contenitore dell'archivio BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, sono necessari un account di archiviazione di Azure e un contenitore dell'archiviazione. L'account di archiviazione deve trovarsi nello stesso data center che include il cluster HDInsight.

**Per creare un account di archiviazione di Azure**

-   Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

          $storageAccountName = "<StorageAcccountName>"
          $location = "<MicrosoftDataCenter>"     # For example, "West US"
            
          # Create an Azure storage account
          New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Se si dispone già di un account di archiviazione, ma non si conosce il nome account e la chiave dell'account, è possibile utilizzare i comandi di PowerShell seguenti per recuperare le informazioni:

          # List storage accounts for the current subscription
          Get-AzureStorageAccount

          # List the keys for a storage account
          Get-AzureStorageKey "<StorageAccountName>"

**Per creare il contenitore dell'archiviazione di Azure**

-   Eseguire i comandi seguenti da una finestra di Azure PowerShell:

          $storageAccountName = "<StorageAccountName>"
          $storageAccountKey = "<StorageAccountKey>"
          $containerName="<ContainerName>"

          # Create a storage context object
          $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                                 -StorageAccountKey $storageAccountKey  
             
          # Create a Blob storage container
          New-AzureStorageContainer -Name $containerName -Context $destContext

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per eseguire il provisioning di un cluster HDInsight**

-   Eseguire i comandi seguenti da una finestra di Azure PowerShell:

          $subscriptionName = "<SubscriptionName>"     # The name of the Azure subscription.
          $storageAccountName = "<StorageAccountName>"    # The Azure storage account that hosts the default container. Il contenitore predefinito verrà utilizzato come file system predefinito.
          $containerName = "<ContainerName>"              # The Azure Blob storage container that will be used as the default file system for the HDInsight cluster.

          $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
          $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. Deve trovarsi nello stesso data center dell'account di archiviazione.
          $clusterNodes = <ClusterSizeInNodes>          # The number of nodes in the HDInsight cluster.

          # Get the storage primary key based on the account name
          Select-AzureSubscription $subscriptionName
          $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

          # Create a new HDInsight cluster
          New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    Il provisioning del cluster può richiedere alcuni minuti.

    ![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

È inoltre possibile effettuare il provisioning del cluster e configurarlo per connettere più archiviazioni BLOB di Azure o metastore Hive e Oozie personalizzati. Questa funzionalità avanzata consente di separare la durata dei dati e dei metadati da quella del cluster.

**Per effettuare il provisioning di un cluster HDInsight mediante la configurazione**

-   Eseguire i comandi seguenti da una finestra di Windows PowerShell:

          $subscriptionName = "<SubscriptionName>"
          $clusterName = "<ClusterName>"
          $location = "<MicrosoftDataCenter>"
          $clusterNodes = <ClusterSizeInNodes>
            
          $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
          $containerName_Default = "<DefaultFileSystemContainerName>"
            
          $storageAccountName_Add1 = "<AdditionalStorageAccountName>"
            
          $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
          $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
          $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
          $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"
            
          # Get the storage account keys
          Select-AzureSubscription $subscriptionName
          $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
          $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
            
          $oozieCreds = Get-Credential -Message "Oozie metastore"
          $hiveCreds = Get-Credential -Message "Hive metastore"
            
          # Create a Blob storage container
          #$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
          #New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context
            
          # Create a new HDInsight cluster
          $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
              Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
              Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
              Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
              Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                  New-AzureHDInsightCluster -Name $clusterName -Location $location

**Per elencare i cluster HDInsight**

-   Eseguire i comandi seguenti da una finestra di Azure PowerShell:

          Get-AzureHDInsightCluster -Name <ClusterName>

Utilizzo della riga di comando multipiattaforma
-----------------------------------------------

Un'altra opzione per effettuare il provisioning di un cluster HDInsight è l'interfaccia della riga di comando multipiattaforma. Lo strumento da riga di comando viene implementato in Node.js. Può essere utilizzato in tutte le piattaforme che supportano Node.js, inclusi Windows, Mac e Linux. Lo strumento da riga di comando è open source. Il codice sorgente viene gestito GitHub all'indirizzo <https://github.com/WindowsAzure/azure-sdk-tools-xplat>. Per una guida generale sull'utilizzo dell'interfaccia della riga di comando, vedere [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/). Per la documentazione di riferimento completa, vedere [Strumento da riga di comando di Azure per Mac e Linux](/en-us/manage/linux/other-resources/command-line-tools/). In questo articolo viene descritto solo l'utilizzo dell'interfaccia della riga di comando da Windows.

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight utilizzando la riga di comando multipiattaforma:

-   Installazione della riga di comando multipiattaforma
-   Download e importazione di impostazioni di pubblicazione dell'account Azure
-   Creazione di un account di archiviazione di Azure
-   Provisioning di un cluster

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

1.  Andare a **http://www.windowsazure.com/en-us/downloads/**.
2.  Scorrere verso il basso fino alla sezione **Strumenti da riga di comando** e quindi fare clic sul collegamento relativo all'**interfaccia della riga di comando multipiattaforma** e seguire l'Installazione guidata piattaforma Web.

Prima di utilizzare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono utilizzate dall'interfaccia della riga di comando per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà utilizzata per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

> [WACOM.NOTE] Il file di impostazioni di pubblicazione contiene informazioni riservate. Si consiglia di eliminare il file o di eseguire ulteriori passaggi per crittografare la cartella utente contenente il file. In Windows, modificare le proprietà della cartella o utilizzare BitLocker.

**Per scaricare e importare impostazioni di pubblicazione**

1.  Aprire **Command Prompt**.
2.  Per scaricare il file di impostazioni di pubblicazione, eseguire il comando seguente.

        azure account download

    ![HDI.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png)

    Il comando mostra le istruzioni per scaricare il file, incluso un URL.

3.  Aprire **Internet Explorer** e andare all'URL elencato nella finestra del prompt dei comandi.
4.  Fare clic su **Save** per salvare il file nella workstation.
5.  Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

        azure account import <file>

    Nella schermata precedente, il file di impostazioni di pubblicazione è stato salvato nella cartella C:\\HDInsight sulla workstation.

HDInsight utilizza un contenitore dell'archivio BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, è necessario un account di archiviazione di Azure. L'account di archiviazione deve trovarsi nello stesso data center.

**Per creare un account di archiviazione di Azure**

-   Nella finestra del prompt dei comandi eseguire il comando seguente:

          azure account storage create [options] <StorageAccountName>

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

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per creare un cluster HDInsight**

-   Nella finestra del prompt dei comandi eseguire il comando seguente:

          azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation](./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png)

In genere, si effettua il provisioning di un cluster HDInsight, si eseguono i processi e quindi si elimina il cluster per ridurre il costo. L'interfaccia della riga di comando consente di salvare le configurazioni in un file, per poterle riutilizzare ogni volta che si effettua il provisioning di un cluster.

**Per effettuare il provisioning di un cluster HDInsight mediante un file di configurazione**

-   Nella finestra del prompt dei comandi eseguire il comando seguente:

          azure hdinsight cluster config create <file>
             
          azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
             
          azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
                 --storageAccountKey "<StorageAccountKey>"
             
          azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
                 --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
             
          azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
                 --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
             
          azure hdinsight cluster create --config <file>

    ![HDI.CLIClusterCreationConfig](./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png)

**Per elencare e mostrare i dettagli dei cluster**

-   Utilizzare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

          azure hdinsight cluster list
          azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster](./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Elenco e visualizzazione di cluster")

**Per eliminare un cluster**

-   Utilizzare il comando seguente per eliminare un cluster:

          azure hdinsight cluster delete <ClusterName>

Utilizzo di .NET SDK per HDInsight
----------------------------------

.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'utilizzo di HDInsight da .NET.

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight utilizzando l'SDK:

-   Installazione di .NET SDK per HDInsight
-   Creazione di un'applicazione console
-   Esecuzione dell'applicazione

**Per installare .NET SDK per HDInsight** È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare l'applicazione console di Visual Studio**

1.  Aprire Visual Studio 2012.

2.  Scegliere **Nuovo** dal menu File, quindi fare clic su **Progetto**.

3.  In Nuovo progetto digitare o selezionare i valori seguenti:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Proprietà
    Valore</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Categoria
    Templates/Visual C#/Windows</td>
    <td align="left">Modello
    Applicazione console</td>
    </tr>
    </tbody>
    </table>

4.  Fare clic su **OK** per creare il progetto.

5.  Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Package Manager Console**.

6.  Eseguire i seguenti comandi nella console per installare i pacchetti.

         Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7.  In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8.  Aggiungere le istruzioni using seguenti all'inizio del file:

         using System.Security.Cryptography.X509Certificates;
         using Microsoft.WindowsAzure.Management.HDInsight;
         using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Nella funzione Main() copiare e incollare il codice seguente:

         string certfriendlyname = "<CertificateFriendlyName>";
         string subscriptionid = "<WindowsAzureSubscriptionID>";
         string clustername = "<HDInsightClusterName>";
         string location = "<MicrosoftDataCenter>";
         string storageaccountname = "<WindowsAzureStorageAccountName>";     // Full path must be used
         string storageaccountkey = "<WindowsAzureStorageAccountKey>";
         string containername = "<HDInsightDefaultContainerName>";
         string username = "<HDInsightUsername>";
         string password = "<HDInsightUserPassword>";
         int clustersize = <NumberOfNodesInTheCluster>;

         // Get the certificate object from certificate store using the friendly name to identify it
         X509Store store = new X509Store();
         store.Open(OpenFlags.ReadOnly);
         X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

         // Create the storage account if it doesn't exist.

         // Create the container if it doesn't exist.

         // Create an HDInsightClient object
         HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
         var client = HDInsightClient.Connect(creds);

         // Supply th cluster information
         ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
         {
             Name = clustername,
             Location = location,
             DefaultStorageAccountName = storageaccountname,
             DefaultStorageAccountKey = storageaccountkey,
             DefaultStorageContainer = containername,
             UserName = username,
             Password = password,
             ClusterSizeInNodes = clustersize
         };

         // Create the cluster
         Console.WriteLine("Creating the HDInsight cluster ...");

         ClusterDetails cluster = client.CreateCluster(clusterInfo);

         Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
         Console.WriteLine("Press ENTER to continue.");
         Console.ReadKey();

10. Sostituire le variabili all'inizio della funzione main().

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. La creazione di un cluster HDInsight può richiedere alcuni minuti.

Passaggi successivi
-------------------

In questo articolo si sono appresi vari modi per eseguire il provisioning di un cluster HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Amministrazione di HDInsight tramite PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Invio di processi Hadoop a livello di codice](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Documentazione di Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

