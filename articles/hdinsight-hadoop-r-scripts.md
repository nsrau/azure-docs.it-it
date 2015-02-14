<properties 
	pageTitle="Usare R in HDInsight per la personalizzazione dei cluster| Azure" 
	description="Informazioni su come installare e usare R per personalizzare cluster Hadoop." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="bradsev"/>

# Installare e usare R in cluster Hadoop HDInsight

È possibile installare R in qualsiasi tipo di cluster in Hadoop su HDInsight usando la personalizzazione dei cluster **Script Action**. Questo consente ai data scientist e agli analisti di usare R per distribuire il potente framework di programmazione MapReduce/YARN per l'elaborazione di grandi quantità di dati nei cluster Hadoop distribuiti in HDInsight.

Azione script consente di eseguire script per personalizzare un cluster solo durante la creazione. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante Azione script][hdinsight-cluster-customize].


## Contenuto dell'articolo

- [Che cos'è R?](#whatIs)
- [Come si installa R?](#install)
- [Come si eseguono gli script R in HDInsight?](#useR) 
- [Installare R in cluster Hadoop di HDInsight mediante PowerShell](#usingPS)
- [Installare R in cluster Hadoop di HDInsight mediante .NET SDK](#usingSDK)
- [Vedere anche](#seeAlso)


## <a name="whatIs"></a>Che cos'è R?

Il <a href="http://www.r-project.org/" target="_blank">progetto R</a> è un linguaggio open source e un ambiente per l'elaborazione statistica. R fornisce centinaia di funzioni statistiche integrate e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica. R è l'ambiente di programmazione preferito da numerosi statistici e scienziati professionisti attivi in un'ampia gamma di campi. 

Gli script R possono essere eseguiti su cluster Hadoop in HDInsight personalizzati mediante Script Action al momento della creazione per installare l'ambiente R. R è compatibile con WASB (Azure Blob Storage), consentendo di elaborare i dati archiviati usando R in HDInsight.  

## <a name="install"></a>Come si installa R?

Uno script di esempio per l'installazione di R in un cluster HDInsight è disponibile in un BLOB di archiviazione di sola lettura di Azure cluster all'indirizzo [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). Questa sezione fornisce istruzioni su come usare lo script di esempio quando si effettua il provisioning del cluster mediante il portale di gestione di Azure.

> [AZURE.NOTE] Lo script di esempio funziona solo con cluster HDInsight versione 3.1. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-component-versioning/).

1. Per avviare il provisioning di un cluster, usare l'opzione **CREAZIONE PERSONALIZZATA**, come descritto in [Effettuare il provisioning di un cluster con opzioni personalizzate](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-provision-clusters/#portal). 
2. Nella pagina **Azioni script** della procedura guidata fare clic su **aggiungi azione script** per specificare i dettagli relativi all'azione script, come descritto di seguito:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome</td>
			<td>Specificare un nome per l'azione script, ad esempio <b>Installazione R</b>.</td></tr>
		<tr><td>URI script</td>
			<td>Specificare l'URI dello script da richiamare per personalizzare il cluster. Ad esempio, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
		<tr><td>Tipo di nodo</td>
			<td>Specifica i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
		<tr><td>Parametri</td>
			<td>Specificare i parametri, se richiesti dallo script. Lo script per installare R non richiede alcun parametro, di conseguenza è possibile lasciare vuoto questo campo.</td></tr>
	</table>	

	È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.

È inoltre possibile usare lo script per installare R in HDInsight usando PowerShell o .NET SDK per HDInsight. Le istruzioni relative a queste procedure vengono fornite più avanti in questo argomento.

## <a name="useR"></a>Come si eseguono gli script R in HDInsight?
Questa sezione descrive come eseguire uno script R nel cluster Hadoop con HDInsight.

1. **Stabilire una connessione desktop remoto al cluster**: Dal portale di gestione di Azure abilitare il desktop remoto per il cluster creato con R installato, quindi accedere in remoto al cluster. Per le istruzioni, vedere <a href="http://azure.microsoft.com/it-it/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connettersi a cluster HDInsight tramite RDP</a>.

2. **Aprire la console di R**: L'installazione di R inserisce un collegamento alla console di R sul desktop del nodo head. Fare clic su di esso per aprire la console di R.

3. **Eseguire lo script R**: Lo script R può essere eseguito direttamente dalla console di R incollandolo in essa, selezionandolo e premendo **Invio**. Di seguito è riportato un semplice script di esempio che genera i numeri da 1 a 100 e li moltiplica per 2.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

Le prime due righe chiamano le librerie RHadoop installate con R. La riga finale stampa i risultati nella console. L'output dovrebbe essere simile al seguente:

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>Installare R in HDInsight mediante PowerShell

In questa sezione viene usato il cmdlet **<a href = "http://msdn.microsoft.com/it-it/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** per richiamare gli script usando Azione script per personalizzare un cluster. Prima di procedere, assicurarsi di aver installato e configurato PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

Eseguire la procedura seguente:

1. Aprire una finestra di Azure PowerShell e dichiarare le variabili seguenti:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. Specificare i valori di configurazione, ad esempio i nodi del cluster e l'archivio predefinito da usare.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Richiamare lo script di esempio per l'installazione di R usando il cmdlet **Add-AzureHDInsightScriptAction**. 

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1

 Il cmdlet **Add-AzureHDInsightScriptAction** accetta i parametri seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parametro</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definizione</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Oggetto di configurazione a cui vengono aggiunte le informazioni dell'azione script</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome dell'azione script</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifica i nodi in cui viene eseguito lo script di personalizzazione. I valori validi sono HeadNode (per eseguire l'installazione nel nodo head) o DataNode (per eseguire l'installazione in tutti i nodi di dati). È possibile usare uno o entrambi i valori.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri richiesti dallo script. 
	</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifica l'URI per lo script eseguito</td></tr>
	</table>
	
4. Al termine, eseguire il provisioning del cluster personalizzato per installare R.  
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando richiesto, immettere le credenziali per il cluster. La creazione del cluster può richiedere alcuni minuti.


## <a name="usingSDK"></a>Installare R in cluster Hadoop di HDInsight mediante .NET SDK

.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET. 

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight usando l'SDK:

- [Installare HDInsight .NET SDK](#installSDK)
- [Creare un certificato autofirmato](#createCert)
- [Creare un'applicazione .NET in Visual Studio](#createApp)
- [Eseguire l'applicazione](#runApp)

Le sezioni seguenti mostrano come eseguire queste procedure.

### <a name="installSDK"></a>Per installare HDInsight .NET SDK

È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

### <a name="createCert"></a>Per creare un certificato autofirmato

Creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione di Azure. Per le istruzioni, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138). 


### <a name="createApp"></a>Per creare un'applicazione .NET in Visual Studio

1. Aprire Visual Studio 2013.

2. Scegliere **Nuovo** dal menu File, quindi fare clic su **Progetto**.

3. In Nuovo progetto digitare o selezionare i valori seguenti:
	
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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
	</table>

4. Fare clic su **OK** per creare il progetto.

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6. Eseguire i seguenti comandi nella console per installare il pacchetto.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Questo comando aggiunge librerie .NET e riferimenti ad esse dal progetto corrente di Visual Studio.

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni using seguenti all'inizio del file:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. In the Main() function, copy and paste the following code, and provide values for the variables :
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. Aggiungere il codice seguente alla funzione Main() per usare la classe [ScriptAction](http://msdn.microsoft.com/it-it/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) per richiamare uno script personalizzato che consente di installare R.

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. Al termine, creare il cluster.

		client.CreateCluster(clusterInfo);

11. Salvare le modifiche all'applicazione e compilare la soluzione. 

### <a name="runApp"></a>Per eseguire l'applicazione

Aprire una console di PowerShell, passare al percorso in cui è stato salvato il progetto, spostarsi nella directory \bin\debug all'interno del progetto, quindi eseguire il comando seguente:

	.\CreateRCluster <cluster-name>

Specificare un nome per il cluster e premere INVIO per eseguire il provisioning di un cluster con R installato.

## <a name="seeAlso"></a>Vedere anche

- [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark] per istruzioni su come usare la personalizzazione dei cluster per installare e usare Spark nei cluster Hadoop di HDInsight. Spark è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data.
- [Installare Giraph in cluster HDInsight](../hdinsight-hadoop-giraph-install). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph consente di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.
- [Installare Solr in cluster HDInsight](../hdinsight-hadoop-solr-install). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
<!--HONumber=42-->
