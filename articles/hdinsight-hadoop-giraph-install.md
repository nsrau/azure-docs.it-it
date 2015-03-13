<properties 
	pageTitle="Usare le azioni script in HDInsight per installare Giraph nel cluster Hadoop | Azure" 
	description="Informazioni su come personalizzare un cluster HDInsight per installare Giraph. Verrà usata un'opzione di configurazione Azione script per installare Giraph tramite uno script." 
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
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# Installare e usare Giraph nei cluster Hadoop di HDInsight

È possibile installare Giraph in qualsiasi tipo di cluster in Hadoop su HDInsight usando la personalizzazione dei cluster **Azione script**. Azione script consente di eseguire script per personalizzare un cluster solo durante la creazione. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante Azione script][hdinsight-cluster-customize].

In questo argomento si apprenderà come installare Giraph mediante le azioni script. Dopo l'installazione di Giraph, verranno fornite anche informazioni su come usare Giraph per la maggior parte delle applicazioni tipiche, ovvero per l'elaborazione di grafici di grandi dimensioni.


## Contenuto dell'articolo

- [Che cos'è Giraph?](#whatis)
- [Come si installa Giraph?](#install)
- [Come si usa Giraph in HDInsight?](#usegiraph)
- [Installare Giraph nei cluster Hadoop di HDInsight mediante PowerShell](#usingPS)
- [Installare Giraph nei cluster Hadoop di HDInsight mediante .NET SDK](#usingSDK) 


## <a name="whatis"></a>Che cos'è Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permette di elaborare grafici con Hadoop e può essere usato con Azure HDInsight. È possibile usare i grafici per modellare le relazioni tra gli oggetti, ad esempio le connessioni tra router in una rete di grandi dimensioni, come Internet, oppure le relazioni tra persone iscritte a social network, come nel cosiddetto grafico dei social network. Grazie all'elaborazione del grafico è possibile ottenere informazioni dettagliate sulle relazioni tra gli oggetti in un grafico e in particolare di:

- Identificare possibili amici sulla base delle relazioni correnti
- Identificare la route più breve tra due computer di una rete
- Calcolare la posizione in classifica di pagine Web

   
## <a name="install"></a>Come si installa Giraph?

Uno script di esempio per l'installazione di Giraph in un cluster HDInsight è disponibile in un BLOB di archiviazione di sola lettura di Azure cluster all'indirizzo [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Questa sezione fornisce istruzioni su come usare lo script di esempio quando si effettua il provisioning del cluster mediante il portale di gestione di Azure. 

> [AZURE.NOTE] Lo script di esempio funziona solo con cluster HDInsight versione 3.1.  Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

1. Per avviare il provisioning di un cluster, usare l'opzione **CREAZIONE PERSONALIZZATA**, come descritto in [Effettuare il provisioning di un cluster con opzioni personalizzate](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. Nella pagina **Azioni script** della procedura guidata fare clic su **aggiungi azione script** per specificare i dettagli relativi all'azione script, come descritto di seguito:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome</td>
			<td>Specificare un nome per l'azione script. Ad esempio, <b>Installare Giraph</b>.</td></tr>
		<tr><td>URI script</td>
			<td>Specificare l'URI dello script da richiamare per personalizzare il cluster. Ad esempio, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
		<tr><td>Tipo di nodo</td>
			<td>Specifica i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
		<tr><td>Parametri</td>
			<td>Specificare i parametri, se richiesti dallo script. Lo script per installare Giraph non richiede alcun parametro, di conseguenza è possibile lasciare vuoto questo campo.</td></tr>
	</table>	

	È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.

È inoltre possibile usare lo script per installare Giraph in HDInsight usando PowerShell o .NET SDK per HDInsight. Le istruzioni relative a queste procedure vengono fornite più avanti in questo argomento.

## <a name="usegiraph"></a>Come si usa Giraph in HDInsight?

Verrà usato l'esempio SimpleShortestPathsComputation per illustrare l'implementazione di base di <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> per individuare il percorso più breve tra gli oggetti in un grafico. Usare la procedura seguente per caricare i dati e il file JAR di esempio, eseguire un processo con l'esempio SimpleShortestPathsComputation e quindi visualizzare i risultati.

1. **Caricare un file di dati di esempio in WASB**. Nella workstation locale creare un nuovo file denominato **tiny_graph.txt**. Questo file deve contenere le righe seguenti.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Caricare il file tiny_graph.txt nella risorsa di archiviazione primaria per il cluster HDInsight. Per istruzioni su come caricare i dati, vedere [Caricare dati per processi Hadoop in HDInsight](../hdinsight-upload-data/)

	Questi dati descrivono una relazione tra gli oggetti in un grafico diretto, usando il formato [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Ogni riga rappresenta una relazione tra un oggetto **source\_id** e uno o più oggetti **dest\_id**. Il valore **edge\_value** (o peso) costituisce la potenza o la distanza della connessione tra **source_id** e **dest\_id**.

	I dati disegnati usando il valore (o peso) come distanza tra gli oggetti sono simili a quelli raffigurati di seguito.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-hadoop-giraph-install\giraph-graph.png)

	

4. Eseguire l'esempio **SimpleShortstPathsComputation**. Usare i cmdlet di PowerShell seguenti per eseguire l'esempio specificando come input il file tiny_graph.txt. In questo caso è necessario avere installato e configurato [Azure PowerShell][powershell-install-configure].

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	Nell'esempio precedente sostituire **clustername** con il nome del cluster HDInsight in cui è installato Giraph.

5. **Visualizzare i risultati**.  Dopo il completamento del processo, i risultati verranno archiviati in due file di output nella cartella __wasb:///example/out/shotestpaths__. I file sono denominati __part-m-00001__ e __part-m-00002__. Eseguire la procedura seguente per scaricare e visualizzare l'output.

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	Nella directory attuale nella workstation verrà creata la struttura di directory __example/output/shortestpaths__ e i due file di output verranno scaricati in questo percorso.

	Usare il cmdlet __Cat__ per visualizzare i contenuti dei file: 

		Cat example/output/shortestpaths/part*

	L'output sarà simile al seguente:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	L'esempio SimpleShortestPathComputation è hardcoded in modo da essere avviato con l'ID oggetto 1 e individuare il percorso più breve ad altri oggetti. L'output dovrebbe quindi essere  `destination_id distance`, in cui distance è il valore (o il peso) dei confini attraversati tra l'ID oggetto 1 e l'ID di destinazione.
	
	Con questa visualizzazione è possibile verificare i risultati attraversando i percorsi più brevi tra l'ID 1 e tutti gli altri oggetti. Notare che il percorso più breve tra l'ID 1 e l'ID 4 è 5, che corrisponde alla distanza totale tra <span style="color:orange">ID 1 e 3</span>e quindi <span style="color:red">ID 3 e 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png) 


## <a name="usingPS"></a>Installare Giraph nei cluster Hadoop di HDInsight mediante PowerShell

In questa sezione viene usato il cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** per richiamare gli script usando Azione script per personalizzare un cluster. Prima di procedere, assicurarsi di aver installato e configurato PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

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
	
3. Usare il cmdlet **Add-AzureHDInsightScriptAction** per aggiungere Azione script alla configurazione del cluster. Successivamente, quando viene creato il cluster, viene eseguito Azione script. 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifica l'URI per lo script eseguito</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri richiesti dallo script. Poiché lo script di esempio usato in questo argomento non richiede alcun parametro, questo parametro non viene visualizzato nel frammento riportato sopra.
	</td></tr>
	</table>
	
4. Al termine, avviare il provisioning di un cluster personalizzato con Giraph installato.  
	
		# START PROVISIONING A CLUSTER WITH GIRAPH INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando richiesto, immettere le credenziali per il cluster. La creazione del cluster può richiedere alcuni minuti.


## <a name="usingSDK"></a>Installare Giraph nei cluster Hadoop di HDInsight mediante .NET SDK

.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET. Questa sezione fornisce istruzioni sull'uso delle azioni script da SDK per eseguire il provisioning di un cluster con Giraph installato. È necessario eseguire le procedure seguenti:

- Installare .NET SDK per HDInsight
- Creare un certificato autofirmato
- Creare un'applicazione console
- Eseguire l'applicazione


**Per installare HDInsight .NET SDK**

È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.0com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un certificato autofirmato**

Creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione di Azure. Per le istruzioni, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138). 


**Per creare un'applicazione Visual Studio**

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
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
	
9. Nella funzione Main() copiare e incollare il codice seguente e fornire i valori per le variabili:
		
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

10. Aggiungere il codice seguente alla funzione Main() per usare la classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) per richiamare uno script personalizzato che consente di installare Giraph.

		// ADD THE SCRIPT ACTION TO INSTALL GIRAPH
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //because the script used does not require any parameters.
        ));

11. Al termine, creare il cluster.

		client.CreateCluster(clusterInfo);

11. Salvare le modifiche all'applicazione e compilare la soluzione. 

**Per eseguire l'applicazione**

Aprire una console di PowerShell, passare al percorso in cui è stato salvato il progetto di Visual Studio, spostarsi nella directory \bin\debug all'interno del progetto stesso, quindi eseguire il comando seguente:

	.\CreateGiraphCluster <cluster-name>

Specificare un nome per il cluster e premere INVIO per eseguire il provisioning di un cluster con Giraph installato.


## Vedere anche##
- [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark] per istruzioni su come usare la personalizzazione dei cluster per installare e usare Spark nei cluster Hadoop di HDInsight. Spark è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data.
- [Installare R in cluster HDInsight][hdinsight-install-r] per istruzioni su come usare la personalizzazione dei cluster per installare e usare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. R fornisce centinaia di funzioni statistiche integrate e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.
- [Installare Solr in cluster HDInsight](../hdinsight-hadoop-solr-install). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
<!--HONumber=42-->
