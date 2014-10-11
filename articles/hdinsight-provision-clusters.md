<properties linkid="manage-services-hdinsight-provision-hadoop-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision Hadoop clusters in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Provisioning di cluster Hadoop in HDInsight con opzioni personalizzate

Questo articolo descrive i diversi modi disponibili per effettuare il provisioning personalizzato di un cluster Hadoop in Azure HDInsight, ovvero usando il portale di gestione di Azure, PowerShell, gli strumenti da riga di comando o .NET SDK per HDInsight. In questo articolo viene illustrato il provisioning dei cluster Hadoop. Per istruzioni su come effettuare il provisioning di un cluster HBase, vedere [Provisioning di cluster HBase in HDInsight][]. Per informazioni utili per scegliere tra Hadoop e HBase, vedere l'articolo che illustra le [differenze tra Hadoop e HBase][].

## Informazioni sui cluster HDInsight

Il motivo per cui vengono menzionati i cluster ogni volta che si discute di Hadoop o BigData è che Hadoop consente l'elaborazione distribuita di dati di grandi dimensioni tra diversi nodi di un cluster. Il cluster presenta un'architettura master/slave con un master (chiamato anche nodo head o Name Node) e un numero indeterminato di slave (chiamati anche nodi dati). Per altre informazioni, vedere [Apache Hadoop][].

Un cluster HDInsight astrae i dettagli dell'implementazione di Hadoop in modo che non sia necessario preoccuparsi di come comunicare con diversi nodi di un cluster. Quando si esegue il provisioning di un cluster HDInsight, si esegue il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. Per altre informazioni, vedere [Introduzione a Hadoop in HDInsight][].

In questo articolo vengono fornite istruzioni sui diversi modi di effettuare il provisioning di un cluster. Per informazioni sull'approccio introduttivo al provisioning di un cluster, vedere [Introduzione all'utilizzo di Azure HDInsight][].

**Prerequisiti:**

Per eseguire le procedure descritte nell'articolo è necessario:

-   Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. I cmdlet di PowerShell HDInsight eseguono le attività con la sottoscrizione. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][], [Offerte per i membri][] oppure [Versione di valutazione gratuita][].

## Contenuto dell'articolo

-   [Utilizzo del portale di gestione di Azure][]
-   [Utilizzo di Azure PowerShell][]
-   [Utilizzo della riga di comando multipiattaforma][]
-   [Utilizzo di .NET SDK per HDInsight][]
-   [Passaggi successivi][]

## <span id="portal"></span></a> Utilizzo del portale di gestione di Azure

I cluster HDInsight usano un contenitore dell'archivio BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight][]. Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione][].

> [WACOM.NOTE] Attualmente i cluster HDInsight possono essere ospitati solo nelle aree seguenti: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali** e **Stati Uniti centro-meridionali**.

**Per creare un cluster HDInsight mediante l'opzione di creazione personalizzata**

1.  Accedere al [portale di gestione di Azure][].
2.  Fare clic su **+ NEW** nella parte inferiore della pagina, quindi su **DATA SERVICES**, **HDINSIGHT** e infine su **CUSTOM CREATE**.
3.  Nella pagina **Dettagli cluster** digitare o scegliere i valori seguenti:

    ![HDI.CustomCreateCluster][]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Proprietà</th>
    <th align="left">Valore</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome del cluster</td>
    <td align="left"><p>Assegnare un nome al cluster.</p>
    <ul>
    <li>Il nome DNS deve iniziare e finire con un carattere alfanumerico e può contenere trattini.</li>
    <li>Il campo deve essere una stringa contenente da 3 a 63 caratteri.</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Tipo di cluster</td>
    <td align="left">Per il tipo di cluster selezionare <strong>Hadoop</strong>.</td>
    </tr>
    <tr class="odd">
    <td align="left">Versione di HDInsight</td>
    <td align="left">Scegliere la versione. Per Hadoop, l'impostazione predefinita è HDInsight versione 3.1, che usa Hadoop 2.4.</td>
    </tr>
    </tbody>
    </table>

    Immettere o selezionare i valori come illustrato nella tabella, quindi fare clic sulla freccia destra.

