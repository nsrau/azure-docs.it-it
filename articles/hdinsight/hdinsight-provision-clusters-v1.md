<properties 
   pageTitle="Effettuare il provisioning personalizzato di cluster Hadoop in HDInsight | Microsoft Azure" 
   description="Informazioni su come effettuare il provisioning personalizzato di cluster per Azure HDInsight tramite il portale di Azure classico, Azure PowerShell, una riga di comando o .NET SDK" 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2015"
   ms.author="jgao"/>

#Effettuare il provisioning di cluster Hadoop in HDInsight

Informazioni su come pianificare il provisioning di cluster HDInsight.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Effettuare il provisioning di cluster Hadoop in HDInsight](hdinsight-provision-clusters.md) 

**Prerequisiti:**

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opzioni di configurazione di base


- **Nome cluster**

	Il campo Nome cluster consente di identificare un cluster. Il nome del cluster deve rispettare le linee guida seguenti:
	
	- Il campo deve essere una stringa contenente da 3 a 63 caratteri.
	- Il campo può contenere solo lettere, numeri e trattini.

- **Nome sottoscrizione**

	Un cluster HDInsight è associato a una sottoscrizione di Azure.
 
- **Sistema operativo**

	È possibile effettuare il provisioning di cluster HDInsight in uno dei due sistemi operativi seguenti: - **HDInsight su Windows (Windows Server 2012 R2 Datacenter)**: - **HDInsight su Linux (Ubuntu 12.04 LTS per Linux) (Anteprima)**: HDInsight consente di configurare cluster Linux in Azure. Se si ha familiarità con Linux o Unix, è infatti possibile configurare un cluster Linux eseguendo la migrazione da una soluzione Hadoop basata su Linux esistente oppure è possibile scegliere di integrarsi facilmente con i componenti dell'ecosistema Hadoop sviluppati per Linux. Per altre informazioni, vedere l'articolo di [introduzione all'uso di Hadoop su Linux in HDInsight](hdinsight-hadoop-linux-get-started.md).


- **Versione HDInsight**

	Consente di determinare la versione di HDInsight da usare per questo cluster. Per altre informazioni, vedere [Versioni di HDInsight e componenti di Hadoop](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Tipo di cluster** e **Dimensioni del cluster (definiti anche nodi di dati)**

	HDInsight consente ai clienti di distribuire svariati tipi di cluster, per diversi carichi di lavoro di analisi di dati. Sono attualmente disponibili i tipi di cluster seguenti:
	
	- Cluster Hadoop: per carichi di lavoro di query e analisi
	- Cluster HBase: per carichi di lavoro NoSQL
	- Cluster Storm: per carichi di lavoro di elaborazione di eventi in tempo reale
	- Cluster Spark (anteprima): per carichi di lavoro di elaborazione in memoria, query interattive, streaming e Machine Learning.

	![Cluster HDInsight](./media/hdinsight-provision-clusters-v1/hdinsight.clusters.png)
 
	> [AZURE.NOTE]Il *Cluster HDInsight di Azure* è definito anche *Cluster Hadoop in HDInsight* o *Cluster HDInsight*. In alcuni casi viene usato in modo intercambiabile con *Cluster Hadoop*. Tutte queste definizioni fanno riferimento ai cluster Hadoop ospitati nell'ambiente Microsoft Azure.

	Un determinato tipo di cluster include ruoli diversi per i diversi nodi, che consentono a un cliente di ridimensionare i nodi in un ruolo specifico in modo appropriato per i dettagli del carico di lavoro. Ad esempio, è possibile effettuare il provisioning dei nodi di lavoro di un cluster Hadoop con una quantità di memoria elevata, se il tipo di analisi da eseguire richiede un utilizzo intensivo della memoria.

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters-v1/HDInsight.Hadoop.roles.png)

	I cluster Hadoop per HDInsight vengono distribuiti con due ruoli:

	- Nodo head (2 nodi)
	- Nodo dati (almeno 1 nodo)

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters-v1/HDInsight.HBase.roles.png)

	I cluster HBase per HDInsight vengono distribuiti con tre ruoli: - Server head (2 nodi) - Server area (almeno 1 nodo) - Nodi master/Zookeeper (3 nodi)
	
	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters-v1/HDInsight.Storm.roles.png)

	I cluster Storm per HDInsight vengono distribuiti con tre ruoli: - Nodi Nimbus (2 nodi) - Server Supervisor (almeno 1 nodo) - Nodi Zookeeper (3 nodi)
	

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters-v1/HDInsight.Spark.roles.png)

	I cluster Spark per HDInsight vengono distribuiti con tre ruoli: - Nodo head (2 nodi) - Nodo di lavoro (almeno 1 nodo) - Nodi Zookeeper (3 nodi) (gratuiti per Zookeeper A1)

	L'uso di questi nodi viene addebitato ai clienti per la durata del ciclo di vita del cluster. La fatturazione inizia dopo la creazione di un cluster e si interrompe quando il cluster viene eliminato. Non è possibile deallocare o mettere in attesa i cluster. Le dimensioni del cluster influiscono sul prezzo del cluster. Ai fini dell'apprendimento, è consigliabile usare un nodo di dati. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.
	
