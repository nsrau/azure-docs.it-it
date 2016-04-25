<properties 
	pageTitle="Usare Apache Spark per creare applicazioni di machine learning su HDInsight | Microsoft Azure" 
	description="Istruzioni dettagliate su come usare notebook con Apache Spark per compilare applicazioni di machine learning" 
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
	ms.date="04/08/2016" 
	ms.author="nitinme"/>


# Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib con Spark in HDInsight (Linux)

> [AZURE.TIP] Questa esercitazione è disponibile anche come notebook Jupyter in un cluster Spark (Linux) creato in HDInsight. L'esperienza offerta dal notebook consente di eseguire i frammenti di codice Python dal notebook stesso. Per eseguire l'esercitazione da un notebook, creare un cluster Spark, avviare un notebook Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) e quindi eseguire il notebook **Spark Machine Learning - Predictive analysis on food inspection data using MLLib.ipynb** nella cartella **Python**.


Questo articolo illustra come usare **MLLib**, le librerie di apprendimento automatico predefinite di Spark, per eseguire una semplice analisi predittiva su un set di dati aperto. MLLib è una libreria Spark di base che fornisce alcune utilità che agevolano le attività di apprendimento automatico, incluse utilità adatte a:

* Classificazione

* Regressione

* Clustering

* Modellazione di argomenti

* Scomposizione di valori singolari e analisi in componenti principali

* Testing e calcolo ipotetici di statistiche di esempio

Questo articolo illustra un semplice approccio alla *classificazione* tramite la regressione logistica.

## Che cosa sono la classificazione e regressione logistica?

La *classificazione*, un'attività molto comune di apprendimento automatico, è il processo di ordinamento dei dati in categorie. È il processo eseguito da un algoritmo di classificazione per determinare come assegnare le "etichette" ai dati di input specificati. Si pensi, ad esempio, a un algoritmo di apprendimento automatico che accetta informazioni sulle azioni come input e divide le azioni in due categorie: azioni da vendere e azioni da conservare.

