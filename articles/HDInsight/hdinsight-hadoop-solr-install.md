<properties 
	pageTitle="Usare l'azione script per installare Solr in cluster Hadoop| Microsoft Azure" 
	description="Informazioni su come personalizzare un cluster HDInsight con Solr. Verrà usata un'opzione di configurazione Azione script per installare Solr tramite uno script." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# Installare e usare Solr nei cluster Hadoop di HDInsight

È possibile installare Solr in qualsiasi tipo di cluster in Hadoop in Azure HDInsight usando la personalizzazione del cluster di **azione script**. Azione script consente di eseguire script per personalizzare un cluster solo durante la creazione. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script][hdinsight-cluster-customize].

In questo argomento si apprenderà come installare Solr usando l'azione script. Solr è una piattaforma di ricerca avanzata e offre funzionalità di ricerca di livello aziendale per i dati gestiti da Hadoop. Dopo l'installazione di Solr nel cluster HDInsight, verrà illustrato anche come eseguire ricerche nei dati usando Solr.

> [AZURE.NOTE]Lo script di esempio usato in questo argomento crea un cluster Solr con una configurazione specifica. Per configurare il cluster Solr con raccolte, partizioni, schemi, repliche diverse e così via, sarà necessario modificare di conseguenza lo script e i file binari di Solr.


## <a name="whatis"></a>Che cos'è Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> è una piattaforma di ricerca aziendale che permette di eseguire ricerche full-text avanzate sui dati. Mentre Hadoop consente di archiviare e gestire quantità elevate di dati, Apache Solr offre le funzionalità di ricerca necessarie per recuperare rapidamente i dati. Questo argomento fornisce istruzioni su come personalizzare un cluster HDInsight per l'installazione di Solr.

## <a name="install"></a>Come si installa Solr?

Uno script di esempio per l'installazione di Solr in un cluster HDInsight è disponibile in un BLOB di archiviazione di Azure di sola lettura all'indirizzo [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). Questa sezione fornisce istruzioni su come usare lo script di esempio quando si effettua il provisioning del cluster usando il portale di Azure.


> [AZURE.NOTE]Lo script di esempio funziona solo con cluster HDInsight versione 3.1. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Versioni cluster HDInsight](hdinsight-component-versioning.md).


