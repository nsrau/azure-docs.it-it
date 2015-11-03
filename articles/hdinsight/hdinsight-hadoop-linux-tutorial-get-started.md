<properties
   	pageTitle="Esercitazione di Linux: Introduzione a Hadoop e Hive | Microsoft Azure"
   	description="Seguire questa esercitazione di Linux per iniziare a utilizzare Hadoop in HDInsight. Informazioni su come eseguire il provisioning di cluster Linux ed eseguire query sui dati con Hive."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/23/2015"
   	ms.author="nitinme"/>

# Esercitazione di Hadoop: Introduzione all'uso di Hadoop con Hive in HDInsight in Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

In questo documento viene fornita un'introduzione rapida all'uso di Azure HDInsight in Linux e viene illustrato come creare un cluster Hadoop basato su Linux, connettersi al cluster tramite SSH (Secure Shell) e quindi eseguire una query Hive sui dati di esempio inclusi nel cluster.

> [AZURE.NOTE]Se non si ha esperienza di Hadoop e dell'uso dei Big Data, sono disponibili altre informazioni su [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [Hadoop Distributed File System (HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087) e [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Per informazioni sull'abilitazione di Hadoop in Azure tramite HDInsight, vedere [Introduzione a Hadoop in HDInsight](hdinsight-hadoop-introduction.md).

## Prerequisiti

