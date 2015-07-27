<properties 
	pageTitle="ADAPT (Advanced Analytics Process and Technology) in azione: usare cluster Hadoop di HDInsight sul set di dati Criteo da 1 TB | Microsoft Azure" 
	description="Uso di ADAPT (Advanced Analytics Process and Technology) per uno scenario end-to-end in cui un cluster Hadoop di HDInsight viene usato per creare e distribuire un modello con un set di dati di grandi dimensioni (1 TB) disponibile pubblicamente" 
	services="machine-learning,hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# ADAPT (Advanced Analytics Process and Technology) in azione - Uso di cluster Hadoop di Azure HDInsight su un set di dati da 1 TB

In questa procedura dettagliata viene descritto come usare uno scenario end-to-end ADAPT (Advanced Analytics Process and Technology) con un [cluster Hadoop di Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) per archiviare, esplorare e sottocampionare i dati, nonché progettare caratteristiche, da uno dei set di dati [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponibili pubblicamente. Viene usato Azure Machine Learning per creare modelli di classificazione binaria e regressione per questi dati. Viene inoltre illustrato come pubblicare uno di questi modelli come servizio Web.

Per eseguire le attività presentate in questa procedura dettagliata, è anche possibile usare iPython Notebook. Se si vuole provare questo approccio, vedere l'argomento relativo alla [procedura dettagliata Criteo con una connessione Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).


## <a name="dataset"></a>Descrizione del set di dati Criteo

I dati Criteo sono un set di dati di stima dei clic raccolti in file TSV compressi nel formato gzip con dimensioni di circa 370 GB (circa 1,3 TB non compressi) e includono più di 4,3 miliardi di record. I valori sono ottenuti da 24 giorni di dati sui clic resi disponibili da [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Per semplificare il lavoro dei data scientist, sono disponibili dati non compressi con cui eseguire l'esperimento.

Ogni record nel set di dati contiene 40 colonne:

- la prima colonna è una colonna di etichetta che indica se un utente fa clic (valore 1) o meno (valore 0) su un annuncio 
- le successive 13 colonne sono di tipo numerico 
- le ultime 26 colonne sono di tipo categorico 

Le colonne sono rese anonime e usano una serie di nomi enumerati: da "Col1" (per la colonna di etichetta) a "Col40" (per l'ultima colonna categorica).

Di seguito è riportato un estratto delle prime 20 colonne di due osservazioni (righe) di questo set di dati:

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Il set di dati presenta valori mancanti sia nelle colonne numeriche sia in quelle categoriche. Di seguito viene descritto un semplice metodo per la gestione dei valori mancanti. Altri dettagli relativi ai dati sono illustrati più avanti, quando viene eseguita l'archiviazione nelle tabelle Hive.

**Definizione:** *percentuale di click-through (CTR, Clickthrough Rate):* indica la percentuale di clic nei dati. In questo set di dati Criteo, il valore corrisponde circa al 3,3% o 0,033.

## <a name="mltasks"></a>Esempi di attività di stima
Questa procedura dettagliata illustra due problemi di stima di esempio:

1. **Classificazione binaria**: stima se un utente ha fatto o meno clic su un annuncio:
	- Classe 0: nessun clic
	- Classe 1: clic

2. **Regressione**: stima la probabilità di un clic su un annuncio in base alle caratteristiche dell'utente.


## <a name="setup"></a>Configurare un cluster Hadoop di HDInsight per l'analisi scientifica

**Nota:** in genere, questa attività viene svolta da un **amministratore**.

Per configurare l'ambiente di analisi scientifica dei dati di Azure per la creazione di soluzioni di analisi predittiva con i cluster HDInsight, sono necessari tre passaggi:

1. [Creare un account di archiviazione](../storage-whatis-account.md): l'account di archiviazione viene usato per archiviare i dati nell'archivio BLOB di Azure. I dati usati nei cluster HDInsight vengono archiviati in questa posizione.

2. [Personalizzare i cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati](machine-learning-data-science-customize-hadoop-cluster.md): questo passaggio consente di creare un cluster Hadoop di Azure HDInsight con la versione a 64 bit di Anaconda Python 2.7 installata in tutti i nodi. Quando si personalizza il cluster HDInsight, ci sono due importanti passaggi (descritti in questo argomento) da completare.

	* È necessario collegare l'account di archiviazione creato nel passaggio 1 al cluster HDInsight al momento della creazione. Questo account di archiviazione viene usato per accedere ai dati che possono essere elaborati all'interno del cluster.

	* È necessario abilitare l'accesso remoto al nodo head del cluster dopo la creazione. Ricordare le credenziali di accesso remoto specificate in questa posizione (diverse da quelle specificate per il cluster durante la creazione), che saranno necessarie in seguito.

3. [Creare un'area di lavoro di Azure ML](machine-learning-create-workspace.md): l'area di lavoro di Azure Machine Learning viene usata per la creazione di modelli di Machine Learning dopo l'esplorazione iniziale dei dati e il loro sottocampionamento nel cluster HDInsight.

## <a name="getdata"></a>Ottenere e utilizzare i dati da un'origine pubblica

È possibile accedere al set di dati [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) facendo clic sul collegamento, accettando le condizioni per l'utilizzo e specificando un nome. Di seguito è illustrato uno snapshot della pagina:

![Accettazione delle condizioni Criteo](http://i.imgur.com/hLxfI2E.png)

Fare clic su **Continue to Download** per leggere altre informazioni sul set di dati e sulla sua disponibilità.

I dati si trovano in un [archivio BLOB di Azure](../storage-dotnet-how-to-use-blobs.md) pubblico: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. La sintassi "wasb" indica la posizione di archiviazione BLOB di Azure.

1. I dati in questo archivio BLOB pubblico sono costituiti da tre sottocartelle di dati non compressi.
		
	1. La sottocartella *raw/count/* contiene i primi 21 giorni di dati, da day_00 a day_20
	2. La sottocartella *raw/train/* è costituita da un singolo giorno di dati, day_21
	3. La sottocartella *raw/test/* è costituita da due giorni di dati, day_22 e day_23

2. Se si vuole iniziare usando i dati in formato gzip non elaborati, questi sono disponibili nella cartella principale *raw/* e indicati come day_NN.gz, dove NN va da 00 a 23.

Un approccio alternativo per accedere ai dati, esplorarli e modellarli senza eseguire alcun download locale è descritto più avanti in questa procedura dettagliata, al momento della creazione delle tabelle Hive.

## <a name="login"></a>Accedere al nodo head del cluster

Per accedere al nodo head del cluster, usare il portale di [gestione di Azure](manage.windowsazure.com) per individuare il cluster. Fare clic sull'icona di HDInsight a forma di elefante a sinistra e quindi fare doppio clic sul nome del cluster. Passare alla scheda **Configurazione**, fare doppio clic sull'icona CONNETTI nella parte inferiore della pagina e immettere le credenziali di accesso remoto quando richiesto. In questo modo, verrà visualizzato il nodo head del cluster.

Di seguito è illustrata la tipica finestra visualizzata al primo accesso al nodo head del cluster:

![Accesso al cluster](http://i.imgur.com/Yys9Vvm.png)

A sinistra è presente la riga di comando di Hadoop, che verrà usata per l'esplorazione di dati. Sono inoltre disponibili due utili URL, "Hadoop Yarn Status" e "Hadoop Name Node". Il primo URL mostra lo stato del processo, mentre il secondo fornisce informazioni dettagliate sulla configurazione del cluster.

Ora che la configurazione è stata completata, è possibile iniziare la prima parte della procedura dettagliata, ovvero l'esplorazione dei dati tramite Hive e la loro preparazione per Azure Machine Learning.

## <a name="hive-db-tables"></a> Creare il database e le tabelle Hive

Per creare le tabelle Hive per il set di dati Criteo, aprire la ***riga di comando di Hadoop*** sul desktop del nodo head e specificare la directory Hive immettendo il comando

    cd %hive_home%\bin

**NOTA IMPORTANTE**: **eseguire tutti i comandi di Hive in questa procedura dettagliata dal prompt della directory bin/ Hive indicato sopra. In questo modo, eventuali problemi di percorso verranno risolti automaticamente. I termini "prompt della directory Hive", "prompt della directory bin/ Hive" e "riga di comando di Hadoop" verranno usati in modo intercambiabile in questo documento.**

**NOTA IMPORTANTE 2**: **per eseguire qualsiasi query Hive, è sempre possibile usare il comando ** cd %hive_home%\bin hive

Dopo che viene visualizzata la shell REPL Hive con l'indicazione "hive >", è sufficiente tagliare e incollare la query per eseguirla.

Il codice seguente crea un database "criteo" e quindi genera 4 tabelle:


* una *tabella di conteggio* basata sui giorni da 0 a 20 (da day_00 a day_20) 
* una *tabella di training* basata sul giorno 21 (day_21) 
* due *tabelle di test* basate rispettivamente sul giorno 22 (day_22) e 23 (day_23). 

Il set di dati di test è suddiviso in due diverse tabelle perché uno dei giorni è festivo e si vuole determinare se il modello è in grado di rilevare le differenze tra un giorno festivo e uno non festivo in base alla percentuale di click-through.

Lo script [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) è visualizzato sotto per praticità:

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

È possibile notare che queste tabelle sono tutte esterne in quanto si punta semplicemente a posizioni di archiviazione BLOB di Azure (wasb).

**Ci sono due modi per eseguire QUALSIASI query Hive, come viene spiegato di seguito.**

1. **Usando la riga di comando REPL Hive**: il primo modo consiste nell'eseguire un comando "hive" e copiare e incollare la query precedente nella riga di comando REPL Hive. A tale scopo, eseguire l'operazione seguente:

		cd %hive_home%\bin
		hive

 	A questo punto, tagliando e incollando la query nella riga di comando REPL questa viene eseguita.

2. **Salvando le query in un file ed eseguendo il comando**: il secondo modo consiste nel salvare le query in un file con estensione hql ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e quindi eseguire il comando seguente per eseguire la query:

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### Verificare la creazione del database e della tabella

Verrà quindi verificata la creazione del database eseguendo il comando seguente dal prompt della directory bin/ Hive:

		hive -e "show databases;"

Il risultato è il seguente:

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

Ciò conferma la creazione del nuovo database, "criteo".

Per vedere quali tabelle sono state create, è sufficiente eseguire il comando seguente dal prompt della directory bin/ Hive:

		hive -e "show tables in criteo;"

Verrà quindi visualizzato l'output seguente:

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a> Esplorazione dei dati in Hive

A questo punto, è possibile passare ad alcune attività di base di esplorazione dei dati in Hive. Innanzitutto, viene contato il numero di esempi nelle tabelle di dati di training e di test.

### Numero di esempi di training

Il contenuto di [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) è illustrato di seguito:

		SELECT COUNT(*) FROM criteo.criteo_train;

Il risultato è il seguente:

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

In alternativa, è anche possibile eseguire il comando seguente dal prompt della directory bin/ Hive:

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### Numero di esempi di test nei due set di dati di test

Viene ora contato il numero di esempi di test nei due set di dati di test. Il contenuto di [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) è illustrato di seguito:

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Il risultato è il seguente:
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

Come di consueto, è anche possibile chiamare lo script dal prompt della directory bin/ Hive eseguendo il comando seguente:

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Infine, viene esaminato il numero di esempi di test nel set di dati di test basato sul giorno 23 (day_23).

Il comando per questo scopo è simile a quello illustrato in precedenza (vedere [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Il risultato è il seguente:
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### Distribuzione delle etichette nel set di dati di training

La distribuzione delle etichette nel set di dati di training è un aspetto interessante. Per vederla, è necessario visualizzare il contenuto di [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Il risultato sarà la distribuzione delle etichette:

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

Si noti che la percentuale di etichette positive equivale circa al 3,3% (coerente con il set di dati originale).
		
### Distribuzioni nell'istogramma di alcune variabili numeriche nel set di dati di training

È possibile usare la funzione "histogram_numeric" nativa di Hive per visualizzare la distribuzione delle variabili numeriche. Il contenuto di [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) è indicato di seguito:

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Il risultato è il seguente:

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

La combinazione LATERAL VIEW - explode in Hive serve a produrre un output simile a quello di SQL anziché il solito elenco. Si noti che nella tabella precedente la prima colonna corrisponde al centro delle classi di dati (bin) e la seconda alla loro frequenza.

### Percentili approssimativi di alcune variabili numeriche nel set di dati di training

Un altro aspetto interessante in relazione alle variabili numeriche è il calcolo dei percentili approssimativi. A tale scopo, è disponibile la funzione "percentile_approx" nativa di Hive. Il contenuto di [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) è il seguente:

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Il risultato è il seguente:

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

Tenere presente che la distribuzione dei percentili è in genere strettamente correlata alla distribuzione nell'istogramma di qualsiasi variabile numerica.

### Trovare il numero di valori univoci per alcune colonne categoriche nel set di dati di training

Continuando l'esplorazione dei dati viene ora trovato, per alcune colonne categoriche, il numero di valori univoci accettati. A tale scopo, viene visualizzato il contenuto di [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Il risultato è il seguente:

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

Si noti che Col15 ha 19 milioni di valori univoci. Non è possibile usare tecniche di base come la "codifica one-hot" per codificare variabili categoriche con dimensionalità così elevata. In particolare, viene spiegata e illustrata una tecnica avanzata e affidabile di [apprendimento in base ai conteggi](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) per affrontare il problema in modo efficiente.

Come ultima operazione di questa sottosezione, viene esaminato il numero di valori univoci per altre colonne categoriche. Il contenuto di [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) è il seguente:

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

Il risultato è il seguente:

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

Anche in questo caso è possibile notare che, ad eccezione di Col20, tutte le altre colonne includono numerosi valori univoci.

### Conteggio della co-occorrenza di coppie di variabili categoriche nel set di dati di training

Anche il conteggio della co-occorrenza di coppie di variabili categoriche è un aspetto interessante. Per determinarlo, è possibile usare il codice in [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

In questo caso, i conteggi vengono visualizzati in ordine inverso in base all'occorrenza e vengono osservati i primi 15 risultati. Il risultato è il seguente:

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Sottocampionare i set di dati per Azure Machine Learning

Dopo avere esplorato i set di dati e avere visto come eseguire questo tipo di esplorazione per qualsiasi variabile (inclusione le combinazioni), è ora possibile sottocampionare i set di dati in modo da poter creare modelli in Azure Machine Learning. Tenere presente il problema centrale, ovvero dato un set di attributi di esempio (valori delle caratteristiche da Col2 a Col40), si deve stimare se Col1 è 0 (nessun clic) o 1 (clic).

Per sottocampionare i set di dati di training e di test riducendoli all'1% delle dimensioni originali, è possibile usare la funzione RAND() nativa di Hive. Lo script successivo, [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) consente di eseguire questa operazione per il set di dati di training:

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Il risultato è il seguente:

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

Lo script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) esegue la stessa operazione per i dati di test del giorno 22 (day_22):

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Il risultato è il seguente:

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


Infine, lo script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) esegue l'operazione per i dati di test del giorno 23 (day_23):

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Il risultato è il seguente:

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

A questo punto, è possibile usare i set di dati di training e di test sottocampionati per creare modelli in Azure Machine Learning.

Prima di passare ad Azure Machine Learning, c'è un importante componente finale, relativo alla tabella di conteggio. Questo aspetto verrà analizzato in modo più dettagliato nella sottosezione successiva.

##<a name="count"></a> Breve discussione sulla tabella di conteggio

Come è stato visto, diverse variabili categoriche hanno una dimensionalità molto elevata. Nella procedura dettagliata viene presentata una tecnica avanzata, detta tecnica di [apprendimento in base ai conteggi](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx), che consente di codificare queste variabili in modo affidabile ed efficiente. Altre informazioni su questa tecnica sono disponibili facendo clic sul relativo collegamento.

**Nota:** in questa procedura dettagliata viene esaminato l'uso di tabelle di conteggio per produrre rappresentazioni compatte di caratteristiche categorie a elevata dimensionalità. Questa tecnica non è assolutamente l'unico modo disponibile per codificare le caratteristiche categoriche. Per altre informazioni su tecniche diverse, è possibile vedere gli argomenti relativi a [codifica one-hot](http://en.wikipedia.org/wiki/One-hot) e [hashing delle caratteristiche](http://en.wikipedia.org/wiki/Feature_hashing).

Per creare tabelle di conteggio sui dati di conteggio, vengono usati i dati nella cartella raw/count. Nella sezione relativa alla modellazione viene illustrato come creare da zero queste tabelle di conteggio per le caratteristiche categoriche oppure, in alternativa, come usare una tabella di conteggio preesistente per le analisi. Con "tabelle di conteggio preesistenti" si intendono le tabelle fornite da Microsoft. Di seguito sono disponibili istruzioni dettagliate su come accedere a queste tabelle.

## <a name="aml"></a> Creare un modello con Azure Machine Learning

Il processo di creazione di un modello in Azure Machine Learning prevede i passaggi seguenti:

1. [Ottenere i dati dalle tabelle Hive in Azure Machine Learning](#step1)
2. [Creare l'esperimento: pulire i dati, scegliere uno strumento di apprendimento e definire le caratteristiche con le tabelle di conteggio](#step2)
3. [Eseguire il training del modello](#step3)
4. [Assegnare un punteggio al modello in base ai dati di test](#step4)
5. [Valutare il modello](#step5)
6. [Pubblicare il modello come servizio Web per consentirne l'utilizzo](#step6)

È ora possibile creare modelli in Azure Machine Learning Studio. I dati sottocampionati vengono salvati come tabelle Hive nel cluster. Per leggere i dati verrà usato il modulo Reader di Azure Machine Learning. Le credenziali per accedere all'account di archiviazione del cluster sono indicate sotto.

### <a name="step1"></a> Passaggio 1: Ottenere i dati dalle tabelle Hive in Machine Learning usando il modulo Reader e selezionarli per un esperimento di Machine Learning

Per il modulo **Reader** i valori dei parametri impostati nel grafico sono solo esempi. Di seguito sono illustrate le indicazioni generali per la "compilazione" del set di parametri per il modulo **Reader**.

1. Scegliere "Hive query" come origine dati
2. Per la query Hive è sufficiente specificare l'istruzione SELECT * FROM <nome_database.nome_tabella>.
3. Hcatalog server URI: se il cluster è "abc", specificare https://abc.azurehdinsight.net
4. Hadoop user account name: nome utente scelto al momento dell'autorizzazione del cluster (NON il nome utente di accesso remoto)
5. Hadoop user account password: password per il nome utente indicato sopra, scelta al momento dell'autorizzazione del cluster (NON la password di accesso remoto)
6. Location of output data: scegliere "Azure"
7. Azure storage account name: account di archiviazione associato al cluster
8. Azure storage account key: chiave dell'account di archiviazione associato al cluster
9. Azure container name: se il nome del cluster è "abc", in genere questo valore è semplicemente "abc" 

Ecco l'aspetto del modulo **Reader** durante il recupero dei dati dalla tabella Hive:

![Il lettore ottiene i dati](http://i.imgur.com/i3zRaoj.png)

Quando il modulo **Reader** termina il recupero di dati (il completamento è indicato da un segno di spunta verde nel modulo), salvare i dati come set di dati (con un nome di propria scelta). L'aspetto è il seguente:

![Il lettore salva i dati](http://i.imgur.com/oxM73Np.png)


Fare clic con il pulsante destro del mouse sulla porta di output del modulo **Reader**. Verranno visualizzate le opzioni **Save as dataset** e **Visualize**. Facendo clic sull'opzione **Visualize** vengono visualizzati 100 righe di dati e un pannello, sul lato destro, utile per le statistiche di riepilogo. Per salvare i dati, è sufficiente selezionare **Save as dataset** e seguire le istruzioni.

Per selezionare il set di dati salvato per l'uso in un esperimento di Machine Learning, individuare il set di dati usando la casella **Search** illustrata di seguito. Digitare quindi una parte del nome del set di dati per accedervi e trascinare il set di dati nel pannello principale. Rilasciando il set di dati sul pannello principale, questo viene selezionato per la modellazione in Machine Learning.

![Individuazione del set di dati](http://i.imgur.com/rx4nnUH.png)

Eseguire questa operazione per entrambi i set di dati, di training e di test.

### <a name="step2"></a>Passaggio 2: Creare un semplice esperimento in Azure Machine Learning per stimare i clic o l'assenza di clic

Viene prima illustrata una semplice architettura di esperimento, quindi vengono analizzate in modo più dettagliato le specifiche. Innanzitutto vengono puliti i dati, quindi viene scelto uno strumento di apprendimento e infine viene illustrato come definire le caratteristiche con tabelle di conteggio preesistenti o create da zero dall'utente.

![Architettura dell'esperimento](http://i.imgur.com/R4iTLYi.png)

Per eseguire l'analisi dettagliata, si partirà dai set di dati salvati, come illustrato.

Il modulo **Clean Missing Data** pulisce i dati mancanti in un modo che può essere specificato dall'utente. Analizzando il modulo, è possibile vedere quanto segue:

![Pulizia dei dati mancanti](http://i.imgur.com/0ycXod6.png)

In questo caso, si sceglie di sostituire tutti i valori mancanti con 0. Sono disponibili anche altre opzioni, che è possibile visualizzare aprendo gli elenchi a discesa nel modulo.

È quindi necessario scegliere lo strumento di apprendimento. Verrà usato un albero delle decisioni con boosting a due classi. In particolare, verrà illustrato come usare le funzionalità di conteggio per caratteristiche categoriche con dimensionalità elevata per creare rappresentazioni compatte del modello, nonché per operazioni di training e test efficienti.

Le tabelle di conteggio sono conteggi condizionali di classi e talvolta vengono indicate semplicemente come "conteggi condizionali". Essenzialmente, rappresentano un metodo per conteggiare i valori di una caratteristica per ogni classe e usare questi conteggi per calcolare i log-odd.


#### Ottenere l'accesso alle tabelle di conteggio preesistenti per la modellazione

Per ottenere l'accesso alle tabelle di conteggio preesistenti, fare clic su **Raccolta**, come illustrato di seguito:

![Raccolta](http://i.imgur.com/TsWkig3.png)

Facendo clic su **Raccolta** viene visualizzata una pagina simile alla seguente:

![Pagina principale della raccolta](http://i.imgur.com/dmXo0KR.png)

In questa pagina eseguire una ricerca per "criteo counts" e scorrere l'elenco di risultati. Dovrebbe essere visualizzato:

![Conteggi Criteo](http://i.imgur.com/JZ119Jf.png)

Facendo clic su questo esperimento viene visualizzata una pagina simile alla seguente:

![Conteggi](http://i.imgur.com/dxdjMjh.png)

In questa pagina fare clic su **Apri in Studio** per copiare l'esperimento nell'area di lavoro. In questo modo, verranno automaticamente copiati anche i set di dati. In questo caso, i due set di dati principali di interesse sono la tabella di conteggio e i metadati di conteggio, che verranno descritti più dettagliatamente.

#### Caratteristiche di conteggio nel set di dati

I moduli successivi nell'esperimento comportano l'uso di tabelle di conteggio preesistenti. Per usare queste tabelle di conteggio preesistenti, cercare "cr_count_" nella scheda Search di un nuovo esperimento. Dovrebbero venire visualizzati due set di dati: "cr_count_cleanednulls_metadata" e "cr_count_table_cleanednulls". Trascinare entrambi i set di dati e rilasciarli sul riquadro dell'esperimento a destra. Facendo clic con il pulsante destro del mouse sulle porte di output è possibile, come sempre, visualizzare l'aspetto.

La visualizzazione dei metadati della tabella di conteggio è simile alla seguente:

![Metadati tabella di conteggio](http://i.imgur.com/A39PIe7.png)

Si noti che i metadati contengono informazioni sulle colonne in base a cui sono stati creati i conteggi condizionali, sul fatto che sia stato o meno usato un dizionario per la creazione (in alternativa è possibile usare l'algoritmo count-min sketch), sul valore di inizializzazione dell'hash usato, sul numero di bit di hash usati per l'hashing delle caratteristiche, sul numero di classi e altri dati analoghi.

La visualizzazione della tabella di conteggio è simile alla seguente:

![Tabella di conteggio](http://i.imgur.com/NJn1EQO.png)

Come è possibile vedere, la tabella di conteggio contiene informazioni sui conteggi condizionali di classe. Il valore delle caratteristiche categoriche è indicato in "Hash Value", pertanto anche per le caratteristiche viene eseguito l'hashing.

Come vengono integrate le caratteristiche di conteggio nei set di dati? A tale scopo, viene usato il modulo di conteggio **Featurizer**, illustrato di seguito:

![Modulo Count Featurizer](http://i.imgur.com/dnMdrR1.png)

Una volta creata la tabella conteggio (tenere presente che si stanno producendo conteggi condizionali di classe delle caratteristiche categoriche) viene usato il modulo **Count Featurizer** illustrato in precedenza per integrare queste caratteristiche di conteggio nel set di dati. Come è possibile notare, il modulo **Featurizer** consente di scegliere le caratteristiche da usare per il conteggio, indicare se si desidera ottenere solo log-odd o anche i conteggi, nonché impostare altre opzioni avanzate.

#### Creare una tabella di conteggio da zero

Come indicato nella sezione "Breve discussione sulla tabella di conteggio", oltre a usare tabelle di conteggio preesistenti, come illustrato in dettaglio nelle sezioni precedenti, è possibile anche creare tabelle di conteggio da zero.

In questa sezione viene illustrato come creare una tabella di conteggio da zero. A tale scopo, viene usato il modulo **Build Count Table**, illustrato di seguito con le relative impostazioni:

![Modulo Build Count Table](http://i.imgur.com/r7pP8Qq.png)

Il gruppo finale di impostazioni per il modulo è il seguente:

![Impostazioni modulo Build Count Table](http://i.imgur.com/PvmSh3C.png)


**Nota importante:** per le impostazioni del cluster e dell'account di archiviazione, usare i propri valori pertinenti.

Fare clic su **Run** per creare le tabelle di conteggio nel cluster selezionato. L'output è costituito, come illustrato in precedenza, dalla tabella di conteggio e dai metadati associati. Quando le tabelle sono pronte, è possibile creare l'esperimento.


### <a name="step3"></a> Passaggio 3: Eseguire il training del modello

Per eseguire questa operazione, digitare "two class boosted" nella casella di ricerca e trascinare il modulo. Vengono usati i valori predefiniti dello strumento di apprendimento albero delle decisioni con boosting, come illustrato di seguito:

![Strumento di apprendimento albero delle decisioni con boosting](http://i.imgur.com/dDk0Jtv.png)

Prima di eseguire l'esperimento ML sono necessari tre componenti finali.

Il primo è un modulo Train Model. Mentre la prima porta accetta lo strumento di apprendimento come input, la seconda porta accetta il set di dati di training per l'apprendimento. Di seguito sono illustrati il modulo e i parametri da impostare nel modulo.

![Connessioni del modulo Train Model per l'albero delle decisioni con boosting](http://i.imgur.com/szS2xBb.png)

![Impostazioni del modulo Train Model per l'albero delle decisioni con boosting](http://i.imgur.com/nd7lHBL.png)

### <a name="step4"></a> Passaggio 4: Assegnare un punteggio al modello in base a un set di dati di test

Il secondo componente è un modo per assegnare un punteggio in base al set di dati di test. A tale scopo, è sufficiente usare il modulo **Score Model**, che accetta come input il modello preparato con i dati di training e il set di dati di test in base a cui eseguire le stime. L'aspetto è il seguente.

![Connessioni del modulo Score Model per l'albero delle decisioni con boosting](http://i.imgur.com/AwIH1rH.png)

### <a name="step5"></a> Passaggio 5: Valutare il modello

Infine, si desidera analizzare le prestazioni del modello. In genere, per i problemi di classificazione a due classi (binaria), un buon metodo di misurazione è costituito dall'area sottesa alla curva (AUC). Per visualizzare quest'area, è necessario collegare il modulo "Score Model" a un modulo "Evaluate Model". Fare clic su **Visualize** nel modulo **Evaluate Model** per visualizzare un grafico simile al seguente:

![Modulo di valutazione del modello per l'albero delle decisioni con boosting](http://i.imgur.com/0Tl0cdg.png)

Per i problemi di classificazione binaria (o a due classi), un buon metodo di misurazione dell'accuratezza della stima è costituito dall'area sottesa alla curva (AUC). Di seguito sono illustrati i risultati dell'uso del modello sul set di dati di test. Per ottenere i risultati, fare clic con il pulsante destro del mouse sulla porta di output del modulo **Evaluate Model** e quindi fare clic su **Visualize**.

![Visualizzazione del modulo Evaluate Model](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> Passaggio 6: Pubblicare il modello come servizio Web
Molto interessante è la possibilità di pubblicare i modelli di Machine Learning come servizi Web. Al termine dell'operazione, è possibile effettuare chiamate al servizio Web usando i dati per i quali sono necessarie stime e il modello restituirà una stima di qualche tipo.

A tale scopo, è innanzitutto necessario salvare il modello sottoposto a training come oggetto Trained Model. Fare clic con il pulsante destro del mouse su **Train Model** e scegliere "Save as Trained Model".

È quindi necessario creare una porta di input e una di output per il servizio Web: una porta di input che accetta dati nello stesso formato dei dati per i quali sono necessarie stime e una porta di output che restituisce le etichette con i punteggi e le probabilità associate.

#### Selezionare alcune righe di dati per la porta di input

Viene ora illustrato come selezionare solo poche righe di dati per la porta di input.

![Dati porta di input](http://i.imgur.com/XqVtSxu.png)

Per praticità, è possibile usare una trasformazione Apply SQL per selezionare solo 10 righe come dati della porta di input.

#### Servizio Web
È ora possibile eseguire un piccolo esperimento che può essere usato per pubblicare il servizio Web.

#### Generare i dati di input per il servizio Web

Come passaggio iniziale, poiché la tabella di conteggio è grande, vengono prese poche righe di dati di test e da esse vengono generati i dati di output con le caratteristiche di conteggio. Questo è il formato dei dati di input per il servizio Web, come illustrato di seguito:

![Creazione dati di input per l'albero delle decisioni con boosting](http://i.imgur.com/OEJMmst.png)

Nota: per il formato dei dati di input, verrà ora usato l'OUTPUT del modulo **Count Featurizer**. Una volta terminata l'esecuzione dell'esperimento, salvare l'output del modulo **Count Featurizer** come set di dati. **Nota importante:** il set di dati verrà usato per i dati di input nel servizio Web.

#### Esperimento di assegnazione dei punteggi per la pubblicazione del servizio Web

Innanzitutto, viene illustrato l'aspetto. La struttura essenziale è un modulo Score Model che accetta l'oggetto modello sottoposto a training e poche righe di dati di input generate nei passaggi precedenti usando il modulo **Count Featurizer**. Viene usato il modulo "Project Columns" per ottenere le etichette con i punteggi e le probabilità di stima.

![Selezione delle colonne](http://i.imgur.com/kRHrIbe.png)

È interessante vedere in che modo è possibile usare il modulo Project Columns per filtrare i dati da escludere da un set di dati. Il contenuto è illustrato di seguito:

![Filtro tramite il modulo Project Columns](http://i.imgur.com/oVUJC9K.png)

Per ottenere le porte di input e di output indicate in blu, è sufficiente fare clic su "prepare webservice" in basso a destra. L'esecuzione di questo esperimento consente anche di pubblicare il servizio Web facendo clic sull'icona **PUBLISH WEB SERVICE** in basso a destra, illustrata di seguito.

![Pubblicazione del servizio Web](http://i.imgur.com/WO0nens.png)

Dopo la pubblicazione del servizio Web, si viene reindirizzati a una pagina simile alla seguente:

![](http://i.imgur.com/YKzxAA5.png)

A sinistra sono presenti due collegamenti per i servizi Web:

* Il collegamento **REQUEST/RESPONSE** (o RRS) serve per le stime singole e verrà usato in questo workshop. 
* Il collegamento **BATCH EXECUTION** (BES) viene usato per le stime in batch e richiede che i dati per i quali effettuare le stime si trovino in un BLOB di Azure.

Facendo clic sul collegamento **REQUEST/RESPONSE** viene visualizzata una pagina che fornisce codice predefinito in C#, Python ed R. Questo codice può essere usato in modo semplice per effettuare chiamate al servizio Web. Si noti che la chiave API in questa pagina deve essere usata per l'autenticazione.

È consigliabile copiare questo codice Python in una nuova cella di iPython Notebook.

Di seguito è illustrato un segmento di codice Python con la chiave API corretta.

![Codice Python](http://i.imgur.com/f8N4L4g.png)

Si noti che la chiave API predefinita è stata sostituita con la chiave API dei servizi Web. Facendo clic su "Run" in questa cella in iPython Notebook viene restituita la risposta seguente:

![Risposta iPython](http://i.imgur.com/KSxmia2.png)

Si noti che per i due esempi di test chiesti (nel framework JSON dello script Python) si ottengono le risposte nel formato "Scored Labels, Scored Probabilities". In questo caso, sono stati scelti i valori predefiniti forniti dal codice predefinito (0 per tutte le colonne numeriche, la stringa "value" per tutte le colonne categoriche).

Con questa osservazione si conclude la discussione sulla gestione di scenari end-to-end di set di dati di grandi dimensioni con Azure ML durante la quale, partendo da un terabyte di dati si è arrivati alla distribuzione di un modello di stima come servizio Web nel cloud.

 

<!---HONumber=July15_HO2-->