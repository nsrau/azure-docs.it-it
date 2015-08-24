<properties 
   pageTitle="Provisioning Apache Spark cluster in HDInsight | Microsoft Azure" 
   description="Informazioni su come effettuare il provisioning di cluster Spark per Azure HDInsight tramite il portale di Azure, Azure PowerShell, una riga di comando o HDInsight .NET SDK" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/10/2015"
   ms.author="nitinme"/>

# I cluster Apache Spark provisioning in HDInsight utilizzano le opzioni personalizzate

Per la maggior parte degli scenari, è possibile eseguire il provisioning di un cluster Spark utilizzando il metodo veloce come descritto in [Introduzione a Spark Apache in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). In alcuni scenari, è possibile eseguire il provisioning di un cluster personalizzato. Ad esempio, è possibile collegare un archivio di metadati esterno per conservare i metadati Hive in modo persistente oltre la durata di un cluster o è possibile utilizzare ulteriore spazio di archiviazione con il cluster.

Per questi e altri scenari, in questo articolo vengono fornite istruzioni su come utilizzare il portale di Azure, Azure PowerShell o .NET SDK per HDInsight per eseguire il provisioning di un cluster di Spark personalizzato in HDInsight.


**Prerequisiti:**

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di: Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quali sono le diverse opzioni di configurazione?

###Risorse di archiviazione aggiuntive

Durante la configurazione, è necessario specificare un account di archiviazione BLOB di Azure e un contenitore predefinito, che viene usato come posizione di archiviazione predefinita dal cluster. È possibile specificare anche un account di archiviazione di Azure aggiuntivo da associare al cluster.

>[AZURE.NOTE] Non condividere un contenitore di archiviazione BLOB tra più cluster. Questa operazione non è supportata.

Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).

###Metastore

Spark consente di definire schema e Hive tabelle su dati non elaborati. È possibile salvare questi schemi e i metadati della tabella in Metastore esterni. Consente di conservare i propri metadati Hive e Oozie, in modo che non sia necessario creare nuovamente tabelle di Hive o processi Oozie quando si effettuerà il provisioning di un nuovo cluster. Per impostazione predefinita, Hive usa un database incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato.

### Personalizzazione cluster

L'uso di script durante il provisioning consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un'**azione script**, ovvero un'opzione di configurazione che può essere usata da portale di Azure, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script][hdinsight-customize-cluster].


###Reti virtuali

