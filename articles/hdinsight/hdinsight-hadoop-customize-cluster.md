<properties
	pageTitle="Personalizzare cluster HDInsight mediante Azione di script | Microsoft Azure"
	description="Informazioni su come personalizzare cluster HDInsight mediante Azione di script."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/30/2015"
	ms.author="nitinme"/>

# Personalizzare cluster HDInsight basati su Windows tramite Azione script

[AZURE.INCLUDE [selettore](../../includes/hdinsight-create-windows-cluster-selector.md)]

**Azione script** può essere utilizzato per richiamare [gli script personalizzati](hdinsight-hadoop-script-actions.md) durante il processo di creazione di cluster per l'installazione del software aggiuntivo in un cluster.

Le informazioni contenute in questo articolo sono specifiche per i cluster HDInsight basati su Windows. Usare la scheda seleziona indicata sopra per passare a una versione di questo articolo che è specifica dei cluster basati su Linux.

I cluster HDInsight possono essere personalizzati in molti modi diversi, ad esempio includendo account di archiviazione di Azure aggiuntivi, modificando i file di configurazione Hadoop (core-site.xml, hive-site.xml e così via) o aggiungendo librerie condivise (ad esempio Hive e Oozie) in posizioni comuni nel cluster. Queste personalizzazioni possono essere apportate tramite Azure PowerShell, Azure HDInsight .NET SDK o il portale di Azure. Per altre informazioni, vedere [Creare cluster Hadoop in HDInsight][hdinsight-provision-cluster].

## Azione di script nel processo di creazione di cluster

L'opzione Azione di script viene usata solo mentre è in corso il processo di creazione di un cluster. Il diagramma seguente illustra quando l'opzione Azione di script viene eseguita durante il processo di creazione:

![Personalizzazione di cluster HDInsight e fasi durante la creazione di un cluster][img-hdi-cluster-states]

Quando lo script è in esecuzione, il cluster entra nella fase **ClusterCustomization**. In questa fase, lo script viene eseguito con l'account di amministratore di sistema in parallelo su tutti i nodi specificati nel cluster e fornisce privilegi di amministratore completi sui nodi.

> [AZURE.NOTE]Perché durante la fase **ClusterCustomization** si dispone dei privilegi di amministratore sui nodi del cluster, è possibile usare lo script per eseguire operazioni come arresto e avvio dei servizi, inclusi quelli correlati a Hadoop. Pertanto, come parte dello script, è necessario assicurarsi che i servizi di Ambari e altri servizi correlati a Hadoop siano attivi prima che lo script termini l'esecuzione. Questi servizi sono necessari per verificare correttamente l'integrità e lo stato del cluster durante la creazione. Se si modifica qualsiasi configurazione del cluster che influisce su questi servizi, è necessario usare le funzioni di supporto fornite. Per altre informazioni sulle funzioni di supporto, vedere [Sviluppare script di Azione script per HDInsight][hdinsight-write-script].

L'output e i log degli errori dello script vengono archiviati nell'account di archiviazione predefinito specificato per il cluster. I log vengono archiviati in una tabella con il nome **u<\\frammento-nome-cluster><\\timestamp>setuplog**. Si tratta di log aggregati dello script eseguito su tutti i nodi (nodo head e nodi di lavoro) del cluster.

Ogni cluster può accettare più azioni di script che vengono richiamate nell'ordine in cui sono specificate. Uno script può essere eseguito nel nodo head, nei nodi di lavoro o in entrambi.

HDInsight fornisce diversi script di esempio per installare i componenti seguenti nei cluster HDInsight:

Nome | Script
----- | -----
**Installare Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Vedere [Installare e usare Spark in cluster Hadoop di HDInsight][hdinsight-install-spark].
**Installare R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Vedere [Installare e usare R nei cluster Hadoop HDInsight][hdinsight-install-r].
**Installare Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Vedere [Installare e usare Solr nei cluster Hadoop di HDInsight](hdinsight-hadoop-solr-install.md).
- **Installare Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Vedere [Installare e usare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install.md).



## Chiamare script tramite il portale di Azure

**Nel portale di Azure**

