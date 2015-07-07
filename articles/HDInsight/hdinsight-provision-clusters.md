<properties
   pageTitle="Effettuare il provisioning personalizzato di cluster Hadoop in Linux in HDInsight | Microsoft Azure"
   description="Informazioni su come effettuare il provisioning di cluster per Azure HDInsight tramite il portale di Azure, Azure PowerShell, una riga di comando o HDInsight .NET SDK"
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
   ms.date="04/21/2015"
   ms.author="nitinme"/>

#Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate

Questo articolo descrive i vari modi disponibili per effettuare il provisioning personalizzato di cluster Hadoop in Azure HDInsight usando il portale di Azure, Azure PowerShell, l’interfaccia della riga di comando di Azure o .NET SDK per HDInsight. Per istruzioni su come effettuare il provisioning di cluster HBase e Storm, vedere [Effettuare il provisioning di cluster HBase in HDInsight](../hdinsight-hbase-get-started.md) e [Introduzione all'uso di Storm in HDInsight](../hdinsight-storm-getting-started.md). Per informazioni utili per scegliere tra Hadoop e HBase, vedere l'articolo che illustra le <a href="http://go.microsoft.com/fwlink/?LinkId=510237">differenze tra Hadoop e HBase</a>.

## Informazioni sui cluster HDInsight

Il motivo per cui vengono chiamati in causa i cluster ogni volta che si discute di Hadoop o Big Data è che Hadoop consente l'elaborazione distribuita di dati di grandi dimensioni tra diversi nodi di un cluster. Il cluster presenta un'architettura master/slave con un master (chiamato anche nodo head o Name Node) e un numero indeterminato di slave (chiamati anche nodi dati). Per altre informazioni, vedere [Apache Hadoop][apache-hadoop].

![Cluster HDInsight][img-hdi-cluster]

Un cluster HDInsight astrae i dettagli dell'implementazione di Hadoop in modo che non sia necessario preoccuparsi di come comunicare con diversi nodi di un cluster. Quando si effettua il provisioning di un cluster HDInsight, si effettua il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. Per altre informazioni, vedere [Introduzione a Hadoop in HDInsight](hdinsight-hadoop-introduction.md). I dati per la varianza sono archiviati nell'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).

Questo articolo fornisce istruzioni sui diversi modi di effettuare il provisioning di un cluster. Per informazioni su come iniziare rapidamente a effettuare il provisioning di un cluster, vedere l'articolo [Introduzione all'uso di Azure HDInsight](../hdinsight-get-started.md).

**Prerequisiti:**

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. I cmdlet di Windows PowerShell per HDInsight eseguono le attività usando la sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].

##<a id="configuration"></a>Opzioni di configurazione

###Cluster in Linux

HDInsight consente di configurare cluster Linux in Azure. Se si ha familiarità con Linux o Unix, è infatti possibile configurare un cluster Linux eseguendo la migrazione da una soluzione Hadoop basata su Linux esistente oppure è possibile scegliere di integrarsi facilmente con i componenti dell'ecosistema Hadoop sviluppati per Linux. Per altre informazioni, vedere l'articolo di [introduzione all'uso di Hadoop su Linux in HDInsight](../hdinsight-hadoop-linux-get-started.md).

###Risorse di archiviazione aggiuntive

Durante la configurazione, è necessario specificare un account di archiviazione BLOB di Azure e un contenitore predefinito, che viene usato come posizione di archiviazione predefinita dal cluster. È possibile specificare anche un account di archiviazione di Azure aggiuntivo da associare al cluster.

>[AZURE.NOTE]Non condividere un contenitore di archiviazione BLOB tra più cluster. Questa operazione non è supportata.

Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).

###Metastore

Il metastore contiene metadati Hive e Oozie, tra cui tabelle di Hive, partizioni, schemi e colonne. Consente di conservare i propri metadati Hive e Oozie, in modo che non sia necessario creare nuovamente tabelle di Hive o processi Oozie quando si effettuerà il provisioning di un nuovo cluster. Per impostazione predefinita, Hive usa un database incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato.

