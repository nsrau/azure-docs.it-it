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
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# Personalizzare cluster HDInsight mediante l'azione script

È possibile personalizzare un cluster HDInsight di Azure per installare altro software in un cluster o per modificare la configurazione delle applicazioni nel cluster. HDInsight offre un'opzione di configurazione denominata **Azione script** in grado di richiamare script personalizzati che definiscono la personalizzazione da apportare nel cluster. Questi script possono essere usati per personalizzare un cluster *durante la distribuzione*.

I cluster HDInsight possono essere personalizzati in molti modi diversi, ad esempio includendo account di archiviazione di Azure aggiuntivi, modificando i file di configurazione Hadoop (core-site.xml, hive-site.xml e così via) o aggiungendo librerie condivise (ad esempio Hive e Oozie) in posizioni comuni nel cluster. Queste personalizzazioni possono essere apportate tramite Azure PowerShell, Azure HDInsight .NET SDK o il portale di Azure. Per altre informazioni, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision-cluster].



> [AZURE.NOTE]L'uso di Script Action per la personalizzazione di un cluster è supportato solo in cluster HDInsight versione 3.1. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Versioni cluster HDInsight](hdinsight-component-versioning.md).


## <a name="lifecycle"></a>Come usare lo script durante la creazione del cluster

Quando si usa Azione script, è possibile personalizzare un cluster HDInsight solo durante la sua creazione. Durante la creazione, un cluster HDInsight passa attraverso le fasi seguenti:

![Personalizzazione di cluster HDInsight e fasi durante il provisioning di un cluster][img-hdi-cluster-states]

Lo script viene richiamato dopo che il processo di creazione del cluster ha completato la fase **HDInsightConfiguration** e prima della fase **ClusterOperational**. Ogni cluster può accettare più azioni script che vengono richiamate nell'ordine in cui sono specificate.

> [AZURE.NOTE]L'opzione di personalizzazione dei cluster HDInsight è disponibile come parte delle sottoscrizioni standard di Azure HDInsight, senza alcun costo aggiuntivo.

### Funzionamento dello script

È possibile eseguire lo script sul nodo head, sui nodi di lavoro o su entrambi i tipi di nodo. Quando lo script è in esecuzione, il cluster entra nella fase **ClusterCustomization**. In questa fase, lo script viene eseguito con l'account di amministratore di sistema in parallelo su tutti i nodi specificati nel cluster e fornisce privilegi di amministratore completi sui nodi.

> [AZURE.NOTE]Perché durante la fase **ClusterCustomization** si dispone dei privilegi di amministratore sui nodi del cluster, è possibile usare lo script per eseguire operazioni come arresto e avvio dei servizi, inclusi quelli correlati a Hadoop. Pertanto, come parte dello script, è necessario assicurarsi che i servizi di Ambari e altri servizi correlati a Hadoop siano attivi prima che lo script termini l'esecuzione. Questi servizi sono necessari per verificare correttamente l'integrità e lo stato del cluster durante la creazione. Se si modifica qualsiasi configurazione del cluster che influisce su questi servizi, è necessario usare le funzioni di supporto fornite. Per altre informazioni sulle funzioni di supporto, vedere [Sviluppo di azioni script con HDInsight][hdinsight-write-script].

L'output e i log degli errori dello script vengono archiviati nell'account di archiviazione predefinito specificato per il cluster. I log vengono archiviati in una tabella con il nome **u<\\frammento-nome-cluster><\\timestamp>setuplog**. Si tratta di log aggregati dello script eseguito su tutti i nodi (nodo head e nodi di lavoro) del cluster.

## <a name="writescript"></a>Come scrivere uno script per la personalizzazione di un cluster

Per informazioni su come scrivere uno script di personalizzazione di cluster, vedere [Sviluppo di azioni script con HDInsight][hdinsight-write-script].

## <a name="howto"></a>Come usare Azione script per personalizzare un cluster

Per personalizzare un cluster, è possibile usare Azione script dal portale di Azure, i cmdlet di Azure PowerShell o HDInsight .NET SDK.

**Uso del portale di Azure**

