<properties
	pageTitle="Creare un cluster Spark su Azure HDInsight e usare Spark SQL da Zeppelin e Jupyter per l'analisi interattiva | Microsoft Azure"
	description="Istruzioni dettagliate su come creare rapidamente un cluster Apache Spark in HDInsight, quindi usare Spark SQL da notebook di Zeppelin e Jupyter per l'esecuzione di query interattive."
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
	ms.date="03/07/2016"
	ms.author="nitinme"/>


# Introduzione: creare Apache Spark in HDInsight ed eseguire query interattive mediante Spark SQL

> [AZURE.NOTE] HDInsight offre ora cluster Spark su Linux. Per informazioni su come iniziare a usare i cluster Spark in HDInsight per Linux, vedere l'articolo di [introduzione alla creazione di Apache Spark in Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).

Informazioni sulla creazione di un cluster Apache Spark in HDInsight tramite l'opzione Creazione rapida e sull'uso di notebook di [Zeppelin](https://zeppelin.incubator.apache.org) e [Jupyter](https://jupyter.org) basati sul Web per l'esecuzione di query interattive Spark SQL sul cluster Spark.


   ![Introduzione all'uso di Apache Spark in HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.getstartedflow.png "Introduzione al tutorial di Apache Spark in HDInsight. Passaggi illustrati: creare un account di archiviazione, creare un cluster, eseguire istruzioni Spark SQL")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Creare un cluster HDInsight Spark

In questa sezione viene creato un cluster HDInsight versione 3.2, basato su Spark versione 1.3.1. Per informazioni sulle versioni di HDInsight e sui relativi contratti di servizio, vedere l'articolo relativo al [controllo delle versioni del componente HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE] I passaggi descritti in questo articolo creano un cluster Apache Spark in HDInsight usando le impostazioni di configurazione di base. Per informazioni su altre impostazioni di configurazione del cluster (ad esempio l'uso di archiviazione aggiuntiva, di una rete virtuale di Azure o di un metastore per Hive) vedere [Creare cluster HDInsight con opzioni personalizzate](hdinsight-apache-spark-provision-clusters.md).


**Per creare un cluster Spark**

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).

2. Fare clic su **NUOVO**, **Analisi di dati** e quindi su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.1.png "Creazione di un nuovo cluster nel portale di Azure")

3. Immettere un **Nome cluster**, selezionare **Spark** per il **Tipo di cluster** e dall'elenco a discesa **Sistema operativo cluster** selezionare **Windows Server 2012 R2 Datacenter**. Un segno di spunta verde appare accanto al Nome del cluster, se disponibile.

	![Immettere il nome del cluster e il tipo](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.2.png "Immettere il nome del cluster e il tipo")

4. Se si dispone di più di una sottoscrizione, fare clic sulla voce **Sottoscrizione** per selezionare la sottoscrizione di Azure da usare per il cluster.

5. Fare clic su **Gruppo di risorse** per visualizzare un elenco di gruppi di risorse esistenti e selezionare quello in cui creare il cluster. In alternativa, è possibile fare clic su **Crea nuovo** e quindi immettere il nome del nuovo gruppo di risorse. Viene visualizzato un segno di spunta verde per indicare se il nome del nuovo gruppo è disponibile.

	> [AZURE.NOTE] Questa voce è impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Fare clic su **Credenziali** e quindi immettere i valori di **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster**. Se si vuole abilitare il Desktop remoto nel nodo del cluster, su **Abilita Desktop remoto**, fare clic su **Sì** e quindi specificare i valori richiesti. In questa esercitazione non richiede desktop remoto, quindi è possibile ignorare questo passaggio. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.

	![Fornire le credenziali del cluster](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.3.png "Fornire le credenziali del cluster")

