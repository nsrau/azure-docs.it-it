<properties
	pageTitle="Esercitazione di Apache Storm: Introduzione a Storm basato su Linux in HDInsight | Microsoft Azure"
	description="Introduzione all'analisi di Big Data mediante Apache Storm ed esempi di Storm Starter in HDInsight basato su Linux. Informazioni su come usare Storm per elaborare i dati in tempo reale."
	keywords="apache storm,esercitazione su apache storm,analisi Big Data,storm starter"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/31/2016"
   ms.author="larryfr"/>


# Esercitazione di Apache Storm: Introduzione all'analisi di Big Data mediante esempi di Storm Starter su HDInsight

Apache Storm è un sistema di calcolo in tempo reale scalabile, a tolleranza di errore e distribuito per l'elaborazione di flussi di dati. Con Storm in Azure HDInsight è possibile creare un cluster Storm basato sul cloud che esegue analisi di Big Data in tempo reale.

> [AZURE.NOTE] I passaggi descritti in questo articolo consentono di creare un cluster HDInsight basato su Linux. Per la procedura necessaria per creare un cluster Storm basato su Windows nel cluster HDInsight, vedere [Esercitazione di Apache Storm: Introduzione all'esempio Storm Starter usando l'analisi dei dati in HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

## Prima di iniziare

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per completare correttamente questa esercitazione di Apache Storm, è necessario quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Familiarità con SSH e SCP**. Per altre informazioni sull'uso di SSH e SCP con HDInsight, vedere gli articoli seguenti:

    - **Client Linux, Unix o OS X**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

	- **Client Windows**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Creare un cluster di Storm

In questa sezione si crea un cluster HDInsight versione 3.2 (Storm versione 0.9.3) usando un modello di Azure Resource Manager. Per informazioni sulle versioni di HDInsight e sui relativi contratti di servizio, vedere l'articolo relativo al [controllo delle versioni del componente HDInsight](hdinsight-component-versioning.md). Per altri metodi di creazione dei cluster, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Fare clic sull'immagine seguente per aprire un modello di Gestione risorse di Azure nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/it-IT/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello di Azure Resource Manager è disponibile in un contenitore BLOB pubblico, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*.
   
2. Nel pannello Parametri immettere le informazioni seguenti:

    - **ClusterName**: immettere un nome per il cluster Hadoop che verrà creato.
    - **Nome utente e password di accesso del cluster**: il nome dell'account di accesso predefinito è admin.
    - **Nome utente e password SSH**.
    
    Annotare questi valori. Sarà necessario usarli più avanti nell'esercitazione.

    > [AZURE.NOTE] Il protocollo SSH viene usato per accedere in modalità remota al cluster HDInsight tramite una riga di comando. Il nome utente e la password specificati qui sono usati per la connessione al cluster tramite SSH. Il nome utente SSH deve essere univoco, in quanto crea un account utente in tutti i nodi del cluster HDInsight. Di seguito sono riportati alcuni nomi di account riservati per l'uso da parte dei servizi nel cluster che non devono essere usati come nome utente SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	> Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	> * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	> * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\. Fare clic su **OK** per salvare i parametri.

4\. Nel pannello **Distribuzione personalizzata** fare clic su **Gruppo di risorse** nella casella di riepilogo a discesa e quindi scegliere **Nuovo** per creare un nuovo gruppo di risorse. Il gruppo di risorse è un contenitore che raggruppa il cluster, l'account di archiviazione dipendente e altre risorse collegate.

5\. Fare clic su **Note legali** e quindi su **Crea**.

6\. Fare clic su **Crea**. Verrà visualizzato un nuovo riquadro denominato Invio della distribuzione per Distribuzione modello. La creazione del cluster e del database SQL richiede circa 20 minuti.


##Eseguire un esempio Storm Starter in HDInsight

Gli esempi di [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) sono inclusi nel cluster HDInsight. Nei passaggi seguenti, verrà eseguito l'esempio WordCount.

1. Connettersi al cluster HDInsight usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Se è stata usata una password per proteggere l'account utente SSH, verrà richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. Ad esempio: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Per altre informazioni sull'uso di SSH con HDInsight basato su Linux, vedere gli articoli seguenti:
	
	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows)