4.  Nella pagina **Configura cluster** immettere o selezionare i valori seguenti:

    | Nome               | Valore                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    |--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nodi di dati       | Numero di nodi di dati che si desidera distribuire. Ai fini di test, creare un cluster a singolo nodo.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
                          Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | Area/Rete virtuale | Selezionare la stessa area geografica specificata per l'account di archiviazione creato nell'ultima procedura. HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Più avanti nella configurazione sarà possibile selezionare un account di archiviazione situato nella stessa area geografica specificata qui. Le aree geografiche disponibili sono: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali**, **Stati Uniti centro-meridionali** |

5.  Nella pagina **Configura utente cluster** digitare o scegliere il valore seguente:

    ![HDI.CustomCreateCluster.ClusterUser][]

    | Proprietà                  | Valore                                                                                                                                                                                                                                                                                                             |
    |----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome utente                | Specificare il nome utente del cluster HDInsight.                                                                                                                                                                                                                                                                  |
    | Password/Conferma password | Specificare la password utente del cluster HDInsight.                                                                                                                                                                                                                                                              |
    | Enter Hive/Oozie Metastore | Selezionare questa casella di controllo per specificare un database SQL nello stesso data center del cluster, da usare come metastore Hive/Oozie. Ciò risulta utile se si vogliono conservare i metadati sui progetti Hive/Oozie anche dopo aver eliminato un cluster.                                             |
    | Database metastore         | Specificare il database SQL di Azure che verrà utilizzato come metastore per Hive/OOzie. Questo database SQL deve trovarsi nello stesso data center del cluster HDInsight. Nella casella di riepilogo vengono elencati solo i database SQL nello stesso data center specificato nella pagina **Dettagli cluster**. |
    | Utente database            | Specificare il database SQL che verrà utilizzato per connettersi al database.                                                                                                                                                                                                                                      |
    | Password utente database   | Specificare la password utente del database SQL.                                                                                                                                                                                                                                                                   |

    > [WACOM.NOTE] Il database SQL usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

    Fare clic sulla freccia destra.

6.  Nella pagina **Account di archiviazione** indicare il valore seguente:

    ![HDI.CustomCreateCluster.StorageAccount][]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Proprietà</th>
    <th align="left">Valore</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Storage Account</td>
    <td align="left">Specificare l'account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HDInsight. È possibile scegliere una delle tre opzioni seguenti:
    <ul>
    <li>Use Existing Storage</li>
    <li>Create New Storage</li>
    <li>Use Storage From Another Subscription</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Nome account</td>
    <td align="left"><ul>
    <li>Se si sceglie di usare le risorse di archiviazione esistenti, per <strong>Nome account</strong> selezionare un account di archiviazione esistente. Nella casella di riepilogo vengono elencati solo gli account di archiviazione che si trovano nello stesso data center in cui si è scelto di effettuare il provisioning del cluster..</li>
    <li>Se si sceglie l'opzione <strong>Crea nuova archiviazione</strong> o <strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong> è necessario indicare il nome dell'account di archiviazione.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">Chiave account</td>
    <td align="left">Se si sceglie l'opzione <strong>Utilizza l'archiviazione da un'altra sottoscrizione</strong> specificare la chiave dell'account relativa a tale account di archiviazione.</td>
    </tr>
    <tr class="even">
    <td align="left">Contenitore predefinito</td>
    <td align="left"><p>Consente di specificare il contenitore predefinito nell'account di archiviazione viene usato come file system predefinito per il cluster HDInsight. Se si sceglie <strong>Utilizzare l'archiviazione esistente</strong> per il campo <strong>Account di archiviazione</strong> e non ci sono contenitori in tale account, il contenitore viene creato per impostazione predefinita con lo stesso nome del cluster. Se esiste già un contenitore con il nome del cluster, al nome del contenitore verrà aggiunto un numero di sequenza. Ad esempio, mycontainer1, mycontainer2 e così via. Tuttavia, se un account di archiviazione esistente ha un contenitore con un nome differente da quello del cluster specificato, sarà possibile usare anche tale contenitore.</p>
    <p>Se si è scelto di creare una nuova risorsa di archiviazione da un'altra sottoscrizione di Azure è necessario specificare il nome del contenitore predefinito.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Account di archiviazione aggiuntivi</td>
    <td align="left">HDInsight supporta più account di archiviazione. Un cluster può utilizzare un numero illimitato di account di archiviazione aggiuntivi. Se tuttavia si crea un cluster mediante il portale di gestione, esiste un limite di sette dovuto ai vincoli dell'interfaccia utente. Per ogni account di archiviazione aggiuntivo specificato viene aggiunta un'ulteriore pagina Account di archiviazione alla procedura guidata, in cui è possibile specificare le informazioni account. Nella schermata precedente, ad esempio, è selezionato un account di archiviazione aggiuntivo, per cui la pagina 5 viene aggiunta alla finestra di dialogo.</td>
    </tr>
    </tbody>
    </table>

    Fare clic sulla freccia destra.