[Rete virtuale di Azure](http://azure.microsoft.com/documentation/services/virtual-network/) consente di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.

	![Diagramma di una configurazione da sito a sito](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

	![Diagramma di una configurazione da punto a sito](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE] È necessario creare la rete virtuale di Azure prima del provisioning di un cluster. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.
>
> È consigliabile designare una singola subnet per un cluster.

##<a id="portal"></a> Uso del portale di Azure

I cluster Spark in HDInsight usano un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione][azure-create-storageaccount].

**Per creare un cluster HDInsight con l'opzione di creazione personalizzata**

1. Accedere al [portale di Azure][azure-management-portal].
2. Fare clic su **+ NUOVO** nella parte inferiore della pagina, quindi su **SERVIZI DATI**, **HDINSIGHT** e infine su **CREAZIONE PERSONALIZZATA**.
3. Nella pagina **Dettagli cluster** digitare o scegliere i valori seguenti:

	![Fornire Spark sui dettagli dei cluster HDInsight](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page1.png "Fornire Spark sui dettagli dei cluster HDInsight")

    <table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Cluster Name</td>
		<td><p>Assegnare un nome al cluster. </p>
			<ul>
			<li>Il nome DNS (Domain Name System) deve iniziare e finire con un carattere alfanumerico e può contenere trattini.</li>
			<li>Il campo deve essere una stringa contenente da 3 a 63 caratteri.</li>
			</ul></td></tr>
	<tr><td>Tipo di cluster</td>
		<td>Per il tipo di cluster selezionare <strong>Spark</strong>.</td></tr>
	<tr><td>Sistema operativo</td>
		<td>Selezionare <b>Data Center di Windows Server 2012 R2</b> per eseguire il provisioning di un cluster di Spark in esecuzione su Windows. </td></tr>
	<tr><td>HDInsight Version</td>
		<td>Scegliere la versione. Per Spark, la sola opzione disponibile per la versione è <b>HDInsight 3.2</b> che utilizza Spark 1.3.1.</td></tr>
	</table>

	Immettere o selezionare i valori come illustrato nella tabella, quindi fare clic sulla freccia destra.

4. Nella pagina **Configura cluster** immettere o selezionare i valori seguenti:

	![Fornire il numero di nodi e i tipi di nodi per il cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page2.png "Fornire il numero di nodi e i tipi di nodi per il cluster")

	<table border="1">
<tr><th>Nome</th><th>Valore</th></tr>
<tr><td>Nodi dati</td><td>Numero di nodi di dati che si vuole distribuire. Ai fini di test, creare un cluster a singolo nodo. <br />Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.</td></tr>
<tr><td>Area/Rete virtuale</td><td><p>Selezionare la stessa area geografica specificata per l'account di archiviazione creato nell'ultima procedura. HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Più avanti nella configurazione sarà possibile selezionare solo un account di archiviazione situato nella stessa area geografica specificata qui.</p>.<br/>Se è stata creata una rete virtuale di Azure, sarà possibile selezionare la rete che verrà usata dal cluster HDInsight.</p><p>Per altre informazioni sulla creazione di una rete virtuale di Azure, vedere <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Attività di configurazione della rete virtuale</a>.</p></td></tr>
<tr><td>Dimensione nodo head</td><td><p>Selezionare una dimensione di macchina virtuale per il nodo head.</p></td></tr>
<tr><td>Dimensione nodo dati</td><td><p>Selezionare una dimensione di macchina virtuale per il nodo dati.</p></td></tr>
</table>
	>[AZURE.NOTE] Il costo può variare in base alla scelta delle macchine virtuali. HDInsight usa tutte macchine virtuali di livello standard per i nodi del cluster. Per informazioni sul modo in cui le dimensioni delle macchine virtuali influiscono sui prezzi, vedere <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Prezzi di HDInsight</a>.


5. Nella pagina **Configura utente cluster** digitare o scegliere il valore seguente:

    ![Specificare l'utente admin e dettagli utente RDP](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page3.png "Specificare l'utente admin e dettagli utente RDP")

    <table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Nome utente HTTP</td>
		<td>Specificare il nome utente del cluster HDInsight.</td></tr>
	<tr><td>Password HTTP/Conferma password</td>
		<td>Specificare la password utente del cluster HDInsight.</td></tr>
	<tr><td>Abilita Desktop remoto per il cluster</td>
		<td>Selezionare questa casella di controllo per specificare un nome utente, una password e una data di scadenza per un desktop remoto in grado di accedere in remoto ai nodi del cluster al termine del provisioning. È possibile abilitare Desktop remoto anche in un secondo momento, al termine del provisioning del cluster. Per istruzioni, vedere <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Connettersi a cluster HDInsight tramite RDP</a>.</td></tr>
	<tr><td>Immettere metastore Hive/Oozie</td>
		<td>Selezionare questa casella di controllo per specificare un database SQL nello stesso data center del cluster, da usare come metastore Hive/Oozie. Se si seleziona questa casella di controllo, è necessario specificare i dettagli relativi al database SQL di Azure nelle pagine successive della procedura guidata. Ciò risulta utile se si vogliono conservare i metadati sui progetti Hive/Oozie anche dopo aver eliminato un cluster. Per istruzioni su come creare un metastore, vedere <a href="https://azure.microsoft.com/it-it/documentation/articles/sql-database-get-started/" target="_blank">creare il primo Database di SQL Azure</a>.</td></tr>
	</td></tr>
</table>
	>[AZURE.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows****Azure** fare clic su **Sì** e infine su **Salva**.

    Fare clic sulla freccia destra.

6. Nella pagina **Configura metastore Hive/Oozie** specificare i valori seguenti:

    ![Fornire metastore Hive/Oozie dettagli database](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page4.png "Fornire metastore Hive/Oozie dettagli database")

	Specificare un database SQL di Azure che verrà usato come metastore Hive/Oozie. È possibile specificare lo stesso database per entrambi i metastore Hive e Oozie. Questo database SQL deve trovarsi nello stesso data center del cluster HDInsight. Nella casella di riepilogo vengono elencati solo i database SQL presenti nello stesso data center specificato nella pagina <strong>Dettagli cluster</strong>. Specificare anche il nome utente e la password per la connessione al database SQL di Azure selezionato.

    >[AZURE.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

    Fare clic sulla freccia destra.

7. Nella pagina **Account di archiviazione** specificare i valori seguenti:

    ![Immissione dei dettagli dell'account di archiviazione](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page5.png "Immissione dei dettagli dell'account di archiviazione")

	<table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Account di archiviazione</td>
		<td>Specificare l'account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HDInsight. È possibile scegliere una delle tre opzioni seguenti:
		<ul>
			<li><strong>Usare l'archiviazione esistente</strong></li>
			<li><strong>Crea nuova archiviazione</strong></li>
			<li><strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong></li>
		</ul>
		</td></tr>
	<tr><td>Nome account</td>
		<td><ul>
			<li>Se si sceglie di usare l'archiviazione esistente, per il campo <strong>Nome account</strong> selezionare un account di archiviazione esistente. Nella casella di riepilogo vengono elencati solo gli account di archiviazione che si trovano nello stesso data center in cui si è scelto di effettuare il provisioning del cluster.</li>
			<li>Se si sceglie l'opzione <strong>Crea nuova archiviazione</strong> o <strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong> è necessario indicare il nome dell'account di archiviazione.</li>
		</ul></td></tr>
	<tr><td>Chiave account</td>
		<td>Se si sceglie l'opzione <strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong> specificare la chiave dell'account relativa a tale account di archiviazione.</td></tr>
	<tr><td>Contenitore predefinito</td>
		<td><p>Specificare il contenitore predefinito nell'account di archiviazione usato come file system predefinito per il cluster HDInsight. Se si sceglie <strong>Usare l'archiviazione esistente</strong> per il campo <strong>Account di archiviazione</strong> e nell'account non è presente alcun contenitore, per impostazione predefinita il contenitore verrà creato con lo stesso nome del cluster. Se esiste già un contenitore con il nome del cluster, al nome del contenitore verrà aggiunto un numero di sequenza. Ad esempio, mycontainer1, mycontainer2 e così via. Tuttavia, se in un account di archiviazione esistente è presente un contenitore con un nome diverso da quello del cluster specificato, si potrà usare anche tale contenitore.</p>
        <p>Se si è scelto di creare una nuova risorsa di archiviazione o di usarne una proveniente da un'altra sottoscrizione di Azure, è necessario specificare il nome del contenitore predefinito.</p>
    </td></tr>
	<tr><td>Account di archiviazione aggiuntivi</td>
		<td>HDInsight supporta più account di archiviazione. Un cluster può usare un numero illimitato di account di archiviazione aggiuntivi. Se tuttavia si crea un cluster tramite il portale di Azure, esiste un limite di sette dovuto ai vincoli dell'interfaccia utente. Per ogni nuovo account di archiviazione specificato viene aggiunta un'altra pagina **Account di archiviazione** alla procedura guidata, in cui è possibile specificare le informazioni sull'account. Nella schermata precedente, ad esempio, è stato selezionato un account di archiviazione aggiuntivo e, quindi, alla finestra di dialogo è stata aggiunta la pagina 5.</td></tr>
</table>

	Fare clic sulla freccia destra.

8. Se si è scelto di configurare un account di archiviazione aggiuntivo per il cluster, nella pagina **Account di archiviazione** immettere le informazioni relative all'account aggiuntivo:

	![Fornire ulteriori account di archiviazione](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page6.png "Fornire ulteriori account di archiviazione")

    Anche in questo caso, è possibile scegliere risorse di archiviazione esistenti, creare nuove risorse di archiviazione o usare le risorse di archiviazione di un'altra sottoscrizione di Azure. La procedura che consente di ottenere i valori è simile al passaggio precedente.

    > [AZURE.NOTE] Dopo aver scelto un account di archiviazione di Azure per il cluster HDInsight, non è possibile eliminare l'account né sostituirlo con un altro account.

8. Nella pagina **Azioni script** fare clic sul pulsante che consente di **aggiungere azioni di script** per specificare i dettagli relativi allo script da eseguire per personalizzare un cluster, ad esempio quello da creare. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script][hdinsight-customize-cluster].

	Se è necessario utilizzare un'azione di script, è necessario fornire input seguenti.
	
	<table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Nome</td>
		<td>Specificare un nome per l'azione script.</td></tr>
	<tr><td>URI script</td>
		<td>Specificare l'URI (Uniform Resource Identifier) dello script da richiamare per personalizzare il cluster.</td></tr>
	<tr><td>Tipo di nodo</td>
		<td>Specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
	<tr><td>Parametri</td>
		<td>Specificare i parametri, se richiesti dallo script.</td></tr>
</table>

	È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.



##<a id="powershell"></a> Uso di Azure PowerShell

In questa sezione vengono fornite istruzioni su come eseguire il provisioning di un cluster Apache Spark in Azure HDInsight tramite Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet di Windows PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell][powershell-install-configure]. Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell][hdinsight-admin-powershell]. Per l'elenco dei cmdlet di Windows PowerShell per HDInsight, vedere la [documentazione di riferimento dei cmdlet per HDInsight][hdinsight-powershell-reference].

> [AZURE.NOTE] Gli script di questa sezione possono essere usati per configurare un cluster HDInsight in una rete virtuale di Azure, ma non possono creare una rete virtuale di Azure. Per informazioni sulla creazione di una rete virtuale di Azure, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/library/azure/jj156206.aspx).

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

- Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $credentials = Get-Credential		              # User name and password for the Hadoop account. You will use this account to connect to the cluster and run jobs.
           
        # Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName -ClusterSizeInNodes $clusterNodes -Version "3.2" -ClusterType Spark

	>[AZURE.NOTE] Le credenziali specificate vengono utilizzate per creare l'account utente di Hadoop per il cluster. Questo account verrà usato per connettersi al cluster ed eseguire processi. Se si usa l'opzione Creazione rapida del portale di Azure per effettuare il provisioning di un cluster, il nome utente Hadoop predefinito sarà "admin". Non confondere questo account con l'account utente RDP (Remote Desktop Protocol), che deve essere diverso dall'account utente Hadoop. Per altre informazioni, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure][hdinsight-admin-portal].

	Il completamento del provisioning del cluster può richiedere alcuni minuti.

	![HDI.PS.Provision](./media/hdinsight-apache-spark-provision-clusters/hdi-spark-ps-provisioning.png "Provisioning di cluster di Spark mediante configurazione di base in PowerShell")


