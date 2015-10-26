<properties
   pageTitle="Provisioning Apache Spark cluster in HDInsight | Microsoft Azure"
   description="Informazioni su come effettuare il provisioning di cluster Spark per Azure HDInsight tramite il portale di Azure, Azure PowerShell, una riga di comando o HDInsight .NET SDK"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/22/2015"
    ms.author="nitinme"/>

# I cluster Apache Spark provisioning in HDInsight utilizzano le opzioni personalizzate

Per la maggior parte degli scenari, è possibile eseguire il provisioning di un cluster Spark utilizzando il metodo veloce come descritto in [Introduzione a Spark Apache in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). In alcuni scenari, è possibile eseguire il provisioning di un cluster personalizzato. Ad esempio, è possibile collegare un archivio di metadati esterno per conservare i metadati Hive in modo persistente oltre la durata di un cluster o è possibile utilizzare ulteriore spazio di archiviazione con il cluster.

Per questi e altri scenari, in questo articolo vengono fornite istruzioni su come utilizzare il portale di Azure, Azure PowerShell o .NET SDK per HDInsight per eseguire il provisioning di un cluster di Spark personalizzato in HDInsight.


**Prerequisiti:**

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di: Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quali sono le diverse opzioni di configurazione?

###Risorse di archiviazione aggiuntive

Durante la configurazione, è necessario specificare un account di archiviazione BLOB di Azure e un contenitore predefinito, che viene usato come posizione di archiviazione predefinita dal cluster. È possibile specificare anche un account di archiviazione di Azure aggiuntivo da associare al cluster.

>[AZURE.NOTE]Non condividere un contenitore di archiviazione BLOB tra più cluster. Questa operazione non è supportata.

Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).

###Metastore

Spark consente di definire schema e Hive tabelle su dati non elaborati. È possibile salvare questi schemi e i metadati della tabella in Metastore esterni. Consente di conservare i propri metadati Hive e Oozie, in modo che non sia necessario creare nuovamente tabelle di Hive o processi Oozie quando si effettuerà il provisioning di un nuovo cluster. Per impostazione predefinita, Hive usa un database incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato.

Per istruzioni su come creare un database SQL in Azure, vedere [Creare il primo Database di SQL Azure](sql-database-get-started.md).

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

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]È necessario creare la rete virtuale di Azure prima del provisioning di un cluster. Per alte informazioni, vedere [Come creare una rete virtuale](virtual-networks-create-vnet.md).
>
> Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.
>
> È consigliabile designare una singola subnet per un cluster.

##<a id="portal"></a>Utilizzo del Portale di anteprima di Azure

I cluster Spark in HDInsight usano un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione][azure-create-storageaccount].

