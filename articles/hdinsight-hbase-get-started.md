<properties 
	pageTitle="Configurare tabelle HBase su cui eseguire query usando Hive in HDInsight | Azure" 
	description="Introduzione all'uso di HBase con Hadoop in HDInsight. Informazioni sulla creazione di tabelle HBase e sull'esecuzione di query sulle tabelle mediante Hive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/7/2015" 
	ms.author="bradsev"/>



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

HBase è un database NoSQL a bassa latenza che permette di eseguire l'elaborazione transazionale online dei Big Data. HBase è offerto come cluster gestito integrato nell'ambiente di Azure. I cluster sono configurati per l'archiviazione dei dati direttamente nell'archiviazione BLOB di Azure, che offre bassa latenza e maggiore flessibilità nelle opzioni relative a prestazioni e costi. Ciò permette ai clienti di creare siti Web interattivi da usare con grandi set di dati, per creare servizi che archiviano dati di sensori e telemetria da milioni di endpoint e per analizzare questi dati tramite processi Hadoop. Per altre informazioni su HBase e sugli scenari in cui può essere usato, vedere [Panoramica su HBase di HDInsight][hdinsight-hbase-overview].

> [AZURE.NOTE] HBase (versione 0.98.0) è disponibile solo per l'uso con cluster HDInsight 3.1 in HDInsight (basato su Apache Hadoop e YARN 2.4.0). Per informazioni sulla versione, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure** Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].
- **Un account di archiviazione di Azure** Per le istruzioni, vedere [Come creare un account di archiviazione][azure-create-storageaccount].
- **Una workstation** in cui sia stato installato Visual Studio 2013. Per le istruzioni, vedere [Installazione di Visual Studio](http://msdn.microsoft.com/it-it/library/e2h7fzkw.aspx).

##<a name="create-hbase-cluster"></a>Eseguire il provisioning di un cluster HBase nel portale di Azure

Questa sezione descrive come eseguire il provisioning di un cluster HBase tramite il portale di gestione di Azure.


[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Per eseguire il provisioning di un cluster HDInsight nel portale di gestione di Azure** 


1. Accedere al [portale di gestione di Azure][azure-management-portal]. 
2. Nell'angolo inferiore sinistro fare clic su **NUOVO**, quindi su **SERVIZI DATI**, **HDINSIGHT**, **HBASE**. 
3. Immettere **NOME CLUSTER**, **DIMENSIONE DEL CLUSTER **, CONFERMA PASSWORD e **ACCOUNT DI ARCHIVIAZIONE**.
 
	![Choosing and HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

4. Fare clic sull'icona del segno di spunta in basso a sinistra per creare il cluster HBase.


##<a name="create-sample-table"></a>Creare una tabella di esempio HBase dalla shell HBase
Questa sezione descrive come abilitare e usare Remote Desktop Protocol (RDP) per accedere alla shell HBase e quindi usarla per creare una tabella di esempio HBase, aggiungere righe e quindi elencare le righe nella tabella.

Presuppone che sia già stata completata la procedura descritta nella prima sezione e che quindi sia stato creato un cluster HBase.

**Per abilitare la connessione RDP per il cluster HBase**

1. Nel portale di gestione fare clic su **HDINSIGHT** a sinistra per visualizzare l'elenco dei cluster esistenti.
2. Fare clic sul cluster HBase in cui si vuole aprire la shell di HBase.
3. Fare clic su **CONFIGURAZIONE** nella parte superiore.
4. Fare clic su **ABILITA MODALITÀ REMOTA** nella parte inferiore.
5. Immettere il nome utente e la password RDP.  Il nome utente deve essere diverso dal nome utente del cluster usato per il provisioning del cluster. Per la **DATA DI SCADENZA** è possibile impostare una data non oltre sette giorni a partire dalla data odierna.
6. Fare clic sul segno di spunta nella parte inferiore destra per abilitare Desktop remoto.
7. Dopo avere abilitato RPD, fare clic su **CONNETTI** nella parte inferiore della scheda **CONFIGURAZIONE**, quindi seguire le istruzioni.

 
**Per aprire la shell di HBase**

1. Nella sessione RDP fare clic sul collegamento alla **riga dei comandi Hadoop** sul desktop.

2. Passare alla directory home di HBase:
		
		cd %HBASE_HOME%\bin

3. Aprire la shell HBase:

		hbase shell


**Per creare una tabella di esempio, aggiungere dati e recuperare i dati**

1. Creare una tabella di esempio:

		create 'sampletable', 'cf1'

2. Aggiungere una riga alla tabella di esempio:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Elencare le righe nella tabella di esempio:
	
		scan 'sampletable'

**Controllare lo stato del cluster nell'interfaccia utente Web di HBase**
	
HBase include anche un'interfaccia utente Web per il monitoraggio del cluster, in cui è possibile visualizzare, ad esempio, le statistiche relative alle richieste o le informazioni sulle aree. L'interfaccia utente Web è disponibile nel cluster HBase all'indirizzo zookeepernode.


	http://zookeepernode:60010/master-status
	
In un cluster a disponibilità elevata verrà invece visualizzato un collegamento al nodo master HBase attivo corrente che ospita l'interfaccia utente Web.

**Caricamento bulk di una tabella di esempio**

1. Creare il file samplefile1.txt contenente i dati seguenti, quindi caricarlo nell'archivio BLOB di Azure nel percorso /tmp/samplefile1.txt:

		row1	c1	c2
		row2	c1	c2
		row3	c1	c2
		row4	c1	c2
		row5	c1	c2
		row6	c1	c2
		row7	c1	c2
		row8	c1	c2
		row9	c1	c2
		row10    c1	c2

2. Passare alla directory home di HBase:
		
		cd %HBASE_HOME%\bin

3. Eseguire ImportTsv:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,a:b,a:c" -Dimporttsv.bulk.output=/tmpOutput sampletable2 /tmp/samplefile1.txt

4. Caricare l'output dal comando precedente in HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput sampletable2



##<a name="hive-query"></a>Usare Hive per interrogare una tabella HBase

A questo punto dopo aver eseguito il provisioning di un cluster HBase e aver creato una tabella, è possibile interrogarla con Hive. In questa sezione una tabella Hive mappata alla tabella HBase viene creata e usata per interrogare i dati nella tabella HBase.

**Per aprire il dashboard del cluster**

1. Accedere al [portale di gestione di Azure][azure-management-portal]. 
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster creati, incluso il cluster creato nella sezione precedente.
3. Fare clic sul nome del cluster in cui eseguire il processo Hive.
4. Fare clic su **CONSOLE QUERY** nella parte inferiore della pagina per aprire il dashboard del cluster. Verrà aperta una pagina Web in una scheda diversa del browser.   
5. Immettere l'account utente e la password per Hadoop.  Il nome utente predefinito è **admin**. La password corrisponde a quella immessa durante il processo di provisioning. Verrà visualizzata una nuova scheda del browser. 
6. Fare clic su **Editor Hive** nella parte superiore della pagina. L'editor Hive ha un aspetto simile al seguente:

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]





























**Per eseguire query Hive**

1. Immettere lo script HiveQL seguente nell'editor Hive e fare clic su **INVIA** per creare una tabella Hive mappata alla tabella HBase. Prima di eseguire questa istruzione, assicurarsi di aver creato in HBase la tabella di esempio usata qui come riferimento con la shell HBase.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

	Attendere fino a quando lo **Stato** non passa a **Completato**.
 
2. Immettere lo script HiveQL seguente nell'editor Hive, quindi fare clic sul pulsante **INVIA**. La query Hive interroga i dati nella tabella HBase:

     	SELECT count(*) FROM hbasesampletable;
 
4. Per recuperare i risultati della query Hive, fare clic sul collegamento **Visualizza dettagli** nella finestra **Sessione processo** al termine dell'esecuzione del processo. L'output del processo sarà 1 perché nella tabella HBase è presente un solo record.




**Per esplorare il file di output**

1. In Console query fare clic su **Browser file** nella parte superiore.
2. Specificare l'account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HBase.
3. Fare clic sul nome del cluster HBase. Il contenitore dell'account di archiviazione di Azure predefinito usa il nome del cluster.
4. Fare clic su **user**.
5. Fare clic su **admin**. Si tratta del nome utente Hadoop.
6. Fare clic sul nome del processo con la **Data ultima modifica** corrispondente a quella in cui è stata eseguita la query Hive SELECT.
4. Fare clic su **stdout**. Salvare il file e aprirlo con il Blocco note. L'output sarà 1.

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]
	
##<a name="hbase-powershell"></a>Usare le API C# della libreria client REST di HBase per .NET per creare una tabella HBase e recuperare dati dalla tabella.

È necessario scaricare il progetto di libreria client REST di Microsoft HBase per .NET da GitHub e compilarlo per usare .NET SDK per HBase. Nella procedura seguente sono incluse le istruzioni per l'attività.

1. Creare una nuova applicazione console C# Desktop di Windows in Visual Studio.
2. Aprire la console di Gestione pacchetti NuGet facendo clic sul menu **STRUMENTI**, su **Gestione pacchetti NuGet**, **Console di Gestione pacchetti**.
3. Eseguire il comando NuGet seguente nella console:

	Install-Package Microsoft.HBase.Client

5. Aggiungere le istruzioni using seguenti all'inizio del file:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Sostituire la funzione Main con il codice seguente:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35. 
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Impostare le prime tre variabili nella funzione Main.
8. Premere **F5** per eseguire l'applicazione.



##<a name="next"></a> Passaggi successivi
In questa esercitazione si è appreso come eseguire il provisioning di un cluster HBase, creare tabelle e visualizzare i dati delle tabelle dalla shell HBase. Si è inoltre appreso come usare Hive per interrogare i dati nelle tabelle HBase e come usare le API C# di HBase per creare una tabella HBase e recuperare i dati dalla tabella. 

Per altre informazioni, vedere:

- [Panoramica su HBase di HDInsight][hdinsight-hbase-overview]:
HBase è un database NoSQL open source Apache basato su Hadoop che fornisce accesso rapido e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati.
- [Eseguire il provisioning di cluster HBase in Rete virtuale di Azure][hdinsight-hbase-provision-vnet]:
Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, permettendo così alle applicazioni di comunicare direttamente con HBase.
- [Analisi dei sentimenti Twitter con HBase in HDInsight][hbase-twitter-sentiment]:
istruzioni per l'esecuzione dell'[analisi dei sentimenti](http://en.wikipedia.org/wiki/Sentiment_analysis) dei Big Data in tempo reale usando HBase in un cluster Hadoop in HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/
[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
[azure-purchase-options]: http://azure.microsoft.com/it-it/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/it-it/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/it-it/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/it-it/documentation/articles/storage-create-storage-account/ 

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png









<!--HONumber=42-->