7. Fare clic su **Origine dati** per scegliere un'origine dati esistente per il cluster o crearne una nuova. Quando si crea un cluster Hadoop in HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account viene designato come file system predefinito, come in HDFS (Hadoop Distributed File System, file system distribuito Hadoop). Per impostazione predefinita, il cluster HDInsight viene creato nello stesso data center dell'account di archiviazione specificato. Per altre informazioni, vedere l'argomento relativo all'[uso dell'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

	![Pannello di origine dati](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.4.png "Fornire la configurazione origine dati")

	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Usare le informazioni seguenti per comprendere le voci nel pannello **Origine dati**.

	- **Metodo di selezione**: impostare questa voce su **Da tutte le sottoscrizioni** per consentire l'esplorazione degli account di archiviazione da tutte le sottoscrizioni. Impostare questa voce su **Chiave di accesso** se si vuole immettere un valore nei campi **Nome archiviazione** e **Chiave di accesso** per un account di archiviazione esistente.

	- **Seleziona account di archiviazione / Crea nuovo**: fare clic su **Seleziona account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. In alternativa, fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Usare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, viene visualizzato un segno di spunta verde.

	- **Scegliere il contenitore predefinito**: usare questa opzione per immettere il nome del contenitore predefinito da usare per il cluster. È possibile immettere qualsiasi nome, è consigliabile usare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene usato per tale cluster specifico.

	- **Località**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione.

		> [AZURE.IMPORTANT] La selezione del percorso per l'origine dati predefinito imposta anche il percorso del cluster HDInsight. L'origine dati del cluster e l'origine dati predefinita devono trovarsi nella stessa area.

	Fare clic su **Seleziona** per salvare la configurazione dell'origine dati.

8. Fare clic su **Piani tariffari per il nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.5.png "Specificare il numero di nodi del cluster")

	Fare clic su **Seleziona** per salvare la configurazione dei piani tariffari per il nodo.

9. Nel pannello **Nuovo cluster HDInsight** assicurarsi che l'opzione **Aggiungi a Schermata iniziale** sia selezionata e quindi fare clic su **Crea**. Verrà creato il cluster e verrà aggiunto un riquadro apposito nella Schermata iniziale del portale di Azure. L'icona indica che il cluster è in fase di creazione e verrà visualizzata l'icona di HDInsight al termine dell'operazione.

	| Durante la creazione | Creazione completata |
	| ------------------ | --------------------- |
	| ![Indicatore della creazione sulla schermata iniziale](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioning.png) | ![Riquadro del cluster creato](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE] La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina per controllare il processo di creazione.

10. Dopo aver completato la creazione, fare clic sul riquadro per il cluster Spark dalla Schermata iniziale per avviare il pannello del cluster.


## <a name="zeppelin"></a>Eseguire query interattive Spark SQL mediante un notebook di Zeppelin

Dopo aver creato un cluster, è possibile usare un notebook di Zeppelin basato sul Web per eseguire query interattive Spark SQL nel cluster HDInsight Spark. In questa sezione, verrà usato un file dati di esempio (hvac.csv) disponibile per impostazione predefinita sul cluster per eseguire alcune query interattive SQL Spark.

>[AZURE.NOTE] Il notebook che avete creato seguendo le istruzioni seguenti è inoltre disponibile come predefinito sul cluster. Dopo aver avviato Zeppelin, si noterà il notebook dal nome **Esercitazione Zeppelin HVAC**.

1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi**, e dal pannello **Dashboard del Cluster**, fare clic su **Notebook Zeppelin**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Zeppelin per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Creare un nuovo notebook. Dal riquadro intestazione fare clic su **Notebook** quindi fare clic su **Creare una nuova nota**.

	![Creare un nuovo notebook Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createnewnote.png "Creare un nuovo notebook Zeppelin")

	Nella stessa pagina, sotto l'intestazione **Notebook**, verrà visualizzato un nuovo notebook con il nome che inizia con **Nota XXXXXXXXX**. Fare clic su nuovo notebook.

3. Nella pagina web per il nuovo notebook, fare clic sull'intestazione e se si vuole modificare il nome del notebook. Premere INVIO per salvare la modifica del nome. Verificare anche che l'intestazione del notebook mostri lo stato **Connesso** nell'angolo in alto a destra.

	![Stato di notebook Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.newnote.connected.png "Stato di notebook Zeppelin")

4. Caricare i dati di esempio in una tabella temporanea. Quando si crea un cluster Spark in HDInsight, il file di dati di esempio **hvac.csv** viene copiato nell'account di archiviazione associato in **\\HdiSamples\\SensorSampleData\\hvac**.

	Nel paragrafo vuoto creato per impostazione predefinita del nuovo notebook, incollare il seguente snippet:

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

	Premere **MAIUSC+INVIO** oppure fare clic sul pulsante **Riproduci** in modo che il paragrafo esegua il codice. Lo stato nell'angolo destro superiore del paragrafo deve passare da PRONTO, IN ATTESA, IN ESECUZIONE, a COMPLETATO. L'output appare nella parte inferiore dello stesso paragrafo. Nella schermata è simile al seguente:

	![Crea una tabella temporanea dai dati non elaborati](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.loaddataintotable.png "Crea una tabella temporanea dai dati non elaborati")

	È inoltre possibile fornire un titolo a ogni paragrafo. Nell'angolo superiore destro fare clic sull'icona **Impostazioni** e quindi su **Mostra titolo**.