7.  Nella pagina **Account di archiviazione** immettere le informazioni per l'account di archiviazione aggiuntivo:

    ![HDI.CustomCreateCluster.AddOnStorage][]

    Anche in questo caso, è possibile scegliere risorse di archiviazione esistenti, creare nuove risorse di archiviazione o usare le risorse di archiviazione di un'altra sottoscrizione di Azure. La procedura che consente di ottenere i valori è simile al passaggio precedente.

    Fare clic sul segno di spunta per avviare il provisioning del cluster. Al termine del provisioning, nella colonna relativa allo stato verrà visualizzato il valore **In esecuzione**.

    > [WACOM.NOTE] Dopo aver scelto un account di archiviazione di Azure per il cluster HDInsight, non è possibile eliminare l'account né sostituirlo con un altro account.

## <span id="powershell"></span></a> Utilizzo di Azure PowerShell

Azure PowerShell è un ambiente di scripting potente che può essere utilizzato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. In questa sezione sono contenute le istruzioni su come effettuare il provisioning di un cluster HDInsight con Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell][]. Per ulteriori informazioni sull'utilizzo di PowerShell con HDInsight, vedere [Amministrazione di HDInsight tramite PowerShell][]. Per l'elenco dei cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight][].

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight utilizzando PowerShell:

-   Creazione di un account di archiviazione di Azure
-   Creazione di un contenitore BLOB di Azure
-   Creazione di un cluster HDInsight

HDInsight utilizza un contenitore dell'archivio BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, sono necessari un account di archiviazione di Azure e un contenitore dell'archiviazione. L'account di archiviazione deve trovarsi nello stesso data center che include il cluster HDInsight.

**Per creare un account di archiviazione di Azure**

-   Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Se si dispone già di un account di archiviazione, ma non si conosce il nome account e la chiave dell'account, è possibile utilizzare i comandi di PowerShell seguenti per recuperare le informazioni:

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**Per creare il contenitore dell'archiviazione BLOB di Azure**

-   Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per eseguire il provisioning di un cluster HDInsight**

-   Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    Quando richiesto, immettere le credenziali per il cluster. Il provisioning del cluster può richiedere alcuni minuti.

    ![HDI.CLI.Provision][]

**Per effettuare il provisioning di un cluster HDInsight con le opzioni di configurazione personalizzate**

Durante il provisioning di un cluster, è possibile usare altre opzioni di configurazione, come la connessione a più risorse di archiviazione BLOB di Azure oppure l'utilizzo di un database SQL di Azure per i metastore Hive e Oozie. Ciò consente di separare la durata dei dati e dei metadati da quella del cluster.

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
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location

    > [WACOM.NOTE] Il database SQL usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

**Per elencare i cluster HDInsight**

-   Eseguire i comandi seguenti da una finestra della console di Azure Powershell per elencare il cluster HDInsight e verificare che sia stato effettuato correttamente il provisioning del cluster:

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a> Utilizzo della riga di comando multipiattaforma

