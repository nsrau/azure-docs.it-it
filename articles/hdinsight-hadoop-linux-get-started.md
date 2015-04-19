<properties 
   pageTitle="Introduzione all'uso di Hadoop con Hive in HDInsight in Linux| Azure" 
   description="Introduzione all'uso di Hadoop in HDInsight in Linux. Informazioni su come eseguire il provisioning di cluster HDInsight Hadoop in esecuzione in Linux ed eseguire query sui dati con Hive" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>

# Introduzione all'uso di Hadoop con Hive in HDInsight in Linux (anteprima)

Questa esercitazione consente di iniziare a usare rapidamente HDInsight in Linux illustrando come eseguire il provisioning di un cluster HDInsight Hadoop in Linux ed eseguire una query Hive per estrarre informazioni significative da dati non strutturati. Verranno quindi analizzati i risultati in uno strumento di business intelligence (BI), ad esempio Tableau.


> [AZURE.NOTE] Se non si ha esperienza di Hadoop e dell'uso dei Big Data, sono disponibili altre informazioni su <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Per informazioni sull'abilitazione di Hadoop in Azure tramite HDInsight, vedere [Introduzione a Hadoop in HDInsight](hdinsight-hadoop-introduction.md).


## Quali risultati si ottengono con questa esercitazione? ##

Si supponga di avere un set di dati non strutturati di grandi dimensioni e di volere eseguire query sul set di dati per estrarre informazioni significative. Il risultato sarà ottenuto nel modo seguente:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Tableau.](./media/hdinsight-hadoop-linux-get-started/HDI.Linux.GetStartedFlow.png)


**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:


- Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">Opzioni di acquisto</a>, <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">Offerte per i membri</a> oppure <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">Versione di valutazione gratuita</a>.

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'esercitazione:

