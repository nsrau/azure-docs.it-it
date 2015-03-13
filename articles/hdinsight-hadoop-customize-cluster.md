<properties 
	pageTitle="Personalizzare cluster HDInsight mediante le azioni script | Azure" 
	description="Informazioni su come personalizzare cluster HDInsight tramite le azioni script." 
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
	ms.date="01/15/2015" 
	ms.author="nitinme"/> 

# Personalizzare cluster HDInsight mediante le azioni script

È possibile personalizzare un cluster HDInsight di Azure per installare altro software in un cluster o per modificare la configurazione delle applicazioni nel cluster. HDInsight offre un'opzione di configurazione denominata **Script Action** in grado di richiamare gli script personalizzati che definiscono la personalizzazione da eseguire nel cluster. Questi script possono essere usati per personalizzare un cluster *as it is being deployed*.  

I cluster HDInsight possono essere personalizzati in molti modi diversi, ad esempio includendo account di archiviazione aggiuntivi, modificando i file di configurazione di Hadoop (core-Site, hive-Site. XML e così via) o aggiungendo librerie condivise (ad esempio Hive e Oozie) in percorsi comuni del cluster. Questa personalizzazione può essere eseguita tramite HDInsight PowerShell, .NET SDK o il portale di gestione di Azure. Per altre informazioni, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision-cluster].



> [AZURE.NOTE] L'uso di Script Action per la personalizzazione di un cluster è supportato solo in cluster HDInsight versione 3.1. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).



## Contenuto dell'articolo