- **Area/Rete virtuale (o posizione)**

	![Aree di Azure](./media/hdinsight-provision-clusters-v1/Azure.regions.png)

	Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Dimensioni nodo**

	![Dimensioni dei nodi delle VM di HDInsight](./media/hdinsight-provision-clusters-v1/hdinsight.node.sizes.png)

	Selezionare le dimensioni delle VM per i nodi. Per altre informazioni, vedere [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md)

	Il costo può variare in base alla scelta delle macchine virtuali. HDInsight usa tutte macchine virtuali di livello standard per i nodi del cluster. Per informazioni sul modo in cui le dimensioni delle macchine virtuali influiscono sui prezzi, vedere <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Prezzi di HDInsight</a>.


- **Utenti di HDInsight**

	I cluster HDInsight consentono di configurare due account utente durante il provisioning:

	- Utente HTTP. Il nome utente predefinito è admin con la configurazione di base nel portale di Azure classico.
	- Utente RDP (cluster Windows): usato per la connessione al cluster tramite RDP. Quando si crea l'account, è necessario impostare una scadenza corrispondente a una data entro 90 giorni dalla data odierna. 
	- Utente SSH (cluster Linux): usato per la connessione ai cluster tramite SSH. È possibile creare account utente SSH aggiuntivi dopo la creazione del cluster tramite l'esecuzione dei passaggi illustrati in [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
  
 

- **Account di archiviazione di Azure**


	L'interfaccia HDFS originale usa molti dischi locali sul cluster. HDInsight usa invece l'archivio BLOB di Azure per l'archiviazione dei dati. L'archiviazione BLOB di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. Grazie a un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati presenti nell'archiviazione BLOB. L'archiviazione dei dati nell'archiviazione BLOB consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.
	
	Durante la configurazione è necessario specificare un account di archiviazione di Azure e un contenitore di archiviazione BLOB di Azure nell'account di archiviazione di Azure. Alcuni processi di provisioning richiedono prima di tutto la creazione dell'account di archiviazione di Azure e del contenitore di archiviazione BLOB. Il contenitore di archiviazione BLOB viene usato dal cluster come posizione di archiviazione predefinita. Facoltativamente, è possibile specificare account di archiviazione di Azure aggiuntivi (account di archiviazione collegati) a cui il cluster potrà accedere. Il cluster può accedere anche a eventuali contenitori BLOB configurati con accesso in lettura pubblico completo o accesso in lettura pubblico solo per i BLOB. Per altre informazioni sull'accesso con limitazioni, vedere [Gestire l'accesso alle risorse di archiviazione di Azure](storage-manage-access-to-resources.md).

	![Archiviazione di HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.storage.png)
	
	>[AZURE.NOTE]Un contenitore di archiviazione BLOB offre un raggruppamento di un set di BLOB, come illustrato nell'immagine:
	
	![Archivio BLOB di Azure](./media/hdinsight-provision-clusters-v1/Azure.blob.storage.jpg)
	  
	
	>[AZURE.WARNING]Non condividere un contenitore di archiviazione BLOB tra più cluster. Questa operazione non è supportata.
	
	Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).