* [Creare un account di archiviazione di Azure](#storage)
* [Effettuare il provisioning di un cluster HDInsight Linux](#provision)
* [Inviare un processo Hive](#hivequery)
* [Passaggi successivi](#nextsteps)

## <a name="storage"></a>Creare un account di Archiviazione di Azure

HDInsight usa l'archiviazione BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata  *WASB* o  *Archiviazione di Azure - Blob*. WASB è l'implementazione Microsoft di HDFS nell'archiviazione BLOB di Azure. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).

Quando si esegue il provisioning di un cluster HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account è designato come file system predefinito, come in HDFS. Per impostazione predefinita, il provisioning del cluster HDInsight è eseguito nello stesso data center che include l'account di archiviazione specificato.

Oltre a questo account di archiviazione, è possibile aggiungere altri account di archiviazione durante la configurazione personalizzata di un cluster HDInsight. L'account di archiviazione aggiuntivo può appartenere alla stessa sottoscrizione di Azure o a sottoscrizioni di Azure diverse. Per istruzioni, vedere la pagina relativa al [provisioning di cluster HDInsight Linux con opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md). 

Per semplificare questa esercitazione, verranno usati solo il contenitore BLOB predefinito e l'account di archiviazione predefinito. In pratica, i file di dati sono in genere archiviati in un account di archiviazione designato.

**Per creare un account di archiviazione di Azure**

1. Accedere al <a href="https://manage.windowsazure.com/" target="_blank">portale di gestione di Azure</a>.
2. Fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **SERVIZI DATI**, quindi **ARCHIVIAZIONE** e infine **CREAZIONE RAPIDA**.

	![Azure portal where you can use Quick Create to set up a new storage account.](./media/hdinsight-hadoop-linux-get-started/HDI.StorageAccount.QuickCreate.png)

3. Immettere i valori desiderati per **URL**, **PERCORSO** e **REPLICA**, quindi fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**. I gruppi di affinità non sono supportati. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.

	>[AZURE.NOTE]  L'opzione Creazione rapida per il provisioning di un cluster HDInsight Linux, come quella usata in questa esercitazione, non richiede una posizione durante il provisioning del cluster. Per impostazione predefinita, colloca il cluster nello stesso data center dell'account di archiviazione. Assicurarsi quindi di creare l'account di archiviazione nei percorsi supportati per il cluster, ovvero nei percorsi seguenti:  **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali**, **Stati Uniti centro-meridionali**.

4. Attendere che il valore dello **STATO** per il nuovo account di archiviazione venga modificato in **Online**.
5. Selezionare il nuovo account di archiviazione dall'elenco, quindi fare clic su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore della pagina.
7. Prendere nota dei valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA**. È possibile usare indifferentemente una delle due chiavi.  Sarà necessario usarli più avanti nell'esercitazione.


Per altre informazioni, vedere
[Come creare un account di archiviazione](storage-create-storage-account.md) e [Usare l'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).
	
## <a name="provision"></a>Effettuare il provisioning di un cluster HDInsight in Linux

Quando si esegue il provisioning di un cluster HDInsight, si esegue il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. In questa sezione viene effettuato il provisioning di un cluster HDInsight in Linux tramite l'opzione Creazione rapida. Questa opzione usa nomi utente e contenitori di archiviazione di Azure predefiniti e configura un cluster con HDInsight versione 3.2 (Hadoop versione 2.5, HDP versione 2.2) in esecuzione su Ubuntu 12.04 LTS. Per informazioni sulle diverse versioni di HDInsight e i relativi contratti di servizio, vedere la pagina relativa al [controllo delle versioni del componente HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

>[AZURE.NOTE]  È inoltre possibile creare cluster Hadoop in esecuzione nel sistema operativo Windows Server. Per istruzioni, vedere la pagina relativa all'[introduzione ad HDInsight in Windows](hdinsight-get-started.md).


**Per eseguire il provisioning di un cluster HDInsight** 

1. Accedere al <a href="https://manage.windowsazure.com/" target="_blank">portale di gestione di Azure</a>. 

2. Fare clic su **NUOVO** nell'angolo inferiore sinistro, su **Data Services**, **HDInsight**, quindi su **Hadoop on Linux**.

	![Creation of a Hadoop cluster in HDInsight.](./media/hdinsight-hadoop-linux-get-started/HDI.QuickCreateCluster.png)

4. Immettere o selezionare i valori seguenti:

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td>Nome del cluster</td><td>Nome del cluster</td></tr>
	<tr><td>Dimensione del cluster</td><td>Numero di nodi di dati che si vuole distribuire. Il valore predefinito è 4. Tuttavia, nell'elenco a discesa è disponibile anche l'opzione relativa ai nodi di dati 1 o 2. Quando si usa l'opzione <strong>Creazione personalizzata</strong> è possibile specificare un numero qualsiasi di nodi del cluster. Sono disponibili i dettagli sui prezzi relativi alle tariffe di fatturazione per le diverse dimensioni di cluster. Fare clic sul simbolo <strong>?</strong> immediatamente sopra la casella a discesa, quindi selezionare il collegamento disponibile nel popup.</td></tr>
	<tr><td>Password</td><td>La password per l'account <i>HTTP</i> (nome utente predefinito: admin) e per l'account <i>SSH</i> (nome utente predefinito: hdiuser). Si noti che questi NON sono gli account amministratore per le macchine virtuali in cui viene effettuato il provisioning dei cluster. </td></tr>
	
	<tr><td>Account di archiviazione</td><td>Selezionare l'account di archiviazione creato dalla casella a discesa. <br/>

	Dopo la selezione, non sarà possibile modificare l'account di archiviazione. In caso di rimozione dell'account di archiviazione, il cluster non sarà più disponibile per l'uso.

	Il cluster HDInsight è posizionato nello stesso data center dell'account di archiviazione. 
	</td></tr>
	</table>

	Annotare il nome del cluster. Sarà necessario usarlo più avanti nell'esercitazione.

	
5. Fare clic su **Creazione del cluster HDInsight**. Al termine del provisioning, nella colonna relativa allo stato verrà visualizzato il valore **In esecuzione**.

	>[AZURE.NOTE] La procedura precedente crea un cluster Linux con l'opzione Creazione rapida che usa il nome utente SSH e i contenitori di archiviazione di Azure predefiniti. Per creare un cluster con opzioni personalizzate, ad esempio con la chiave SSH per l'autenticazione o con account di archiviazione aggiuntivi, vedere la pagina relativa al [provisioning di cluster HDInsight Linux con opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="hivequery"></a>Inviare un processo Hive nel cluster
Dopo il provisioning di un cluster HDInsight Linux, il passaggio successivo consiste nell'eseguire un processo Hive di esempio per eseguire query su dati di esempio (sample.log) inclusi nei cluster HDInsight. I dati di esempio contengono informazioni di log tra cui tracce, avvisi, informazioni, errori e così via. Vengono eseguite query su questi dati per recuperare tutti i log degli errori con una gravità specifica. È necessario seguire questa procedura per eseguire una query Hive su un cluster HDInsight Linux.

- Connettersi a un cluster Linux
- Eseguire un processo Hive



### Per connettersi a un cluster

È possibile connettersi a un cluster HDInsight in Linux da un computer Linux o da un computer Windows tramite SSH.

**Per connettersi da un computer Linux**

1. Aprire un terminale e immettere il seguente comando:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Poiché è stato eseguito il provisioning di un cluster con l'opzione Creazione rapida, il nome utente SSH predefinito è **hdiuser**. Il comando pertanto deve essere

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. Quando richiesto, immettere la password fornita durante il provisioning del cluster. Dopo aver stabilito la connessione, il prompt cambierà come segue:

		hdiuser@headnode-0:~$


**Per connettersi da un computer Windows**

1. Scaricare **PuTTY** per i client Windows. Lo strumento è disponibile all'indirizzo <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

2. Aprire **PuTTY**. In **Category** fare clic su **Session**. Nella schermata **Basic options for your PuTTY session** immettere l'indirizzo SSH del server HDInsight nel campo **Host name (or IP address)**. L'indirizzo SSH è costituito dal nome del cluster seguito da **-ssh.azurehdinsight.net**. Ad esempio, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-get-started/HDI.linux.connect.putty.png)

3. Per salvare le informazioni di connessione per un uso futuro, immettere un nome per la connessione in **Saved Sessions**, quindi fare clic su **Save**. La connessione verrà aggiunta all'elenco delle sessioni salvate.

4. Fare clic su **Open** per connettersi al cluster. Quando viene richiesto il nome utente, immettere *hdiuser*. Per la password, immettere la password specificata durante il provisioning del cluster. Dopo aver stabilito la connessione, il prompt cambierà come segue:

		hdiuser@headnode-0:~$

### Per eseguire un processo Hive

Dopo aver stabilito la connessione al cluster tramite SSH, usare i seguenti comandi per eseguire una query Hive.

1. Avviare l'interfaccia della riga di comando di Hive immettendo il seguente comando al prompt:

		hive

2. Dall'interfaccia della riga di comando immettere le seguenti istruzioni per creare una nuova tabella denominata log4jLogs con i dati di esempio già disponibili nel cluster.

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Queste istruzioni eseguono le azioni seguenti.

	- **DROP TABLE**: elimina la tabella e il file di dati, qualora la tabella esista già.
	- **CREATE EXTERNAL TABLE**: crea una nuova tabella 'esterna' in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione originale.
	- **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.
	- **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
	- **SELECT**: seleziona un numero di tutte le righe in cui la colonna t4 include il valore [ERROR]. 

	>[WACOM.NOTE] È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti. L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

	Viene restituito il seguente output.

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

	Si noti che l'output contiene **[ERROR] 3**, poiché sono presenti tre righe che contengono questo valore.

3. Usare le seguenti istruzioni per creare una nuova tabella "internal" denominata **errorLogs**.

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Queste istruzioni eseguono le azioni seguenti.

	- **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave EXTERNAL, questa è una tabella  interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive. A differenza delle tabelle **EXTERNAL**, se si elimina una tabella interna verranno eliminati anche i dati sottostanti.
	- **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Questo è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
	- **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti [ERROR], quindi inserisce i dati nella tabella **errorLogs**.

4. Per verificare che solo le righe contenenti **[ERROR]** nella colonna t4 vengano archiviate nella tabella **errorLogs**, usare la seguente istruzione per restituire tutte le righe da errorLogs.

		SELECT * from errorLogs;

	The following output should be displayed on the console.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	I dati restituiti devono corrispondere tutti ai log [ERROR].


## <a name="nextsteps"></a>Passaggi successivi
In questa esercitazione si è appreso come eseguire il provisioning di un cluster Hadoop Linux con HDInsight ed eseguire una query Hive su di esso tramite SSH. Per altre informazioni, vedere gli articoli seguenti:

- [Provisioning di HDInsight in Linux con opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md)
- [Uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md)
- [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md)
- [Usare Hadoop MapReduce in HDInsight][hdinsight-use-mapreduce]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Usare Pig con HDInsight][hdinsight-use-pig]
- [Usare l'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md)
- [Caricare i dati in HDInsight][hdinsight-upload-data]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install

[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=47-->
