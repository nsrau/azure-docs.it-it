<properties 
	pageTitle="Eseguire un cluster Spark su HDInsight e usare SQL Spark da Zeppelin e Jupyter per l'analisi interattiva | Azure" 
	description="Istruzioni dettagliate su come eseguire rapidamente un cluster Apache Spark in HDInsight, quindi usare notebook SQL Spark da Zeppelin e Jupyter per l'esecuzione di query interattive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="nitinme"/>


# Avvio rapido: eseguire il provisioning Apache Spark e avviare query interattive usando SQL Spark in HDInsight

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Eseguire il provisioning di Apache Spark in HDInsight ed eseguire query interattive mediante SQL di Spark](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)

Imparare come eseguire il provisioning di un cluster Apache Spark in HDInsight tramite l'opzione Creazione rapida e quindi usare i notebook sul Web [Zeppelin](https://zeppelin.incubator.apache.org) e [Jupyter](https://jupyter.org) per l'esecuzione di query interattive sul cluster Spark.


   ![Introduzione all'uso di Apache Spark in HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.GetStartedFlow.Spark.png "Introduzione al tutorial di Apache Spark in HDInsight. I passaggi illustrati: creare un account di archiviazione, eseguire un cluster, eseguire istruzioni SQL Spark.")

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="storage"></a>Creare un account di archiviazione di Azure

Quando si esegue un cluster HDInsight in HDInsight, creare un account di archiviazione in Azure. Un specifico file di archiviazione BLOB dell'account è designato come file system predefinito. Per impostazione predefinita, il provisioning del cluster HDInsight viene effettuato nello stesso data center dell'account di archiviazione specificato. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage].


**Per creare un account di archiviazione di Azure**

1. Accedere al [portale di Azure][azure-management-portal].
2. Fare clic su **NUOVO** nell'angolo in basso a sinistra e quindi immettere i valori come illustrato nell'immagine.

	![Portale di Azure dove è possibile usare la funzione di creazione rapida per configurare un nuovo account di archiviazione](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.StorageAccount.QuickCreate.png "Portale di Azure dove è possibile usare la funzione di creazione rapida per configurare un nuovo account di archiviazione")

>[AZURE.NOTE]Assicurarsi quindi di creare l'account di archiviazione in una località supportata dal cluster,

Selezionare il nuovo account di archiviazione dall'elenco e fare clic su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore della pagina. Prendere nota dei valori di **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA** (funzionano entrambe le chiavi). Sarà necessario usarli più avanti nell'esercitazione. Per altre informazioni, vedere l'articolo relativo alla [creazione di un account di archiviazione][azure-create-storageaccount].
	
##<a name="provision"></a>Eseguire il provisioning di un cluster HDInsight Spark

In questa sezione si esegue una versione 3.2 del cluster HDInsight, basato sulla versione 1.3.1. in Spark. Per informazioni sulle versioni di HDInsight e sui relativi contratti di servizio, vedere l'articolo relativo al [controllo delle versioni del componente HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]I passaggi descritti in questo articolo creano un cluster Apache Spark in HDInsight usando le impostazioni di configurazione di base. Per informazioni su altre impostazioni di configurazione di cluster (ad esempio l'uso di un'archiviazione aggiuntiva, l'uso di una rete virtuale di Azure o di un metastore per Hive) vedere: [Eseguire il provisioning di cluster HDInsight con opzioni personalizzate](hdinsight-apache-spark-provision-clusters.md).


**Come eseguire un cluster Spark**

1. Accedere al [portale di Azure][azure-management-portal]. 

2. Fare clic su **NUOVO** nell'angolo in basso a sinistra e quindi immettere i valori come illustrato nell'immagine.

	![Creare un cluster Spark in HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.QuickCreateCluster.png "Creare un cluster Spark in HDInsight")


##<a name="zeppelin"></a>Eseguire query interattive SQL Spark usando un notebook Zeppelin

Dopo che è stato eseguito un cluster, è possibile usare un notebook on line Zeppelin per eseguire query interattive SQL Spark contro il cluster HDInsight Spark. In questa sezione, verrà usato un file dati di esempio (hvac.csv) disponibile per impostazione predefinita sul cluster per eseguire alcune query interattive SQL Spark.

>[AZURE.NOTE]Il notebook che avete creato seguendo le istruzioni seguenti è inoltre disponibile come predefinito sul cluster. Dopo aver avviato Zeppelin, si noterà il notebook dal nome **Esercitazione Zeppelin HVAC**.

