<properties
   pageTitle="Creare cluster Hadoop in HDInsight | Microsoft Azure"
   	description="Informazioni su come creare cluster per Azure HDInsight tramite il portale di Azure, Azure PowerShell, una riga di comando o .NET SDK."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Creare cluster Hadoop in HDInsight

Informazioni su come pianificare la creazione di cluster HDInsight.


###Prerequisiti:

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opzioni di configurazione di base

Di seguito sono presenti le opzioni di configurazione di base per la creazione di un cluster HDInsight.

- **Nome cluster**

	Il campo Nome cluster consente di identificare un cluster. Il nome del cluster deve rispettare le linee guida seguenti:

	- Il campo deve essere una stringa contenente da 3 a 63 caratteri.
	- Il campo può contenere solo lettere, numeri e trattini.

- **Nome sottoscrizione**

	Un cluster HDInsight è associato a una sottoscrizione di Azure.

- **Nome del gruppo di risorse**

	Gestione risorse di Azure (ARM) consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md).
	
- **Sistema operativo**

	È possibile creare cluster HDInsight in uno dei due sistemi operativi seguenti: - **HDInsight su Windows (Windows Server 2012 R2 Datacenter)**: - **HDInsight su Linux (Ubuntu 12.04 LTS per Linux)**: HDInsight consente di configurare cluster Linux in Azure. Se si ha familiarità con Linux o Unix, è infatti possibile configurare un cluster Linux eseguendo la migrazione da una soluzione Hadoop basata su Linux esistente oppure è possibile scegliere di integrarsi facilmente con i componenti dell'ecosistema Hadoop sviluppati per Linux. Per altre informazioni, vedere l'articolo di [introduzione all'uso di Hadoop su Linux in HDInsight](hdinsight-hadoop-linux-get-started.md).

- **Tipo di cluster** e **Dimensioni del cluster (definiti anche nodi di dati)**

	HDInsight consente ai clienti di distribuire svariati tipi di cluster, per diversi carichi di lavoro di analisi di dati. Sono attualmente disponibili i tipi di cluster seguenti:

	- Cluster Hadoop: per carichi di lavoro di query e analisi
	- Cluster HBase: per carichi di lavoro NoSQL
	- Cluster Storm: per carichi di lavoro di elaborazione di eventi in tempo reale
	- Cluster Spark (anteprima): per carichi di lavoro di elaborazione in memoria, query interattive, streaming e Machine Learning.

	![Cluster HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]Il *Cluster HDInsight di Azure* è definito anche *Cluster Hadoop in HDInsight* o *Cluster HDInsight*. In alcuni casi viene usato in modo intercambiabile con *Cluster Hadoop*. Tutte queste definizioni fanno riferimento ai cluster Hadoop ospitati nell'ambiente Microsoft Azure.

	Un determinato tipo di cluster include ruoli diversi per i diversi nodi, che consentono a un cliente di ridimensionare i nodi in un ruolo specifico in modo appropriato per i dettagli del carico di lavoro. Ad esempio, è possibile creare i nodi di lavoro di un cluster Hadoop con una quantità di memoria elevata, se il tipo di analisi da eseguire richiede un uso intensivo della memoria.

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	I cluster Hadoop per HDInsight vengono distribuiti con due ruoli:

	- Nodo head (2 nodi)
	- Nodo dati (almeno 1 nodo)

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	I cluster HBase per HDInsight vengono distribuiti con tre ruoli: - Server head (2 nodi) - Server area (almeno 1 nodo) - Nodi master/Zookeeper (3 nodi)

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	I cluster Storm per HDInsight vengono distribuiti con tre ruoli: - Nodi Nimbus (2 nodi) - Server Supervisor (almeno 1 nodo) - Nodi Zookeeper (3 nodi)


	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	I cluster Spark per HDInsight vengono distribuiti con tre ruoli: - Nodo head (2 nodi) - Nodo di lavoro (almeno 1 nodo) - Nodi Zookeeper (3 nodi) (gratuiti per Zookeeper A1)

	L'uso di questi nodi viene addebitato ai clienti per la durata del ciclo di vita del cluster. La fatturazione inizia dopo la creazione di un cluster e si interrompe quando il cluster viene eliminato. Non è possibile deallocare o mettere in attesa i cluster. Le dimensioni del cluster influiscono sul prezzo del cluster. Ai fini dell'apprendimento, è consigliabile usare un nodo di dati. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.

- **Versione HDInsight**

	Consente di determinare la versione di HDInsight da usare per questo cluster. Per altre informazioni, vedere [Versioni del cluster e componenti di Hadoop in HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)


- **Località (Area)**

	Il cluster HDInsight e l'account di archiviazione predefinito devono trovarsi nella stessa località di Azure.
	
	![Aree di Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Dimensioni nodo**

	![Dimensioni dei nodi delle VM di HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Selezionare le dimensioni delle VM per i nodi. Per altre informazioni, vedere [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md)

	Il costo può variare in base alla scelta delle macchine virtuali. HDInsight usa tutte macchine virtuali di livello standard per i nodi del cluster. Per informazioni sul modo in cui le dimensioni delle macchine virtuali influiscono sui prezzi, vedere <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Prezzi di HDInsight</a>.


- **Utenti di HDInsight**

	I cluster HDInsight consentono di configurare due account utente durante il provisioning:

	- Utente HTTP. Il nome utente predefinito è admin con la configurazione di base nel portale di Azure.
	- Utente RDP (cluster Windows): usato per la connessione al cluster tramite RDP. Quando si crea l'account, è necessario impostare una scadenza corrispondente a una data entro 90 giorni dalla data odierna.
	- Utente SSH (cluster Linux): usato per la connessione ai cluster tramite SSH. È possibile creare account utente SSH aggiuntivi dopo la creazione del cluster tramite l'esecuzione dei passaggi illustrati in [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Account di archiviazione di Azure**

	L'interfaccia HDFS originale usa molti dischi locali sul cluster. HDInsight usa invece l'archivio BLOB di Azure per l'archiviazione dei dati. L'archiviazione BLOB di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. Grazie a un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati presenti nell'archiviazione BLOB. L'archiviazione dei dati nell'archiviazione BLOB consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

	Durante la configurazione è necessario specificare un account di archiviazione di Azure e un contenitore di archiviazione BLOB di Azure nell'account di archiviazione di Azure. Alcuni processi di creazione richiedono prima di tutto la creazione dell'account di archiviazione di Azure e del contenitore di archiviazione BLOB. Il contenitore di archiviazione BLOB viene usato dal cluster come posizione di archiviazione predefinita. Facoltativamente, è possibile specificare account di archiviazione di Azure aggiuntivi (account di archiviazione collegati) a cui il cluster potrà accedere. Il cluster può accedere anche a eventuali contenitori BLOB configurati con accesso in lettura pubblico completo o accesso in lettura pubblico solo per i BLOB. Per altre informazioni sull'accesso con limitazioni, vedere [Gestire l'accesso alle risorse di archiviazione di Azure](storage-manage-access-to-resources.md).

	![Archiviazione di HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]Un contenitore di archiviazione BLOB offre un raggruppamento di un set di BLOB, come illustrato nell'immagine:

	![Archivio BLOB di Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]Non condividere un contenitore di archiviazione BLOB tra più cluster. Questa operazione non è supportata.

	Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).

- **Metastore Hive/Oozie**

	Il metastore contiene metadati Hive e Oozie, tra cui tabelle di Hive, partizioni, schemi e colonne. Consente di conservare i propri metadati Hive e Oozie, in modo che non sia necessario creare di nuovo tabelle di Hive o processi Oozie quando si crea un nuovo cluster. Per impostazione predefinita, Hive usa un database SQL di Azure incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato. Ad esempio, è disponibile un cluster creato con un metastore Hive. Sono state create alcune tabelle Hive. Dopo avere eliminato il cluster e ricreato il cluster mediante lo stesso metastore Hive, sarà possibile visualizzare le tabelle Hive create nel cluster originale.
    
    > [AZURE.NOTE]La configurazione Metastore non è disponibile per i tipi di cluster HBase.

## Opzioni di configurazione avanzate

### Personalizzare i cluster mediante la personalizzazione dei cluster HDInsight

A volte potrebbe essere necessario modificare i file di configurazione:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

I cluster non possono conservare le modifiche a causa del re-imaging. Per ulteriori informazioni, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Per mantenere le modifiche per l'intero ciclo di vita dei cluster, è possibile usare la personalizzazione dei cluster HDInsight durante il processo di creazione.

L'esempio di script di Azure PowerShell seguente illustra la personalizzazione di una configurazione Hive:

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




### Personalizzare i cluster mediante Azione di script

L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un'**azione script**, ovvero un'opzione di configurazione che può essere usata dal portale, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).