- **Metastore Hive/Oozie**

	Il metastore contiene metadati Hive e Oozie, tra cui tabelle di Hive, partizioni, schemi e colonne. Consente di conservare i propri metadati Hive e Oozie, in modo che non sia necessario creare nuovamente tabelle di Hive o processi Oozie quando si effettuerà il provisioning di un nuovo cluster. Per impostazione predefinita, Hive usa un database SQL di Azure incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato. Ad esempio, è disponibile un cluster sottoposto a provisioning con un metastore Hive. Sono state create alcune tabelle Hive. Dopo avere eliminato il cluster e ricreato il cluster mediante lo stesso metastore Hive, sarà possibile visualizzare le tabelle Hive create nel cluster originale.

## Opzioni di configurazione avanzate

>[AZURE.NOTE]Questa sezione è attualmente applicabile solo ai cluster HDInsight basati su Windows.

### Personalizzare i cluster mediante la personalizzazione dei cluster HDInsight

A volte potrebbe essere necessario modificare i file di configurazione. Ecco alcuni file di configurazione.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

I cluster non possono conservare le modifiche a causa del re-imaging. Per ulteriori informazioni, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Per mantenere le modifiche per l'intero ciclo di vita dei cluster, è possibile usare la personalizzazione dei cluster HDInsight durante il processo di provisioning.

L'esempio di script di Azure PowerShell seguente illustra la personalizzazione di una configurazione Hive:

	# hive-site.xml configuration 
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60
	
	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues 
	
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

Ecco altri esempi relativi alla personalizzazione di altri file di configurazione:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1
	
	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50
	
	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000
	
	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
	
