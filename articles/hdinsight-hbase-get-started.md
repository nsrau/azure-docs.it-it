<properties urlDisplayName="Get Started" pageTitle="Introduzione all'utilizzo di HBase con Hadoop in HDInsight | Azure" metaKeywords="" description="Introduzione all'utilizzo di HBase con Hadoop in HDInsight. Informazioni su come creare tabelle di HBase su cui eseguire query con Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduzione a HBase con Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev" />

# Introduzione a HBase con Hadoop in HDInsight

HBase è un database NoSQL a bassa latenza che permette di eseguire l'elaborazione transazionale online dei Big Data. HBase è offerto come cluster gestito integrato nell'ambiente di Azure. I cluster sono configurati per l'archiviazione dei dati direttamente nell'archiviazione BLOB di Azure, che offre bassa latenza e maggiore flessibilità nelle opzioni relative a prestazioni e costi. Ciò permette ai clienti di creare siti Web interattivi da usare con grandi set di dati, per creare servizi che archiviano dati di sensori e telemetria da milioni di endpoint e per analizzare questi dati tramite processi Hadoop.

In questa esercitazione viene descritto come creare e interrogare tabelle HBase con HDInsight. Le procedure descritte sono le seguenti:

-   Eseguire il provisioning di un cluster HBase tramite il portale di Azure.
-   Abilitare e usare RDP per accedere alla shell HBase e usarla per creare una tabella di esempio HBase, aggiungere righe e quindi elencare le righe nella tabella.
-   Creare una tabella Hive che viene mappata a una tabella HBase esistente e usare HiveQL per interrogare i dati nella tabella HBase.
-   Usare .NET SDK per creare una nuova tabella HBase, elencare le tabelle HBase disponibili nell'account e aggiungere o recuperare le righe dalle tabelle.

> [WACOM.NOTE] HBase (versione 0.98.0) è disponibile solo per l'uso con cluster HDInsight 3.1 in HDInsight (basato su Apache Hadoop e YARN 2.4.0). Per informazioni sulla versione, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][Novità delle versioni cluster di Hadoop incluse in HDInsight]

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][Opzioni di acquisto], [Offerte per i membri][Offerte per i membri] oppure [Versione di valutazione gratuita][Versione di valutazione gratuita].
-   Un account di archiviazione di Azure. Per istruzioni, vedere [Come creare un account di archiviazione][Come creare un account di archiviazione].
-   Una copia di Visual Studio.

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'esercitazione:

-   [Eseguire il provisioning di un cluster HBase nel portale di Azure][Eseguire il provisioning di un cluster HBase nel portale di Azure]
-   [Creare una tabella di esempio HBase dalla shell HBase][Creare una tabella di esempio HBase dalla shell HBase]
-   [Usare Hive per interrogare una tabella HBase][Usare Hive per interrogare una tabella HBase]
-   [Usare le API C# di HBase per creare una tabella HBase e recuperare dati dalla tabella][Usare le API C# di HBase per creare una tabella HBase e recuperare dati dalla tabella]
-   [Riepilogo][Riepilogo]
-   [Passaggi successivi][Passaggi successivi]

## <a name="create-hbase-cluster"></a>Eseguire il provisioning di un cluster HBase nel portale di Azure

Questa sezione descrive come eseguire il provisioning di un cluster HBase tramite il portale di Azure.

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Per eseguire il provisioning di un cluster HDInsight nel portale di Azure**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic su **HDInsight** a sinistra per elencare lo stato dei cluster disponibili nell'account e quindi sull'icona **+NUOVO** in basso a sinistra.

    ![][0]

3.  Fare clic sull'icona HDInsight nella seconda colonna da sinistra e quindi sull'opzione HBase nella colonna successiva. Specificare i valori per NOME DEL CLUSTER e DIMENSIONE DEL CLUSTER, il nome dell'account di archiviazione e una password per il nuovo cluster HBase.

    ![][1]

4.  Fare clic sull'icona del segno di spunta in basso a sinistra per creare il cluster HBase.

## <a name="create-sample-table"></a>Creare una tabella di esempio HBase dalla shell HBase

Questa sezione descrive come abilitare e usare Remote Desktop Protocol (RDP) per accedere alla shell HBase e quindi usarla per creare una tabella di esempio HBase, aggiungere righe e quindi elencare le righe nella tabella.

Presuppone che sia già stata completata la procedura descritta nella prima sezione e che quindi sia stato creato un cluster HBase.

**Abilitare la connessione RDP per il cluster HBase**

1.  Per abilitare una connessione Desktop remoto per il cluster HDInsight, selezionare il cluster HBase appena creato e fare clic sulla scheda **CONFIGURAZIONE**. Fare clic sul pulsante **ABILITA MODALITÀ REMOTA** nella parte inferiore della pagina per abilitare la connessione RDP per il cluster.
2.  Fornire le credenziali e la data di scadenza nella procedura guidata **CONFIGURA DESKTOP REMOTO** e fare clic sul cerchio selezionato in basso a destra. L'operazione potrebbe richiedere qualche minuto.
3.  Per connettersi al cluster HDInsight, fare clic sul pulsante **CONNETTI** nella parte inferiore della scheda **CONFIGURAZIONE**.

**Aprire la shell HBase**

1.  Nella sessione RDP fare clic sul collegamento al **prompt dei comandi Hadoop** sul desktop.

2.  Passare alla directory home di HBase:

        cd %HBASE_HOME%\bin

3.  Aprire la shell HBase:

        hbase shell

**Creare una tabella di esempio, aggiungere dati e recuperare i dati**

1.  Creare una tabella di esempio:

        create 'sampletable', 'cf1'

2.  Aggiungere una riga alla tabella di esempio:

        put 'sampletable', 'row1', 'cf1:col1', 'value1'

3.  Elencare le righe nella tabella di esempio:

        scan 'sampletable'

## <a name="hive-query"></a>Usare Hive per interrogare una tabella HBase

A questo punto dopo aver eseguito il provisioning di un cluster HBase e aver creato una tabella, è possibile interrogarla con Hive. In questa sezione una tabella Hive mappata alla tabella HBase viene creata e usata per interrogare i dati nella tabella HBase.

**Per aprire il dashboard cluster**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster creati, incluso il cluster creato nella sezione precedente.
3.  Fare clic sul nome del cluster in cui eseguire il processo Hive.
4.  Fare clic su **GESTIONE DEL CLUSTER** nella parte inferiore della pagina per aprire il dashboard cluster. Verrà aperta una pagina Web in una scheda diversa del browser.
5.  Immettere l'account utente e la password per Hadoop. Il nome utente predefinito è **admin**. La password corrisponde a quella immessa durante il processo di provisioning. L'aspetto del dashboard è simile al seguente:

    ![][2]

**Per eseguire una query Hive**

1.  Per creare una tabella Hive con un mapping alla tabella HBase, immettere lo script HiveQL seguente nella finestra della console Hive e fare clic su **INVIA**. Prima di eseguire questa istruzione, assicurarsi di aver creato in HBase la tabella di esempio usata qui come riferimento con la shell HBase.

        CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
        TBLPROPERTIES ('hbase.table.name' = 'sampletable');

2.  Per eseguire una query Hive sui dati in HBase, immettere lo script HiveQL seguente nella finestra della console Hive e fare clic su **INVIA**.

        SELECT count(*) FROM hbasesampletable;

3.  Per recuperare i risultati della query Hive, fare clic sul collegamento **Visualizza dettagli** nella finestra **Sessione processo** al termine del processo.

Nota: il collegamento della shell HBase consente di passare dalla scheda alla **shell HBase**.

**Per passare al file di output**

1.  Nel dashboard del cluster fare clic su **File** nella parte superiore della pagina.
2.  Fare clic su **Templeton-Job-Status**.
3.  Fare clic sul numero di GUID la cui modifica più recente è stata eseguita poco dopo l'ora di avvio del processo annotata in precedenza. Annotare il valore relativo al GUID. Sarà necessario nella sezione successiva.
4.  Il file **stdout** contiene i dati necessari nella prossima sezione. Se necessario, fare clic su **stdout** per scaricare una copia del file di dati.

## <a name="hbase-powershell"></a>Usare le API C# di HBase per creare una tabella HBase e recuperare dati dalla tabella

Marlin è un thin layer installato sull'API REST, che gestisce l'interazione con HBase tramite ProtoBuf in C#. È necessario scaricare il progetto Marlin da github e compilarlo per usare .NET SDK per HBase.

1.  Attenersi alla procedura di compilazione descritta nella sezione relativa al download nella [pagina del progetto Marlin][pagina del progetto Marlin]. Decomprimerla in una directory locale.

2.  Aprire il progetto in Visual Studio. Per aprire la procedura guidata di gestione pacchetti NuGet, passare a **STRUMENTI** menu -\> **Gestione pacchetti libreria** e selezionare **Gestisci pacchetti NuGet per la soluzione.**

    ![][3]

3.  Cercare protobuf-net nella casella della ricerca online in alto a destra e installare la versione 2.0.0.68. Per compilare il progetto Marlin, fare clic con il pulsante destro del mouse sul progetto **Marlin** in **Esplora soluzioni** e selezionare **Compila**.

4.  Recuperare il file marlin.dll risultante compilato e aggiungerlo al progetto C#.

5.  Creare una nuova istanza di Marlin usando le credenziali del cluster e recuperare la versione del cluster:

        var credentials = ClusterCredentials.Create("https://yourclustername.azurehdinsight.net/", "user", "password");
            var marlin = new Marlin(credentials);
        // retrieve the version as a test
        var version = marlin.GetVersion();
        Console.WriteLine(version);

6.  Per elencare le tabelle nel cluster, è possibile usare il codice seguente:

        var tables = marlin.ListTables();
        foreach(var tableName in tables.name) 
                Console.WriteLine(tableName);

7.  Per creare una nuova tabella HBase, usare il codice seguente:

        var schema = new TableSchema();
        schema.name = "sampletable";
        schema.columns.Add(new ColumnSchema() { name = "cf1" });
        schema.columns.Add(new ColumnSchema() { name = "cf2" });
        marlin.CreateTable(schema);

8.  Per recuperare una riga in base alla chiave, specificare il nome della tabella e una chiave di riga per recuperare un valore di riga.

        var cells = marlin.GetCells("sampletable", "row1");
        var row = cells.rows[0];
            foreach(var val in row.values) 
            {
               Console.WriteLine(Encoding.UTF8.GetString(val.data));
            }

9.  Per archiviare una nuova riga di dati, è possibile usare il codice seguente:

        CellSet set = new CellSet();
        CellSet.Row row = new CellSet.Row() { key = BitConverter.GetBytes(1337) };
            var value = new Cell()
                    {
                        column = Encoding.UTF8.GetBytes("cf1:d"),
                        data = Encoding.UTF8.GetBytes("Hello World!")
                    };
            row.values.Add(value);
            set.rows.Add(row);
        marlin.StoreCells("sampletable", set);

## <a name="summary"></a>Riepilogo

In questa esercitazione si è appreso come eseguire il provisioning di un cluster HBase, creare tabelle e visualizzare i dati delle tabelle dalla shell HBase. Si è inoltre appreso come usare Hive per interrogare i dati nelle tabelle HBase e come usare le API C# di HBase per creare una tabella HBase e recuperare i dati dalla tabella.

## <a name="next"></a> Passaggi successivi

[Panoramica di HDInsight HBase][Panoramica di HDInsight HBase]:
HBase è un database NoSQL open source Apache basato su Hadoop che fornisce un accesso casuale e coerenza assoluta a grandi quantità di dati non strutturati e semistrutturati.

[Provisioning di cluster HBase in Rete virtuale di Azure][Provisioning di cluster HBase in Rete virtuale di Azure]:
con l'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti alle stessa rete virtuale delle applicazioni in modo che quest'ultime possano comunicare direttamente con HBase.

  [Novità delle versioni cluster di Hadoop incluse in HDInsight]: ../hdinsight-component-versioning/
  [Opzioni di acquisto]: http://azure.microsoft.com/it-it/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/it-it/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [Come creare un account di archiviazione]: http://azure.microsoft.com/it-it/documentation/articles/storage-create-storage-account/
  [Eseguire il provisioning di un cluster HBase nel portale di Azure]: #create-hbase-cluster
  [Creare una tabella di esempio HBase dalla shell HBase]: #create-sample-table
  [Usare Hive per interrogare una tabella HBase]: #hive-query
  [Usare le API C# di HBase per creare una tabella HBase e recuperare dati dalla tabella]: #hbase-powershell
  [Riepilogo]: #summary
  [Passaggi successivi]: #next
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: http://i.imgur.com/PmGynKZ.jpg
  [1]: http://i.imgur.com/ecxbB9K.jpg
  [2]: http://i.imgur.com/tMwXlj9.jpg
  [pagina del progetto Marlin]: https://github.com/thomasjungblut/marlin
  [3]: http://i.imgur.com/hUNoJDJ.jpg
  [Panoramica di HDInsight HBase]: ../hdinsight-hbase-overview/
  [Provisioning di cluster HBase in Rete virtuale di Azure]: ../hdinsight-hbase-provision-vnet