Un'altra opzione per effettuare il provisioning di un cluster HDInsight è l'interfaccia della riga di comando multipiattaforma. Lo strumento da riga di comando viene implementato in Node.js. Può essere utilizzato in tutte le piattaforme che supportano Node.js, inclusi Windows, Mac e Linux. Lo strumento da riga di comando è open source. Il codice sorgente viene gestito in GitHub all'indirizzo <https://github.com/Azure/azure-sdk-tools-xplat>. Per una guida generale sull'utilizzo dell'interfaccia della riga di comando, vedere [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux][]. Per la documentazione di riferimento completa, vedere [Strumento da riga di comando di Azure per Mac e Linux][]. In questo articolo viene descritto solo l'utilizzo dell'interfaccia della riga di comando da Windows.

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight utilizzando la riga di comando multipiattaforma:

-   Installazione della riga di comando multipiattaforma
-   Download e importazione di impostazioni di pubblicazione dell'account Azure
-   Creazione di un account di archiviazione di Azure
-   Provisioning di un cluster

L'interfaccia della riga di comando può essere installata mediante *Node.js Package Manager (NPM)* o Windows Installer. Microsoft consiglia di eseguire l'installazione usando solo una delle due opzioni.

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

1.  Andare a **[http://azure.microsoft.com/it-it/downloads/][]**.
2.  Scorrere verso il basso fino alla sezione **Strumenti da riga di comando** e quindi fare clic sul collegamento relativo all'**interfaccia della riga di comando multipiattaforma** e seguire l'Installazione guidata piattaforma Web.

**Per scaricare e importare impostazioni di pubblicazione**

Prima di utilizzare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono utilizzate dall'interfaccia della riga di comando per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà utilizzata per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

> [WACOM.NOTE] Il file di impostazioni di pubblicazione contiene informazioni riservate. Microsoft consiglia di eliminare il file o di eseguire ulteriori passaggi per crittografare la cartella utente contenente il file. In Windows, modificare le proprietà della cartella o utilizzare BitLocker.

1.  Aprire **Command Prompt**.
2.  Per scaricare il file di impostazioni di pubblicazione, eseguire il comando seguente.

        azure account download

    ![HDI.CLIAccountDownloadImport][]

    Il comando consente di avviare la pagina Web in cui scaricare il file di impostazioni di pubblicazione.

3.  Al prompt fare clic su **Salva** e indicare un percorso in cui salvare il file.
4.  Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

        azure account import <file>

    Nella schermata precedente, il file di impostazioni di pubblicazione è stato salvato nella cartella C:\\HDInsight sulla workstation.

**Per creare un account di archiviazione di Azure**

HDInsight utilizza un contenitore dell'archivio BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, è necessario un account di archiviazione di Azure. L'account di archiviazione deve trovarsi nello stesso data center.

-   Al prompt dei comandi eseguire il comando seguente per un account di archiviazione di Azure:

        azure storage account create [options] <StorageAccountName>

    Quando viene richiesto un percorso, selezionare un percorso in cui sia possibile effettuare il provisioning di un cluster HDInsight. La risorsa di archiviazione deve trovarsi nello stesso percorso del cluster HDInsight. Attualmente i cluster HDInsight possono essere ospitati solo nelle aree seguenti: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali** e **Stati Uniti centro-meridionali**.

Per informazioni sulla creazione di un account di archiviazione di Azure mediante il portale di gestione di Azure, vedere [Come creare un account di archiviazione][].

Se si ha già un account di archiviazione, ma non si conosce il nome account e la chiave dell'account, è possibile utilizzare i comandi seguenti per recuperare le informazioni:

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di gestione, vedere la sezione *Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione* di [Come gestire gli account di archiviazione][].

Un cluster HDInsight richiede anche un contenitore in un account di archiviazione. Se l'account di archiviazione fornito non ha già un contenitore, la *creazione guidata del cluster di azure hdinsight* richiede il nome di un contenitore e lo crea. Se tuttavia si vuole creare il contenitore prima, è possibile usare il comando seguente:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per eseguire il provisioning di un cluster HDInsight**

-   Nella finestra del prompt dei comandi eseguire il comando seguente:

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][]

**Per effettuare il provisioning di un cluster HDInsight mediante un file di configurazione**

In genere, si effettua il provisioning di un cluster HDInsight, si eseguono i processi e quindi si elimina il cluster per ridurre il costo. L'interfaccia della riga di comando consente di salvare le configurazioni in un file, per poterle riutilizzare ogni volta che si effettua il provisioning di un cluster.