2. Usare il comando seguente per avviare una topologia di esempio:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE] La parte `0.9.3.2.2.4.9-1` del nome di file può essere modificata quando HDinsight viene aggiornato con le versioni più recenti di Storm.

    Verrà avviata la topologia di esempio WordCount nel cluster usando 'wordcount' come nome descrittivo. Verranno generate in modo casuale le frasi e verranno conteggiate le occorrenze di ogni parola nelle frasi.

    > [AZURE.NOTE] Durante l'invio di una topologia al cluster, è prima di tutto necessario copiare il file con estensione JAR contenente il cluster prima di usare il comando `storm`. A tale scopo, usare il comando `scp` dal client in cui è presente il file. Ad esempio: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > L'esempio WordCount e altri esempi di avvio dell'utilità storm sono già inclusi nel cluster in `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Monitorare la topologia

L'interfaccia utente di Storm è inclusa nel cluster HDInsight e fornisce un'interfaccia Web da usare con le topologie in esecuzione.

Usare la procedura seguente per monitorare la topologia con l'interfaccia utente Storm:

1. Aprire un Web browser e passare a https://CLUSTERNAME.azurehdinsight.net/stormui, dove __CLUSTERNAME__ è il nome del cluster. Verrà aperta l'interfaccia utente Storm.

	> [AZURE.NOTE] Se viene richiesto di fornire un nome utente e una password, immettere l'amministratore del cluster e la password usati durante la creazione del cluster.

2. Nella sezione **Topology summary** selezionare la voce **wordcount** nella colonna **Name**. Verranno visualizzate altre informazioni sulla topologia.

	![Storm Dashboard con informazioni sulla topologia di Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	In questa pagina sono disponibili le informazioni seguenti:

	* **Topology stats**: informazioni di base sulle prestazioni della topologia, organizzate in intervalli di tempo.

		> [AZURE.NOTE] La selezione di un intervallo di tempo specifico determina la modifica dell'intervallo di tempo relativo a informazioni visualizzate in altre sezioni della pagina.

	* **Spouts**: informazioni di base sugli spout, incluso l'ultimo errore restituito da ciascuno di essi.

	* **Bolts**: informazioni di base sui bolt.

	* **Topology configuration**: informazioni dettagliate sulla configurazione della topologia.

	Questa pagina fornisce anche azioni che possono essere eseguite sulla topologia:

	* **Activate**: riprende l'elaborazione di una topologia disattivata.

	* **Deactivate**: sospende una topologia in esecuzione.

	* **Rebalance**: regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi del cluster. Questo consente alla topologia di regolare il parallelismo per compensare l'aumento o la diminuzione del numero di nodi del cluster. Per altre informazioni, vedere l'articolo relativo al [parallelismo di una topologia Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	* **Kill**: arresta una topologia Storm dopo il timeout specificato.

3. In questa pagina selezionare una voce nella sezione **Spouts** o **Bolts**. Verranno visualizzate informazioni relative al componente selezionato.

	![Storm Dashboard con informazioni sui componenti selezionati.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	In questa pagina vengono visualizzate le informazioni seguenti:

	* **Spout/Bolt stats**: informazioni di base sulle prestazioni, organizzate in intervalli di tempo.

		> [AZURE.NOTE] La selezione di un intervallo di tempo specifico determina la modifica dell'intervallo di tempo relativo a informazioni visualizzate in altre sezioni della pagina.

	* **Input stats** (solo bolt): informazioni sui componenti che generano dati utilizzati dal bolt.

	* **Output stats**: informazioni sui dati generati dal bolt.

	* **Executors**: informazioni sulle istanze del componente.

	* **Errors**: errori generati dal componente.

4. Quando si visualizzano i dettagli di uno spout o di un bolt, selezionare una voce nella colonna **Port** della sezione **Executors** per visualizzare i dettagli relativi a una specifica istanza del componente.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	Da questi dati è possibile vedere che la parola **seven** è stata rilevata 1493957 volte. In altri termini, il numero 1493957 indica le occorrenze della parola dall'avvio della topologia.

##Arrestare la topologia

Tornare alla pagina **Topology summary** per la topologia relativa al conteggio parole e quindi fare clic sul pulsante **Kill** nella sezione **Topology actions**. Quando richiesto, immettere 10 per il numero di secondi di attesa prima dell'arresto della topologia. Dopo il periodo di timeout, la topologia non verrà più visualizzata nella sezione **Storm UI** del dashboard.

##Eliminazione del cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a id="next"></a>Passaggi successivi

In questa esercitazione di Apache Storm si è usato Storm Starter per apprendere come creare un cluster Storm in HDInsight e usare Storm Dashboard per distribuire, monitorare e gestire topologie Storm. Vedere quindi altre informazioni su come [sviluppare topologie basate su Java con Maven](hdinsight-storm-develop-java-topology.md).

Se si ha già familiarità con lo sviluppo di topologie basate su Java e si vuole distribuire una topologia esistente in HDInsight, vedere [Distribuire e gestire topologie Apache Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md).

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0706_2016-->