- [Come viene usato lo script durante la creazione del cluster?](#lifecycle)
- [Come si scrive uno script per la personalizzazione di un cluster?](#writescript)
- [Come si usano le azioni script per personalizzare un cluster?](#howto)
- [Esempi di personalizzazione dei cluster](#example)
- [Supporto per software open source usato nei cluster HDInsight](#support)


## <a name="lifecycle"></a>Come viene usato lo script durante la creazione del cluster?

Quando si usa Script Action, è possibile personalizzare un cluster HDInsight solo mentre tale cluster è in fase di creazione. Durante la creazione, un cluster HDInsight passa attraverso le fasi seguenti:

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

Lo script viene richiamato dopo che il processo di creazione del cluster ha completato la fase  *HDInsightConfiguration* e prima della fase the *ClusterOperational*. Ciascun cluster è in grado di accettare più azioni script che vengono richiamate nell'ordine in che cui sono specificate.

> [AZURE.NOTE] L'opzione di personalizzazione dei cluster HDInsight è disponibile come parte delle sottoscrizioni standard di Azure HDInsight, senza alcun costo aggiuntivo.

### Come funziona lo script?

È possibile eseguire lo script sul nodo head, sul nodo di lavoro o su entrambi. Quando lo script è in esecuzione, il cluster entra nella fase  *ClusterCustomization*. In questa fase, lo script viene eseguito con l'account di amministratore di sistema in parallelo su tutti i nodi specificati nel cluster e fornisce privilegi di amministratore completi sui nodi. 

> [AZURE.NOTE] Poiché durante la fase  *Cluster customization* è necessario avere privilegi di amministratore sui nodi del cluster, è possibile usare lo script per eseguire operazioni quali l'arresto e avvio dei servizi, inclusi quelli correlati a Hadoop. Pertanto, come parte dello script, è necessario assicurarsi che i servizi di Ambari e altri servizi correlati a Hadoop siano attivi prima che lo script termini l'esecuzione. Questi servizi sono necessari per verificare correttamente l'integrità e lo stato del cluster durante la creazione. Se si modifica qualsiasi configurazione del cluster che influisce su questi servizi, è necessario usare le funzioni di supporto fornite. Per altre informazioni sulle funzioni di supporto, vedere l'argomento [Sviluppo di azioni script con HDInsight][hdinsight-write-script].

L'output, nonché il log degli errori dello script, vengono archiviati nell'account di archiviazione predefinito specificato per il cluster. I log vengono archiviati in una tabella denominata *u<\cluster-name-fragment><\time-stamp>setuplog*. Questi sono log aggregati dello script eseguito su tutti i nodi (nodo head e nodo di lavoro) del cluster.

## <a name="writescript"></a>Come si scrive uno script per la personalizzazione di un cluster?

Per informazioni su come scrivere uno script di personalizzazione di cluster, vedere l'argomento [Sviluppo di azioni script con HDInsight][hdinsight-write-script]. 

## <a name="howto"></a>Come si usano le azioni script per personalizzare un cluster?

È possibile usare le azioni script dal portale di gestione di Azure, i cmdlet di PowerShell o HDInsight .NET SDK per personalizzare un cluster. 

**Dal portale di gestione**

1. Per avviare il provisioning di un cluster, usare l'opzione **CREAZIONE PERSONALIZZATA**, come descritto in [Effettuare il provisioning di un cluster con opzioni personalizzate](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. Nella pagina relativa alle azioni script della procedura guidata fare clic su **aggiungi azione script** per specificare i dettagli relativi all'azione script, come descritto di seguito:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome</td>
			<td>Specificare un nome per l'azione script.</td></tr>
		<tr><td>URI script</td>
			<td>Specificare l'URI dello script da richiamare per personalizzare il cluster.</td></tr>
		<tr><td>Tipo di nodo</td>
			<td>Specifica i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
		<tr><td>Parametri</td>
			<td>Specificare i parametri, se richiesti dallo script.</td></tr>
	</table>

	È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster. 
  
**Uso dei cmdlet di PowerShell**

Usare i comandi di PowerShell per HDInsight per eseguire una singola azione script o più azioni script. È possibile usare il cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** per richiamare gli script personalizzati. Per usare questi cmdlet, è necessario installare e configurare PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

Usare il seguente comando di PowerShell per eseguire una singola azione script durante la distribuzione di un cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Usare il seguente comando di PowerShell per eseguire più azioni script durante la distribuzione di un cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Uso di HDInsight .NET SDK**

HDInsight .NET SDK fornisce una classe <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> per richiamare gli script personalizzati. Per usare HDInsight .NET SDK:

1. Creare un'applicazione di Visual Studio, quindi installare l'SDK da Nuget. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**. Eseguire i seguenti comandi nella console per installare il pacchetto:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Creare un cluster tramite l'SDK. Per le istruzioni, vedere [Effettuare il provisioning di cluster HDInsight mediante .NET SDK](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#sdk).

3. Usare la classe **ScriptAction** per richiamare uno script personalizzato, come illustrato di seguito:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// PROVIDE THE CLUSTER INFORMATION LIKE
			// NAME, STORAGE ACCOUNT, CREDENTIALS,
			// CLUSTER SIZE, and VERSION		    
			...
			...
		};

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script.
		));


## <a name="example"></a>Esempi di personalizzazione dei cluster

Per iniziare, HDInsight fornisce script di esempio per installare i componenti seguenti in un cluster HDInsight.

- **Installare Spark**. Vedere [Installare Spark nei cluster HDInsight][hdinsight-install-spark].
- **Installare R**. Vedere [Installare R nei cluster HDInsight][hdinsight-install-r].
- **Installare Solr**. [Installare e usare Solr nei cluster HDInsight](../hdinsight-hadoop-solr-install)
- **Installare Giraph**. [Installare e usare Giraph nei cluster HDInsight](../hdinsight-hadoop-giraph-install)

## <a name="support"></a>Supporto per software open source usato nei cluster HDInsight
Il servizio Microsoft Azure HDInsight è una piattaforma flessibile che permette di creare applicazioni Big Data nel cloud usando un ecosistema di tecnologie open source basate su Hadoop. Microsoft Azure fornisce un livello di supporto generale per le tecnologie open source, come illustrato nella <a href="http://azure.microsoft.com/support/faq/" target="_blank">sezione sull'ambito del supporto del sito relativo alle domande frequenti sul supporto tecnico per Azure</a>. Il servizio HDInsight fornisce anche un livello di supporto aggiuntivo per alcuni componenti, come illustrato di seguito.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

- **Componenti predefiniti**. Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. Questa categoria include ad esempio il gestore delle risorse YARN, il linguaggio di query Hive e la libreria Mahout. Un elenco completo dei componenti del cluster è disponibile <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">qui</a>.
- **Componenti personalizzati**. Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato dall'utente stesso.

I componenti predefiniti sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.

I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile usare molti siti di community, quali: <a href ="https://social.msdn.microsoft.com/Forums/azure/it-it/home?forum=hdinsight" target="_blank">forum MSDN forum per HDInsight</a>, <a href="http://stackoverflow.com" target="_blank">http://stackoverflow.com</a>. Per i progetti Apache sono inoltre disponibili siti specifici in <a href="http://apache.org" target="_blank">http://apache.org</a>, ad esempio: <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a>, <a href="http://spark.apache.org/" target="_blank">Spark</a>.

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui il componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. L'elenco seguente illustra i modi più comuni in cui è possibile usare i componenti personalizzati nei cluster HDInsight.

1. Invio di processi. È possibile inviare processi Hadoop o di altro tipo al cluster che esegue o usa componenti personalizzati.
2. Personalizzazione dei cluster. Durante la creazione di un cluster è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.
3. Esempi. Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.


## Vedere anche##
[Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision-cluster] per istruzioni relative al provisioning di un cluster HDInsight con altre opzioni personalizzate.

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/
[powershell-install-configure]: ../install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"
<!--HONumber=42-->