Per altre informazioni, vedere il blog di Azim Uddin relativo alla [personalizzazione del provisioning di cluster HDInsight ](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Personalizzare i cluster mediante l'azione script

L'uso di script durante il provisioning consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un'**azione di script**, ovvero un'opzione di configurazione che può essere usata dal portale di Azure classico, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).


### Usare le reti virtuali di Azure

[Rete virtuale di Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permette di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.

	![Diagramma di una configurazione da sito a sito](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-site-to-site.png)

	La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

	![Diagramma di una configurazione da punto a sito](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-point-to-site.png)

Per informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere l'articolo su come [estendere le funzionalità di HDInsight mediante una rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## Strumenti per il provisioning

- Il portale di Azure classico
- Azure PowerShell
- .NET SDK
- CLI

### Usare il portale di Azure classico

Per una spiegazione dei campi, vedere le [opzioni di configurazione di base] e le [opzioni di configurazione avanzate].

**Per creare un cluster HDInsight con l'opzione di creazione personalizzata**

1. Accedere al [portale di Azure classico][azure-management-portal].
2. Fare clic su **+ NUOVO** nella parte inferiore della pagina, quindi su **SERVIZI DATI**, **HDINSIGHT** e infine su **CREAZIONE PERSONALIZZATA**.
3. Nella pagina **Dettagli cluster** digitare o scegliere i valori seguenti:

	- Cluster Name
	- Subscription Name
	- Tipo di cluster
	- Sistema operativo
	- HDInsight Version

4. Nella pagina **Configura cluster** immettere o selezionare i valori seguenti:

	- Nodi dati
	- Area/Rete virtuale
	- Dimensione nodo head
	- Dimensione nodo dati

5. Nella pagina **Configura utente cluster** digitare o scegliere il valore seguente:

	- Nome utente HTTP
	- Password HTTP/Conferma password
	- Abilita Desktop remoto per il cluster
	- Immettere metastore Hive/Oozie

6. Se si è scelto di immettere il metastore Hive/Oozie nella schermata precedente, nella pagina **Configurare il metastore Hive/Oozie** specificare i valori seguenti:

	- Database metastore Hive
	- Utente database
	- Password utente database
	- Database metastore Oozie
	- Utente database
	- Password utente database

7. Nella pagina **Account di archiviazione** specificare i valori seguenti:

	- Account di archiviazione
	- Nome account
	- Contenitore predefinito
	- Account di archiviazione aggiuntivi

8. Nella pagina **Azioni script** fare clic sul pulsante che consente di **aggiungere azioni di script** per specificare i dettagli relativi allo script da eseguire per personalizzare un cluster, ad esempio quello da creare. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).

	- Nome: specificare un nome per l'azione script.
	- URI script: specificare l'URI (Uniform Resource Identifier) dello script da richiamare per personalizzare il cluster.
	- Tipo di nodo: specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
	- Parametri: specificare i parametri, se richiesti dallo script.</td></tr>

	È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.

### Uso di Azure PowerShell
Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questa sezione include le istruzioni su come effettuare il provisioning di un cluster HDInsight con Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet di Windows PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell](../install-configure-powershell.md). Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet di Windows PowerShell per HDInsight, vedere la [documentazione di riferimento dei cmdlet per HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

> [AZURE.NOTE]Gli script di questa sezione possono essere usati per configurare un cluster HDInsight in una rete virtuale di Azure, ma non possono creare una rete virtuale di Azure. Per informazioni sulla creazione di una rete virtuale di Azure, vedere [Attività di configurazione della rete virtuale](../services/virtual-machines/).

Le procedure seguenti sono necessarie per effettuare il provisioning di un cluster HDInsight con Azure PowerShell:

- Creare un account di archiviazione di Azure
- Creazione di un contenitore BLOB di Azure
- Creazione di un cluster HDInsight

Per eseguire gli script, è possibile usare la console di Windows PowerShell o l'ambiente Windows PowerShell Integrated Scripting Environment (ISE).

HDInsight usa un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight sono necessari un account di archiviazione di Azure e un contenitore di archiviazione. L'account di archiviazione deve trovarsi nello stesso data center che include il cluster HDInsight.

**Per connettersi all'account di Azure**

	Add-AzureAccount

Verrà richiesto di immettere le credenziali dell'account Azure.

**Per creare un account di archiviazione di Azure**

	$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
	$location = "<MicrosoftDataCenter>"				# For example, "West US"

	# Create an Azure Storage account
	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Se si dispone già di un account di archiviazione, ma non se ne conosce né il nome né la chiave, è possibile usare i comandi di Windows PowerShell seguenti per recuperare tali informazioni:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount

	# List the keys for a Storage account
	Get-AzureStorageKey "<StorageAccountName>"

**Per creare il contenitore dell'archiviazione BLOB di Azure**

	$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
	$containerName="<ContainerName>"				# Provide a container name

	# Create a storage context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
	                                       -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per effettuare il provisioning di un cluster HDInsight**

> [AZURE.NOTE]I cmdlet di Azure PowerShell rappresentano l'unica soluzione consigliata per modificare le variabili di configurazione in un cluster HDInsight. Le modifiche apportate ai file di configurazione Hadoop durante la connessione al cluster con Desktop remoto possono essere sovrascritte in caso di applicazione di patch del cluster. I valori di configurazione impostati tramite Azure PowerShell vengono conservati se al cluster sono applicate patch.

- Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
	
		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system
	
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
		$hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
		$hadoopUserPassword = "<HadoopUserPassword>"
	
		$secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)
	
		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster
	
		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}
	
		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]I comandi $hadoopUserName e $hadoopUserPassword consentono di creare l'account utente Hadoop per il cluster. Questo account verrà usato per connettersi al cluster ed eseguire processi. Se si usa l'opzione Creazione rapida del portale di Azure classico per effettuare il provisioning di un cluster, il nome utente Hadoop predefinito sarà "admin". Non confondere questo account con l'account utente RDP (Remote Desktop Protocol), che deve essere diverso dall'account utente Hadoop. Per altre informazioni, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure classico][hdinsight-admin-portal].

	Il completamento del provisioning del cluster può richiedere alcuni minuti.

	![HDI.CLI.Provision][image-hdi-ps-provision]

