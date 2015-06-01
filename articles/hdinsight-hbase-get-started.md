<properties
	pageTitle="Configurare tabelle HBase su cui eseguire query usando Hive in HDInsight | Azure"
	description="Introduzione all'uso di HBase con Hadoop in HDInsight. Informazioni sulla creazione di tabelle HBase e sull'esecuzione di query sulle tabelle mediante Hive."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2015"
	ms.author="jgao"/>



# Introduzione ad Apache HBase in HDInsight

Informazioni su come creare tabelle HBase su cui eseguire query usando Hive in HDInsight.

HBase è un database NoSQL a bassa latenza che permette di eseguire l'elaborazione transazionale online dei Big Data. HBase è offerto come cluster gestito integrato nell'ambiente di Azure. I cluster sono configurati per l'archiviazione dei dati direttamente nell'archivio BLOB di Azure, che offre bassa latenza e maggiore flessibilità nelle opzioni relative a prestazioni e costi. Ciò permette ai clienti di creare siti Web interattivi da usare con grandi set di dati, per creare servizi che archiviano dati di sensori e telemetria da milioni di endpoint e per analizzare questi dati tramite processi Hadoop. Per altre informazioni su HBase e sugli scenari in cui può essere usato, vedere [Panoramica su HBase di HDInsight][hdinsight-hbase-overview].

