<properties
   pageTitle="Creare cluster HDInsight con Archivio Data Lake di Azure tramite il portale | Azure"
   description="Utilizzare il portale di Azure per creare e usare cluster HDInsight con Archivio Data Lake di Azure"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/29/2016"
   ms.author="nitinme"/>

# Creare un cluster HDInsight con Archivio Data Lake tramite il portale di Azure

> [AZURE.SELECTOR]
- [Uso del portale](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Tramite PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Informazioni su come usare il portale di Azure per creare un cluster HDInsight (Hadoop, HBase, Spark o Storm) con accesso ad Archivio Azure Data Lake. Alcune considerazioni importanti per questa versione:

* **Per i cluster Spark (Linux) e Hadoop (Windows e Linux)**, Archivio Data Lake può essere utilizzato solo come account di archiviazione aggiuntivo. L'account di archiviazione predefinito per tali cluster continuerà a essere WASB (BLOB di Archiviazione di Azure).

* **Per i cluster Storm (Windows e Linux)**, Archivio Data Lake può essere usato per scrivere dati da una topologia Storm. È anche possibile usare Archivio Data Lake per archiviare dati di riferimento che possono essere letti da una topologia Storm. Per altre informazioni, vedere [Usare Archivio Data Lake in una topologia Storm](#use-data-lake-store-in-a-storm-topology).

* **Per i cluster HBase (Windows e Linux)**, Data Lake Store può essere usato come risorsa di archiviazione predefinita o aggiuntiva. Per altre informazioni, vedere [Usare Archivio Data Lake con cluster HBase](#use-data-lake-store-with-hbase-clusters).

> [AZURE.NOTE] Alcuni elementi importanti da considerare:
> 
> * L'opzione per creare cluster HDInsight con accesso ad Archivio Data Lake è disponibile solo con HDInsight versioni 3.2 e 3.4 (per i cluster Hadoop, HBase e Storm sia per Windows che per Linux). Per i cluster Spark su Linux, questa opzione è disponibile solo sui cluster HDInsight 3.4.
>
> * Come indicato in precedenza, Archivio Data Lake è disponibile come risorsa di archiviazione predefinita per alcuni tipi di cluster (HBase) e come risorsa di archiviazione aggiuntiva per altri tipi (Hadoop, Spark, Storm). L'uso di Archivio Data Lake come account di archiviazione aggiuntivo non ha impatto sulle prestazioni o sulla possibilità di leggere/scrivere nella risorsa di archiviazione dal cluster. In uno scenario in cui Archivio Data Lake viene usato come risorsa di archiviazione aggiuntiva, i file correlati al cluster (ad esempio log e così via) vengono scritti nella risorsa di archiviazione predefinita (BLOB di Azure), mentre i dati da elaborare possono essere archiviati in un account di Archivio Data Lake.


## Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup).
- **Account di Archivio Data Lake di Azure**. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md). Dopo aver creato l'account, eseguire le attività seguenti per caricare alcuni dati di esempio. Questi dati saranno necessari più avanti nell'esercitazione per eseguire i processi da un cluster HDInsight che accede ai dati nell'Archivio Data Lake.

	* [Creare una cartella in Archivio Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Caricare un file in Archivio Data Lake](data-lake-store-get-started-portal.md#uploaddata). Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## Apprendimento rapido con i video

Guardare i video seguenti per informazioni su come effettuare il provisioning di cluster HDInsight con accesso ad Archivio Data Lake.

* [Creare un cluster HDInsight con accesso ad Archivio Data Lake](https://mix.office.com/watch/l93xri2yhtp2)
* Dopo aver configurato il cluster, [accedere ai dati in Archivio Data Lake usando gli script Hive e Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)

## Creare un cluster Azure HDInsight con accesso ad Archivio Azure Data Lake.

In questa sezione si creerà un cluster HDInsight Hadoop che usa Archivio Data Lake come risorsa di archiviazione aggiuntiva. In questa versione, Archivio Data Lake può essere usato solo come risorsa di archiviazione aggiuntiva per i cluster Hadoop. L'archivio predefinito continuerà a essere WASB (BLOB di Archiviazione di Azure). Si procederà quindi prima di tutto alla creazione dell'account di archiviazione e dei contenitori di archiviazione richiesti per il cluster.

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).

2. Seguire i passaggi descritti in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) per avviare il provisioning di un cluster HDInsight.

3. Nel pannello **Configurazione facoltativa** fare clic su **Origine dati**. Nel pannello **Origine dati** specificare i dettagli per l'account di archiviazione e il contenitore di archiviazione, impostare la **Località** su **Stati Uniti orientali 2** e quindi fare clic su **Identità AAD cluster**.

	![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Aggiungere entità servizio a cluster HDInsight")

4. Nel pannello **Identità AAD cluster** è possibile scegliere di selezionare un'entità servizio esistente o crearne una nuova.

	* **Creare una nuova entità servizio.**

		* Nel pannello **Identità AAD cluster** fare clic su **Crea nuovo**, poi su **Entità servizio** e quindi nel pannello **Creare un'entità servizio** specificare i valori per creare una nuova entità servizio. Nell'ambito questa operazione vengono creati anche un certificato e un'applicazione Azure Active Directory. Fare clic su **Create**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Aggiungere entità servizio a cluster HDInsight")

		* Nel pannello **Identità AAD cluster** fare clic su **Gestisci l'accesso ad Archivio Azure Data Lake**. Il riquadro mostra gli account di Data Lake Store associati alla sottoscrizione. Tuttavia, è possibile impostare le autorizzazioni solo per l'account creato. Selezionare le autorizzazioni di LETTURA/SCRITTURA/ESECUZIONE per l'account che si vuole associare al cluster HDInsight e quindi fare clic su **Salva autorizzazioni**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Aggiungere entità servizio a cluster HDInsight")

		* Nel pannello **Identità AAD del cluster** fare clic su **Scarica certificato** per scaricare il certificato associato all'entità servizio creata. Questa operazione è utile se si vuole usare la stessa entità servizio in futuro durante la creazione di altri cluster HDInsight. Fare clic su **Seleziona**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Aggiungere entità servizio a cluster HDInsight")


	* **Scegliere un'entità servizio esistente**.

		* Nel pannello **Identità AAD cluster** fare clic su **Usa esistente**, su **Entità servizio** e quindi nel pannello **Selezione entità servizio** cercare un'entità servizio esistente. Fare clic sul nome di un'entità servizio e quindi fare clic su **Seleziona**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Aggiungere entità servizio a cluster HDInsight")

		* Nel pannello **Identità AAD cluster** caricare il certificato con estensione pfx associato all'entità servizio selezionata e quindi specificare la password del certificato.

5. Fare clic su **Gestisci l'accesso ad Archivio Azure Data Lake** e quindi fare clic su **Selezionare le autorizzazioni file**.

	![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "Aggiungere entità servizio a cluster HDInsight")

6. Selezionare l'account Data Lake Store da associare al cluster HDInsight nel pannello **Selezionare le autorizzazioni file** nell'elenco a discesa **Account**. Il pannello elenca i file e le cartelle disponibili nell'account Data Lake Store selezionato.
 
	![Fornire l'accesso a Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-1.png "Fornire l'accesso a Data Lake Store")

	Determinare quindi le autorizzazioni da fornire per le cartelle e i file selezionati. Per le cartelle specificare anche se applicare le autorizzazioni solo alla cartella o alla cartella e a tutti gli elementi figlio in essa contenuti. È possibile specificare questa impostazione selezionando il valore appropriato nell'elenco a discesa **Applica a**. Per rimuovere un'autorizzazione, scegliere l'icona **Elimina**

	![Fornire l'accesso a Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-2.png "Fornire l'accesso a Data Lake Store")

	Ripetere questi passaggi anche per le cartelle e i file associati da altri account Data Lake Store. Al termine dell'operazione di assegnazione delle autorizzazioni, fare clic su **Seleziona** nella parte inferiore del pannello.

7. Nel pannello **Assegnare le autorizzazioni selezionate** verificare le autorizzazioni fornite e quindi fare clic su **Esegui** per concedere queste autorizzazioni.

	![Fornire l'accesso a Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-3.png "Fornire l'accesso a Data Lake Store")

	La colonna dello stato visualizza lo stato di avanzamento. Dopo la corretta assegnazione di tutte le autorizzazioni, fare clic su **Operazione completata**.

6. Fare clic su **Seleziona** nei pannelli **Identità AAD del cluster** e **Origine dati** e continuare con la creazione del cluster come descritto in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

7. Al termine del provisioning del cluster, è possibile verificare che l'entità servizio sia effettivamente associata al cluster HDInsight. A tale scopo, nel pannello del cluster fare clic su **Identità AAD del cluster** per visualizzare l'entità servizio associata.

	![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Aggiungere entità servizio a cluster HDInsight")

## Eseguire processi di test sul cluster HDInsight per usare Archivio Data Lake di Azure

Dopo aver configurato un cluster HDInsight, è possibile eseguire processi di test sul cluster per verificare che il cluster HDInsight possa effettivamente accedere ad Archivio Data Lake di Azure. A tale scopo, si eseguiranno ora alcune query Hive destinate ad Archivio Data Lake.

### Per un cluster Linux

1. Aprire il pannello relativo al cluster di cui è appena stato eseguito il provisioning e quindi fare clic su **Dashboard**. Verrà aperto Ambari per il cluster Linux. Quando si accede ad Ambari, verrà richiesto di eseguire l'autenticazione al sito. Immettere il nome dell'account amministratore, il cui valore predefinito è admin, e la password usati durante la creazione del cluster.

	![Avviare il dashboard del cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Avviare il dashboard del cluster")

	È anche possibile passare direttamente ad Ambari accedendo a https://CLUSTERNAME.azurehdinsight.net in un Web browser, dove **CLUSTERNAME** è il nome del cluster HDInsight.

2. Aprire la visualizzazione Hive. Per elencare le viste disponibili, selezionare il set di quadratini nel menu della pagina, accanto al collegamento **Admin** e al pulsante a destra della pagina. Selezionare **Hive view** (Visualizzazione Hive).

	![Selezione delle visualizzazioni di Ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Verrà visualizzata una pagina simile alla seguente:

	![Immagine della pagina della visualizzazione Hive, contenente una sezione di Query Editor](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. Nella sezione **Query Editor** (Editor di query) della pagina incollare l'istruzione HiveQL seguente nel foglio di lavoro:

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Fare clic sul pulsante **Execute** (Esegui) nella parte inferiore della sezione **Query Editor** (Editor di query) per avviare la query. Sotto **Query Editor** (Editor di query) verrà visualizzata la sezione **Query Process Results** (Risultati elaborazione query) con le informazioni sul processo.

6. Una volta completata la query, la sezione **Query Process Results** (Risultati elaborazione query) visualizzerà i risultati dell'operazione. La scheda **Results** conterrà le informazioni seguenti:

7. Per verificare che la tabella sia stata creata correttamente, eseguire la query seguente.

		SHOW TABLES;

	La scheda **Results** (Risultati) dovrebbe visualizzare quanto segue:

		hivesampletable
		vehicles

	**vehicles** costituisce la tabella creata in precedenza, mentre **hivesampletable** è una tabella di esempio disponibile in tutti i cluster HDInsight per impostazione predefinita.

8. È anche possibile eseguire una query per recuperare i dati dalla tabella **vehicles**.

		SELECT * FROM vehicles LIMIT 5;

### Per un cluster Windows

1. Aprire il pannello relativo al cluster di cui è appena stato eseguito il provisioning e quindi fare clic su **Dashboard**.

	![Avviare il dashboard del cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Avviare il dashboard del cluster")

	Quando richiesto, immettere le credenziali di amministratore per il cluster.

2. Verrà aperta la console di query di Microsoft Azure HDInsight. Fare clic su **Editor Hive**.

	![Aprire l'editor Hive](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Aprire l'editor Hive")

3. Nell'editor Hive immettere la query seguente e fare clic su **Invia**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	In questa query Hive si crea una tabella a partire dai dati presenti in Archivio Data Lake, nel percorso `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Questo percorso corrisponde a un file di dati di esempio caricato in precedenza.

	La tabella **Sessione processo** nella parte inferiore riporta lo stato del processo, che cambia da **Inizializzazione** a **In esecuzione** a **Completato**. È anche possibile fare clic su **Visualizza dettagli** per visualizzare maggiori informazioni sul processo completato.

	![Creare una tabella](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Creare una tabella")

4. Per verificare che la tabella sia stata creata correttamente, eseguire la query seguente.

		SHOW TABLES;

	Facendo clic su **View Details** (Visualizza dettagli) in corrispondenza della query, dovrebbe essere visualizzato quanto segue:

		hivesampletable
		vehicles

	**vehicles** costituisce la tabella creata in precedenza, mentre **hivesampletable** è una tabella di esempio disponibile in tutti i cluster HDInsight per impostazione predefinita.

5. È anche possibile eseguire una query per recuperare i dati dalla tabella **vehicles**.

		SELECT * FROM vehicles LIMIT 5;


## Accedere ad Archivio Data Lake tramite comandi HDFS

Dopo aver configurato il cluster HDInsight perché funzioni con Archivio Data Lake, è possibile usare i comandi della shell HDFS per accedere all'archivio.

### Per un cluster Linux

In questa sezione viene usato SSH nel cluster e vengono eseguiti i comandi HDFS. Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Dopo avere stabilito la connessione, usare il comando del file system HDFS seguente per elencare i file nell'Archivio Data Lake.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Dovrebbe essere elencato anche il file precedentemente caricato in Archivio Data Lake.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.


### Per un cluster Windows

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).

2. Fare clic su **Sfoglia**, su **Cluster HDInsight** e quindi sul cluster HDInsight creato.

3. Nel pannello del cluster fare clic su **Desktop remoto** e quindi nel pannello **Desktop remoto** fare clic su **Connetti**.

	![Remoto nel cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Creare un gruppo di risorse di Azure")

	Quando richiesto, immettere le credenziali fornite per l'utente desktop remoto.

4. Nella sessione remota, avviare Windows PowerShell e usare i comandi del file system HDFS per elencare i file presenti in Archivio Data Lake.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Dovrebbe essere elencato anche il file precedentemente caricato in Archivio Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.

## Usare Archivio Data Lake con un cluster Spark

In questa sezione si usa Jupyter Notebook disponibile con i cluster HDInsight Spark per eseguire un processo che legge dati da un account di Archivio Data Lake che è stato associato con un cluster HDInsight Spark, anziché l'account predefinito del BLOB di archiviazione di Azure.

1. Copiare alcuni dei dati di esempio dall'account di archiviazione predefinito (WASB) associato con il cluster Spark all'account di Archivio Data Lake di Azure associato con il cluster. Per farlo, è possibile usare lo [strumento ADLCopy](http://aka.ms/downloadadlcopy). Scaricare e installare lo strumento dal collegamento.

2. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.

3. Eseguire il comando seguente per copiare un BLOB specifico dal contenitore di origine a un'istanza di Archivio Data Lake:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Per questa esercitazione copiare il file di dati di esempio **HVAC.csv** all'indirizzo **/HdiSamples/HdiSamples/SensorSampleData/hvac/** nell'account Azure Data Lake Store. Il frammento di codice dovrebbe essere simile al seguente:

		AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	>[AZURE.WARNING] Assicurarsi che nel nome del file e nel percorso le maiuscole siano corrette.

4. Verrà richiesto di immettere le credenziali per la sottoscrizione di Azure in cui si trova l'account Azure Data Lake Store. L'output visualizzato sarà simile al seguente:

		Initializing Copy.
		Copy Started.
		100% data copied.
		Copy Completed. 1 file copied.

	Il file di dati (**HVAC.csv**) sarà copiato in una cartella denominata **/hvac** nell'account Data Lake Store.

4. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook. Fare clic su **Nuovo** e quindi su **PySpark**.

	![Creare un nuovo notebook Jupyter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.note.jupyter.createnotebook.png "Creare un nuovo notebook Jupyter")

3. Un nuovo notebook viene creato e aperto con il nome **Untitled.pynb**.

4. Poiché il notebook è stato creato tramite il kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice. È possibile iniziare con l'importazione dei tipi necessari per questo scenario. A tale scopo incollare il frammento di codice seguente in una cella e premere **MAIUSC+INVIO**.

		from pyspark.sql.types import *
		
	Ogni volta che viene eseguito un processo in Jupyter, il titolo della finestra del Web browser visualizzerà lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio pieno accanto al testo **PySpark** nell'angolo in alto a destra. Dopo il completamento del processo, viene visualizzato un cerchio vuoto.

	 ![Stato di un processo notebook Jupyter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.jupyter.job.status.png "Stato di un processo notebook Jupyter")

4. Caricare i dati di esempio in una tabella temporanea mediante il file **HVAC.csv** che è stato copiato nell'account Data Lake Store. È possibile accedere ai dati dell'account di Archivio Data Lake mediante il seguente modello di URL.

		adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

	In una cella vuota incollare l'esempio di codice seguente, sostituire **MYDATALAKESTORE** con il nome dell'account Data Lake Store e premere **MAIUSC + INVIO**. Questo esempio di codice registra i dati in una tabella temporanea denominata **hvac**.

		# Load the data
		hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. Dato che si usa un kernel PySpark, è ora possibile eseguire direttamente una query SQL sulla tabella temporanea **hvac** appena creata usando il comando speciale `%%sql`. Per altre informazioni sul comando speciale `%%sql` e sugli altri comandi speciali disponibili con il kernel PySpark, vedere [Kernel disponibili per i notebook Jupyter con cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"

5. Una volta che il processo viene completato correttamente, per impostazione predefinita viene visualizzato l'output tabulare seguente.

 	![Output tabulare dei risultati della query](./media/data-lake-store-hdinsight-hadoop-use-portal/tabular.output.png "Output tabulare dei risultati della query")

	È anche possibile visualizzare i risultati in altri formati. Ad esempio, un grafico ad area per lo stesso output apparirebbe come segue.

	![Grafico ad area dei risultati della query](./media/data-lake-store-hdinsight-hadoop-use-portal/area.output.png "Grafico ad area dei risultati della query")


6. Dopo aver eseguito l'applicazione, è consigliabile arrestare il notebook per rilasciare le risorse. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt**. Questa operazione consente di arrestare e chiudere il notebook.

## Usare Archivio Data Lake in una topologia Storm

È possibile usare Archivio Data Lake per scrivere dati da una topologia Storm. Per istruzioni su come ottenere questo scenario, vedere [Usare Azure Data Lake Store con Apache Storm in HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## Usare Archivio Data Lake con cluster HBase

Con i cluster HBase, è possibile usare Archivio Data Lake come archivio predefinito nonché come spazio di archiviazione aggiuntivo. A tale scopo, procedere come segue:

1.  Nel pannello **Origine dati** per **Percorso dati HBase** selezionare **Data Lake Store**.
2.  Selezionare il nome dell'Archivio Data Lake che si desidera usare o crearne uno nuovo.
3.  Infine, specificare **Cartella radice HBase** all'interno di Data Lake Store. Se l'account di Archivio Data Lake non dispone di una cartella radice, crearne una nuova.

	![HBase con Archivio Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Creare un gruppo di risorse di Azure")

### Considerazioni sull'uso di Archivio Data Lake come archivio predefinito per i cluster HBase

* È possibile usare lo stesso account di Archivio Data Lake per più cluster HBase. Il valore specificato in **Cartella radice HBase** per il cluster (passaggio 4 nella schermata riportata sopra) deve tuttavia essere univoco. **Non** usare la stessa cartella radice in due diversi cluster HBase.
* Anche se si utilizza l'account di Archivio Data Lake come archivio predefinito, i file di log del cluster HBase rimangono archiviati nei BLOB di archiviazione di Azure (WASB) associati al cluster. Questo è evidenziato nella casella blu nella schermata riportata sopra.



## Vedere anche

* [PowerShell: Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0831_2016-->