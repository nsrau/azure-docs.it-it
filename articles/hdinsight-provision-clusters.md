<properties urlDisplayName="HDInsight Administration" pageTitle="Provisioning dei cluster Hadoop in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="jgao" />

#Provisioning di cluster Hadoop in HDInsight con opzioni personalizzate

Questo articolo descrive i diversi modi disponibili per effettuare il provisioning personalizzato di un cluster Hadoop in Azure HDInsight, ovvero usando il portale di gestione di Azure, PowerShell, gli strumenti da riga di comando o .NET SDK per HDInsight. In questo articolo viene illustrato il provisioning dei cluster Hadoop. Per istruzioni su come effettuare il provisioning di un cluster HBase, vedere [Provisioning di cluster HBase in HDInsight][hdinsight-hbase-custom-provision]. Vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Differenze tra Hadoop e HBase</a> per informazioni utili per la scelta del cluster appropriato.

## Informazioni sui cluster HDInsight

Il motivo per cui vengono menzionati i cluster ogni volta che si discute di Hadoop o BigData è che Hadoop consente l'elaborazione distribuita di dati di grandi dimensioni tra diversi nodi di un cluster. Il cluster presenta un'architettura master/slave con un master (chiamato anche nodo head o Name Node) e un numero indeterminato di slave (chiamati anche nodi dati). Per altre informazioni, vedere [Apache Hadoop][apache-hadoop].

![HDInsight Cluster][img-hdi-cluster]

Un cluster HDInsight astrae i dettagli dell'implementazione di Hadoop in modo che non sia necessario preoccuparsi di come comunicare con diversi nodi di un cluster. Quando si esegue il provisioning di un cluster HDInsight, si esegue il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. Per altre informazioni, vedere [Introduzione a Hadoop in HDInsight][hadoop-hdinsight-intro]. I dati per la varianza sono archiviati nell'archiviazione BLOB di Azure, chiamata anche *Archiviazione Azure - BLOB* (o WASB) nel contesto di HDInsight. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

In questo articolo vengono fornite istruzioni sui diversi modi di effettuare il provisioning di un cluster. Per informazioni sull'approccio introduttivo al provisioning di un cluster, vedere [Introduzione all'uso di Azure HDInsight][hdinsight-get-started].

**Prerequisiti:**

Per eseguire le procedure descritte nell'articolo è necessario:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. I cmdlet di PowerShell HDInsight eseguono le attività con la sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].

##Contenuto dell'articolo

