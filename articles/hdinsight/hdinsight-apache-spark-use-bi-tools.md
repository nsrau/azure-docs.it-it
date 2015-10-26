<properties 
	pageTitle="Usare gli strumenti di Business Intelligence con Apache Spark in HDInsight | Microsoft Azure" 
	description="Istruzioni dettagliate su come usare i notebook con Apache Spark per creare schemi su dati non elaborati, salvarle come tabelle Hive e quindi usare gli strumenti Business Intelligence per la tabella Hive per l'analisi dei dati" 
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
	ms.date="09/30/2015" 
	ms.author="nitinme"/>


# Usare gli strumenti di Business Intelligence con Apache Spark in Azure HDInsight

Informazioni su come usare Apache Spark in Azure HDInsight per eseguire le operazioni seguenti:

* Accettare dati di esempio non elaborati e salvarli come tabella Hive.
* Usare gli strumenti di Business Intelligence quali Power BI e Tableau per analizzare e visualizzare i dati.

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark. Per istruzioni, vedere [Provisioning dei cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Un computer con installato il driver ODBC di Microsoft Spark (obbligatorio per Scintilla su HDInsight per lavorare con Tableau). È possibile installare il driver da [qui](http://go.microsoft.com/fwlink/?LinkId=616229).
- Strumenti di Business Intelligence quali [Power BI](http://www.powerbi.com/) o [Tableau Desktop](http://www.tableau.com/products/desktop). È possibile ottenere una sottoscrizione di anteprima gratuita di Power BI da [http://www.powerbi.com/](http://www.powerbi.com/).

##<a name="hivetable"></a>Salvare i dati non elaborati come una tabella Hive

In questa sezione viene usato il notebook [Jupyter](https://jupyter.org) associato a un cluster Apache Spark in HDInsight per eseguire i processi che elaborano i dati di esempio non elaborati e li salvano come una tabella Hive. I dati di esempio sono un file CSV (hvac.csv) disponibile in tutti i cluster per impostazione predefinita.

Dopo avere salvato i dati come tabella Hive, nella sezione successiva verrà effettuata la connessione alla tabella Hive mediante strumenti di Business Intelligence come Power BI e Tableau.

1. Dalla Schermata iniziale del [portale di anteprima di Azure](https://portal.azure.com/) fare clic sul riquadro per il cluster Spark (se è stato bloccato sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard del cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE]È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook. Fare clic su **Nuovo** e quindi su **Python 2**.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.CreateNotebook.png "Creare un nuovo notebook Jupyter")

3. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

	![Specificare un nome per il notebook](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.Notebook.Name.png "Specificare un nome per il notebook")

4. Importare i moduli necessari e creare i contesti di Spark e Hive. Incollare il frammento di codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**.

		from pyspark import SparkContext
		from pyspark.sql import *
		from pyspark.sql import HiveContext

		# Create Spark and Hive contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		hiveCtx = HiveContext(sc)

	Ogni volta che viene eseguito un processo in Jupyter, il titolo della finestra del Web browser visualizzerà lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio nero accanto al testo **Python 2** nell'angolo in alto a destra. Dopo il completamento del processo, viene visualizzato un cerchio vuoto.

	 ![Status of a Jupyter notebook job](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Job.Status.png "Stato di un processo notebook Jupyter")

4. Caricare i dati di esempio in una tabella temporanea. Quando si esegue il provisioning di un cluster di Spark in HDInsight, il file di dati di esempio **hvac.csv** viene copiato nell'account di archiviazione associato **\\HdiSamples\\SensorSampleData\\hvac**.

	In una cella vuota, incollare il frammento di codice seguente e premere **MAIUSC + INVIO**. Questo frammento di codice consente di registrare i dati in una tabella Hive denominata **hvac**.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date")
		hvac = hvacParts.map(lambda p: {"Date": str(p[0]), "Time": str(p[1]), "TargetTemp": int(p[2]), "ActualTemp": int(p[3]), "BuildingID": int(p[6])})
		
		# Infer the schema and create a table		
		hvacTable = hiveCtx.inferSchema(hvac)
		hvacTable.registerAsTable("hvactemptable")
		hvacTable.saveAsTable("hvac")

5. Verificare che la tabella è stata creata correttamente. In una cella vuota del notebook, copiare il frammento di codice seguente e premere **MAIUSC + INVIO**.

		hiveCtx.sql("SHOW TABLES").show()

	Verrà visualizzato un output simile al seguente:

		tableName       isTemporary
		hvactemptable   true       
		hivesampletable false      
		hvac            false

	Solo le tabelle che presentano il valore false nella colonna **isTemporary** sono tabelle hive che verranno archiviate nel metastore ed è possibile accedervi dagli strumenti di Business Intelligence. In questa esercitazione ci si connetterà alla tabella **hvac** appena creata.

6. Verificare che la tabella contiene i dati desiderati. In una cella vuota del notebook, copiare il frammento di codice seguente e premere **MAIUSC + INVIO**.

		hiveCtx.sql("SELECT * FROM hvac LIMIT 10").show()
	
7. È ora possibile chiudere il notebook riavviando il kernel. Dalla barra del menu superiore fare clic su **Kernel** e su **Riavvia** e quindi fare di nuovo clic su **Riavvia** al prompt.

	![Riavviare il Kernel Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Restart.Kernel.png "Riavviare il Kernel Jupyter")

##<a name="powerbi"></a>Usare Power BI per analizzare i dati della tabella Hive

Dopo aver salvato i dati come una tabella Hive, è possibile usare Power BI per connettersi ai dati e la visualizzazione per creare rapporti, dashboard e così via.

1. Accedere a [Power BI](http://www.powerbi.com/).

2. Nella schermata iniziale fare clic su **Database e altro ancora**.

	![Recupero di dati in Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Get.Data.png "Recupero di dati in Power BI")

3. Nella schermata successiva fare clic su **Spark** e quindi fare clic su **Connetti**.

4. Nella pagina Spark in Azure HDInsight, indicare i valori per la connessione al cluster Spark e quindi fare clic su **Connetti**.

	![Connettersi a un cluster di Spark in HDInsight](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Connect.Spark.png "Connettersi a un cluster di Spark in HDInsight")

	Dopo aver stabilito la connessione, Power BI avvia l'importazione di dati dal cluster Spark in HDInsight.

5. Power BI importa i dati e viene visualizzato il nuovo dashboard. Viene anche aggiunto un nuovo set di dati sotto l'intestazione **Set di dati**. Fare clic sul riquadro Spark nel dashboard per aprire un foglio di lavoro per visualizzare i dati.

	![Riquadro Spark nel dashboard di Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Tile.png "Riquadro Spark nel dashboard di Power BI")

6. Si noti che l'elenco **Campi** a destra elenca la tabella **hvac** creata in precedenza. Espandere la tabella per visualizzare i campi della tabella, come definito in precedenza nel notebook.

	  ![Elencare le tabelle Hive](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Display.Tables.png "Elencare le tabelle Hive")

7. Creare una visualizzazione per mostrare la variazione tra temperatura di destinazione e temperatura effettiva per ogni compilazione. A tale scopo, trascinare e rilasciare il campo **BuildingID** nei campi **Asse** e **ActualTemp**/**TargetTemp** in **Valore**.

	![Creare visualizzazioni](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.1.png "Creare visualizzazioni")

	Selezionare anche **Mappa area** (in rosso) per visualizzare i dati.

8. Per impostazione predefinita, la visualizzazione mostra la somma di **ActualTemp** e **TargetTemp**. Per entrambi i campi, dall'elenco a discesa selezionare **Media** per ottenere una media della temperature effettiva e di destinazione per entrambi gli edifici.

	![Creare visualizzazioni](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.2.png "Creare visualizzazioni")

9. La visualizzazione dei dati deve essere simile al seguente. Spostare il cursore sopra la visualizzazione per ottenere suggerimenti con i dati rilevanti.

	![Creare visualizzazioni](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.3.png "Creare visualizzazioni")

10. Fare clic su **Salva** nel menu principale e specificare un nome per il report. È inoltre possibile aggiungere l'oggetto visivo. Quando si blocca una visualizzazione, verrà archiviata nel dashboard e consente di tenere traccia dell'ultimo valore in modo immediato.

	È possibile aggiungere tutte le visualizzazioni per lo stesso set di dati e aggiungerle al dashboard per uno snapshot dei dati. Inoltre, i cluster Spark in HDInsight sono direttamente connessi a Power BI. Ciò significa che Power BI è sempre più aggiornato dal cluster in modo che non è necessario pianificare gli aggiornamenti per il set di dati.

##<a name="tableau"></a>Usare Tableau Desktop per analizzare i dati della tabella Hive
	
1. Avviare Tableau Desktop. Nel riquadro di sinistra, nell'elenco dei server a cui connettersi, fare clic su **Spark SQL**.

2. Nella finestra di dialogo della connessione a SQL Spark, specificare i valori come illustrato di seguito e quindi fare clic su **OK**.

	![Connettersi a un cluster di Spark](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Connect.png "Connettersi a un cluster di Spark")

	L'elenco a discesa di autenticazione indica il **servizio **Microsoft** Azure HDInsight** come opzione solo se nel computer è installato il [driver ODBC di Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229).

3. Nella schermata successiva fare clic sull'icona **Trova** dal menu a discesa **Schema** e quindi fare clic su **Predefinito**.

	![Trovare lo schema](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Schema.png "Trovare lo schema")

4. Per il campo **Tabella** fare di nuovo clic sull'icona **Trova** per elencare tutte le tabelle Hive disponibili nel cluster. Verrà visualizzata la tabella **hvac** creata in precedenza tramite il notebook.

	![Trovare le tabelle](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Table.png "Trovare le tabelle")

5. Trascinare e rilasciare la tabella nella casella superiore a destra. Tableau importa i dati e lo schema viene visualizzato come evidenziato dalla casella rossa.

	![Aggiunta di tabelle a Tableau](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Table.png "Aggiunta di tabelle a Tableau")

6. Fare clic sulla scheda **Sheet1** nella parte inferiore sinistra. Apportare una visualizzazione che mostra la temperatura effettiva e destinazione media per tutti gli edifici e per ogni data. Trascinare **Data** e **ID edificio** su **Colonne** e **Actual Temp**/**Target Temp** su **Righe**. In **Contrassegni** selezionare **Area** per usare una visualizzazione della mappa dell'area.

	 ![Aggiungere campi per la visualizzazione](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Fields.png "Aggiungere campi per la visualizzazione")

7. Per impostazione predefinita, i campi di temperatura vengono visualizzati come funzione di aggregazione. Se si vuole visualizzare la media delle temperature invece, è possibile farlo dall'elenco a discesa, come illustrato di seguito.

	![Eseguire Media della temperatura](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Temp.Avg.png "Eseguire Media della temperatura")

8. È anche possibile super-imporre una mappa di temperatura rispetto a un'altra per farsi un'idea migliore della differenza tra le temperature effettive e di destinazione. Spostare il puntatore del mouse nell'angolo della mappa area inferiore fino a visualizzare la forma di quadratino evidenziata in un cerchio rosso in alto. Trascinare la mappa sull'altra mappa nella parte superiore e rilasciare il mouse quando viene visualizzata la forma evidenziata di un rettangolo rosso riportata sopra.

	![Esegue il mapping di tipo merge](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Merge.png "Esegue il mapping di tipo merge")

	 Occorre modificare la visualizzazione dei dati per le operazioni seguenti:

	![Visualizzazione](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Final.Visual.png "Visualizzazione")
	 
9. Fare clic su **Salva** per salvare il foglio di lavoro. È possibile creare dashboard e aggiungervi uno o più fogli.

##<a name="seealso"></a>Vedere anche

* [Panoramica: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Avvio rapido: eseguire il provisioning Apache Spark e avviare query interattive usando SQL Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Usare Spark in HDInsight per la creazione di applicazioni di Machine Learning](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=Oct15_HO3-->