1. Per avviare il provisioning di un cluster, usare l'opzione **CREAZIONE PERSONALIZZATA**, come descritto in [Effettuare il provisioning di un cluster con opzioni personalizzate](hdinsight-provision-clusters.md#portal). 
2. Nella pagina **Azioni script** della procedura guidata fare clic su **aggiungi azione script** per specificare i dettagli relativi all'azione script, come descritto di seguito:

	![Usare l'azione script per personalizzare un cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Usare l'azione script per personalizzare un cluster")
	
	<table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Nome</td>
		<td>Specificare un nome per l'azione script. Ad esempio, <b>Install Solr</b>.</td></tr>
	<tr><td>URI script</td>
		<td>Specificare l'URI (Uniform Resource Identifier) dello script da richiamare per personalizzare il cluster. Ad esempio, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
	<tr><td>Tipo di nodo</td>
		<td>Specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
	<tr><td>Parametri</td>
		<td>Specificare i parametri, se richiesti dallo script. Lo script per installare Solr non richiede alcun parametro, di conseguenza è possibile lasciare vuoto questo campo.</td></tr>
</table>È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.

È inoltre possibile usare lo script per installare Solr in HDInsight usando Azure PowerShell o HDInsight .NET SDK. Le istruzioni relative a queste procedure vengono fornite più avanti in questo argomento.

## <a name="usesolr"></a>Come si usa Solr in HDInsight?

È prima di tutto necessario indicizzare Solr con alcuni file di dati. Sarà quindi possibile usare Solr per eseguire query di ricerca sui dati indicizzati. Eseguire la procedura seguente per usare Solr in un cluster HDInsight:

1. **Usare RDP (Remote Desktop Protocol) per accedere in remoto al cluster HDInsight con Solr installato**. Dal portale di Azure abilitare il desktop remoto per il cluster creato con Solr installato, quindi accedere in remoto al cluster. Per istruzioni, vedere <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connettersi a cluster HDInsight tramite RDP</a>.

2. **Indicizzare Solr mediante il caricamento di file di dati**. Quando si indicizza Solr, si inseriscono documenti su cui potrebbe essere necessario eseguire ricerche. Per indicizzare Solr, usare RDP per accedere in remoto al cluster, passare al desktop, aprire la riga di comando di Hadoop e quindi passare a **C:\apps\dist\solr-4.7.2\example\exampledocs**. Eseguire il comando seguente:
	
		java -jar post.jar solr.xml monitor.xml

	Nella console viene visualizzato il seguente risultato:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	L'utilità post.jar indicizza Solr con due documenti di esempio, **solr.xml** e **monitor.xml**. L'utilità post.jar e i documenti di esempio sono disponibili con l'installazione di Solr.

3. **Usare il dashboard di Solr per eseguire ricerche nei documenti indicizzati**. Nella sessione RDP per il cluster HDInsight, aprire Internet Explorer, quindi avviare il dashboard di Solr all'indirizzo **http://headnodehost:8983/solr/#/**. Nel riquadro a sinistra selezionare **collection1** dal menu a discesa **Core Selector** e quindi fare clic su **Query**. Ad esempio, per selezionare e restituire tutti i documenti in Solr, specificare i valori seguenti:
	1. Nella casella di testo **q** immettere ***:***. Verranno restituiti tutti i documenti indicizzati in Solr. Per cercare una stringa specifica nei documenti, è possibile immettere qui la stringa.
	2. Selezionare il formato di output nella casella di testo **wt**. Il valore predefinito è **json**. Fare clic su **Esegui query**.

		![Usare l'azione script per personalizzare un cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Eseguire una query sul dashboard Solr")
	
	L'output restituisce i due documenti usati per l'indicizzazione di Solr. L'output sarà simile al seguente:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **Consigliato: backup dei dati indicizzati da Solr all'archivio BLOB di Azure associato al cluster HDInsight**. È consigliabile eseguire il backup dei dati indicizzati dai nodi del cluster Solr nell'archivio BLOB di Azure. Eseguire quindi la procedura seguente:

	1. Aprire Internet Explorer dalla sessione RDP, quindi selezionare l'URL seguente:

			http://localhost:8983/solr/replication?command=backup

		Viene visualizzata una risposta simile alla seguente:

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. Nella sessione remota passare a {SOLR_HOME}{Collection}\data. Per il cluster creato tramite lo script di esempio, sarà uguale a **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. In questo percorso dovrebbe essere creata una cartella snapshot con nome simile a **snapshot.*timestamp***.
	
	3. Comprimere la cartella snapshot e caricarla nell'archivio BLOB di Azure. Dalla riga di comando di Hadoop passare al percorso della cartella snapshot usando il comando seguente:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Questo comando copia lo snapshot in /example/data/ nel contenitore disponibile nell'account di archiviazione predefinito associato al cluster.

## <a name="usingPS"></a>Installare Solr nei cluster Hadoop di HDInsight mediante Azure PowerShell

In questa sezione si usa il cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** per richiamare gli script usando l'azione script per personalizzare un cluster. Prima di procedere, assicurarsi di aver installato e configurato Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet Windows PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell][powershell-install-configure].

Eseguire la procedura seguente:

1. Aprire una finestra di Azure PowerShell e dichiarare le variabili seguenti:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. Specificare i valori di configurazione, ad esempio i nodi del cluster e l'archivio predefinito da usare.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Usare il cmdlet **Add-AzureHDInsightScriptAction** per aggiungere un'azione script alla configurazione del cluster. Successivamente, quando viene creato il cluster, viene eseguita l'azione script.

		# Add the script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

	Il cmdlet **Add-AzureHDInsightScriptAction** accetta i parametri seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parametro</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definizione</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Oggetto di configurazione a cui vengono aggiunte le informazioni dell'azione di script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome dell'azione di script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nodi in cui viene eseguito lo script di personalizzazione. I valori validi sono HeadNode (per eseguire l'installazione nel nodo head) o DataNode (per eseguire l'installazione in tutti i nodi di dati). È possibile usare uno o entrambi i valori.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI per lo script eseguito.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri richiesti dallo script. Poiché lo script di esempio usato in questo argomento non richiede alcun parametro, questo parametro non viene visualizzato nel frammento riportato sopra.
</td></tr>
</table>
	
4. Al termine, avviare il provisioning di un cluster personalizzato con Solr installato.
	
		# Start provisioning a cluster with Solr installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando richiesto, immettere le credenziali per il cluster. La creazione del cluster può richiedere alcuni minuti.


## <a name="usingSDK"></a>Installare Solr nei cluster Hadoop di HDInsight mediante .NET SDK

HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET Framework. Questa sezione fornisce istruzioni sull'uso delle azioni script da SDK per effettuare il provisioning di un cluster con Solr installato. È necessario eseguire le procedure seguenti:

- Installare HDInsight .NET SDK
- Creare un certificato autofirmato
- Creare un'applicazione console
- Eseguire l'applicazione


**Per installare .NET SDK per HDInsight**

È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un certificato autofirmato**

Creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione di Azure. Per ottenere istruzioni, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138).


**Per creare un'applicazione Visual Studio**

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
</table>

4. Fare clic su **OK** per creare il progetto.

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6. Eseguire il comando seguente nella console per installare il pacchetto:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Questo comando aggiunge librerie .NET e riferimenti ad esse dal progetto corrente di Visual Studio.

	
7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni using seguenti all'inizio del file:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Nella funzione Main() copiare e incollare il codice seguente e fornire i valori per le variabili:
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
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

10. Aggiungere il codice seguente alla funzione Main() per usare la classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) per richiamare uno script personalizzato che consente di installare Solr.

		// Add the script action to install Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //Because the script used does not require any parameters
        ));

11. Al termine, creare il cluster.

		client.CreateCluster(clusterInfo);

11. Salvare le modifiche all'applicazione e compilare la soluzione.

**Per eseguire l'applicazione**

Aprire una console di Windows PowerShell o di Azure PowerShell, passare al percorso in cui è stato salvato il progetto Visual Studio, spostarsi nella directory \bin\debug all'interno del progetto stesso, quindi eseguire il comando seguente:

	.\CreateSolrCluster <cluster-name>

Specificare un nome per il cluster e premere INVIO per effettuare il provisioning di un cluster con Solr installato.


## Vedere anche##
- [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark]. Usare la personalizzazione dei cluster per installare Spark nei cluster Hadoop di HDInsight. Spark è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data.
- [Installare R nei cluster HDInsight][hdinsight-install-r]. Usare la personalizzazione dei cluster per installare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.
- [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph permette di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.



[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=62-->