> [AZURE.NOTE]HBase (versione 0.98.0) è disponibile solo per l'uso con cluster HDInsight 3.1 in HDInsight (basato su Apache Hadoop e YARN 2.4.0). Per informazioni sulla versione, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions]

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**: per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].
- **Un account di archiviazione di Azure**: per le istruzioni, vedere [Come creare un account di archiviazione][azure-create-storageaccount].
- **Una workstation** con installato Visual Studio 2013: per le istruzioni, vedere [Installazione di Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Effettuare il provisioning di un cluster HBase

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Per effettuare il provisioning di un cluster HBase usando il portale di Azure**


1. Accedere al [portale di Azure][azure-management-portal].
2. Nell'angolo inferiore sinistro fare clic su **NUOVO**, quindi su **SERVIZI DATI** > **HDINSIGHT** > **HBASE**.

	È possibile usare anche l'opzione CREAZIONE PERSONALIZZATA.
3. Immettere **NOME CLUSTER**, **DIMENSIONE DEL CLUSTER**, CONFERMA PASSWORD e **ACCOUNT DI ARCHIVIAZIONE**.

	![Scelta di un tipo di cluster HBase e immissione delle credenziali di accesso al cluster.][img-hdinsight-hbase-cluster-quick-create]

	Il NOME UTENTE HTTP predefinito è admin. Per personalizzare il nome, usare l'opzione CREAZIONE PERSONALIZZATA.

	> [AZURE.WARNING]Per garantire disponibilità elevata dei servizi di HBase, è necessario effettuare il provisioning di un cluster contenente almeno **tre** nodi. Questo assicura che, se un nodo viene disattivato, le aree dati HBase siano disponibili in altri nodi.

4. Fare clic sull'icona del segno di spunta in basso a destra per creare il cluster HBase.

>[AZURE.NOTE]Dopo l'eliminazione di un cluster HBase, è possibile creare un altro cluster HBase usando lo stesso BLOB predefinito. Il nuovo cluster selezionerà le tabelle HBase create nel cluster originale.

## Creare una tabella di esempio HBase dalla shell HBase
Questa sezione descrive come usare la shell HBase per creare tabelle HBase e aggiungere ed elencare righe. Per accedere alla shell HBase, prima è necessario abilitare Remote Desktop Protocol (RDP) e quindi stabilire una connessione RDP al cluster HBase. Per istruzioni, vedere [Gestire i cluster Hadoop in HDInsight con il portale di Azure][hdinsight-manage-portal].


**Per usare la shell HBase**

1. Nella sessione RDP fare clic sul collegamento **Hadoop Command Line** sul desktop.
2. Passare alla directory home di HBase:

		cd %HBASE_HOME%\bin
3. Aprire la shell HBase:

		hbase shell

4. Creare una tabella HBase con una famiglia di colonne e inserire una riga:

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	Per altre informazioni sullo schema di tabella Hbase, vedere l'[introduzione alla progettazione dello schema HBase][hbase-schema]. Per altri comandi HBase, vedere la [guida di riferimento di Apache HBase][hbase-quick-start].
5. Elencare le tabelle HBase:

		list

**Controllare lo stato del cluster nell'interfaccia utente Web di HBase**

HBase include anche un'interfaccia utente Web per il monitoraggio del cluster, in cui è possibile richiedere, ad esempio, statistiche o informazioni sulle aree. L'interfaccia utente Web è disponibile nel cluster HBase all'indirizzo zookeepernode:


	http://zookeepernode:60010/master-status

In un cluster a disponibilità elevata verrà invece visualizzato un collegamento al nodo master HBase attivo corrente che ospita l'interfaccia utente Web.

**Caricamento bulk di una tabella di esempio**

1. Dalla shell HBase creare una tabella HBase con due famiglie di colonne:

		create 'Contacts', 'Personal', 'Office'


3. Creare un elenco di contatti contenente i seguenti dati e caricarlo in una cartella denominata /tmp/contacts.txt nell'archivio BLOB di Azure. Per le istruzioni, vedere [Caricare dati per processi Hadoop in HDInsight][hdinsight-upload-data].

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. In una riga di comando di Hadoop passare alla directory home di HBase:

		cd %HBASE_HOME%\bin

3. Eseguire ImportTsv. ImportTsv è uno strumento che carica i dati in formato TSV in HBase. Ha due distinti utilizzi: il caricamento in HBase dei dati dal formato TSV nel file system distribuito Hadoop (HDFS) e la preparazione dei file da caricare. Per altre informazioni, vedere la [guida di riferimento di Apache HBase][hbase-reference].

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. Caricare l'output dal comando precedente in HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Usare Hive per interrogare una tabella HBase

Dopo avere effettuato il provisioning di un cluster HBase e creato una tabella HBase, è possibile eseguire una query dati nella tabella usando Hive. In questa sezione una tabella Hive mappata alla tabella HBase viene creata e usata per interrogare i dati nella tabella HBase.

**Per aprire il dashboard cluster**

1. Accedere al [portale di Azure][azure-management-portal].
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster, incluso quello creato nella sezione precedente.
3. Fare clic sul nome del cluster in cui eseguire il processo Hive.
4. Fare clic su **CONSOLE QUERY** nella parte inferiore della pagina per aprire il dashboard del cluster. Verrà aperta una pagina Web in una scheda diversa del browser.
5. Immettere l'account utente e la password per Hadoop. Il nome utente predefinito è **admin** e la password corrisponde a quella immessa durante il processo di provisioning. Si apre una nuova scheda del browser.
6. Fare clic su **Editor Hive** nella parte superiore della pagina. L'editor Hive ha un aspetto simile a questo:

	![Dashboard cluster di HDInsight][img-hdinsight-hbase-hive-editor]





























**Per eseguire query Hive**

1. Immettere il seguente script HiveQL nell'editor Hive e fare clic su **INVIA** per creare una tabella Hive mappata alla tabella HBase. Prima di eseguire questa istruzione, assicurarsi di aver creato la tabella di esempio usata precedentemente in questa esercitazione come riferimento con la shell HBase.

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Attendere fino a quando lo **Stato** non passa a **Completato**.

2. Immettere il seguente script HiveQL nell'editor Hive, quindi fare clic su **INVIA**. La query Hive interroga i dati nella tabella HBase:

     	SELECT count(*) FROM hbasecontactstable;

4. Per recuperare i risultati della query Hive, fare clic sul collegamento **Visualizza dettagli** nella finestra **Sessione processo** al termine del processo. Sarà presente solo un file di output del processo perché si è inserito un solo record nella tabella HBase.




**Per passare al file di output**

1. In Console query fare clic su **Browser file**.
2. Specificare l'account di archiviazione di Azure che viene usato come file system predefinito per il cluster HBase.
3. Fare clic sul nome del cluster HBase. Il contenitore dell'account di archiviazione di Azure predefinito usa il nome del cluster.
4. Fare clic su **Utente** e quindi su **Amministratore**. Si tratta del nome utente Hadoop.
6. Fare clic sul nome del processo con la **Data ultima modifica** corrispondente a quella in cui è stata eseguita la query Hive SELECT.
4. Fare clic su **stdout**. Salvare il file e aprirlo con il Blocco note. Sarà presente un file di output.

	![Browser file dell'editor Hive HBase di HDInsight][img-hdinsight-hbase-file-browser]

## Usare la libreria client delle API REST di HBase per .NET C# per creare e recuperare dati da un tabella HBase.

È necessario scaricare la libreria client delle API REST di HBase per .NET da GitHub e compilare il progetto in modo da potere usare .NET SDK per HBase. Nella procedura seguente sono incluse le istruzioni per l'attività.

1. Creare una nuova applicazione console C# Desktop di Windows in Visual Studio.
2. Aprire la console di Gestione pacchetti NuGet facendo clic sul menu **STRUMENTI** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.
3. Eseguire il comando NuGet seguente nella console:

		Install-Package Microsoft.HBase.Client

5. Aggiungere le istruzioni **using** seguenti all'inizio del file:

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Sostituire la funzione **Main** con il codice seguente:

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

7. Impostare le prime tre variabili nella funzione **Main**.
8. Premere **F5** per eseguire l'applicazione.



## Passaggi successivi
In questa esercitazione si è appreso come effettuare il provisioning di un cluster HBase e come creare tabelle e visualizzare i dati delle tabelle dalla shell HBase. Si è inoltre appreso come usare Hive per interrogare i dati nelle tabelle HBase e come usare le API REST C# di HBase per creare una tabella HBase e recuperare i dati dalla tabella.

Per altre informazioni, vedere:

- [Panoramica di HDInsight HBase][hdinsight-hbase-overview]: HBase è un database NoSQL open source Apache basato su Hadoop che fornisce un accesso casuale e coerenza assoluta a grandi quantità di dati non strutturati e semistrutturati.
- [Provisioning di cluster HBase in Rete virtuale di Azure][hdinsight-hbase-provision-vnet]: con l'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti alle stessa rete virtuale delle applicazioni in modo che queste ultime possano comunicare direttamente con HBase.
- [Configurare la replica HBase in HDInsight](hdinsight-hbase-geo-replication.md): informazioni su come configurare la replica HBase in due data center di Azure. 
- [Analisi dei sentimenti Twitter con HBase in HDInsight][hbase-twitter-sentiment]: informazioni su come eseguire [l'analisi dei sentimenti](http://en.wikipedia.org/wiki/Sentiment_analysis) dei Big Data in tempo reale usando HBase in un cluster Hadoop in HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->