1. Per avviare il provisioning di un cluster, usare l'opzione **CREAZIONE PERSONALIZZATA**, come descritto in [Effettuare il provisioning di un cluster con opzioni personalizzate](hdinsight-provision-clusters.md#portal). 
2. Nella pagina **Azioni script** della procedura guidata fare clic su **aggiungi azione script** per specificare i dettagli relativi all'azione script, come descritto di seguito:

	![Usare l'azione script per personalizzare un cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Usare l'azione script per personalizzare un cluster")
	
	<table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Nome</td>
		<td>Specificare un nome per l'azione script.</td></tr>
	<tr><td>URI script</td>
		<td>Specificare l'URI dello script da richiamare per personalizzare il cluster.</td></tr>
	<tr><td>Tipo di nodo</td>
		<td>Specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
	<tr><td>Parametri</td>
		<td>Specificare i parametri, se richiesti dallo script.</td></tr>
</table>È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.
  
**Uso dei cmdlet di Azure PowerShell**

Usare i comandi di Azure PowerShell per HDInsight per eseguire una o più azioni script. È possibile usare il cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** per richiamare script personalizzati. Per usare questi cmdlet, è necessario installare e configurare PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet Azure PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell][powershell-install-configure].

Usare i comandi di Azure PowerShell seguenti per eseguire una singola azione script durante la distribuzione di un cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName –Uri http://uri.to/scriptaction.ps1 –Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Usare i comandi di Azure PowerShell seguenti per eseguire più azioni script durante la distribuzione di un cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName1 –Uri http://uri.to/scriptaction1.ps1 –Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName2 –Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Uso di HDInsight .NET SDK**

HDInsight .NET SDK fornisce una classe <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> per richiamare script personalizzati. Per usare HDInsight .NET SDK:

1. Creare un'applicazione di Visual Studio e quindi installare l'SDK da NuGet. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**. Eseguire il comando seguente nella console per installare il pacchetto:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Creare un cluster tramite l'SDK. Per istruzioni, vedere [Effettuare il provisioning di cluster HDInsight mediante .NET SDK](hdinsight-provision-clusters.md#sdk).

3. Usare la classe **ScriptAction** per richiamare uno script personalizzato, come illustrato di seguito:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// Provide the cluster information, like
			// name, Storage account, credentials,
			// cluster size, and version		    
			...
			...
		};

		// Add the script action to install Spark
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install the component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script
		));


## <a name="example"></a>Esempi di personalizzazione di cluster

Per iniziare, HDInsight fornisce script di esempio per installare i componenti seguenti in un cluster HDInsight:

- **Installare Spark** - Vedere [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark].
- **Installare R** - Vedere [Installare e usare R nei cluster HDInsight][hdinsight-install-r].
- **Installare Solr** - Vedere [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install.md).
- **Installare Giraph** - Vedere [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install.md).

## <a name="support"></a>Supporto per software open source usato nei cluster HDInsight
Il servizio Microsoft Azure HDInsight è una piattaforma flessibile che permette di creare applicazioni Big Data nel cloud usando un ecosistema di tecnologie open source basate su Hadoop. Microsoft Azure fornisce un livello di supporto generale per le tecnologie open source, come illustrato nella sezione relativa all'**ambito del supporto** del <a href="http://azure.microsoft.com/support/faq/" target="_blank">sito Web di domande frequenti sul supporto di Azure</a>. Il servizio HDInsight fornisce un livello di supporto aggiuntivo per alcuni componenti, come illustrato di seguito.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

- **Componenti predefiniti** - Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. Questa categoria include ad esempio il gestore risorse YARN, il linguaggio di query Hive (HiveQL) e la libreria Mahout. L'elenco completo dei componenti del cluster è disponibile in <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Novità delle versioni cluster di Hadoop incluse in HDInsight</a>.
- **Componenti personalizzati** - Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

I componenti predefiniti sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.

I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, ad esempio <a href ="https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight" target="_blank">forum MSDN per HDInsight</a> e il sito <a href="http://stackoverflow.com" target="_blank">Stack Overflow</a>. Per i progetti Apache sono inoltre disponibili siti specifici in <a href="http://apache.org" target="_blank">Apache.org</a>, ad esempio <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a> e <a href="http://spark.apache.org/" target="_blank">Spark</a>.

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui un componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. L'elenco seguente illustra i modi più comuni per usare i componenti personalizzati nei cluster HDInsight:

1. Invio di processi - È possibile inviare al cluster processi Hadoop o di altro tipo che eseguono o usano componenti personalizzati.
2. Personalizzazione del cluster - Durante la creazione di un cluster, è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.
3. Esempi - Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.


## Vedere anche##
[Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision-cluster]: questo argomento fornisce istruzioni relative al provisioning di un cluster HDInsight con altre opzioni personalizzate.

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fasi durante il provisioning di un cluster"

<!--HONumber=54-->