**Per creare un cluster HDInsight con l'opzione di creazione personalizzata**

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, fare clic su **Analisi di dati**, quindi fare clic su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di anteprima di Azure](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di anteprima di Azure")

3. Immettere un **Nome cluster**, selezionare **Spark** per il **tipo di Cluster**, e dall’elenco a discesa **Sistema operativo Cluster**, selezionare **Windows Server 2012 R2 Datacenter**. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.

	![Immettere il nome del cluster e il tipo](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.2.png "Immettere il nome del cluster e il tipo")

4. Se si dispone di più di una sottoscrizione, fare clic sulla voce**Sottoscrizione** per selezionare la sottoscrizione di Azure che verrà utilizzata per il cluster.

5. Fare clic su **Gruppo di risorse** per visualizzare un elenco di gruppi di risorse esistenti, quindi selezionare quello in cui creare il cluster. In alternativa, è possibile fare clic su **Crea nuovo** e quindi immettere il nome del nuovo gruppo di risorse. Verrà visualizzato un segno di spunta verde per indicare se il nome del nuovo gruppo è disponibile.

	> [AZURE.NOTE]Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Fare clic su **Credenziali**, quindi immettere un **Nome utente di accesso al Cluster** e la **Password dell'account di accesso Cluster**. Se si desidera attivare il desktop remoto nel nodo del cluster, su**Abilita Desktop remoto**, fare clic su **Sì**. Selezionare una data di scadenza per l’accesso desktop remoto al cluster e fornire il nome utente e la password per l'utente di desktop remoto. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.

	![Fornire le credenziali del cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.3.png "Fornire le credenziali del cluster")

7. Fare clic su **Origine dati** per scegliere un'origine dati esistente per il cluster o crearne una nuova.

	![Pannello di origine dati](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.4.png "Fornire la configurazione origine dati")

	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Utilizzare quanto segue per comprendere le voci nel pannello **Origine dati**.

	- **Metodo di selezione**: impostare questa proprietà su **Da tutte le sottoscrizioni** per consentire l'esplorazione di account di archiviazione da tutte le sottoscrizioni. Impostare questa proprietà su **Tasto di scelta** se si desidera immettere il **Nome di archiviazione** e il **Tasto di scelta** di un account di archiviazione esistente.

	- **Seleziona account di archiviazione / Crea nuovo**: fare clic su **Seleziona account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. Fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.

	- **Scegli contenitore predefinito**: utilizzare questa opzione per immettere il nome del contenitore predefinito da utilizzare per il cluster. È possibile immettere qualsiasi nome, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene utilizzato per tale cluster specifico.

	- **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione.

		> [AZURE.IMPORTANT]La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l’origine dati predefinita devono trovarsi nella stessa area.

	Fare clic su **Seleziona** per salvare la configurazione dell’origine dati.

8. Fare clic su **Livelli di prezzi nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.5.png "Specificare il numero di nodi del cluster")

	Fare clic su **Seleziona** per salvare la configurazione del prezzo del nodo.

9. Fare clic su **Configurazione facoltativa** per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **Rete virtuale**, l’impostazione di un **Metastore esterno** per contenere i dati per Hive e Oozie, utilizzare azioni di Script per personalizzare un cluster per installare i componenti personalizzati o utilizzare ulteriori account di archiviazione con il cluster.

	* Fare clic sull’elenco a discesa **Versione HDInsight** e selezionare la versione che si desidera utilizzare per il cluster. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).

	* Fare clic su **Rete virtuale** per fornire i dettagli di configurazione per configurare il cluster come parte di una rete virtuale. Nel pannello **Rete virtuale**, fare clic su **Rete virtuale** e quindi fare clic sulla rete che si desidera utilizzare. Analogamente, selezionare una **Subnet** per la rete e quindi fare clic su **Seleziona** per salvare la configurazione della rete virtuale.

		![Pannello della rete virtuale](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.6.png "Specificare i dettagli della rete virtuale")

	* Fare clic su **Metastore esterni** per specificare il database SQL che si desidera utilizzare per salvare i metadati Hive e Oozie associati al cluster.

		![Pannello metastore personalizzati](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.7.png "Specificare metastore esterni")

		Per i metadati**Utilizzare un database SQL esistente per Hive**, fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questi passaggi se si desidera **Utilizzare un database SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** fino a quando non si visualizza di nuovo il pannello **Configurazione facoltativa**.

		>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

	* Fare clic su **Azioni di Script** se si desidera utilizzare uno script personalizzato per personalizzare un cluster, come il cluster che si sta creando. Per altre informazioni sulle azioni di script, vedere [Personalizzare i cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md). Nel pannello Azioni di Script fornire i dettagli, come illustrato nella schermata.

		![Pannello azione di script](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.8.png "Specificare l'azione di script")

		Fare clic su **Seleziona** per salvare le modifiche alla configurazione dell’azione di script.

	* Fare clic su **Chiavi di archiviazione di Azure** per specificare gli account di archiviazione aggiuntivi da associare al cluster. Nel pannello **Chiavi di archiviazione di Azure**, fare clic su **Aggiungi una chiave di archiviazione**, quindi selezionare un account di archiviazione esistente o creare un nuovo account.

		![Pannello risorse di archiviazione aggiuntive](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.9.png "Specificare gli account di archiviazione aggiuntivi")

		Fare clic su **Seleziona** fino a quando non verrà visualizzato il pannello **Nuovo cluster HDInsight**.