* [Opzioni di configurazione](#configuration)
* [Uso del portale di gestione di Azure](#portal)
* [Uso di Azure PowerShell](#powershell)
* [Uso della riga di comando multipiattaforma](#cli)
* [Uso di HDInsight .NET SDK](#sdk)
* [Passaggi successivi](#nextsteps)

##<a id="configuration"></a>Opzioni di configurazione

###Risorse di archiviazione aggiuntive

Durante la configurazione, è necessario specificare un account di archiviazione BLOB di Azure e un contenitore predefinito. Questo viene usato come posizione di archiviazione predefinita dal cluster. In alternativa è possibile specificare BLOB aggiuntivi da associare al cluster.

Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-use-blob-storage/).

###Metastore

Il metastore contiene informazioni su tabelle Hive, partizioni, schemi, colonne, ecc. Queste informazioni vengono usate da Hive per individuare la posizione di archiviazione dei dati in HDFS (o WASB per HDInsight.) Per impostazione predefinita, Hive usa un database incorporato per l'archiviazione di queste informazioni.

Quando si effettua il provisioning di un cluster HDInsight, è possibile specificare un database SQL che contiene il metastore per Hive. Ciò consente di conservare le informazioni dei metadati quando si elimina un cluster perché vengono archiviate esternamente nel database SQL.

### Personalizzazione cluster

È possibile installare componenti aggiuntivi o personalizzare la configurazione del cluster durante la creazione di un cluster HDInsight. I cluster possono essere personalizzati tramite la scrittura di script che vengono eseguiti durante la creazione del cluster. Questi script vengono chiamati tramite un'**azione di script**, ovvero un'opzione di configurazione che può essere usata dai cmdlet PowerShell di HDInsight o dall'SDK .NET di HDInsight. Per altre informazioni, vedere [Personalizzare i cluster HDInsight tramite l'azione di script][hdinsight-customize-cluster].


###Reti virtuali

La [rete virtuale di Azure](http://azure.microsoft.com/it-it/documentation/services/virtual-network/) consente di creare una rete sicura e persistente che contiene le risorse necessarie alla propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud in una rete privata (solo cloud)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN, Virtual Private Network)

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	La configurazione da punto a sito consente di connettere una specifica risorsa alla rete virtuale di Azure usando una rete VPN software

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] È necessario creare la rete virtuale di Azure prima del provisioning di un cluster HDInsight. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/it-it/library/azure/jj156206.aspx).
>
> Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.
>
> È consigliabile designare una singola subnet per un cluster.

##<a id="portal"></a> Uso del portale di gestione di Azure

I cluster HDInsight usano un contenitore dell'archiviazione BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]. Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione][azure-create-storageaccount].


> [WACOM.NOTE] I cluster HDInsight possono essere attualmente ospitati solo nelle aree geografiche **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti Orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali** e **Stati Uniti centro-meridionali**.

**Per creare un cluster HDInsight mediante l'opzione di creazione personalizzata**

1. Accedere al [portale di gestione di Azure][azure-management-portal].
2. Fare clic su**+ NUOVO** nella parte inferiore della pagina, quindi su **SERVIZI DATI**, **HDINSIGHT** e infine su **CREAZIONE PERSONALIZZATA**.
3. Nella pagina **Dettagli cluster** digitare o scegliere i valori seguenti:

	![HDI.CustomCreateCluster][image-hdi-customcreatecluster]

    <table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome del cluster</td>
			<td><p>Assegnare un nome al cluster. </p>
				<ul>
				<li>Il nome DNS deve iniziare e finire con un carattere alfanumerico e può contenere trattini.</li>
				<li>Il campo deve essere una stringa contenente da 3 a 63 caratteri.</li>
				</ul></td></tr>
		<tr><td>Tipo di cluster</td>
			<td>Per il tipo di cluster selezionare <strong>Hadoop</strong>.</td></tr>
		<tr><td>Versione HDInsight</td>
			<td>Scegliere la versione. Per Hadoop, l'impostazione predefinita è HDInsight versione 3.1, che usa Hadoop 2.4.</td></tr>
		</table>

	Immettere o selezionare i valori come illustrato nella tabella, quindi fare clic sulla freccia destra.

4. Nella pagina **Configura cluster** immettere o selezionare i valori seguenti:

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td>Nodi di dati</td><td>Numero di nodi di dati che si vuole distribuire. Ai fini di test, creare un cluster a singolo nodo. <br />Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.</td></tr>
	<tr><td>Area/Rete virtuale</td><td><p>Selezionare la stessa area geografica specificata per l'account di archiviazione creato nell'ultima procedura. HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Più avanti nella configurazione sarà possibile selezionare un account di archiviazione situato nella stessa area geografica specificata qui.</p><p>Le aree geografiche disponibili sono: <strong>Asia orientale</strong>, <strong>Asia sudorientale</strong>, <strong>Europa settentrionale</strong>, <strong>Europa occidentale</strong>, <strong>Stati Uniti orientali</strong>, <strong>Stati Uniti occidentali</strong>, <strong>Stati Uniti centro-settentrionali</strong>, <strong>Stati Uniti centro-meridionali</strong>.<br/>Se è stata creata una Rete virtuale di Azure, sarà possibile selezionare la rete che verrà usata dal cluster HDInsight.</p><p>Per altre informazioni sulla creazione di una rete virtuale di Azure, vedere <a href="http://msdn.microsoft.com/it-it/library/azure/jj156206.aspx">Attività di configurazione della rete virtuale</a>.</p></td></tr>
	</table>


5. Nella pagina **Configura utente cluster** digitare o scegliere i valori seguenti:

    ![HDI.CustomCreateCluster.ClusterUser][image-hdi-customcreatecluster-clusteruser]

    <table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome utente</td>
			<td>Specificare il nome utente del cluster HDInsight.</td></tr>
		<tr><td>Password/Conferma password</td>
			<td>Specificare la password utente del cluster HDInsight.</td></tr>
		<tr><td>Enter Hive/Oozie Metastore</td>
			<td>Selezionare questa casella di controllo per specificare un database SQL nello stesso data center del cluster, da usare come metastore Hive/Oozie. Ciò risulta utile se si vogliono conservare i metadati sui progetti Hive/Oozie anche dopo aver eliminato un cluster.</td></tr>
		<tr><td>Database metastore</td>
			<td>Specificare il database SQL di Azure che verrà usato come metastore per Hive/OOzie. Questo database SQL deve trovarsi nello stesso data center del cluster HDInsight. Nella casella di riepilogo vengono elencati solo i database SQL nello stesso data center specificato nella pagina <strong>Dettagli cluster</strong>.</td></tr>
		<tr><td>Utente database</td>
			<td>Specificare il database SQL che verrà usato per connettersi al database.</td></tr>
		<tr><td>Password utente database</td>
			<td>Specificare la password utente del database SQL.</td></tr>
	</table>

	>[WACOM.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.

    Fare clic sulla freccia destra.


6. Nella pagina **Account di archiviazione** indicare il valore seguente:

    ![HDI.CustomCreateCluster.StorageAccount][image-hdi-customcreatecluster-storageaccount]

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Account di archiviazione</td>
			<td>Specificare l'account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HDInsight. È possibile scegliere una delle tre opzioni seguenti:
			<ul>
				<li>Usare l'archiviazione esistente</li>
				<li>Crea nuova archiviazione</li>
				<li>Usare l'archiviazione da un'altra sottoscrizione</li>
			</ul>
			</td></tr>
		<tr><td>Nome account</td>
			<td><ul>
				<li>Se si sceglie di usare l'archiviazione esistente, per il campo <strong>Nome account</strong> selezionare un account di archiviazione esistente. Nella casella di riepilogo vengono elencati solo gli account di archiviazione che si trovano nello stesso data center in cui si è scelto di effettuare il provisioning del cluster.</li>
				<li>Se si sceglie l'opzione <strong>Crea nuova archiviazione</strong> o <strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong>, sarà necessario specificare il nome dell'account di archiviazione.</li>
			</ul></td></tr>
		<tr><td>Chiave account</td>
			<td>Se si sceglie l'opzione <strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong>, specificare la chiave dell'account per quell'account di archiviazione.</td></tr>
		<tr><td>Contenitore predefinito</td>
			<td><p>Consente di specificare il contenitore predefinito nell'account di archiviazione viene usato come file system predefinito per il cluster HDInsight. Se si sceglie <strong>Utilizzare l'archiviazione esistente</strong> per il campo <strong>Account di archiviazione</strong> e non ci sono contenitori in tale account, il contenitore verrà creato per impostazione predefinita con lo stesso nome del cluster. Se esiste già un contenitore con il nome del cluster, al nome del contenitore verrà aggiunto un numero di sequenza. Ad esempio, mycontainer1, mycontainer2 e così via. Tuttavia, se un account di archiviazione esistente ha un contenitore con un nome differente da quello del cluster specificato, sarà possibile usare anche tale contenitore.</p>
            <p>Se si è scelto di creare una nuova risorsa di archiviazione da un'altra sottoscrizione di Azure è necessario specificare il nome del contenitore predefinito.</p>
        </td></tr>
		<tr><td>Account di archiviazione aggiuntivi</td>
			<td>HDInsight supporta più account di archiviazione. Un cluster può usare un numero illimitato di account di archiviazione aggiuntivi. Se tuttavia si crea un cluster mediante il portale di gestione, esiste un limite di sette dovuto ai vincoli dell'interfaccia utente. Per ogni account di archiviazione aggiuntivo specificato viene aggiunta un'ulteriore pagina Account di archiviazione alla procedura guidata, in cui è possibile specificare le informazioni account. Nella schermata precedente, ad esempio, è selezionato un account di archiviazione aggiuntivo, per cui la pagina 5 viene aggiunta alla finestra di dialogo.</td></tr>
	</table>

	Fare clic sulla freccia destra.

7. Nella pagina **Account di archiviazione** immettere le informazioni per l'account di archiviazione aggiuntivo:

	![HDI.CustomCreateCluster.AddOnStorage][image-hdi-customcreatecluster-addonstorage]

    Anche in questo caso, è possibile scegliere risorse di archiviazione esistenti, creare nuove risorse di archiviazione o usare le risorse di archiviazione di un'altra sottoscrizione di Azure. La procedura che consente di ottenere i valori è simile al passaggio precedente.

    Fare clic sul segno di spunta per avviare il provisioning del cluster. Al termine del provisioning, nella colonna relativa allo stato verrà visualizzato il valore **In esecuzione**.

	> [WACOM.NOTE] Dopo aver scelto un account di archiviazione di Azure per il cluster HDInsight, non è possibile eliminare l'account né sostituirlo con un altro account.

##<a id="powershell"></a> Uso di Azure PowerShell
Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questa sezione include le istruzioni su come effettuare il provisioning di un cluster HDInsight con Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell][powershell-install-configure]. Per altre informazioni sull'uso di PowerShell con HDInsight, vedere [Amministrazione di HDInsight tramite PowerShell][hdinsight-admin-powershell]. Per l'elenco dei cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight][hdinsight-powershell-reference].

> [WACOM.NOTE] Gli script di questa sezione possono essere usati per configurare un cluster HDInsight in una rete virtuale di Azure, ma non possono creare una rete virtuale di Azure. Per informazioni sulla creazione di una rete virtuale di Azure, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/it-it/library/azure/jj156206.aspx).

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight usando PowerShell:

- Creare un account di archiviazione di Azure
- Creazione di un contenitore BLOB di Azure
- Creazione di un cluster HDInsight

HDInsight usa un contenitore dell'archiviazione BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, sono necessari un account di archiviazione di Azure e un contenitore dell'archiviazione. L'account di archiviazione deve trovarsi nello stesso data center che include il cluster HDInsight.


**Per creare un account di archiviazione di Azure**

- Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

		$storageAccountName = "<StorageAcccountName>"	# Provide a storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

	Se si dispone già di un account di archiviazione, ma non si conosce il nome account e la chiave dell'account, è possibile usare i comandi di PowerShell seguenti per recuperare le informazioni:

		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Per creare il contenitore dell'archiviazione BLOB di Azure**

- Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

		$storageAccountName = "<StorageAccountName>"	# Provide the storage account name
		$storageAccountKey = "<StorageAccountKey>"		# Provide either primary or secondary key
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per eseguire il provisioning di un cluster HDInsight**

> [WACOM.NOTE] I cmdlet di PowerShell rappresentano l'unica soluzione consigliata per modificare le variabili di configurazione in un cluster HDInsight.  Le modifiche apportate ai file di configurazione Hadoop durante la connessione al cluster con Desktop remoto possono essere sovrascritte in caso di applicazione di patch del cluster.  I valori di configurazione impostati tramite PowerShell vengono conservati se al cluster sono applicate patch.

- Eseguire i comandi seguenti da una finestra della console di Azure PowerShell:

		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container.
		$containerName = "<ContainerName>"				# Azure Blob container that is used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	Quando richiesto, immettere le credenziali per il cluster. Il provisioning del cluster può richiedere alcuni minuti.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**Per effettuare il provisioning di un cluster HDInsight con le opzioni di configurazione personalizzate**

Durante il provisioning di un cluster, è possibile usare le altre opzioni di configurazione, ad esempio la connessione a più archiviazioni BLOB di Azure, usando una rete virtuale o un database SQL di Azure per metastore Hive e Oozie. Ciò consente di separare la durata dei dati e dei metadati da quella del cluster.

> [WACOM.NOTE] I cmdlet di PowerShell rappresentano l'unica soluzione consigliata per modificare le variabili di configurazione in un cluster HDInsight.  Le modifiche apportate ai file di configurazione Hadoop durante la connessione al cluster con Desktop remoto possono essere sovrascritte in caso di applicazione di patch del cluster.  I valori di configurazione impostati tramite PowerShell vengono conservati se al cluster sono applicate patch.

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
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[WACOM.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.

**Per elencare i cluster HDInsight**

- Eseguire i comandi seguenti da una finestra della console di Azure Powershell per elencare il cluster HDInsight e verificare che sia stato effettuato correttamente il provisioning del cluster:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Uso della riga di comando multipiattaforma

> [WACOM.NOTE] Dal 29/8/2014 non è più possibile usare l'interfaccia della riga di comando multipiattaforma per associare un cluster a una rete virtuale di Azure.

Un'altra opzione per effettuare il provisioning di un cluster HDInsight è l'interfaccia della riga di comando multipiattaforma. Lo strumento da riga di comando viene implementato in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, inclusi Windows, Mac e Linux. Lo strumento da riga di comando è open source.  Il codice sorgente viene gestito in GitHub all'indirizzo <a href= "https://github.com/Azure/azure-sdk-tools-xplat">https://github.com/Azure/azure-sdk-tools-xplat</a>. Per una guida generale sull'uso dell'interfaccia della riga di comando, vedere [Come usare gli strumenti da riga di comando di Azure per Mac e Linux][azure-command-line-tools]. Per la documentazione di riferimento completa, vedere [Strumento da riga di comando di Azure per Mac e Linux][azure-command-line-tool]. Questo articolo descrive solo l'uso dell'interfaccia della riga di comando da Windows.

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight usando la riga di comando multipiattaforma:

- Installazione della riga di comando multipiattaforma
- Download e importazione di impostazioni di pubblicazione dell'account Azure
- Creare un account di archiviazione di Azure
- Provisioning di un cluster

L'interfaccia della riga di comando può essere installata mediante *Node.js Package Manager (NPM)* o Windows Installer. Microsoft consiglia di eseguire l'installazione usando solo una delle due opzioni.

**Per installare l'interfaccia della riga di comando mediante NPM**

1.	Passare a **www.nodejs.org**.
2.	Fare clic su **INSTALL** e seguire le istruzioni usando le impostazioni predefinite.
3.	Aprire **Prompt dei comandi** (o il *prompt dei comandi di Azure* oppure il *prompt dei comandi per sviluppatori per VS2012*) dalla workstation.
4.	Nella finestra del prompt dei comandi eseguire il comando seguente.

		npm install -g azure-cli

	> [WACOM.NOTE] Se viene visualizzato un errore che informa che il comando NPM non è stato trovato, verificare che i percorsi seguenti siano presenti nella variabile di ambiente PATH: <i>C:\Programmi (x86)\nodejs;C:\Users\[nomeutente]\AppData\Roaming\npm</i> o <i>C:\Programmi\nodejs;C:\Users\[nomeutente]\AppData\Roaming\npm</i>

5.	Eseguire il comando seguente per verificare l'installazione:

		azure hdinsight -h

	È possibile usare l'opzione *-h* a livelli diversi per visualizzare le informazioni della Guida. Ad esempio:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Per installare l'interfaccia della riga di comando mediante Windows Installer**

1.	Passare a **http://azure.microsoft.com/it-it/downloads/**.
2.	Scorrere verso il basso fino alla sezione **Strumenti da riga di comando** e quindi fare clic sul collegamento relativo all'**interfaccia della riga di comando multipiattaforma** e seguire l'Installazione guidata piattaforma Web.

**Per scaricare e importare impostazioni di pubblicazione**

Prima di usare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono usate dall'interfaccia della riga di comando per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà usata per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

> [WACOM.NOTE] Il file di impostazioni di pubblicazione contiene informazioni riservate. Microsoft consiglia di eliminare il file o di eseguire ulteriori passaggi per crittografare la cartella utente contenente il file. In Windows, modificare le proprietà della cartella o usare BitLocker.


1.	Aprire un **prompt dei comandi**.
2.	Per scaricare il file di impostazioni di pubblicazione, eseguire il comando seguente.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Il comando consente di avviare la pagina Web in cui scaricare il file di impostazioni di pubblicazione.

3.	Al prompt fare clic su **Salva** e indicare un percorso in cui salvare il file.
5.	Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

		azure account import <file>

	Nella schermata precedente, il file di impostazioni di pubblicazione è stato salvato nella cartella C:\HDInsight sulla workstation.

**Per creare un account di archiviazione di Azure**

HDInsight usa un contenitore dell'archiviazione BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, è necessario un account di archiviazione di Azure. L'account di archiviazione deve trovarsi nello stesso data center.

- Al prompt dei comandi eseguire il comando seguente per un account di archiviazione di Azure:

		azure storage account create [options] <StorageAccountName>

	Quando viene richiesto un percorso, selezionare un percorso in cui sia possibile effettuare il provisioning di un cluster HDInsight. La risorsa di archiviazione deve trovarsi nello stesso percorso del cluster HDInsight. I cluster HDInsight possono essere attualmente ospitati solo nelle aree geografiche **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti Orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali** e **Stati Uniti centro-meridionali**.  

Per informazioni sulla creazione di un account di archiviazione di Azure mediante il portale di gestione di Azure, vedere [Creare, gestire o eliminare un account di archiviazione][azure-create-storageaccount].

Se si ha già un account di archiviazione, ma non si conosce il nome account e la chiave dell'account, è possibile usare i comandi seguenti per recuperare le informazioni:

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di gestione, vedere la sezione *Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione* di [Creare, gestire o eliminare un account di archiviazione][azure-create-storageaccount].

Un cluster HDInsight richiede anche un contenitore in un account di archiviazione. Se l'account di archiviazione fornito non ha già un contenitore, la *creazione guidata del cluster azure hdinsight* richiede il nome di un contenitore e lo crea. Se tuttavia si vuole creare il contenitore prima, è possibile usare il comando seguente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per eseguire il provisioning di un cluster HDInsight**

- Nella finestra del prompt dei comandi eseguire il comando seguente:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Per effettuare il provisioning di un cluster HDInsight mediante un file di configurazione**

In genere, si effettua il provisioning di un cluster HDInsight, si eseguono i processi e quindi si elimina il cluster per ridurre il costo. L'interfaccia della riga di comando consente di salvare le configurazioni in un file, per poterle riusare ogni volta che si effettua il provisioning di un cluster.

- Al prompt dei comandi eseguire i comandi seguenti:


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

	>[WACOM.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Per elencare e mostrare i dettagli dei cluster**

- Usare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Per eliminare un cluster**

- Usare il comando seguente per eliminare un cluster:

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Uso di HDInsight .NET SDK
.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET.

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight usando l'SDK:

- Installazione di .NET SDK per HDInsight
- Creare un certificato autofirmato
- Creazione di un'applicazione console
- Esecuzione dell'applicazione


**Per installare .NET SDK per HDInsight**

È possibile installare l'ultima build pubblicata dell'SDK [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un certificato autofirmato**

Creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione di Azure. Per ottenere istruzioni, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138).


**Per creare l'applicazione console di Visual Studio**

1. Aprire Visual Studio 2013.

2. Scegliere **Nuovo** dal menu File, quindi fare clic su **Progetto**.

3. In Nuovo progetto digitare o selezionare i valori seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. Fare clic su **OK** per creare il progetto.

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6. Eseguire i seguenti comandi nella console per installare i pacchetti.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni using seguenti all'inizio del file:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Nella funzione Main() copiare e incollare il codice seguente:

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



##<a id="nextsteps"></a> Passaggi successivi
In questo articolo si sono appresi vari modi per eseguire il provisioning di un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started] - Informazioni su come iniziare a usare il cluster HDInsight.
* [Usare Sqoop con HDInsight][89e2276a] - Informazioni su come copiare i dati tra HDInsight e il database SQL o SQL Server.
* [Amministrazione di HDInsight tramite PowerShell][hdinsight-admin-powershell] - Informazioni su come usare HDInsight con PowerShell.
* [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs] - Informazioni su come inviare processi a HDInsight a livello di codice.
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation] - Informazioni su HDInsight SDK.

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/it-it/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/it-it/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/it-it/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/it-it/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/it-it/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /it-it/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=35_1-->