**Per effettuare il provisioning di un cluster HDInsight con le opzioni di configurazione personalizzate**

Durante il provisioning di un cluster, è possibile usare altre opzioni di configurazione, come la connessione a più contenitori di archiviazione BLOB di Azure oppure l'uso di un database SQL di Azure per i metastore Hive e Oozie. In questo modo è possibile separare la durata dei dati e dei metadati da quella del cluster.

> [AZURE.NOTE] I cmdlet di Windows PowerShell rappresentano l'unica soluzione consigliata per modificare le variabili di configurazione in un cluster HDInsight. Le modifiche apportate ai file di configurazione Hadoop durante la connessione al cluster con Desktop remoto possono essere sovrascritte in caso di applicazione di patch del cluster. I valori di configurazione impostati tramite Azure PowerShell vengono conservati se al cluster sono applicate patch.

- Eseguire i comandi seguenti da una finestra di Windows PowerShell:

		$subscriptionName = "<Azure subscription>"
		$clusterName = "<cluster name>"
		$clusterNodes = <cluster size in nodes>
		                
		# Get credentials for Hadoop user and Hive/Oozie metastores
		$clusterCredentials = Get-Credential
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Get default storage account, storage container, and add-on storage account
		$storageAccountName_Default = "<Default storage account name>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<Add-on storage account name>"
		
		# Get information about Hive and Oozie metastores
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>""
		$oozieSQLDatabaseName = "<SQLDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Retrieve storage account keys and storage account location
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}
		
		# Specify custom configuration and cluster type
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes -ClusterType Spark -VirtualNetworkId $vnetID -SubnetName $subNetName | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		
		# Provision the cluster with custom configuration                
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $location -Version "3.2"

	>[AZURE.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows****Azure** fare clic su **Sì** e infine su **Salva**.

	Il completamento del provisioning del cluster può richiedere alcuni minuti.

**Per elencare cluster HDInsight**

- Eseguire il comando seguente da una finestra della console di Azure Powershell per elencare il cluster HDInsight e verificare che sia stato effettuato correttamente il provisioning del cluster:

		Get-AzureHDInsightCluster -Name <ClusterName>

##<a id="sdk"></a> Uso di .NET SDK per HDInsight
.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET Framework.

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

1. Aprire Visual Studio 2013.

2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.

3. In **Nuovo progetto** digitare o selezionare i valori seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Proprietà</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valore</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modello</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Applicazione console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Fare clic su **OK** per creare il progetto.

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6. Eseguire il comando seguente nella console per installare i pacchetti:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni using seguenti all'inizio del file:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning.Data;


9. Nella funzione Main() copiare e incollare il codice seguente:

        string thumbprint = "<CertificateFriendlyName>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
		{
		    Name = clustername,
		    Location = location,
		    DefaultStorageAccountName = storageaccountname,
		    DefaultStorageAccountKey = storageaccountkey,
		    DefaultStorageContainer = containername,
		    UserName = username,
		    Password = password,
		    ClusterSizeInNodes = clustersize,
		    Version = "3.2",
		    OSType = OSType.Windows,
			ClusterType = ClusterType.Spark		    
		};


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadKey();

10. Sostituire le variabili all'inizio della funzione main().

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. Possono essere necessari alcuni minuti per creare un cluster HDInsight.


##<a id="nextsteps"></a> Passaggi successivi

* Vedere [Eseguire l’analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md) per imparare a utilizzare Apache Spark in HDInsight con strumenti di Business Intelligence quali Power BI e Tableau. 
* Vedere [Utilizzare Spark in HDInsight per la creazione di applicazioni di Machine Learning](hdinsight-apache-spark-ipython-notebook-machine-learning.md) per imparare a creare applicazioni di machine learning utilizzando Apache Spark in HDInsight.
* Vedere [utilizzare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) per imparare a creare applicazioni flusso utilizzando Apache Spark in HDInsight.
* Vedere [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md) per imparare a utilizzare il gestore di risorse per gestire le risorse allocate al cluster di Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Usare Sqoop con HDInsight"

<!---HONumber=August15_HO7-->