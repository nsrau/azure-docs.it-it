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
   ms.date="01/12/2016"
   ms.author="larryfr"/>


# Esercitazione di Apache Storm: Introduzione all'analisi di Big Data mediante esempi di Storm Starter su HDInsight

Apache Storm è un sistema di calcolo in tempo reale scalabile, a tolleranza di errore e distribuito per l'elaborazione di flussi di dati. Con Storm in Azure HDInsight è possibile creare un cluster Storm basato sul cloud che esegue analisi di Big Data in tempo reale.

> [AZURE.NOTE] I passaggi descritti in questo articolo consentono di creare un cluster HDInsight basato su Linux. Per la procedura necessaria per creare un cluster Storm basato su Windows nel cluster HDInsight, vedere [Esercitazione di Apache Storm: Introduzione all'esempio Storm Starter usando l'analisi dei dati in HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

## Prima di iniziare

Per completare correttamente questa esercitazione di Apache Storm, è necessario quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Familiarità con SSH e SCP**. Per altre informazioni sull'uso di SSH e SCP con HDInsight, vedere gli articoli seguenti:

    - **Client Linux, Unix o OS X**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

	- **Client Windows**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Creare un cluster di Storm

Storm in HDInsight usa Archiviazione BLOB di Azure per archiviare i file di log e le topologie inviate al cluster. Per creare un account di Archiviazione di Azure da usare con il cluster, seguire questa procedura:

1. Accedere al [portale di Azure][preview-portal].

2. Selezionare **NUOVO**, selezionare __Analisi di dati__, quindi selezionare __HDInsight__

	![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. Immettere un valore in __Nome cluster__ e quindi selezionare __Storm__ per il __Tipo di cluster__. Un segno di spunta verde verrà visualizzato accanto al __Nome del cluster__, se disponibile.

	![Nome del cluster, tipo di cluster e tipo di sistema operativo](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

	Selezionare __Ubuntu__ per creare un cluster HDInsight basato su Linux.
    
    > [AZURE.NOTE] Per i passaggi descritti in questo documento, lasciare il campo __Versione__ impostato sul valore predefinito.
	
4. Se sono disponibili più sottoscrizioni, selezionare la voce __Sottoscrizione__ per specificare la sottoscrizione di Azure da usare per il cluster.

5. Selezionare __Gruppo di risorse__ per visualizzare un elenco di gruppi di risorse esistenti e quindi selezionare quello in cui creare il cluster. In alternativa, è possibile selezionare __Crea nuovo__ e quindi immettere il nome del nuovo gruppo di risorse. Verrà visualizzato un segno di spunta verde per indicare se il nome del nuovo gruppo è disponibile.

	> [AZURE.NOTE] Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Selezionare __Credenziali__ e quindi immettere una __password dell'account di accesso del cluster__ per il __nome utente dell'account di accesso del cluster__. È anche necessario specificare un __Nome utente SSH__ e una __PASSWORD__ o una __CHIAVE PUBBLICA__, che verranno usati per autenticare l'utente SSH. Infine, usare il pulsante __Seleziona__ per impostare le credenziali.

	![Pannello credenziali del cluster](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

6. Per __Origine dati__, è possibile selezionare la voce per scegliere un'origine dati esistente o crearne una nuova.

	![Pannello di origine dati](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)
	
	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Usare le informazioni seguenti per comprendere le voci nel pannello __Origine dati__.
	
	- __Metodo di selezione__: impostare questa proprietà su __Da tutte le sottoscrizioni__ per consentire l'esplorazione di account di archiviazione da tutte le sottoscrizioni. Impostare questa proprietà su __Tasto di scelta__ se si desidera immettere il __Nome di archiviazione__ e il __Tasto di scelta__ di un account di archiviazione esistente.
    
    - __Selezionare l'account di archiviazione__: se per la sottoscrizione esiste già un account di archiviazione, selezionare l'account da usare per il cluster tramite questa opzione.
	
	- __Crea nuovo__: per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.
	
	- __Scegli contenitore predefinito__: utilizzare questa opzione per immettere il nome del contenitore predefinito da utilizzare per il cluster. È possibile immettere qualsiasi nome, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene utilizzato per tale cluster specifico.
	
	- __Località__: l'area geografica in cui si trova o in cui verrà creato l'account di archiviazione.
	
		> [AZURE.IMPORTANT] La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l'origine dati predefinita devono trovarsi nella stessa area.
    
    - __Identità AAD del cluster__: consente di selezionare un'identità di Azure Active Directory che verrà usata dal cluster per accedere all'Archivio Azure Data Lake.
    
        > [AZURE.NOTE] Non viene usata in questo documento e si può quindi mantenere l'impostazione predefinita. Per informazioni sull'uso di questa voce e dell'Archivio Azure Data Lake con HDInsight, vedere [Creare un cluster HDInsight che usa Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md).
		
	- __Seleziona__: per salvare la configurazione dell'origine dati.
	
7. Selezionare __Piani tariffari per il nodo__ per visualizzare informazioni sui nodi che verranno creati per questo cluster. Per impostazione predefinita, verrà impostato il numero di nodi di lavoro su __4__. Verrà visualizzato il costo stimato del cluster in fondo al pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)
	
    È possibile selezionare ogni tipo di nodo per modificare il tipo di VM usato per i nodi del cluster. Lasciare le impostazioni predefinite per i passaggi in questo documento.
    
	Utilizzare il pulsante __Seleziona__ per salvare le informazioni sui __livelli di prezzi nodo__.

8. Selezionare __Configurazione facoltativa__. Questo pannello consente di aggiungere il cluster a una __rete virtuale__, usare __azioni script__ per personalizzare il cluster oppure usare un __metastore personalizzato__ per contenere i dati di Hive e Oozie.

	![Pannello configurazione facoltativa](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)
    
    Lasciare queste voci impostate su __Non configurato__ per i passaggi descritti in questo documento.

9. Assicurarsi che __Aggiungi alla Schermata iniziale__ sia selezionato e quindi selezionare __Crea__. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster sta eseguendo il provisioning e verrà visualizzata l'icona di HDInsight, una volta completato il provisioning.

	| Durante il provisioning | Provisioning completato |
	| ------------------ | --------------------- |
	| ![Indicatore del provisioning sulla schermata iniziale](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png) | ![Sezione del cluster su cui è stato effettuato il provisioning](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png) |

	> [AZURE.NOTE] La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Utilizzare il riquadro sulla schermata iniziale, o la voce __Notifiche__ a sinistra della pagina per controllare il processo di provisioning.

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

##Riepilogo

In questa esercitazione di Apache Storm si è usato Storm Starter per apprendere come creare un cluster Storm in HDInsight e usare Storm Dashboard per distribuire, monitorare e gestire topologie Storm.

##<a id="next"></a>Passaggi successivi

* Il documento seguente contiene un elenco di altri esempi che è possibile usare con Storm in HDInsight:

	* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0128_2016-->