La regressione logistica è l'algoritmo usato per la classificazione. L'API di regressione logistica di Spark è utile per la *classificazione binaria*, ovvero la classificazione di dati di input in uno di due gruppi. Per altre informazioni sulle regressioni logistiche, vedere [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Riepilogando, il processo di regressione logistica genera una *funzione logistica* che può essere usata per stimare la probabilità che un vettore di input appartenga a un gruppo o all'altro.

## Qual è lo scopo di questo articolo?

Si userà Spark per eseguire un'analisi predittiva dei dati del controllo degli alimenti (**Food\_Inspections1.csv**) acquisiti dal [portale dati della città di Chicago](https://data.cityofchicago.org/). Questo set di dati contiene informazioni sui controlli degli alimenti condotti a Chicago, incluse informazioni su ogni stabilimento alimentare controllato, sulle eventuali violazioni riscontrate e sui risultati del controllo. Il file di dati in formato CSV è già disponibile nell'account di archiviazione associato al cluster **/HdiSamples/HdiSamples/FoodInspectionData/Food\_Inspections1.csv**.

Nei passaggi seguenti, si svilupperà un modello per sapere che cosa serve per superare o non superare un controllo sugli alimenti.

## Iniziare a compilare un'applicazione di apprendimento automatico usando MLlib di Spark

1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook. Fare clic su **Nuovo** e quindi su **PySpark**.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Creare un nuovo notebook Jupyter")

3. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

	![Specificare un nome per il notebook](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Specificare un nome per il notebook")

3. Poiché il notebook è stato creato tramite il kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice. È possibile iniziare a compilare l'applicazione di Machine Learning importando i tipi necessari per questo scenario. A tale scopo, posizionare il cursore nella cella e premere **MAIUSC + INVIO**.


		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row
		from pyspark.sql.functions import UserDefinedFunction
		from pyspark.sql.types import *

## Creare un frame di dati di input

È possibile che venga usato `sqlContext` per eseguire trasformazioni sui dati strutturati. La prima attività è il caricamento dei dati di esempio (**Food\_Inspections1.csv**) in un *frame di dati* Spark SQL.

1. Poiché i dati non elaborati sono in formato con estensione csv, è necessario usare il contesto Spark per eseguire il pull di ogni riga del file nella memoria come testo non strutturato, quindi si usa la libreria CSV di Python per analizzare ogni singola riga. 


		def csvParse(s):
		    import csv
		    from StringIO import StringIO
		    sio = StringIO(s)
		    value = csv.reader(sio).next()
		    sio.close()
		    return value
		
		inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
		                .map(csvParse)


2. Il file con estensione csv ora è disponibile come RDD. Recuperare ora una riga dal file RDD per conoscere lo schema dei dati.


		inspections.take(1)


	Verrà visualizzato un output simile al seguente:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[['413707',
	      'LUNA PARK INC',
	      'LUNA PARK  DAY CARE',
	      '2049789',
	      "Children's Services Facility",
	      'Risk 1 (High)',
	      '3250 W FOSTER AVE ',
	      'CHICAGO',
	      'IL',
	      '60625',
	      '09/21/2010',
	      'License-Task Force',
	      'Fail',
	      '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
	      '41.97583445690982',
	      '-87.7107455232781',
	      '(41.97583445690982, -87.7107455232781)']]


3. L'output precedente dà un'idea dello schema del file di input che include, tra le altre cose, il nome di ogni stabilimento, il tipo di stabilimento, l'indirizzo, i dati dei controlli e la posizione. Selezioniamo alcune colonne che saranno utili per l'analisi predittiva e raggruppiamo i risultati come frame di dati, che useremo in seguito per creare una tabella temporanea.


		schema = StructType([
        StructField("id", IntegerType(), False), 
        StructField("name", StringType(), False), 
        StructField("results", StringType(), False), 
        StructField("violations", StringType(), True)])

		df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
		df.registerTempTable('CountResults')

4. Ora è disponibile un *frame di dati*, `df`, su cui è possibile eseguire l'analisi. È presente anche la tabella temporanea **CountResults**. Nel frame di dati sono state incluse 4 colonne importanti: **id**, **name**, **results** e **violations**.
	
	Prendere un piccolo campione di dati:

		df.show(5)

	Verrà visualizzato un output simile al seguente:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		+------+--------------------+-------+--------------------+
	    |    id|                name|results|          violations|
	    +------+--------------------+-------+--------------------+
	    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
	    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
	    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
	    |413708|BENCHMARK HOSPITA...|   Pass|                    |
	    |413722|           JJ BURGER|   Pass|                    |
	    +------+--------------------+-------+--------------------+

## Informazioni sui dati

1. Ora si determinerà il contenuto del set di dati. Ad esempio, quali sono i diversi valori della colona **results**?


	df.select('results').distinct().show()

	
	Verrà visualizzato un output simile al seguente:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		+--------------------+
	    |             results|
	    +--------------------+
	    |                Fail|
	    |Business Not Located|
	    |                Pass|
	    |  Pass w/ Conditions|
	    |     Out of Business|
	    +--------------------+
    
2. Una visualizzazione rapida aiuta a riflettere sulla distribuzione di questi risultati. Sono già disponibili i dati nella tabella temporanea **CountResults**. Per comprendere meglio il modo in cui i risultati vengono distribuiti, è possibile eseguire la query SQL seguente sulla tabella.

		%%sql -o countResultsdf
		SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

	Il comando speciale `%%sql` seguito da `-o countResultsdf` assicura che l'output della query venga mantenuto in locale nel server Jupyter, di solito il nodo head del cluster. L'output viene conservato come frame di dati [Pandas](http://pandas.pydata.org/) con il nome specificato **countResultsdf**.
	
	Verrà visualizzato un output simile al seguente:
	
	![Output della query SQL](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/query.output.png "Output della query SQL")

	Per altre informazioni sul comando speciale `%%sql` e sugli altri comandi speciali disponibili con il kernel PySpark, vedere [Kernel disponibili per i notebook di Jupyter con cluster Spark in HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

3. È anche possibile creare un tracciato tramite Matplotlib, una libreria che consente di creare visualizzazioni di dati. Poiché il tracciato deve essere tracciato dal frame di dati **countResultsdf** conservato in locale, il frammento di codice deve iniziare con il codice speciale `%%local`. Ciò garantisce che il codice venga eseguito localmente nel server di Jupyter.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		
		labels = countResultsdf['results']
		sizes = countResultsdf['cnt']
		colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
		plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
		plt.axis('equal')

	Verrà visualizzato un output simile al seguente:

	![Output dei risultati](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


4. Come si può notare, un controllo può portare a cinque diversi risultati:
	
	* Business not located 
	* Fail
	* Pass
	* Pss w/ conditions
	* Out of Business 

	Sviluppare ora un modello che può ricavare il risultato di un controllo degli alimenti, una volta determinate le violazioni. Poiché la regressione logistica è un metodo di classificazione binaria, è consigliabile raggruppare i dati in due categorie: **Fail** e **Pass**. "Pass w/ Conditions" fa parte della categoria Pass, quindi, quando si esegue il training del modello, i due risultati saranno considerati equivalenti. I dati con gli altri risultati ("Business Not Located", "Out of Business"), non essendo utili, verranno rimossi dal set di training. Non dovrebbero verificarsi problemi, perché queste due categorie costituiscono solo una percentuale minima dei risultati.

5. Ora si passerà alla conversione del frame di dati esistente (`df`) in un nuovo frame di dati in cui ogni controllo è rappresentato come coppia etichetta-violazioni. In questo caso, un'etichetta `0.0` rappresenta un controllo non superato, un'etichetta `1.0` rappresenta un controllo superato e un'etichetta `-1.0` rappresenta altri risultati, che verranno esclusi durante il calcolo del nuovo frame di dati.


		def labelForResults(s):
		    if s == 'Fail':
		        return 0.0
		    elif s == 'Pass w/ Conditions' or s == 'Pass':
		        return 1.0
		    else:
		        return -1.0
		label = UserDefinedFunction(labelForResults, DoubleType())
		labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


	Recuperare una riga dai dati con etichetta per verificare come appare.


		labeledData.take(1)


	Verrà visualizzato un output simile al seguente:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		[Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## Creare un modello di regressione logistica dal frame di dati di input

L'ultima attività è la conversione dei dati con etichetta in un formato che possa essere analizzato dalla regressione logistica. L'input per un algoritmo di regressione logistica deve essere un set di *coppie etichetta-vettore di funzionalità*, dove il "vettore di funzionalità" è un vettore di numeri che rappresenta il punto di ingresso. È quindi necessario poter convertire la colonna "violations", che è semistrutturata e contiene numerosi commenti in testo libero, in una matrice di numeri reali facilmente comprensibili per un computer.

Un approccio di apprendimento automatico standard per l'elaborazione del linguaggio naturale consiste nell'assegnare a ogni singola parola un "indice" e quindi nel passare un vettore all'algoritmo di apprendimento automatico in modo che ogni valore dell'indice contenga la frequenza relativa di tale parola nella stringa di testo.

MLLib consente di eseguire facilmente questa operazione. Prima di tutto, si suddividerà in token ogni stringa delle violazioni per ottenere le singole parole di ogni stringa e quindi si userà `HashingTF` per convertire ogni set di token in un vettore di funzionalità che può quindi essere passato all'algoritmo di regressione logistica per creare un modello. Tutti questi passaggi verranno eseguiti in sequenza con una "pipeline".


	tokenizer = Tokenizer(inputCol="violations", outputCol="words")
	hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
	lr = LogisticRegression(maxIter=10, regParam=0.01)
	pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
	
	model = pipeline.fit(labeledData)


## Valutare il modello in un set di dati di test separato

È possibile usare il modello creato prima per *stimare* quali saranno i risultati dei nuovi controlli, in base alle violazioni osservate. Il training di questo modello è stato eseguito nel set di dati **Food\_Inspections1.csv**. Si userà ora un secondo set di dati, **Food\_Inspections2.csv**, per *valutare* l'efficacia di questo modello sui nuovi dati. Questo secondo set di dati (**Food\_Inspections2.csv**) dovrebbe già essere nel contenitore di archiviazione predefinito associato al cluster.

1. Il frammento di codice seguente crea un nuovo frame di dati, **predictionsDf** contenente la stima generata dal modello. Il frammento di codice crea anche la tabella temporanea **Predictions** basata sul frame di dati.


		testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
	             .map(csvParse) \
	             .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
		testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
		predictionsDf = model.transform(testDf)
		predictionsDf.registerTempTable('Predictions')
		predictionsDf.columns


	Verrà visualizzato un output simile al seguente:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
		
		['id',
	     'name',
	     'results',
	     'violations',
	     'words',
	     'features',
	     'rawPrediction',
	     'probability',
	     'prediction']

2. Esaminare una delle stime. Eseguire questo frammento di codice:

		predictionsDf.take(1)

	La stima per la prima voce verrà visualizzata nel set di dati di test.

3. Il metodo `model.transform()` applicherà la stessa trasformazione ai nuovi dati con lo stesso schema e formulerà una stima per la classificazione dei dati. È possibile calcolare alcune semplici statistiche per comprendere l'accuratezza delle stime:


		numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
		                                      (prediction = 1 AND (results = 'Pass' OR 
		                                                           results = 'Pass w/ Conditions'))""").count()
		numInspections = predictionsDf.count()
		
		print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
		print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

	L'output è simile al seguente:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		There were 9315 inspections and there were 8087 successful predictions
	    This is a 86.8169618894% success rate


	L'uso della regressione logistica con Spark offre un modello accurato della relazione tra le descrizioni delle violazioni in inglese e la probabilità che una determinata azienda superi o meno un controllo degli alimenti.

## Creare una rappresentazione visiva della stima

Per comprendere meglio i risultati di questo test, ora è possibile creare una visualizzazione finale.

1. Iniziare dall'estrazione delle diverse stime e dei vari risultati dalla tabella temporanea **Predictions** creata in precedenza. Le query seguenti separano l'output in *true\_positive*, *false\_positive*, *true\_negative* e *false\_negative*. Nelle query seguenti disattivare la visualizzazione usando `-q` e tramite `-o` salvare l'output come frame di dati utilizzabili con il comando speciale `%%local`. 

		%%sql -q -o true_positive
		SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

		%%sql -q -o false_positive
		SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

		%%sql -q -o true_negative
		SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

		%%sql -q -o false_negative
		SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions') 

2. Usare infine il frammento di codice seguente per generare il tracciato con **Matplotlib**.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		labels = ['True positive', 'False positive', 'True negative', 'False negative']
		sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
		colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
		plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
		plt.axis('equal')
	
	Viene visualizzato l'output seguente.
	
	![Output delle stime](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


	In questo grafico un risultato positivo indica il controllo degli alimenti non superato, mentre un risultato negativo indica un controllo superato.

## Arrestare il notebook

Al termine dell'esecuzione dell'applicazione, è necessario arrestare il notebook per rilasciare le risorse. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt**. Questa operazione consente di arrestare e chiudere il notebook.


## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di un edificio con sistemi HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

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

<!---HONumber=AcomDC_0413_2016-->