10. Nel pannello**Nuovo cluster HDInsight**, assicurarsi che **Aggiungi alla schermata iniziale** sia selezionato, quindi fare clic su **Crea**. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster sta eseguendo il provisioning e verrà visualizzata l'icona di HDInsight, una volta completato il provisioning.

	| Durante il provisioning | Provisioning completato |
	| ------------------ | --------------------- |
	| ![Indicatore del provisioning nella Schermata iniziale](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Sezione del cluster su cui è stato effettuato il provisioning](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Utilizzare il riquadro sulla schermata iniziale, o la voce **Notifiche** a sinistra della pagina per controllare il processo di provisioning.

11. Al termine del provisioning, fare clic sul riquadro per il cluster dalla schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.

	![Pannello del cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.Cluster.Blade.png "Proprietà del cluster")

	Utilizzare quanto segue per comprendere le icone nella parte superiore di questo pannello e nelle sezioni **Essentials** e **Collegamenti rapidi**:

	* **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello**Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.

	* **Dashboard** e **URL**: sono modi per accedere al dashboard del cluster, che è un portale Web per eseguire processi nel cluster.

	* **Desktop remoto**: consente di attivare o disattivare desktop remoto nei nodi del cluster.

	* **Scala Cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.

	* **Elimina**: consente di eliminare il cluster HDInsight.

	* **Guide rapide** (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): Visualizza le informazioni che consentiranno di iniziare a utilizzare HDInsight.

	* **Utenti** (![icona utenti](./media/hdinsight-apache-spark-provision-clusters/users.png)): consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.

		> [AZURE.IMPORTANT]Questo influisce _solo_ sull'accesso e sulle autorizzazioni per tale cluster nel portale di anteprima di Azure e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.

	* **Tag** (![icona tag](./media/hdinsight-apache-spark-provision-clusters/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__, e utilizzare un valore comune per tutti i servizi associati a un progetto specifico.

	* **Dashboard del cluster**: avvia il pannello del Dashboard del Cluster da cui è possibile avviare lo stesso dashboard del cluster o avviare i notebook Zeppelin e Jupyter.


##<a id="powershell"></a> Uso di Azure PowerShell

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questa sezione include le istruzioni su come effettuare il provisioning di un cluster HDInsight con Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet di Windows PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell](../install-configure-powershell.md). Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet Windows PowerShell per HDInsight, vedere [Documentazione di riferimento dei cmdlet di HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Le procedure seguenti sono necessarie per effettuare il provisioning di un cluster HDInsight con Azure PowerShell:

- Creare un gruppo di risorse di Azure
- Creare un account di archiviazione di Azure
- Creazione di un contenitore BLOB di Azure
- Creare un cluster HDInsight


		# Use the new Azure Resource Manager mode
		Switch-AzureMode AzureResourceManager

		###########################################
		# Create required items, if none exist
		###########################################

		# Sign in
		Add-AzureAccount

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Register your subscription to use HDInsight
		Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Spark


	![HDI.CLI.Provision](./media/hdinsight-apache-spark-provision-clusters/HDI.ps.provision.png)


## Uso di .NET SDK basato su ARM per HDInsight
.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET Framework. Seguire le istruzioni seguenti per creare un'applicazione console Visual Studio e incollare il codice per la creazione di un cluster.

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

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Gestione pacchetti Nuget per soluzioni**. Nella casella di testo di ricerca nella finestra di dialogo, cercare **HDInsight**. Dai risultati che vengono visualizzati, installare quanto segue:

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Ricercare l'autenticazione di Azure e dai risultati che vengono visualizzati, installare **Microsoft.Azure.Common.Authentication**.

6. Da Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo, incollare il codice seguente e fornire valori per le variabili:


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
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
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. Premere **F5** per eseguire l'applicazione. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. Verrà richiesto di immettere le credenziali dell'account Azure. Possono essere necessari alcuni minuti per creare un cluster HDInsight.


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


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Usare Sqoop con HDInsight"

<!---HONumber=Oct15_HO3-->