**Per effettuare il provisioning di un cluster HDInsight con le opzioni di configurazione personalizzate**

Durante il provisioning di un cluster è possibile usare le altre opzioni di configurazione, ad esempio la connessione a più contenitori di archiviazioni BLOB di Azure, tramite una rete virtuale o un database SQL di Azure per metastore Hive e Oozie. In questo modo è possibile separare la durata dei dati e dei metadati da quella del cluster.

> [AZURE.NOTE]I cmdlet di Windows PowerShell rappresentano l'unica soluzione consigliata per modificare le variabili di configurazione in un cluster HDInsight. Le modifiche apportate ai file di configurazione Hadoop durante la connessione al cluster con Desktop remoto possono essere sovrascritte in caso di applicazione di patch del cluster. I valori di configurazione impostati tramite Azure PowerShell vengono conservati se al cluster sono applicate patch.

- Eseguire i comandi seguenti da una finestra di Windows PowerShell:

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

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
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
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.

**Per elencare cluster HDInsight**

- Eseguire il comando seguente da una finestra della console di Azure PowerShell per elencare il cluster HDInsight e verificare che sia stato effettuato correttamente il provisioning del cluster:

		Get-AzureHDInsightCluster -Name <ClusterName>


### Usare l'interfaccia della riga di comando di Azure

> [AZURE.NOTE]A partire da 29/8/2014 non è più possibile associare l'interfaccia della riga di comando di Azure a una rete virtuale di Azure.

Un'altra opzione per effettuare il provisioning di un cluster HDInsight è l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure viene implementata in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, inclusi Windows, Mac e Linux. È possibile installare l'interfaccia della riga di comando dai percorsi seguenti:

- **Node.js SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Interfaccia della riga di comando** - <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Per una guida generale sull'uso dell'interfaccia della riga di comando di Azure, vedere l'articolo sull'[interfaccia del la riga di comando di Azure per Mac, Linux e Windows](../xplat-cli-install.md).

Le istruzioni seguenti illustrano come installare la riga di comando Azure in Linux e Windows e quindi come usare la riga di comando per effettuare il provisioning di un cluster.