5. È ora possibile eseguire istruzioni SQL Spark su tabella **hvac**. Incollare la query seguente in un nuovo paragrafo. La query recupera l'ID dell'edificio e la differenza tra le temperature effettive e quelle di destinazione per ogni edificio in una determinata data. Premere **MAIUSC + INVIO**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date
		from hvac
		where date = "6/1/13"

	L'istruzione **%sql** all'inizio indica al notebook di usare l'interprete SQL Spark. È possibile esaminare gli interpreti definiti dalla scheda **Interprete** nell'intestazione del notebook.

	Nella schermata riportata di seguito sono illustrate questo output.

	![Eseguire un'istruzione SQL Spark usando il notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.sparksqlquery1.png "Eseguire un'istruzione SQL Spark usando il notebook")

	Scegliere le opzioni di visualizzazione (evidenziate nel rettangolo) per passare tra diverse rappresentazioni per lo stesso output. Fare clic su **Impostazioni** per scegliere la chiave e i valori nell'output. La schermata precedente usa **buildingID** come chiave e la media di **temp\_diff** come valore.

6. È inoltre possibile eseguire istruzioni SQL Spark tramite le variabili nella query. L'esempio di codice seguente mostra come definire una variabile **Temp** nella query con i valori possibili con cui si vuole eseguire una query. Quando si esegue la query per la prima volta, un elenco a tendina viene popolato automaticamente con i valori specificati per la variabile.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}"

	Incollare questo esempio di codice in un nuovo paragrafo e premere **MAIUSC+INVIO**. Nella schermata riportata di seguito sono illustrate questo output.

	![Eseguire un'istruzione SQL Spark usando il notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.sparksqlquery2.png "Eseguire un'istruzione SQL Spark usando il notebook")

	Per le query successive, è possibile selezionare un nuovo valore dall'elenco a tendina e quindi eseguire nuovamente la query. Fare clic su**impostazioni**per scegliere la chiave e i valori nell'output. La schermata precedente usa **buildingID** come chiave, la media di **temp\_diff** come valore e **targettemp** come gruppo.

7. Riavviare l'interprete Spark SQL per uscire dall'applicazione. Scegliere la scheda **Interprete** nella parte superiore e per l'interprete Spark fare clic su **Riavvia**.

	![Riavviare l'interprete Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.zeppelin.restart.interpreter.png "Riavviare l'interprete Zeppelin")

## <a name="jupyter"></a>Eseguire query Spark SQL con un notebook di Jupyter

In questa sezione, è possibile usare un notebook Jupyter per eseguire query SQL Spark su un cluster Spark.

>[AZURE.NOTE] Il notebook che avete creato seguendo le istruzioni seguenti è inoltre disponibile come predefinito sul cluster. Dopo aver avviato Jupyter, si noterà il notebook con nome **HVACTutorial.ipynb**.

1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook. Fare clic su **New** e quindi su **Python2**.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Creare un nuovo notebook Jupyter")

3. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

	![Specificare un nome per il notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Specificare un nome per il notebook")

4. Importare i moduli necessari e creare i contesti SQL e Spark. Incollare l'esempio di codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Ogni volta che viene eseguito un processo in Jupyter, il titolo della finestra del Web browser visualizzerà lo stato **(Occupato)** accanto al titolo del notebook. È inoltre possibile notare un cerchio nero accanto al testo **Python2** nell'angolo in alto a destra. Dopo il completamento del processo, viene visualizzato un cerchio vuoto.

	 ![Status of a Jupyter notebook job](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.jupyter.job.status.png "Stato di un processo notebook Jupyter")

4. Caricare i dati di esempio in una tabella temporanea. Quando si crea un cluster Spark in HDInsight, il file di dati di esempio **hvac.csv** viene copiato nell'account di archiviazione associato in **\\HdiSamples\\SensorSampleData\\hvac**.

	In una cella vuota incollare l'esempio di codice seguente e premere **MAIUSC+INVIO**. Questo esempio di codice registra i dati in una tabella temporanea denominata **hvac**.

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

6. Riavviare il kernel per uscire dall'applicazione. Dalla barra del menu superiore fare clic su **Kernel** e su **Restart** e quindi fare di nuovo clic su **Restart** quando richiesto.

	![Riavviare il Kernel Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.jupyter.restart.kernel.png "Riavviare il Kernel Jupyter")

##Eliminazione del cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Creare un cluster Spark in HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Usare Spark in HDInsight per la creazione di applicazioni di Machine Learning](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0309_2016-->