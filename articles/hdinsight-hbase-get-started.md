<properties urlDisplayName="Get Started" pageTitle="Configurare tabelle HBase su cui eseguire query usando Hive in HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. Learn how to create HBase tables and query them using Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



# Configurare cluster HBase su cui eseguire query usando Hive in Hadoop in HDInsight

Informazioni su come creare tabelle HBase su cui eseguire query usando Hive in Hadoop in HDInsight. 

##Contenuto dell'articolo

* [Che cos'è HBase?](#hbaseintro)
* [Prerequisiti](#prerequisites)
* [Eseguire il provisioning di cluster HBase usando il portale di gestione di Azure](#create-hbase-cluster)
* [Gestire tabelle HBase tramite la shell di HBase](#create-sample-table)
* [Usare HiveQL per eseguire query sulle tabella HBase](#hive-query)
* [Usare la libreria client REST di Microsoft HBase per gestire le tabelle HBase](#hbase-powershell)
* [Vedere anche](#seealso)

##<a name="hbaseintro"></a>Che cos'è HBase?

HBase è un database NoSQL a bassa latenza che permette di eseguire l'elaborazione transazionale online dei Big Data. HBase è offerto come cluster gestito integrato nell'ambiente di Azure. I cluster sono configurati per l'archiviazione dei dati direttamente nell'archiviazione BLOB di Azure, che offre bassa latenza e maggiore flessibilità nelle opzioni relative a prestazioni e costi. Ciò permette ai clienti di creare siti Web interattivi da usare con grandi set di dati, per creare servizi che archiviano dati di sensori e telemetria da milioni di endpoint e per analizzare questi dati tramite processi Hadoop. Per altre informazioni su HBase e sugli scenari in cui può essere usato, vedere [Panoramica su HBase di HDInsight](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-overview/).

> [WACOM.NOTE] HBase (versione 0.98.0) è disponibile solo per l'uso con cluster HDInsight 3.1 in HDInsight (basato su Apache Hadoop e YARN 2.4.0). Per informazioni sulla versione, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure** Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di prova gratuita][azure-free-trial].
- **Un account di archiviazione di Azure** Per istruzioni, vedere [Come creare un account di archiviazione][azure-create-storageaccount].
- **Una workstation** in cui sia stato installato Visual Studio 2013. Per istruzioni, vedere [Installazione di Visual Studio](http://msdn.microsoft.com/it-it/library/e2h7fzkw.aspx).
- Scaricare la [libreria client REST di Microsoft HBase per .NET](https://github.com/hdinsight/hbase-sdk-for-net). 


##<a name="create-hbase-cluster"></a>Eseguire il provisioning di un cluster HBase nel portale di Azure

Questa sezione descrive come eseguire il provisioning di un cluster HBase tramite il portale di Azure.


[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Per eseguire il provisioning di un cluster HDInsight nel portale di Azure** 


1. Accedere al [portale di gestione di Azure][azure-management-portal]. 

2. Fare clic su **HDInsight** a sinistra per visualizzare lo stato dei cluster disponibili nell'account e quindi sull'icona **+NUOVO** in basso a sinistra. 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. Fare clic sull'icona HDInsight nella seconda colonna da sinistra e quindi sull'opzione HBase nella colonna successiva. Specificare i valori per NOME DEL CLUSTER e DIMENSIONE DEL CLUSTER, il nome dell'account di archiviazione e una password per il nuovo cluster HBase.
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. Fare clic sull'icona del segno di spunta in basso a sinistra per creare il cluster HBase.


##<a name="create-sample-table"></a>Creare una tabella di esempio HBase dalla shell HBase
Questa sezione descrive come abilitare e usare Remote Desktop Protocol (RDP) per accedere alla shell HBase e quindi usarla per creare una tabella di esempio HBase, aggiungere righe e quindi elencare le righe nella tabella.

Presuppone che sia già stata completata la procedura descritta nella prima sezione e che quindi sia stato creato un cluster HBase.

**Abilitare la connessione RDP per il cluster HBase**

1. Per abilitare una connessione Desktop remoto per il cluster HDInsight, selezionare il cluster HBase appena creato e fare clic sulla scheda **CONFIGURAZIONE**. Fare clic sul pulsante **ABILITA MODALITÀ REMOTA** nella parte inferiore della pagina per abilitare la connessione RDP al cluster.
2. Fornire le credenziali e la data di scadenza nella procedura guidata **CONFIGURA DESKTOP REMOTO** e fare clic sul cerchio selezionato in basso a destra. L'operazione potrebbe richiedere qualche minuto.
3. Per connettersi al cluster HDInsight, fare clic sul pulsante **CONNETTI** nella parte inferiore della scheda **CONFIGURAZIONE**.

 
**Aprire la shell di HBase**

1. Nella sessione RDP fare clic sul collegamento al **prompt dei comandi Hadoop** sul desktop.

2. Passare alla directory home di HBase:
		
		cd %HBASE_HOME%\bin

3. Aprire la shell HBase:

		hbase shell


**Creare una tabella di esempio, aggiungere dati e recuperare i dati**

1. Creare una tabella di esempio:

		create 'sampletable', 'cf1'

2. Aggiungere una riga alla tabella di esempio:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Elencare le righe nella tabella di esempio:
	
		scan 'sampletable'

##<a name="hive-query"></a>Usare Hive per interrogare una tabella HBase

A questo punto dopo aver eseguito il provisioning di un cluster HBase e aver creato una tabella, è possibile interrogarla con Hive. In questa sezione una tabella Hive mappata alla tabella HBase viene creata e usata per interrogare i dati nella tabella HBase.

**Per aprire il dashboard del cluster**

1. Accedere al [portale di gestione di Azure][azure-management-portal]. 
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster creati, incluso il cluster creato nella sezione precedente.
3. Fare clic sul nome del cluster in cui eseguire il processo Hive.
4. Fare clic su **GESTIONE DEL CLUSTER** nella parte inferiore della pagina per aprire il dashboard del cluster. Verrà aperta una pagina Web in una scheda diversa del browser.   
5. Immettere l'account utente e la password per Hadoop.  Il nome utente predefinito è **admin**. La password corrisponde a quella immessa durante il processo di provisioning.  L'aspetto del dashboard è simile al seguente:

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**Per eseguire una query Hive**

1. Per creare una tabella Hive con un mapping alla tabella HBase, immettere lo script HiveQL seguente nella finestra della console Hive e fare clic su **INVIA**. Prima di eseguire questa istruzione, assicurarsi di aver creato in HBase la tabella di esempio usata qui come riferimento con la shell HBase.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. Per eseguire una query Hive sui dati in HBase, immettere lo script HiveQL seguente nella finestra della console Hive e fare clic su **INVIA**.

     	SELECT count(*) FROM hbasesampletable;
 
4. Per recuperare i risultati della query Hive, fare clic sul collegamento **Visualizza dettagli** nella finestra **Sessione processo** al termine dell'esecuzione del processo.


Nota: il collegamento della shell HBase consente di passare dalla scheda alla **shell HBase**.



**Per esplorare il file di output**

1. Nel dashboard del cluster fare clic su **File** nella parte superiore della pagina.
2. Fare clic su **Templeton-Job-Status**.
3. Fare clic sul numero di GUID la cui modifica più recente è stata eseguita poco dopo l'ora di avvio del processo annotata in precedenza. Annotare il valore relativo al GUID.  Sarà necessario nella sezione successiva.
4. Il file **stdout** contiene i dati necessari nella prossima sezione. Se necessario, fare clic su **stdout** per scaricare una copia del file di dati.

	
##<a name="hbase-powershell"></a>Usare le API C# della libreria client REST di HBase per .NET per creare una tabella HBase e recuperare dati dalla tabella.

È necessario scaricare il progetto di libreria client REST di Microsoft HBase per .NET da GitHub e compilarlo per usare .NET SDK per HBase. Nella procedura seguente sono incluse le istruzioni per l'attività.

1. Scaricare la [libreria client REST di Microsoft HBase per .NET](https://github.com/hdinsight/hbase-sdk-for-net) se questo prerequisito non è ancora soddisfatto.

2. Aprire Marlin.sln in Visual Studio (**File** -> **Apri** -> **Progetto/Soluzione**) dal percorso in cui è stato scaricato. Selezionare **Visualizza** -> **Esplora soluzioni** per visualizzare la soluzione 'Marlin' e il relativo progetto Microsoft.HBase.Client. Compilare il progetto **Marlin** facendo clic con il pulsante destro del mouse su di esso in **Esplora soluzioni** e scegliendo **Compila soluzione**. 

4. Creare una nuova applicazione console in Visual C#. Recuperare i file Microsoft.HBase.Client.dll e protobuf.dll risultanti (dalla directory ...\bin\debug\Microsoft.HBase.Client) e aggiungerli al progetto C#: Fare clic con il pulsante destro del mouse su **Riferimenti**, scegliere **Aggiungi riferimento**, individuare i due assembly e caricarli. [protobuf-net](http://code.google.com/p/protobuf-net/) è un'implementazione .NET del serializzatore di buffer di protocollo usato da Google per la comunicazione dati.

5. Aggiungere le istruzioni using seguenti all'inizio del file:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Creare una nuova istanza di un client HBase usando le credenziali del cluster e recuperare la versione del cluster:

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. Per creare una nuova tabella HBase, usare il codice seguente:

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. Inserire dati in una tabella usando il codice seguente:

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. Per recuperare una cella con la relativa chiave, usare il codice seguente: 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. Analizzare le righe in una tabella usando il codice seguente:

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>Riepilogo
In questa esercitazione si è appreso come eseguire il provisioning di un cluster HBase, creare tabelle e visualizzare i dati delle tabelle dalla shell HBase. Si è inoltre appreso come usare Hive per interrogare i dati nelle tabelle HBase e come usare le API C# di HBase per creare una tabella HBase e recuperare i dati dalla tabella.

##<a name="next"></a> Passaggi successivi

[Panoramica su HBase di HDInsight][hdinsight-hbase-overview]:
HBase è un database NoSQL open source Apache basato su Hadoop che fornisce accesso rapido e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati.

[Eseguire il provisioning di cluster HBase in Rete virtuale di Azure][hdinsight-hbase-provision-vnet]:
Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, permettendo così alle applicazioni di comunicare direttamente con HBase.

[Analizzare il sentiment Twitter con HBase in HDInsight][hbase-twitter-sentiment]:
istruzioni per l'esecuzione dell'[analisi del sentiment](http://en.wikipedia.org/wiki/Sentiment_analysis) dei Big Data in tempo reale usando HBase in un cluster Hadoop in HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/it-it/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/it-it/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/it-it/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/it-it/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35.1-->
