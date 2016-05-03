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
	ms.date="04/18/2016" 
	ms.author="nitinme"/>


# Usare gli strumenti di Business Intelligence con Apache Spark in HDInsight basato su Linux (anteprima)

Informazioni su come usare Apache Spark in Azure HDInsight per eseguire le operazioni seguenti:

* Accettare dati di esempio non elaborati e salvarli come tabella Hive.
* Usare gli strumenti di Business Intelligence quali Power BI e Tableau per analizzare e visualizzare i dati.

> [AZURE.NOTE] Questa esercitazione è applicabile solo per i cluster Spark 1.5.2 creati in Azure HDInsight.

Questa esercitazione è disponibile anche come notebook di Jupyter in un cluster Spark (Linux) creato in HDInsight. L'esperienza offerta dal notebook consente di eseguire i frammenti di codice Python dal notebook stesso. Per eseguire l'esercitazione da un notebook, creare un cluster Spark, avviare un notebook di Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) e quindi eseguire il notebook **Usare gli strumenti di Business Intelligence con Apache Spark in HDInsight.ipynb** nella cartella **Python**.

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
- Un computer con installato il driver ODBC di Microsoft Spark (obbligatorio per Scintilla su HDInsight per lavorare con Tableau). È possibile installare il driver da [qui](http://go.microsoft.com/fwlink/?LinkId=616229).
- Strumenti di Business Intelligence quali [Power BI](http://www.powerbi.com/) o [Tableau Desktop](http://www.tableau.com/products/desktop). È possibile ottenere una sottoscrizione di anteprima gratuita di Power BI da [http://www.powerbi.com/](http://www.powerbi.com/).

##<a name="hivetable"></a>Salvare i dati non elaborati come una tabella Hive

In questa sezione viene usato il notebook di [Jupyter](https://jupyter.org) associato a un cluster Apache Spark in HDInsight per eseguire i processi che elaborano i dati di esempio non elaborati e li salvano come tabella Hive. I dati di esempio sono un file con estensione csv (hvac.csv) disponibile in tutti i cluster per impostazione predefinita.

Dopo avere salvato i dati come tabella Hive, nella sezione successiva verrà effettuata la connessione alla tabella Hive mediante strumenti di Business Intelligence come Power BI e Tableau.

1. Dal [portale di Azure](https://portal.azure.com/), dalla schermata iniziale, fare clic sul riquadro per il cluster Spark (se lo si è bloccato alla schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook. Fare clic su **Nuovo** e quindi su **PySpark**.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "Creare un nuovo notebook Jupyter")

3. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

	![Specificare un nome per il notebook](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "Specificare un nome per il notebook")

4. Poiché il notebook è stato creato tramite il kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice. È possibile iniziare con l'importazione dei tipi necessari per questo scenario. A tale scopo, posizionare il cursore nella cella e premere **MAIUSC + INVIO**.

		from pyspark.sql import *
		
	
5. Caricare i dati di esempio in una tabella temporanea. Quando si crea un cluster Spark in HDInsight, il file di dati di esempio **hvac.csv** viene copiato nell'account di archiviazione associato in **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**.

	In una cella vuota incollare il frammento di codice seguente e premere **MAIUSC+INVIO**. Questo frammento di codice consente di registrare i dati in una tabella Hive denominata **hvac**.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create a schema for our data
		Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
		hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
		
		# Infer the schema and create a table       
		hvacTable = sqlContext.createDataFrame(hvac)
		hvacTable.registerTempTable('hvactemptable')
		dfw = DataFrameWriter(hvacTable)
		dfw.saveAsTable('hvac')

5. Verificare che la tabella è stata creata correttamente. È possibile usare il comando speciale `%%sql` per eseguire direttamente query Hive. Per altre informazioni sul magic `%%sql` e sugli altri magic disponibili con il kernel PySpark, vedere [Kernel disponibili per i notebook di Jupyter con cluster Spark in HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

		%%sql
		SHOW TABLES

	Verrà visualizzato un output simile al seguente:

		+-----------+---------------+
		|isTemporary|tableName		| 
		+-----------+---------------+
		|       true|hvactemptable  |
		|      false|hivesampletable|
		|      false|hvac			|
		+-----------+---------------+


	Solo le tabelle che dispongono di false sotto la colonna **isTemporary** sono tabelle hive che verranno archiviate in metastore ed è possibile accedervi da strumenti di Business Intelligence. In questa esercitazione si connetterà la tabella **hvac** appena creata.

6. Verificare che la tabella contiene i dati desiderati. In una cella vuota del notebook copiare il frammento di codice seguente e premere **MAIUSC+INVIO**.

		%%sql
		SELECT * FROM hvac LIMIT 10
	
7. È possibile chiudere il notebook per rilasciare le risorse. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt**. Questa operazione consente di arrestare e chiudere il notebook.

##<a name="powerbi"></a>Usare Power BI per analizzare i dati della tabella Hive

Dopo aver salvato i dati come una tabella Hive, è possibile usare Power BI per connettersi ai dati e la visualizzazione per creare rapporti, dashboard e così via.

1. Accedere a [Power BI](http://www.powerbi.com/).

2. Nella schermata iniziale fare clic su **Database e altro ancora**.

	![Recupero di dati in Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Recupero di dati in Power BI")

3. Nella schermata successiva fare clic su **Spark in Azure HDInsight** e quindi su **Connetti**. Quando richiesto, immettere l'URL del cluster (`mysparkcluster.azurehdinsight.net`) e le credenziali per connettersi al cluster.

	Dopo aver stabilito la connessione, Power BI avvia l'importazione di dati dal cluster Spark in HDInsight.

5. Power BI importa i dati e aggiunge un nuovo set di dati Spark nell'intestazione **Set di dati**. Fare clic sul set di dati per aprire un nuovo foglio di lavoro per la visualizzazione dei dati. È inoltre possibile salvare il foglio di lavoro come report. A tale scopo, scegliere **Salva** dal menu **File**.

	![Riquadro Spark nel dashboard di Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Riquadro Spark nel dashboard di Power BI")

6. Si noti che l’elenco **campi** negli elenchi di destra la tabella **hvac** creata in precedenza. Espandere la tabella per visualizzare i campi della tabella, come definito in precedenza nel notebook.

	  ![Elencare le tabelle Hive](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "Elencare le tabelle Hive")

7. Creare una visualizzazione per mostrare la variazione tra temperatura di destinazione e temperatura effettiva per ogni edificio. Selezionare **Grafico ad aree** (in rosso) per visualizzare i dati. Per definire l'asse, trascinare e rilasciare il campo **BuildingID** nei campi **Asse** e **ActualTemp**/**TargetTemp** in **Valore**.

	![Creare visualizzazioni](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "Creare visualizzazioni")


8. Per impostazione predefinita, la visualizzazione mostra la somma di **ActualTemp** e **TargetTemp**. Per entrambi i campi, dall'elenco a discesa selezionare **Media** per ottenere una media della temperature effettiva e di destinazione per entrambe le compilazioni.

	![Creare visualizzazioni](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)
    
9. La visualizzazione dei dati deve essere simile al seguente. Spostare il cursore sopra la visualizzazione per ottenere suggerimenti con i dati rilevanti.

	![Creare visualizzazioni](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)

10. Fare clic su **Salva** nel menu principale e specificare un nome per il report. È inoltre possibile aggiungere l'oggetto visivo. Quando si blocca una visualizzazione, verrà archiviata nel dashboard e consente di tenere traccia dell'ultimo valore in modo immediato.

	È possibile aggiungere tutte le visualizzazioni per lo stesso set di dati e aggiungerle al dashboard per uno snapshot dei dati. Inoltre, i cluster Spark in HDInsight sono direttamente connessi a Power BI. Ciò significa che Power BI è sempre più aggiornato dal cluster in modo che non è necessario pianificare gli aggiornamenti per il set di dati.

##<a name="tableau"></a>Usare Tableau Desktop per analizzare i dati della tabella Hive
	
1. Avviare Tableau Desktop. Nel riquadro di sinistra, nell'elenco dei server a cui connettersi, fare clic su **Spark SQL**. Se Spark SQL non è presente per impostazione predefinita nel riquadro sinistro, è possibile trovarlo facendo clic su **More Servers**. 

2. Nella finestra di dialogo della connessione a Spark SQL, specificare i valori come illustrato di seguito e quindi fare clic su **OK**.

	![Connettersi a un cluster di Spark](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "Connettersi a un cluster di Spark")

	L'elenco a discesa di autenticazione indica **Microsoft Azure HDInsight Service** come opzione solo se nel computer è installato il [driver ODBC di Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229).

3. Nella schermata successiva fare clic sull'icona **Find** dal menu a discesa **Schema** e quindi su **default**.

	![Trovare lo schema](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "Trovare lo schema")

4. Per il campo **Table** fare di nuovo clic sull'icona **Find** per elencare tutte le tabelle Hive disponibili nel cluster. Verrà visualizzata la tabella **hvac** creata in precedenza tramite il notebook.

	![Trovare le tabelle](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "Trovare le tabelle")

5. Trascinare e rilasciare la tabella nella casella superiore a destra. Tableau importa i dati e lo schema viene visualizzato come evidenziato dalla casella rossa.

	![Aggiunta di tabelle a Tableau](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "Aggiunta di tabelle a Tableau")

6. Fare clic sulla scheda **Sheet1** nella parte inferiore sinistra. Apportare una visualizzazione che mostra la temperatura effettiva e di destinazione media per tutti gli edifici e per ogni data. Trascinare **Date** e **Building ID** su **Columns** e **Actual Temp**/**Target Temp** su **Rows**. In **Marks** selezionare **Area** per usare una visualizzazione del grafico ad aree.

	 ![Aggiungere campi per la visualizzazione](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "Aggiungere campi per la visualizzazione")

7. Per impostazione predefinita, i campi di temperatura vengono visualizzati come funzione di aggregazione. Se si vuole visualizzare la media delle temperature invece, è possibile farlo dall'elenco a discesa, come illustrato di seguito.

	![Eseguire Media della temperatura](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "Eseguire Media della temperatura")

8. È anche possibile super-imporre una mappa di temperatura rispetto a un'altra per farsi un'idea migliore della differenza tra le temperature effettive e di destinazione. Spostare il puntatore del mouse nell'angolo del grafico ad aree inferiore fino a visualizzare la forma evidenziata in un cerchio rosso. Trascinare il grafico sull'altro grafico nella parte superiore e rilasciare il mouse quando viene visualizzata la forma evidenziata in un rettangolo rosso.

	![Esegue il mapping di tipo merge](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "Esegue il mapping di tipo merge")

	 Occorre modificare la visualizzazione dei dati per le operazioni seguenti:

	![Visualizzazione](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "Visualizzazione")
	 
9. Fare clic su **Save** per salvare il foglio di lavoro. È possibile creare dashboard e aggiungervi uno o più fogli.

##<a name="seealso"></a>Vedere anche

* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione tramite dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Strumenti ed estensioni

* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gestire le risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0420_2016-->