1. Avviare la creazione di un cluster come descritto in [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md#portal).
2. In Configurazione facoltativa fare clic su **aggiungi azione di script** nel pannello **Azioni di script** per specificare i dettagli relativi all'azione di script, come illustrato di seguito:

	![Usare l'azione di script per personalizzare un cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Usare Azione di script per personalizzare un cluster")

	<table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Nome</td>
		<td>Specificare un nome per l'azione di script.</td></tr>
	<tr><td>URI script</td>
		<td>Specificare l'URI dello script da richiamare per personalizzare il cluster.</td></tr>
	<tr><td>Head/ruolo di lavoro</td>
		<td>Specificare i nodi (**Head** o **Ruolo di lavoro**) in cui viene eseguito lo script di personalizzazione.</b>.
	<tr><td>Parametri</td>
		<td>Specificare i parametri, se richiesti dallo script.</td></tr>
</table>È possibile aggiungere altre azioni di script per installare più componenti nel cluster.

3. Fare clic su **Seleziona** per salvare la configurazione dell'azione di script e continuare con la creazione del cluster.

## Chiamare script con Azure PowerShell

Lo script di PowerShell seguente dimostra come installare Spark nel cluster HDInsight basato su Windows.

	# Provide values for these variables
	$subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

	$nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	
	$resourceGroupName = $namePrefix + "rg"
	$location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
	
	$hdinsightClusterName = $namePrefix + "spark"
	$httpUserName = "admin"
	$httpPassword = "<Enter a Password>"
	
	$defaultStorageAccountName = "$namePrefix" + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	
	#############################################################
	# Connect to Azure
	#############################################################
	
	Try{
		Get-AzureRmSubscription
	}
	Catch{
		Login-AzureRmAccount
	}
	Select-AzureRmSubscription -SubscriptionId $subscriptionID
	
	#############################################################
	# Prepare the dependent components
	#############################################################
	
	# Create resource group
	New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	
	# Create storage account
	New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
	New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
	
	#############################################################
	# Create cluster with ApacheSpark
	#############################################################
	
	# Specify the configuration options
	$config = New-AzureRmHDInsightClusterConfig `
				-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
				-DefaultStorageAccountKey $defaultStorageAccountKey 
				
	
	# Add a script action to the cluster configuration
	$config = Add-AzureRmHDInsightScriptAction `
				-Config $config `
				-Name "Install Spark" `
				-NodeType HeadNode `
				-Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
	
	# Start creating a cluster with Spark installed
	New-AzureRmHDInsightCluster `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $hdinsightClusterName `
			-Location $location `
			-ClusterSizeInNodes 2 `
			-ClusterType Hadoop `
			-OSType Windows `
			-DefaultStorageContainer $defaultBlobContainerName `
			-Config $config


Per installare altri software, è necessario sostituire il file script nello script:


Quando richiesto, immettere le credenziali per il cluster. La creazione del cluster può richiedere alcuni minuti.

## Chiamare script con .NET SDK 

L'esempio seguente dimostra come installare Spark nel cluster HDInsight basato su Windows. Per installare altri software, è necessario sostituire il file script nel codice.

**Per creare un cluster HDInsight con Spark**

1. Creare un'applicazione console C# in Visual Studio.
2. Eseguire il comando seguente dalla console di Gestione pacchetti NuGet.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Nel file Program.cs usare le istruzioni using seguenti:

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. Sostituire il codice nella classe con il seguente:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

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

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
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

4. Premere **F5** per eseguire l'applicazione.


## Supporto per software open source usato nei cluster HDInsight
Il servizio Microsoft Azure HDInsight è una piattaforma flessibile che permette di creare applicazioni Big Data nel cloud usando un ecosistema di tecnologie open source basate su Hadoop. Microsoft Azure fornisce un livello di supporto generale per le tecnologie open source, come illustrato nella sezione relativa all'**ambito del supporto** del <a href="http://azure.microsoft.com/support/faq/" target="_blank">sito Web di domande frequenti sul supporto di Azure</a>. Il servizio HDInsight fornisce un livello di supporto aggiuntivo per alcuni componenti, come illustrato di seguito.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

- **Componenti predefiniti** - Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. Questa categoria include ad esempio il gestore risorse YARN, il linguaggio di query Hive (HiveQL) e la libreria Mahout. L'elenco completo dei componenti del cluster è disponibile in [Novità delle versioni cluster di Hadoop incluse in HDInsight](hdinsight-component-versioning.md)</a>.
- **Componenti personalizzati** - Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

I componenti predefiniti sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.

> [AZURE.WARNING]I componenti forniti con il cluster HDInsight sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Per i progetti Apache sono inoltre disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui un componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. L'elenco seguente illustra i modi più comuni per usare i componenti personalizzati nei cluster HDInsight:

1. Invio di processi - È possibile inviare al cluster processi Hadoop o di altro tipo che eseguono o usano componenti personalizzati.
2. Personalizzazione del cluster - Durante la creazione di un cluster, è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.
3. Esempi - Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.

## Sviluppare script di Azione di script

Vedere [Sviluppare script di Azione script per HDInsight][hdinsight-write-script].


## Vedere anche

- [Creare cluster Hadoop in HDInsight][hdinsight-provision-cluster] contiene istruzioni relative alla creazione di un cluster HDInsight con altre opzioni personalizzate.
- [Sviluppare script di Azione di script per HDInsight][hdinsight-write-script]
- [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark]
- [Installare e usare R nei cluster HDInsight][hdinsight-install-r]
- [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install.md).
- [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fasi durante la creazione di un cluster"

<!---HONumber=AcomDC_0107_2016-->