1. Dalla Schermata iniziale del [portale di anteprima di Azure](https://portal.azure.com/) fare clic sul riquadro per il cluster Spark (se è stato bloccato sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e quindi dal pannello **Dashboard del cluster** fare clic su **Notebook di Zeppelin**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE]È anche possibile raggiungere il notebook di Zeppelin per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Creare un nuovo notebook. Dal riquadro intestazioni fare clic su **Notebook** e quindi fare clic su **Crea nuova nota**.

	![Creare un nuovo notebook Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.CreateNewNote.png "Creare un nuovo notebook Zeppelin")

	Nella stessa pagina, sotto l'intestazione **Notebook**, verrà visualizzato un nuovo notebook con il nome che inizia con **Nota XXXXXXXXX**. Fare clic su nuovo notebook.

3. Nella pagina web per il nuovo notebook, fare clic sull'intestazione e se si vuole modificare il nome del notebook. Premere INVIO per salvare la modifica del nome. Verificare anche che l'intestazione del notebook mostri lo stato **Connesso** nell'angolo in alto a destra.

	![Stato di notebook Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.NewNote.Connected.png "Stato di notebook Zeppelin")

4. Caricare i dati di esempio in una tabella temporanea. Quando si esegue il provisioning di un cluster di Spark in HDInsight, il file di dati di esempio **hvac.csv** viene copiato nell'account di archiviazione associato **\\HdiSamples\\SensorSampleData\\hvac**.

	Nel paragrafo vuoto creato per impostazione predefinita del nuovo notebook, incollare il frammento di codice riportato di seguito.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Premere **MAIUSC + INVIO** oppure fare clic sul pulsante **Riproduci** in modo che il paragrafo esegua il frammento di codice. Lo stato nell'angolo destro del paragrafo deve passare da PRONTO, IN ATTESA, IN ESECUZIONE, a COMPLETATO. L'output viene visualizzato nella parte inferiore dello stesso paragrafo. Nella schermata è simile al seguente:

	![Crea una tabella temporanea dai dati non elaborati](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.LoadDataIntoTable.png "Crea una tabella temporanea dai dati non elaborati")

	È inoltre possibile fornire un titolo a ogni paragrafo. Nell'angolo superiore destro fare clic sull'icona **Impostazioni** e quindi su **Mostra titolo**.

5. È ora possibile eseguire istruzioni SQL Spark nella tabella **hvac**. Incollare la query seguente in un nuovo paragrafo. La query recupera l'ID di generazione e la differenza tra la destinazione e le temperature effettive per ogni creazione di una determinata data. Premere **MAIUSC + INVIO**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	L'istruzione **%sql** all'inizio indica al notebook di usare l'interprete SQL Spark. È possibile esaminare gli interpreti definiti dalla scheda **Interprete** nell'intestazione del notebook.

	Nella schermata riportata di seguito sono illustrate questo output.

	![Eseguire un'istruzione SQL Spark usando il notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.SparkSQLQuery1.png "Eseguire un'istruzione SQL Spark usando il notebook")

	 Scegliere le opzioni di visualizzazione (evidenziate nel rettangolo) per passare tra diverse rappresentazioni per lo stesso output. Fare clic su **Impostazioni** per scegliere la chiave e i valori nell'output. La schermata precedente usa **buildingID** come chiave e la media di **temp\_diff** come valore.

	
6. È inoltre possibile eseguire istruzioni SQL Spark tramite le variabili nella query. Il frammento di codice seguente illustra come definire una variabile **Temp** nella query con i valori possibili con cui si desidera eseguire query. Quando si esegue la query per la prima volta, un elenco a tendina viene popolato automaticamente con i valori specificati per la variabile.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Incollare questo frammento di codice in un nuovo paragrafo e premere **MAIUSC + INVIO**. Nella schermata riportata di seguito sono illustrate questo output.

	![Eseguire un'istruzione SQL Spark usando il notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.SparkSQLQuery2.png "Eseguire un'istruzione SQL Spark usando il notebook")

	Per le query successive, è possibile selezionare un nuovo valore dall'elenco a tendina e quindi eseguire nuovamente la query. Fare clic su **Impostazioni** per scegliere la chiave e i valori nell'output. La schermata precedente usa **buildingID** come chiave, la media di **temp\_diff** come valore e **targettemp** come gruppo.

7. Riavviare l'interprete Spark SQL per uscire dall'applicazione. Fare clic sulla scheda **Interprete** nella parte superiore e per l'interprete Spark fare clic su **Riavvia**.

	![Riavviare l'interprete Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Zeppelin.Restart.Interpreter.png "Riavviare l'interprete Zeppelin")

##<a name="jupyter"></a>Eseguire query SQL Spark con un notebook di Jupyter

In questa sezione, è possibile usare un notebook Jupyter per eseguire query SQL Spark su un cluster Spark.

>[AZURE.NOTE]Il notebook che avete creato seguendo le istruzioni seguenti è inoltre disponibile come predefinito sul cluster. Dopo aver avviato Jupyter, si noterà il notebook con nome **HVACTutorial.ipynb**.

1. Dalla Schermata iniziale del [portale di anteprima di Azure](https://portal.azure.com/) fare clic sul riquadro per il cluster Spark (se è stato bloccato sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard del cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE]È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook. Fare clic su **Nuovo** e quindi fare clic su **Python2**.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.Jupyter.CreateNotebook.png "Creare un nuovo notebook Jupyter")

3. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

	![Specificare un nome per il notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.Jupyter.Notebook.Name.png "Specificare un nome per il notebook")

4. Importare i moduli necessari e creare i contesti SQL e Spark. Incollare il frammento di codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Ogni volta che viene eseguito un processo in Jupyter, il titolo della finestra del Web browser visualizzerà lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio nero accanto al testo **Python 2** nell'angolo in alto a destra. Dopo il completamento del processo, viene visualizzato un cerchio vuoto.

	 ![Status of a Jupyter notebook job](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Jupyter.Job.Status.png "Stato di un processo notebook Jupyter")

4. Caricare i dati di esempio in una tabella temporanea. Quando si esegue il provisioning di un cluster di Spark in HDInsight, il file di dati di esempio **hvac.csv** viene copiato nell'account di archiviazione associato **\\HdiSamples\\SensorSampleData\\hvac**.

	In una cella vuota, incollare il frammento di codice seguente e premere **MAIUSC + INVIO**. Questo frammento di codice consente di registrare i dati in una tabella temporanea denominata **hvac**.


		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. Una volta che il processo viene completato correttamente, viene visualizzato l'output seguente.

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. Riavviare il kernel per uscire dall'applicazione. Dalla barra del menu superiore fare clic su **Kernel** e su **Riavvia** e quindi fare di nuovo clic su **Riavvia** al prompt.

	![Riavviare il Kernel Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Jupyter.Restart.Kernel.png "Riavviare il Kernel Jupyter")


##<a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Eseguire il provisioning di uno Spark nel cluster HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
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