-   Al prompt dei comandi eseguire i comandi seguenti:

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] Il database SQL usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

    ![HDI.CLIClusterCreationConfig][]

**Per elencare e mostrare i dettagli dei cluster**

-   Utilizzare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][]

**Per eliminare un cluster**

-   Utilizzare il comando seguente per eliminare un cluster:

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a> Utilizzo di .NET SDK per HDInsight

.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'utilizzo di HDInsight da un'applicazione .NET.

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight utilizzando l'SDK:

-   Installazione di .NET SDK per HDInsight
-   Creare un certificato autofirmato
-   Creazione di un'applicazione console
-   Esecuzione dell'applicazione

**Per installare .NET SDK per HDInsight**

È possibile installare l'ultima build pubblicata dell'SDK da [NuGet][]. Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un certificato autofirmato**

1.  Creare un certificato autofirmato che venga usato per autenticare le richieste. È possibile usare IIS o [makecert][] per creare il certificato.

2.  Passare al percorso del certificato, fare clic con il pulsante destro del mouse su di esso, fare clic su **Installa certificato** quindi installare il certificato nell'archivio personale sul computer. Modificare le proprietà del certificato per assegnare ad esso un nome descrittivo.

3.  Impostare il certificato nel portale di gestione di Azure. Dal portale, fare clic su **Impostazioni** in basso a sinistra, quindi fare clic su **Certificati di gestione**. Nella parte inferiore della pagina, fare clic su **Carica** e seguire le istruzioni per caricare il file con estensione cer creato al passaggio precedente.

    ![HDI.ClusterCreate.UploadCert][]

**Per creare l'applicazione console di Visual Studio**

1.  Aprire Visual Studio 2013.

2.  Scegliere **Nuovo** dal menu File, quindi fare clic su **Progetto**.

3.  In Nuovo progetto digitare o selezionare i valori seguenti:

    | Proprietà | Valore                       |
    |-----------|------------------------------|
    | Categoria | Templates/Visual C#/Windows |
    | Modello   | Applicazione console         |
    | Nome      | CreateHDICluster             |

4.  Fare clic su **OK** per creare il progetto.

5.  Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6.  Eseguire i seguenti comandi nella console per installare i pacchetti.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7.  In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8.  Aggiungere le istruzioni using seguenti all'inizio del file:

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Nella funzione Main() copiare e incollare il codice seguente:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
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

## <span id="nextsteps"></span></a>Passaggi successivi

In questo articolo si sono appresi vari modi per eseguire il provisioning di un cluster HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight][]
-   [Amministrazione di HDInsight tramite PowerShell][]
-   [Invio di processi Hadoop a livello di codice][]
-   [Documentazione di Azure HDInsight SDK][]

  [Provisioning di cluster HBase in HDInsight]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [differenze tra Hadoop e HBase]: http://go.microsoft.com/fwlink/?LinkId=510237
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [Introduzione a Hadoop in HDInsight]: ../hdinsight-introduction/
  [Introduzione all'utilizzo di Azure HDInsight]: ../hdinsight-get-started/
  [Opzioni di acquisto]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Utilizzo del portale di gestione di Azure]: #portal
  [Utilizzo di Azure PowerShell]: #powershell
  [Utilizzo della riga di comando multipiattaforma]: #cli
  [Utilizzo di .NET SDK per HDInsight]: #sdk
  [Passaggi successivi]: #nextsteps
  [Utilizzo dell'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Come creare un account di archiviazione]: ../storage-create-storage-account/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [Installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Documentazione di riferimento di cmdlet di HDInsight]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux]: ../xplat-cli/
  [Strumento da riga di comando di Azure per Mac e Linux]: ../command-line-tools/
  [http://azure.microsoft.com/it-it/downloads/]: http://azure.microsoft.com/en-us/downloads/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [Come gestire gli account di archiviazione]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [makecert]: http://msdn.microsoft.com/en-us/library/bfsktky3(v=vs.110).aspx
  [HDI.ClusterCreate.UploadCert]: ./media/hdinsight-get-started/HDI.ClusterCreate.UploadCert.png
  [Invio di processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Documentazione di Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