Prima di iniziare questa esercitazione Linux per Hadoop, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**: vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un client SSH (Secure Shell)**: i sistemi Linux, Unix e OS X forniscono un client SSH tramite il comando `ssh`. Per i sistemi Windows, è consigliabile [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    > [AZURE.NOTE]I passaggi descritti in questo documento usano SSH per connettersi al cluster HDInsight dal momento che SSH è disponibile per tutti i sistemi operativi client. Per altri metodi di connessione al cluster HDInsight, ad esempio mediante gli strumenti di HDInsight per Visual Studio o API REST, vedere i collegamenti di Hive, Pig e MapReduce nella sezione [Passaggi successivi](#nextsteps) di questo documento.
    
- **Chiavi Secure Shell (SSH)** (facoltativo): è possibile proteggere l'account SSH usato per connettersi al cluster mediante una password o una chiave pubblica. Utilizzando una password è possibile iniziare rapidamente e utilizzare questa opzione se si desidera eseguire il provisioning di un cluster ed eseguire alcuni processi di test. L'uso di una chiave risulta più sicuro, anche se richiede passaggi di impostazione aggiuntivi. È possibile utilizzare questo approccio durante il provisioning di un cluster di produzione. In questo articolo viene utilizzato l'approccio di password. Per istruzioni su come creare e usare chiavi SSH con HDInsight, vedere gli articoli seguenti:

	-  Da un computer Linux: [Usare SSH con HDInsight basato su Linux (Hadoop) da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
	-  Da un computer Windows: [Usare SSH con HDInsight basato su Linux (Hadoop) da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a name="provision"></a>Effettuare il provisioning di un cluster HDInsight in Linux

Quando si esegue il provisioning di un cluster, vengono create le risorse di calcolo di Azure che includono servizi e risorse Hadoop. In questa sezione si effettua il provisioning di un cluster HDInsight versione 3.2 contenente Hadoop versione 2.2. Per informazioni sulle versioni di HDInsight e sui relativi contratti di servizio, vedere l'articolo relativo al [controllo delle versioni del componente HDInsight](hdinsight-component-versioning.md). Per informazioni dettagliate sulla creazione di un cluster HDInsight, vedere [Provisioning di cluster HDInsight con opzioni personalizzate][hdinsight-provision].

>[AZURE.NOTE]È inoltre possibile creare cluster Hadoop in esecuzione nel sistema operativo Windows Server. Per istruzioni, vedere [Introduzione ad HDInsight in Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

Per creare un nuovo cluster, seguire questa procedura.

1. Accedere al [portale di anteprima di Azure](https://ms.portal.azure.com/).
2. Fare clic su **NUOVO**, su **Analisi dei dati** e quindi su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di anteprima di Azure](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di anteprima di Azure")

3. Immettere un valore in **Nome cluster**, selezionare **Hadoop** per **Tipo di cluster** e dall'elenco a discesa **Sistema operativo cluster** selezionare **Ubuntu**. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.

	![Immettere il nome del cluster e il tipo](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Immettere il nome del cluster e il tipo")

4. Se si dispone di più di una sottoscrizione, fare clic sulla voce **Sottoscrizione** per selezionare la sottoscrizione di Azure che verrà usata per il cluster.

5. Fare clic su **Gruppo di risorse** per visualizzare un elenco di gruppi di risorse esistenti e quindi selezionare quello in cui creare il cluster. In alternativa, è possibile fare clic su **Crea nuovo** e quindi immettere il nome del nuovo gruppo di risorse. Verrà visualizzato un segno di spunta verde per indicare se il nome del nuovo gruppo è disponibile.

	> [AZURE.NOTE]Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Fare clic su **Credenziali** e quindi immettere una password per l'utente amministratore. È inoltre necessario immettere un **SSH Username**. Per il **Tipo di autenticazione SSH**, fare clic su **PASSWORD** e specificare una password per l'utente SSH. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.

	![Fornire le credenziali del cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Fornire le credenziali del cluster")

    > [AZURE.NOTE]Il protocollo SSH viene usato per accedere in modalità remota al cluster HDInsight tramite una riga di comando. Il nome utente e la password specificati qui sono usati per la connessione al cluster tramite SSH. Il nome utente SSH deve essere univoco, in quanto crea un account utente in tutti i nodi del cluster HDInsight. Di seguito sono riportati alcuni nomi di account riservati per l'utilizzo da parte dei servizi nel cluster che non devono essere usati come nome utente SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Fare clic su **Origine dati** per scegliere un'origine dati esistente per il cluster o crearne una nuova. Quando si esegue il provisioning di un cluster Hadoop in HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account viene designato come file system predefinito, come in HDFS (Hadoop Distributed File System, file system distribuito Hadoop). Per impostazione predefinita, il provisioning del cluster HDInsight viene effettuato nello stesso data center dell'account di archiviazione specificato. Per altre informazioni, vedere l'argomento relativo all'[uso dell'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).

	![Pannello di origine dati](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "Fornire la configurazione origine dati")

	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Usare le seguenti informazioni per comprendere le voci nel pannello **Origine dati**.

	- **Metodo di selezione**: impostare questa proprietà su **Da tutte le sottoscrizioni** per consentire l'esplorazione di account di archiviazione da tutte le sottoscrizioni. Impostare questa proprietà su **Chiave di accesso** se si desidera immettere un valore nei campi **Nome archiviazione** e **Chiave di accesso** per un account di archiviazione esistente.

	- **Selezionare l'account di archiviazione / Crea nuovo**: fare clic su **Selezionare l'account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. In alternativa, fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.

	- **Scegliere il contenitore predefinito**: usare questa opzione per immettere il nome del contenitore predefinito da usare per il cluster. È possibile immettere qualsiasi nome, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene utilizzato per tale cluster specifico.

	- **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione.

		> [AZURE.IMPORTANT]La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l’origine dati predefinita devono trovarsi nella stessa area.

	Fare clic su **Seleziona** per salvare la configurazione dell'origine dati.

8. Fare clic su **Piani tariffari per il nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "Specificare il numero di nodi del cluster")
    
    > [AZURE.IMPORTANT]Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14GB di RAM.
    >
    > Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Fare clic su **Seleziona** per salvare la configurazione del prezzo del nodo.

9. Nel pannello**Nuovo cluster HDInsight** assicurarsi che **Aggiungi alla schermata iniziale** sia selezionato, quindi fare clic su **Crea**. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster sta eseguendo il provisioning e verrà visualizzata l'icona di HDInsight, una volta completato il provisioning.

Durante il provisioning|Provisioning completato
------------------|---------------------
	![Indicatore del provisioning sulla schermata iniziale](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Sezione del cluster su cui è stato effettuato il provisioning](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE]La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina per controllare il processo di provisioning.

Al termine del provisioning, fare clic sul riquadro per il cluster dalla schermata iniziale per avviare il pannello del cluster.

## <a name="connect"></a> Per connettersi al cluster

È possibile connettersi a un cluster HDInsight in Linux da un computer Linux o da un computer basato su Windows tramite SSH.

###Per connettersi da un computer Linux

1. Aprire un terminale e immettere il seguente comando:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Poiché è stato eseguito il provisioning di un cluster con l'opzione Creazione rapida, il nome utente SSH predefinito è **hdiuser**. Il comando deve pertanto essere:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. Quando richiesto, immettere la password fornita durante il provisioning del cluster. Dopo aver stabilito la connessione, il prompt cambierà come segue:

		hdiuser@headnode-0:~$


###Per connettersi da un computer Windows

1. Scaricare [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) per i client basati su Windows.

2. Aprire PuTTY. In **Category** fare clic **Session**. Nella schermata **Basic options for your PuTTY session** immettere l'indirizzo SSH del server HDInsight nel campo **Host name (or IP address)**. L'indirizzo SSH è costituito dal nome del cluster seguito da**-ssh.azurehdinsight.net**. Ad esempio, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Per salvare le informazioni di connessione per un uso futuro, immettere un nome per la connessione in **Saved Sessions**, quindi fare clic su **Save**. La connessione verrà aggiunta all'elenco delle sessioni salvate.

4. Fare clic su **Open** per connettersi al cluster. Quando viene richiesto il nome utente, immettere **hdiuser**. Per la password, immettere la password specificata durante il provisioning del cluster. Dopo aver stabilito la connessione, il prompt cambierà come segue:

		hdiuser@headnode-0:~$

##<a name="hivequery"></a>Eseguire una query Hive

Dopo aver stabilito la connessione al cluster tramite SSH, usare i comandi seguenti per eseguire una query Hive.

1. Avviare l'interfaccia della riga di comando di Hive immettendo il comando seguente al prompt:

		hive

2. Dall'interfaccia della riga di comando immettere le istruzioni seguenti per creare una nuova tabella denominata **log4jLogs** con i dati di esempio già disponibili nel cluster:

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Le istruzioni eseguono queste azioni:

	- **DROP TABLE**: elimina la tabella e il file di dati, qualora la tabella esista già.
	- **CREATE EXTERNAL TABLE**: crea una nuova tabella "esterna" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione originale.
	- **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.
	- **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
	- **SELECT**: seleziona un numero di tutte le righe in cui la colonna t4 include il valore [ERROR].

	>[AZURE.NOTE]È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti. L'eliminazione di una tabella esterna *non* comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

	Viene restituito l'output seguente:

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched:
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	Si noti che l'output contiene **ERROR 3**, poiché sono presenti tre righe che contengono questo valore.

3. Usare le seguenti istruzioni per creare una nuova tabella "internal" denominata **errorLogs**:

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Le istruzioni eseguono queste azioni:

	- **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive. A differenza di quanto accade con le tabelle esterne, se si elimina una tabella interna verranno eliminati anche i dati sottostanti.
	- **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Questo è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
	- **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti [ERROR], quindi inserisce i dati nella tabella **errorLogs**.

4. Per verificare che solo le righe contenenti [ERROR] nella colonna t4 siano state archiviate nella tabella **errorLogs**, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**:

		SELECT * from errorLogs;

	Nella console viene visualizzato il risultato seguente:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	I dati restituiti devono corrispondere tutti ai log [ERROR].

## <a name="nextsteps"></a>Passaggi successivi

In questo documento, si è appreso come creare un cluster HDInsight basato su Linux tramite il portale di anteprima di Azure, connettersi al cluster tramite SSH e come eseguire query Hive di base.

Per altre informazioni sull'analisi dei dati con HDInsight, vedere gli articoli seguenti:

- Per altre informazioni sull'uso di Hive con HDInsight, incluse le procedure per eseguire query Hive da Visual Studio, vedere [Usare Hive con HDInsight][hdinsight-use-hive].

- Per informazioni su Pig, un linguaggio usato per la trasformazione dei dati, vedere [Usare Pig con HDInsight][hdinsight-use-pig].

- Per altre informazioni su MapReduce, un framework software che consente di scrivere programmi per l'elaborazione dei dati in Hadoop, vedere [Usare MapReduce con HDInsight][hdinsight-use-mapreduce].

- Per altre informazioni sull'uso di Hadoop Tools per Visual Studio per analizzare i dati in HDInsight, vedere [Introduzione all'uso di Hadoop Tools per Visual Studio per HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Se si è pronti per iniziare a usare i dati, ma sono necessarie altre informazioni sulle modalità di archiviazione dei dati in HDInsight o sulle procedure di importazione dei dati in HDInsight, vedere gli argomenti seguenti:

- Per informazioni sul modo in cui HDInsight usa l'archivio BLOB di Azure, vedere [Usare l'archivio BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).

- Per informazioni sulle modalità di caricamento di dati in HDInsight, vedere [Caricare dati in HDInsight][hdinsight-upload-data].

Per altre informazioni sulla creazione o la gestione di un cluster HDInsight, vedere gli argomenti seguenti:

- Per altre informazioni sulla gestione di cluster HDInsight basati su Linux, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md).

- Per altre informazioni sulle opzioni che è possibile selezionare durante la creazione di un cluster HDInsight, vedere [Provisioning di HDInsight in Linux con opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md).

- Se si ha familiarità con Linux e Hadoop ma si vogliono informazioni specifiche su Hadoop in HDInsight, vedere [Uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md). In questo argomento vengono fornite informazioni quali, ad esempio:

	* URL per i servizi ospitati nel cluster, ad esempio Ambari e WebHCat
	* Il percorso del file Hadoop e gli esempi nel file system locale
	* L'utilizzo di archiviazione di Azure (WASB) anziché di HDFS come archivio predefinito di dati


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=Nov15_HO1-->