### Personalizzazione cluster

L'uso di script durante il provisioning consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un'**azione script**, ovvero un'opzione di configurazione che può essere usata da portale di Azure, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).


###Reti virtuali

[Rete virtuale di Azure](http://azure.microsoft.com/documentation/services/virtual-network/) consente di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.

	![Diagramma di una configurazione da sito a sito](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

	![Diagramma di una configurazione da punto a sito](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]È necessario creare la rete virtuale di Azure prima del provisioning di un cluster HDInsight. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.
>
> È consigliabile designare una singola subnet per un cluster.

##<a id="portal"></a> Uso del portale di Azure

I cluster HDInsight usano un contenitore di archiviazione BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione](../storage-create-storage-account.md).


> [AZURE.NOTE]Attualmente i cluster HDInsight possono essere ospitati solo nelle aree seguenti: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali** e **Stati Uniti centro-meridionali**.

**Per creare un cluster HDInsight con l'opzione di creazione personalizzata**

1. Accedere al [portale di Azure][azure-management-portal].
2. Fare clic su **+ NUOVO** nella parte inferiore della pagina, quindi su **SERVIZI DATI**, **HDINSIGHT** e infine su **CREAZIONE PERSONALIZZATA**.
3. Nella pagina **Dettagli cluster** digitare o scegliere i valori seguenti:

	![Specificare dettagli del cluster Hadoop HDInsight][image-customprovision-page1]

    <table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Cluster Name</td>
		<td><p>Assegnare un nome al cluster. </p>
			<ul>
			<li>Il nome DNS (Domain Name System) deve iniziare e finire con un carattere alfanumerico e può contenere trattini.</li>
			<li>Il campo deve essere una stringa contenente da 3 a 63 caratteri.</li>
			</ul></td></tr>
	<tr><td>Tipo di cluster</td>
		<td>Per il tipo di cluster selezionare <strong>Hadoop</strong>.</td></tr>
	<tr><td>Sistema operativo</td>
		<td>Selezionare <b>Windows Server 2012 R2 Data Center</b> per effettuare il provisioning di un cluster Windows. Per effettuare il provisioning di un cluster Linux, vedere l'articolo sul <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisioning di cluster Hadoop Linux in HDInsight</a>.</td></tr>
	<tr><td>HDInsight Version</td>
		<td>Scegliere la versione. Per Hadoop, l'impostazione predefinita è HDInsight versione 3.1, che usa Hadoop 2.4.</td></tr>
	</table>Immettere o selezionare i valori come illustrato nella tabella, quindi fare clic sulla freccia destra.

4. Nella pagina **Configura cluster** immettere o selezionare i valori seguenti:

	![Specificare dettagli del cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>Nome</th><th>Valore</th></tr>
<tr><td>Nodi dati</td><td>Numero di nodi di dati che si vuole distribuire. Ai fini di test, creare un cluster a singolo nodo. <br />Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.</td></tr>
<tr><td>Area/Rete virtuale</td><td><p>Selezionare la stessa area geografica specificata per l'account di archiviazione creato nell'ultima procedura. HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Più avanti nella configurazione sarà possibile selezionare solo un account di archiviazione situato nella stessa area geografica specificata qui.</p><p>Le aree geografiche disponibili sono: <strong>Asia orientale</strong>, <strong>Asia sudorientale</strong>, <strong>Europa settentrionale</strong>, <strong>Europa occidentale</strong>, <strong>Stati Uniti orientali</strong>, <strong>Stati Uniti occidentali</strong>, <strong>Stati Uniti centro-settentrionali</strong>, <strong>Stati Uniti centro-meridionali</strong>.<br/>Se è stata creata una rete virtuale di Azure, sarà possibile selezionare la rete che verrà usata dal cluster HDInsight.</p><p>Per altre informazioni sulla creazione di una rete virtuale di Azure, vedere <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Attività di configurazione della rete virtuale</a>.</p></td></tr>
<tr><td>Dimensione nodo head</td><td><p>Selezionare una dimensione di macchina virtuale per il nodo head.</p></td></tr>
<tr><td>Dimensione nodo dati</td><td><p>Selezionare una dimensione di macchina virtuale per il nodo dati.</p></td></tr>
</table>[AZURE.NOTE]Il costo può variare in base alla scelta delle macchine virtuali. HDInsight usa tutte macchine virtuali di livello standard per i nodi del cluster. Per informazioni sul modo in cui le dimensioni delle macchine virtuali influiscono sui prezzi, vedere <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Prezzi di HDInsight</a>.


5. Nella pagina **Configura utente cluster** digitare o scegliere il valore seguente:

    ![Specificare dettagli relativi al metastore e all'utente del cluster HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Nome utente HTTP</td>
		<td>Specificare il nome utente del cluster HDInsight.</td></tr>
	<tr><td>Password HTTP/Conferma password</td>
		<td>Specificare la password utente del cluster HDInsight.</td></tr>
	<tr><td>Abilita Desktop remoto per il cluster</td>
		<td>Selezionare questa casella di controllo per specificare un nome utente, una password e una data di scadenza per un desktop remoto in grado di accedere in remoto ai nodi del cluster al termine del provisioning. È possibile abilitare Desktop remoto anche in un secondo momento, al termine del provisioning del cluster. Per istruzioni, vedere <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Connettersi a cluster HDInsight tramite RDP</a>.</td></tr>
	<tr><td>Immettere metastore Hive/Oozie</td>
		<td>Selezionare questa casella di controllo per specificare un database SQL nello stesso data center del cluster, da usare come metastore Hive/Oozie. Se si seleziona questa casella di controllo, è necessario specificare i dettagli relativi al database SQL di Azure nelle pagine successive della procedura guidata. Ciò risulta utile se si vogliono conservare i metadati sui progetti Hive/Oozie anche dopo aver eliminato un cluster.</td></tr>
	</td></tr>
</table>Fare clic sulla freccia destra.

6. Nella pagina **Configura metastore Hive/Oozie** specificare i valori seguenti:

    ![Specificare l'utente del cluster HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	Specificare un database SQL di Azure che verrà usato come metastore Hive/Oozie. È possibile specificare lo stesso database per entrambi i metastore Hive e Oozie. Questo database SQL deve trovarsi nello stesso data center del cluster HDInsight. Nella casella di riepilogo vengono elencati solo i database SQL presenti nello stesso data center specificato nella pagina <strong>Dettagli cluster</strong>. Specificare anche il nome utente e la password per la connessione al database SQL di Azure selezionato.

    >[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

    Fare clic sulla freccia destra.


7. Nella pagina **Account di archiviazione** specificare i valori seguenti:

    ![Specificare l'account di archiviazione per il cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

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
</table>Fare clic sulla freccia destra.

7. Se si è scelto di configurare un account di archiviazione aggiuntivo per il cluster, nella pagina **Account di archiviazione** immettere le informazioni relative all'account aggiuntivo:

	![Specificare dettagli di archiviazione aggiuntivi per il cluster HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    Anche in questo caso, è possibile scegliere risorse di archiviazione esistenti, creare nuove risorse di archiviazione o usare le risorse di archiviazione di un'altra sottoscrizione di Azure. La procedura che consente di ottenere i valori è simile al passaggio precedente.

    > [AZURE.NOTE]Dopo aver scelto un account di archiviazione di Azure per il cluster HDInsight, non è possibile eliminare l'account né sostituirlo con un altro account.

8. Nella pagina **Azioni script** fare clic sul pulsante che consente di **aggiungere azioni di script** per specificare i dettagli relativi allo script da eseguire per personalizzare un cluster, ad esempio quello da creare. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).

	![Configurare l'azione di script per personalizzare un cluster HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

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
</table>È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.

##<a id="powershell"></a> Uso di Azure PowerShell
Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questa sezione include le istruzioni su come effettuare il provisioning di un cluster HDInsight con Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet di Windows PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell](../install-configure-powershell.md). Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet di Windows PowerShell per HDInsight, vedere la [documentazione di riferimento dei cmdlet per HDInsight][hdinsight-powershell-reference].

> [AZURE.NOTE]Gli script di questa sezione possono essere usati per configurare un cluster HDInsight in una rete virtuale di Azure, ma non possono creare una rete virtuale di Azure. Per informazioni sulla creazione di una rete virtuale di Azure, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/library/azure/jj156206.aspx).

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

	>[AZURE.NOTE]I comandi $hadoopUserName e $hadoopUserPassword consentono di creare l'account utente Hadoop per il cluster. Questo account verrà usato per connettersi al cluster ed eseguire processi. Se si usa l'opzione Creazione rapida del portale di Azure per effettuare il provisioning di un cluster, il nome utente Hadoop predefinito sarà "admin". Non confondere questo account con l'account utente RDP (Remote Desktop Protocol), che deve essere diverso dall'account utente Hadoop. Per altre informazioni, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure][hdinsight-admin-portal].

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

	>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

**Per elencare cluster HDInsight**

- Eseguire il comando seguente da una finestra della console di Azure Powershell per elencare il cluster HDInsight e verificare che sia stato effettuato correttamente il provisioning del cluster:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Utilizzo dell’interfaccia della riga di comando di Azure

> [AZURE.NOTE]A partire da 29/8/2014 non è più possibile associare l’interfaccia della riga di comando di Azure a una rete virtuale di Azure.

Un'altra opzione per effettuare il provisioning di un cluster HDInsight è l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure viene implementata in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, inclusi Windows, Mac e Linux. È possibile installare l'interfaccia della riga di comando dai percorsi seguenti:

- **Node.js SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Interfaccia della riga di comando** - <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Per una guida generale sull'uso dell'interfaccia della riga di comando di Azure, vedere l'articolo sull'[interfaccia del la riga di comando di Azure per Mac, Linux e Windows](../xplat-cli.md).

Le istruzioni seguenti illustrano come installare la riga di comando Azure in Linux e Windows e quindi come usare la riga di comando per effettuare il provisioning di un cluster.

- [Configurare l'interfaccia della riga di comando di Azure per Linux](#clilin)
- [Configurare l'interfaccia della riga di comando di Azure per Windows](#cliwin)
- [Effettuare il provisioning di cluster HDInsight tramite l’interfaccia della riga di comando di Azure](#cliprovision)

#### <a id="clilin"></a>Configurare l'interfaccia della riga di comando di Azure per Linux

Seguire queste procedure per configurare il computer Linux per l'uso dell’interfaccia della riga di comando di Azure:

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

Prima di usare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono usate dallo strumento per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà utilizzata dall’interfaccia della riga di comando di Azure per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

> [AZURE.NOTE]Il file di impostazioni di pubblicazione contiene informazioni riservate. Microsoft consiglia di eliminare il file o di eseguire ulteriori passaggi per crittografare la cartella utente contenente il file. In Windows modificare le proprietà della cartella o usare Crittografia unità BitLocker.


1.	Aprire una finestra del terminale.
2.	Eseguire il comando seguente per accedere alla sottoscrizione di Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Il comando avvia la pagina Web da cui scaricare il file delle impostazioni di pubblicazione. Se la pagina Web non si apre, fare clic sul collegamento nella finestra del terminale per aprire la pagina del browser e accedere al portale.

3.	Scaricare il file di impostazioni di pubblicazione nel computer.
5.	Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Configurare l'interfaccia della riga di comando di Azure per Windows

Seguire queste procedure per configurare il computer Windows per l'uso dell’interfaccia della riga di comando di Azure:

- Installare l'interfaccia della riga di comando di Azure tramite NPM o Windows Installer
- Scaricare e importare le impostazioni di pubblicazione dell'account Azure


L'interfaccia della riga di comando di Azure può essere installata tramite NPM o Windows Installer. È consigliabile eseguire l'installazione usando solo una delle due opzioni.

**Per installare l'interfaccia della riga di comando di Azure tramite NPM**

1.	Passare a **www.nodejs.org**.
2.	Fare clic su **INSTALL** e seguire le istruzioni usando le impostazioni predefinite.
3.	Aprire **Command Prompt** (o *Azure Command Prompt* o *Developer Command Prompt for VS2012*) dalla workstation.
4.	Nella finestra del prompt dei comandi eseguire il comando seguente.

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Se viene visualizzato un messaggio di errore in cui si informa che il comando NPM non è stato trovato, verificare che i percorsi seguenti siano presenti nella variabile di ambiente PATH: <i>C:\Program Files (x86)\nodejs;C:\Users[username]\AppData\Roaming\npm</i> o <i>C:\Program Files\nodejs;C:\Users[username]\AppData\Roaming\npm</i>

5.	Eseguire il comando seguente per verificare l'installazione:

		azure hdinsight -h

	È possibile usare l'opzione *-h* a livelli diversi per visualizzare le informazioni della Guida. Ad esempio:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Per installare l'interfaccia della riga di comando di Azure tramite Windows Installer**

1.	Passare a **http://azure.microsoft.com/downloads/**. 2.	Scorrere verso il basso fino alla sezione **Strumenti da riga di comando** e quindi fare clic sul collegamento relativo all'**interfaccia della riga di comando di Azure** e seguire l'Installazione guidata piattaforma Web.

**Per scaricare e importare impostazioni di pubblicazione**

Prima di usare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono usate dallo strumento per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà utilizzata dall’interfaccia della riga di comando di Azure per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

> [AZURE.NOTE]Il file di impostazioni di pubblicazione contiene informazioni riservate. Microsoft consiglia di eliminare il file o di eseguire ulteriori passaggi per crittografare la cartella utente contenente il file. In Windows, modificare le proprietà della cartella o usare BitLocker.


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

Per informazioni sulla creazione di un account di archiviazione di Azure tramite il portale di Azure, vedere [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account.md).

Se si dispone già di un account di archiviazione, ma non se ne conosce né il nome né la chiave, è possibile usare i comandi seguenti per recuperare tali informazioni:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di gestione, vedere la sezione *Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione* di [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account.md).

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

	>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Per elencare e mostrare i dettagli dei cluster**

- Usare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Per eliminare un cluster**

- Usare il comando seguente per eliminare un cluster:

		azure hdinsight cluster delete <ClusterName>



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


9. Nella funzione Main() copiare e incollare il codice seguente:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
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
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
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

10. Sostituire le variabili all'inizio della funzione Main().

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. Possono essere necessari alcuni minuti per creare un cluster HDInsight.



##<a id="nextsteps"></a> Passaggi successivi
In questo articolo si sono appresi vari modi per effettuare il provisioning di un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight](../hdinsight-get-started.md) - Informazioni su come iniziare a usare il cluster HDInsight
* [Usare Sqoop con HDInsight](hdinsight-use-sqoop.md) - Informazioni su come copiare i dati tra HDInsight e il database SQL o SQL Server
* [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md) - Informazioni su come usare HDInsight con Azure PowerShell
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md) - Informazioni su come inviare processi a HDInsight a livello di codice
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation] - Informazioni su HDInsight SDK


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli.md

[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Specificare dettagli del cluster Hadoop HDInsight"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Specificare gli utenti del cluster Hadoop HDInsight"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "Specificare dettagli relativi all'account di archiviazione per il cluster Hadoop HDInsight"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "Specificare dettagli aggiuntivi relativi all'account di archiviazione per il cluster Hadoop HDInsight"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "Configurare l'azione di script per personalizzare un cluster HDInsight"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Elenco e visualizzazione di cluster"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: hdinsight-use-sqoop.md "Usare Sqoop con HDInsight"
 

<!---HONumber=62-->