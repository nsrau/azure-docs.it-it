<properties 
	pageTitle="Personalizzare cluster HDInsight mediante le azioni script | Microsoft Azure" 
	description="Informazioni su come personalizzare cluster HDInsight tramite le azioni script." 
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
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# Personalizzare cluster HDInsight mediante l'azione script

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-v1.md)

HDInsight offre un'opzione di configurazione denominata **Azione script** in grado di richiamare script personalizzati che definiscono la personalizzazione da apportare nel cluster durante il processo di provisioning. Questi script possono essere usati per installare altro software in un cluster o per modificare la configurazione delle applicazioni in un cluster.


> [AZURE.NOTE]Azione script è supportato solo nel cluster HDInsight versione 3.1 o successiva con il sistema operativo Windows. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Versioni cluster HDInsight](hdinsight-component-versioning.md).
> 
> Azione script è disponibile come parte delle sottoscrizioni standard di Azure HDInsight, senza alcun costo aggiuntivo.

I cluster HDInsight possono essere personalizzati in molti modi diversi, ad esempio includendo account di archiviazione di Azure aggiuntivi, modificando i file di configurazione Hadoop (core-site.xml, hive-site.xml e così via) o aggiungendo librerie condivise (ad esempio Hive e Oozie) in posizioni comuni nel cluster. Queste personalizzazioni possono essere apportate tramite Azure PowerShell, Azure HDInsight .NET SDK o il portale di anteprima di Azure. Per altre informazioni, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision-cluster].

## Azione script nel processo di provisioning di cluster

Azione script viene usato solo mentre è in corso il processo di creazione di un cluster. Il diagramma seguente illustra quando Azione script viene eseguito durante il processo di provisioning:

![Personalizzazione di cluster HDInsight e fasi durante il provisioning di un cluster][img-hdi-cluster-states]

Quando lo script è in esecuzione, il cluster entra nella fase **ClusterCustomization**. In questa fase, lo script viene eseguito con l'account di amministratore di sistema in parallelo su tutti i nodi specificati nel cluster e fornisce privilegi di amministratore completi sui nodi.

> [AZURE.NOTE]Perché durante la fase **ClusterCustomization** si dispone dei privilegi di amministratore sui nodi del cluster, è possibile usare lo script per eseguire operazioni come arresto e avvio dei servizi, inclusi quelli correlati a Hadoop. Pertanto, come parte dello script, è necessario assicurarsi che i servizi di Ambari e altri servizi correlati a Hadoop siano attivi prima che lo script termini l'esecuzione. Questi servizi sono necessari per verificare correttamente l'integrità e lo stato del cluster durante la creazione. Se si modifica qualsiasi configurazione del cluster che influisce su questi servizi, è necessario usare le funzioni di supporto fornite. Per altre informazioni sulle funzioni di supporto, vedere [Sviluppare script di Azione script per HDInsight][hdinsight-write-script].

L'output e i log degli errori dello script vengono archiviati nell'account di archiviazione predefinito specificato per il cluster. I log vengono archiviati in una tabella con il nome **u<\\frammento-nome-cluster><\\timestamp>setuplog**. Si tratta di log aggregati dello script eseguito su tutti i nodi (nodo head e nodi di lavoro) del cluster.


Ogni cluster può accettare più azioni script che vengono richiamate nell'ordine in cui sono specificate. Uno script può essere eseguito nel nodo head, nei nodi di lavoro o in entrambi.

## Chiamare gli script di Azione script

Gli script di Azione script possono essere usati dal portale di anteprima di Azure, da Azure PowerShell o da HDInsight .NET SDK. In questo articolo viene illustrato come utilizzare Azione di script dal portale. Per informazioni su come utilizzare PowerShell e .NET SDK per utilizzare Azione di script, esaminare gli esempi elencati nella tabella seguente.

HDInsight fornisce diversi script di esempio per installare i componenti seguenti nei cluster HDInsight:

Nome | Script
----- | -----
**Installare Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Vedere [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark].
**Installare R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Vedere [Installare e usare R nei cluster HDInsight][hdinsight-install-r].
**Installare Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Vedere [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install.md).
\- **Installare Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Vedere [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install.md).



**Dal portale di anteprima di Azure**

1. Avviare il provisioning di un cluster come descritto in [Eseguire il provisioning di un cluster utilizzando le opzioni personalizzate](hdinsight-provision-clusters.md#portal). 
2. In Configurazione facoltativa, nel pannello **Azioni script** fare clic su **Aggiungi azione script** per specificare i dettagli relativi all'azione di script, come descritto di seguito:

	![Usare l'azione script per personalizzare un cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Usare l'azione script per personalizzare un cluster")
	
	<table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Nome</td>
		<td>Specificare un nome per l'azione script.</td></tr>
	<tr><td>URI script</td>
		<td>Specificare l'URI dello script da richiamare per personalizzare il cluster.</td></tr>
	<tr><td>Head/ruolo di lavoro</td>
		<td>Specificare i nodi (**Head** o **Ruolo di lavoro**) in cui viene eseguito lo script di personalizzazione.</b>.
	<tr><td>Parametri</td>
		<td>Specificare i parametri, se richiesti dallo script.</td></tr>
</table>È possibile aggiungere altre azioni script per installare più componenti nel cluster.

3. Fare clic su **Seleziona** per salvare la configurazione dell'azione di script e continuare con il provisioning del cluster.
  

## Supporto per software open source usato nei cluster HDInsight
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

## Sviluppare script di Azione script

Vedere [Sviluppare script di Azione script per HDInsight][hdinsight-write-script].


## Vedere anche

- [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision-cluster]\: questo argomento fornisce istruzioni relative al provisioning di un cluster HDInsight con altre opzioni personalizzate.
- [Sviluppare script di Azione script per HDInsight][hdinsight-write-script]
- [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark]
- [Installare e usare R nei cluster HDInsight][hdinsight-install-r]
- [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install.md).
- [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fasi durante il provisioning di un cluster"
 

<!---HONumber=August15_HO8-->