- [Configurare l'interfaccia della riga di comando di Azure per Linux](#clilin)
- [Configurare l'interfaccia della riga di comando di Azure per Windows](#cliwin)
- [Effettuare il provisioning di cluster HDInsight tramite l’interfaccia della riga di comando di Azure](#cliprovision)

#### <a id="clilin"></a>Configurare l'interfaccia della riga di comando di Azure per Linux

Seguire queste procedure per configurare il computer Linux per l'uso dell'interfaccia della riga di comando di Azure:

- Installare l'interfaccia della riga di comando di Azure tramite Node.js Package Manager (NPM)
- Connettersi alla sottoscrizione di Azure

**Per installare l'interfaccia della riga di comando di Azure tramite NPM**

1.	Aprire una finestra del terminale nel computer Linux ed eseguire il seguente comando:

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Eseguire il comando seguente per verificare l'installazione:

		azure hdinsight -h

	È possibile usare l'opzione *-h* a livelli diversi per visualizzare le informazioni della Guida. ad esempio:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Per connettersi alla sottoscrizione di Azure**

Prima di usare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono usate dall'interfaccia della riga di comando di Azure per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà usata dall'interfaccia della riga di comando di Azure per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

> [AZURE.NOTE]Il file di impostazioni di pubblicazione contiene informazioni riservate. Microsoft consiglia di eliminare il file o di eseguire altri passaggi per crittografare la cartella utente contenente il file. In Windows modificare le proprietà della cartella o usare Crittografia unità BitLocker.


1.	Aprire una finestra del terminale.
2.	Eseguire il comando seguente per accedere alla sottoscrizione di Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Il comando avvia la pagina Web da cui scaricare il file delle impostazioni di pubblicazione. Se la pagina Web non si apre, fare clic sul collegamento nella finestra del terminale per aprire la pagina del browser e accedere al portale.

3.	Scaricare il file di impostazioni di pubblicazione nel computer.
5.	Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Configurare l'interfaccia della riga di comando di Azure per Windows

Seguire queste procedure per configurare il computer Windows per l'uso dell'interfaccia della riga di comando di Azure:

- Installare l'interfaccia della riga di comando di Azure tramite NPM o Windows Installer
- Scaricare e importare le impostazioni di pubblicazione dell'account Azure


L'interfaccia della riga di comando di Azure può essere installata tramite NPM o Windows Installer. È consigliabile eseguire l'installazione usando solo una delle due opzioni.

**Per installare l'interfaccia della riga di comando di Azure tramite NPM**

1.	Passare a **www.nodejs.org**.
2.	Fare clic su **INSTALL** e seguire le istruzioni usando le impostazioni predefinite.
3.	Aprire **Command Prompt** (o *Azure Command Prompt* o *Developer Command Prompt for VS2012*) dalla workstation.
4.	Nella finestra del prompt dei comandi eseguire il comando seguente.

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Se viene visualizzato un messaggio di errore in cui si informa che il comando NPM non è stato trovato, verificare che i percorsi seguenti siano presenti nella variabile di ambiente PATH: <i>C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i> o <i>C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i>

5.	Eseguire il comando seguente per verificare l'installazione:

		azure hdinsight -h

	È possibile usare l'opzione *-h* a livelli diversi per visualizzare le informazioni della Guida. Ad esempio:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Per installare l'interfaccia della riga di comando di Azure tramite Windows Installer**

1.	Passare a ****http://azure.microsoft.com/downloads/**.
2.	Scorrere verso il basso fino alla sezione **Strumenti da riga di comando** e quindi fare clic sul collegamento relativo all'**interfaccia della riga di comando di Azure** e seguire l'Installazione guidata piattaforma Web.

**Per scaricare e importare impostazioni di pubblicazione**

Prima di usare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono usate dall'interfaccia della riga di comando di Azure per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà usata dall'interfaccia della riga di comando di Azure per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

> [AZURE.NOTE]Il file di impostazioni di pubblicazione contiene informazioni riservate. Microsoft consiglia di eliminare il file o di eseguire altri passaggi per crittografare la cartella utente contenente il file. In Windows, modificare le proprietà della cartella o usare BitLocker.


1.	Aprire **Command Prompt**.
2.	Per scaricare il file delle impostazioni di pubblicazione, eseguire il comando seguente.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Il comando avvia la pagina Web da cui scaricare il file delle impostazioni di pubblicazione.

3.	Al prompt fare clic su **Salva** e indicare un percorso in cui salvare il file.
5.	Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Effettuare il provisioning di cluster HDInsight tramite l'interfaccia della riga di comando di Azure

Le procedure seguenti sono necessarie per effettuare il provisioning di un cluster HDInsight tramite l'interfaccia della riga di comando di Azure:

- Creare un account di archiviazione di Azure
- Effettuare il provisioning di un cluster

**Per creare un account di archiviazione di Azure**

HDInsight usa un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight è necessario un account di archiviazione di Azure. L'account di archiviazione deve trovarsi nello stesso data center.

- Al prompt dei comandi eseguire il comando seguente per creare un account di archiviazione di Azure:

		azure storage account create [options] <StorageAccountName>

	Quando viene richiesto un percorso, selezionarne uno in cui sia possibile effettuare il provisioning di un cluster HDInsight. La risorsa di archiviazione deve trovarsi nello stesso percorso del cluster HDInsight. Attualmente i cluster HDInsight possono essere ospitati solo nelle aree seguenti: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali** e **Stati Uniti centro-meridionali**.

Per informazioni sulla creazione di un account di archiviazione di Azure tramite il portale di Azure classico, vedere [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account.md).

Se si dispone già di un account di archiviazione, ma non se ne conosce né il nome né la chiave, è possibile usare i comandi seguenti per recuperare tali informazioni:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di Azure classico, vedere la sezione *Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione* di [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account.md).

Un cluster HDInsight richiede anche un contenitore in un account di archiviazione. Se l'account di archiviazione indicato non dispone già un contenitore, il comando *azure hdinsight cluster create* richiede il nome di un contenitore e lo crea. Se tuttavia si vuole creare il contenitore prima, è possibile usare il comando seguente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per effettuare il provisioning di un cluster HDInsight**

- Nella finestra del prompt dei comandi eseguire il comando seguente:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Per effettuare il provisioning di un cluster HDInsight tramite un file di configurazione**

In genere, si effettua il provisioning di un cluster HDInsight, si eseguono i processi e quindi si elimina il cluster per ridurre il costo. L'interfaccia della riga di comando consente di salvare le configurazioni in un file, per poterle riusare ogni volta che si effettua il provisioning di un cluster.

- Al prompt dei comandi eseguire i comandi seguenti:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Per elencare e mostrare i dettagli dei cluster**

- Usare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Per eliminare un cluster**

- Usare il comando seguente per eliminare un cluster:

		azure hdinsight cluster delete <ClusterName>



### Usare HDInsight .NET SDK
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET Framework.

Per effettuare il provisioning di un cluster l'SDK di HDInsight, è necessario seguire questa procedura:

- Installare HDInsight .NET SDK
- Creare un certificato autofirmato
- Creare un'applicazione console
- Eseguire l'applicazione


**Per installare .NET SDK per HDInsight**

È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un certificato autofirmato**

Creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione di Azure. Per ottenere istruzioni, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138).


**Per creare un'applicazione console di Visual Studio**

1. Aprire Visual Studio 2013 o 2015.

2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.

3. In **Nuovo progetto** digitare o selezionare i valori seguenti:

	|Proprietà|Valore|
	|--------|-----|
	|Modello|Templates/Visual C#/Windows/Console Application|
	|Nome|CreateHDICluster|

4. Fare clic su **OK** per creare il progetto.

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6. Eseguire il comando seguente nella console per installare i pacchetti:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Questi comandi aggiungono librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.
8. Sostituire il codice con il codice seguente:

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;
		        private const string NewClusterVersion = "3.2";

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";
		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 
		
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. Sostituire i valori dei membri di classe.

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. Possono essere necessari alcuni minuti per creare un cluster HDInsight.


##<a id="nextsteps"></a> Passaggi successivi
In questo articolo si sono appresi vari modi per effettuare il provisioning di un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight](hdinsight-get-started.md) - Informazioni su come iniziare a usare il cluster HDInsight
* [Usare Sqoop con HDInsight](hdinsight-use-sqoop.md) - Informazioni su come copiare i dati tra HDInsight e il database SQL o SQL Server
* [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md) - Informazioni su come usare HDInsight con Azure PowerShell
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md) - Informazioni su come inviare processi a HDInsight a livello di codice
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation] - Informazioni su HDInsight SDK


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-management-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0114_2016-->