### Usare le reti virtuali di Azure

[Rete virtuale di Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permette di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.

	![Diagramma di una configurazione da sito a sito](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

	![Diagramma di una configurazione da punto a sito](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]È necessario creare la rete virtuale di Azure prima del provisioning di un cluster HDInsight. Per altre informazioni, vedere [Creare un cluster Hadoop in una rete virtuale](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network).
>
> Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su set di affinità. Usare il cmdlet Get-AzureVNetConfig di Azure PowerShell per verificare se una rete virtuale esistente di Azure è basata sulla posizione. Se la rete virtuale non è basata sulla posizione, saranno disponibili le opzioni seguenti:
>
> - Esportare la configurazione di rete virtuale esistente, quindi creare una nuova rete virtuale. Per impostazione predefinita, tutte le nuove reti virtuali sono basate sulla posizione.
> - Eseguire la migrazione a una rete virtuale basata sulla posizione. Vedere la pagina relativa alla [migrazione di servizi esistenti a un ambito a livello di area](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> È consigliabile designare una singola subnet per un cluster.

## Creare con il portale

Per una spiegazione dei campi, vedere le [opzioni di configurazione di base](#basic-configuration-options) e le [opzioni di configurazione avanzate](#advanced-configuration-options).

**Per creare un cluster HDInsight**

1. Accedere al [portale di Azure][azure-preview-portal].
2. Fare clic su **NUOVO**, fare clic su **Analisi di dati** e quindi fare clic su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di Azure")

3. Digitare o selezionare i valori seguenti:

  * **Nome del cluster**: Immettere un nome per il cluster. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.
  * **Tipo di cluster**: selezionare **Hadoop**.
  * **Sistema operativo cluster**: selezionare **Windows Server 2012 R2 Datacenter**.
  * **Sottoscrizione**: selezionare la sottoscrizione di Azure che verrà usata per creare il cluster.
  * **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo. Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.
  * **Credenziali**: configurare il nome utente e la password per l'utente di Hadoop (utente HTTP). Se si abilita desktop remoto per il cluster, è necessario configurare il nome utente e la password di desktop remoto nonché la data di scadenza dell'account. Fare clic su **Seleziona** in basso per salvare le modifiche.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Origine dati**: creare un nuovo account di archiviazione di Azure o selezionarne uno esistente da usare come file system predefinito per il cluster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **Piani tariffari per il nodo**: Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configurazione facoltativa**: per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **Rete virtuale** o la configurazione di un **Metastore esterno** per contenere i dati per Hive e Oozie, usare azioni di script per personalizzare un cluster per l'installazione di componenti personalizzati oppure usare altri account di archiviazione con il cluster.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Windows based HDInsight cluster can only be placed into a classical virtual network.
  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")

			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.

			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")

    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")

4. Fare clic su **Crea**. Se si seleziona **Aggiungi alla Schermata iniziale**, verrà aggiunto un riquadro per il cluster alla Schermata iniziale del portale. L'icona indica che il cluster è in fase di creazione e verrà visualizzata l'icona di HDInsight al completamento dell'operazione.


	| Durante la creazione | Creazione completata |
	| ------------------ | --------------------- |
	| ![Indicatore del provisioning sulla schermata iniziale](./media/hdinsight-provision-clusters/provisioning.png) | ![Sezione del cluster su cui è stato effettuato il provisioning](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina per controllare il processo di provisioning.
	

5. Al termine della creazione, fare clic sul riquadro per il cluster dalla Schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.


	![Pannello del cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Proprietà del cluster")


	Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nella sezione **Essentials**:


	* **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello**Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.
	* **Dashboard**, **Dashboard del cluster** e **URL**: sono tutti modi per accedere al dashboard del cluster, ovvero un portale Web per eseguire processi nel cluster.
	* **Desktop remoto**: consente di attivare o disattivare il desktop remoto nei nodi del cluster.
	* **Ridimensiona cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.
	* **Elimina**: consente di eliminare il cluster HDInsight.
	* **Guida rapida (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-provision-clusters/quickstart.png))**: Visualizza le informazioni che consentiranno di iniziare a usare HDInsight.
	* **Utenti (![icona utenti](./media/hdinsight-provision-clusters/users.png))**: consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.
	

		> [AZURE.IMPORTANT]Questa impostazione influisce _solo_ sull'accesso e sulle autorizzazioni per questo cluster nel portale, e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.
		
	* **Tag (![icona tag](./media/hdinsight-provision-clusters/tags.png))**: consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__, e usare un valore comune per tutti i servizi associati a un progetto specifico.

## Creare mediante il modello ARM

Il modello Gestione risorse di Azure (ARM) facilita la distribuzione e la ridistribuzione del cluster. Con la procedura seguente si creano cluster HDInsight basati su Linux.

**Per distribuire un cluster con il modello ARM**

1. Salvare il file JSON disponibile nell'[Appendice A](#appendix-a---arm-template) nella workstation.
2. Creare i parametri, se necessario.
3. Eseguire il modello con lo script PowerShell seguente:

		$subscriptionId = "<Azure Subscription ID"
		
		$newResourceGroupName = "<Azure Resource Group Name>"
		$Location = "EAST US 2" # for creating ARM group
				
		$armDeploymentName = "New-HDInsigt-Cluster-" + (Get-Date -Format MMdd)
		$newClusterName = "<HDInsight Cluster Name>"
		$clusterStorageAccountName = "<Default Storage Account Name>"
				
		# Connect to Azure
		#Login-AzureRmAccount
		#Select-AzureRmSubscription -SubscriptionId $subscriptionId
				
		# Create a resource group
		New-AzureRmResourceGroup -Name $newResourceGroupName -Location $Location
				
		# Create cluster and the dependent storage accounge
		$parameters = @{clusterName="$newClusterName";clusterStorageAccountName="$clusterStorageAccountName"}
				
		New-AzureRmResourceGroupDeployment `
			-Name $armDeploymentName `
			-ResourceGroupName $newResourceGroupName `
			-TemplateFile E:\HDITutorials-ARM\Create-clusters\hdinsight-arm-template.json `
			-TemplateParameterObject $parameters
				
		# List cluster
		Get-AzureRmHDInsightCluster -ResourceGroupName $newResourceGroupName -ClusterName $newClusterName 

	Lo script di PowerShell configura solo il nome del cluster e il nome dell’account di archiviazione. È possibile impostare altri valori del modello ARM.
	
Per distribuire un modello ARM usando altri metodi, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).


## Creare un cluster con Azure PowerShell
Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questa sezione include le istruzioni su come effettuare il provisioning di un cluster HDInsight con Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet di Windows PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell](../install-configure-powershell.md). Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet di Windows PowerShell per HDInsight, vedere la [documentazione di riferimento dei cmdlet per HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Le procedure seguenti sono necessarie per creare un cluster HDInsight con Azure PowerShell:

- Creare un gruppo di risorse di Azure
- Creare un account di archiviazione di Azure
- Creazione di un contenitore BLOB di Azure
- Creare un cluster HDInsight


	$subscriptionId = "<Azure Subscription ID>"
	
	$newResourceGroupName = "<Azure Resource Group Name>" $location = "<Azure Location>" # ad esempio, "East US 2" $newDefaultStorageAccountName = "<Azure Storage Account Name>" $newClusterName = "<Azure HDInsight Cluster Name>" $clusterSizeInNodes = 1
	
	###########################################
	# Accedere ad Azure
	###########################################Accesso-AzureRmAccount Selezionare-AzureRmSubscription -SubscriptionId $subscriptionId
	
	###########################################
	# Creare il gruppo di risorse.
	########################################### Nuovo-AzureRmResourceGroup -Nome $newRresourceGroupName -Posizione $location
	
	###########################################
	# Preparare l'account di archiviazione e il contenitore predefiniti
	########################################### Nuovo-AzureRmStorageAccount -ResourceGroupName $newResourceGroupName -Nome $newDefaultStorageAccountName -Posizione $location
	
	$defaultStorageAccountKey = Ottieni-AzureRmStorageAccountKey -ResourceGroupName $newResourceGroupName -Nome $newDefaultStorageAccountName | %{ $\_.Key1 } $defaultStorageContext = Nuovo-AzureStorageContext -StorageAccountName $newDefaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey New-AzureStorageContainer -Nome $newClusterName -Contesto $defaultStorageContext #use the cluster name as the container name
		
	###########################################
	# Creare il cluster
	########################################### $httpCredential =Get-Credential -Messaggio "Enter the HTTP account credential:" New-AzureRmHDInsightCluster `
		-ResourceGroupName $newResourceGroupName ` -ClusterName $newClusterName `
		-Location $location ` -ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop ` -OSType Windows `
		-Version "3.2" ` -HttpCredential $httpCredential



## Creare un cluster tramite .NET SDK di HDInsight
.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET Framework. Seguire le istruzioni seguenti per creare un'applicazione console Visual Studio e incollare il codice per la creazione di un cluster.

**Per creare un'applicazione console di Visual Studio**

1. Creare una nuova applicazione console C# in Visual Studio.
2. Eseguire il comando NuGet seguente nella console di Gestione pacchetti NuGet.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

6. Da Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo, incollare il codice seguente e indicare i valori per le variabili:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		namespace CreateHDInsightCluster
		{
			class Program
			{
				private static HDInsightManagementClient _hdiManagementClient;
		
				private static Guid SubscriptionId = new Guid("<Azure Subscription ID");
				private const string ExistingResourceGroupName = "<Azure Resource Group Name>";
				private const string ExistingStorageName = "<Default Storage Account Name>.blob.core.windows.net";
				private const string ExistingStorageKey = "<Default Storage Account Key>";
				private const string ExistingBlobContainer = "<Default Blob Container Name>";
				private const string NewClusterName = "<HDInsight Cluster Name>";
				private const int NewClusterNumNodes = 1;
				private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
				private const OSType NewClusterOsType = OSType.Windows;
				private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
				private const string NewClusterVersion = "3.2";
				private const string NewClusterUsername = "admin";
				private const string NewClusterPassword = "<HTTP User password";
		
				static void Main(string[] args)
				{
					System.Console.WriteLine("Running");
		
					var tokenCreds = GetTokenCloudCredentials();
					var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
					_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
				
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
				}
				private static void CreateCluster()
				{
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
				}
		
				public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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
		
				public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
				{
					return new TokenCloudCredentials(subId.ToString(), creds.Token);
		
				}
			}
		}

7. Premere **F5** per eseguire l'applicazione. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. Verrà richiesto di immettere le credenziali dell'account Azure. Possono essere necessari alcuni minuti per creare un cluster HDInsight.

## Creare mediante SQL Server Integration Services locale

È inoltre possibile usare SQL Server Integration Services (SSIS) per creare o eliminare un cluster HDInsight. Il Feature Pack di Azure per SSIS fornisce i seguenti componenti che funzionano con i cluster HDInsight.


- [Attività di creazione di cluster di Azure HDInsight][ssisclustercreate]
- [Attività di eliminazione di cluster di Azure HDInsight][ssisclusterdelete]
- [Gestione connessione della sottoscrizione di Azure][connectionmanager]

Altre informazioni sul Feature Pack di Azure per SSIS sono disponibili [qui][ssispack].


##Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight](hdinsight-get-started.md) - Informazioni su come iniziare a usare il cluster HDInsight
* [Usare Sqoop con HDInsight](hdinsight-use-sqoop.md) - Informazioni su come copiare i dati tra HDInsight e il database SQL o SQL Server
* [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md) - Informazioni su come usare HDInsight con Azure PowerShell
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md) - Informazioni su come inviare processi a HDInsight a livello di codice
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation] - Informazioni su HDInsight SDK



##Appendice A - Modello ARM

Il modello di Gestione risorse di Azure seguente crea un cluster Hadoop con l'account di archiviazione Azure dipendente.

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
	      ],
	      "metadata": {
	        "description": "The location where all azure resources will be deployed."
	      }
	    },
	    "clusterName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the HDInsight cluster to create."
	      }
	    },
	    "clusterLoginUserName": {
	      "type": "string",
	      "defaultValue": "admin",
	      "metadata": {
	        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
	      }
	    },
	    "clusterLoginPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the cluster login."
	      }
	    },
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "username",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
	      }
	    },
	    "clusterStorageAccountName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the storage account to be created and be used as the cluster's storage."
	      }
	    },
	    "clusterStorageType": {
	      "type": "string",
	      "defaultValue": "Standard_LRS",
	      "allowedValues": [
	        "Standard_LRS",
	        "Standard_GRS",
	        "Standard_ZRS"
	      ]
	    },
	    "clusterWorkerNodeCount": {
	      "type": "int",
	      "defaultValue": 4,
	      "metadata": {
	        "description": "The number of nodes in the HDInsight cluster."
	      }
	    }
	  },
	  "variables": {},
	  "resources": [
	    {
	      "name": "[parameters('clusterStorageAccountName')]",
	      "type": "Microsoft.Storage/storageAccounts",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-05-01-preview",
	      "dependsOn": [],
	      "tags": {},
	      "properties": {
	        "accountType": "[parameters('clusterStorageType')]"
	      }
	    },
	    {
	      "name": "[parameters('clusterName')]",
	      "type": "Microsoft.HDInsight/clusters",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-03-01-preview",
	      "dependsOn": [
	        "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
	      ],
	      "tags": {},
	      "properties": {
	        "clusterVersion": "3.2",
	        "osType": "Linux",
	        "clusterDefinition": {
	          "kind": "hadoop",
	          "configurations": {
	            "gateway": {
	              "restAuthCredential.isEnabled": true,
	              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
	              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
	            }
	          }
	        },
	        "storageProfile": {
	          "storageaccounts": [
	            {
	              "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
	              "isDefault": true,
	              "container": "[parameters('clusterName')]",
	              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
	            }
	          ]
	        },
	        "computeProfile": {
	          "roles": [
	            {
	              "name": "headnode",
	              "targetInstanceCount": "1",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            },
	            {
	              "name": "workernode",
	              "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            }
	          ]
	        }
	      }
	    }
	  ],
	  "outputs": {
	    "cluster": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
	    }
	  }
	}


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx

<!---HONumber=AcomDC_1217_2015-->