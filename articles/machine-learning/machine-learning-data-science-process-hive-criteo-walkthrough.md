<properties
	pageTitle="Processo di analisi scientifica dei dati per i team: uso di cluster Hadoop di HDInsight sul set di dati Criteo da 1 TB | Microsoft Azure"
	description="Uso del Processo di analisi scientifica dei dati per i team per uno scenario end-to-end in cui un cluster Hadoop di HDInsight viene usato per creare e distribuire un modello con un set di dati di grandi dimensioni (1 TB) disponibile pubblicamente"
	services="machine-learning,hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />  

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="bradsev" />  

# Processo di analisi scientifica dei dati per i team in azione: uso di cluster Hadoop di Azure HDInsight su un set di dati da 1 TB

In questa procedura dettagliata viene descritto come usare in uno scenario end-to-end il Processo di analisi scientifica dei dati per i team con un [cluster Hadoop di Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) per archiviare, esplorare e sottocampionare i dati, nonché progettare caratteristiche, da uno dei set di dati [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponibili pubblicamente. Viene usato Azure Machine Learning per creare un modello di classificazione binaria in questi dati. Viene inoltre illustrato come pubblicare uno di questi modelli come servizio Web.

Per eseguire le attività presentate in questa procedura dettagliata, è anche possibile usare IPython Notebook. Se si vuole provare questo approccio, vedere l'argomento [Criteo walkthrough using a Hive ODBC connection](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) (Procedura dettagliata su Criteo con una connessione Hive ODBC).


## <a name="dataset"></a>Descrizione del set di dati Criteo

I dati Criteo sono un set di dati di stima dei clic raccolti in file TSV compressi nel formato gzip con dimensioni di circa 370 GB (circa 1,3 TB non compressi) e includono più di 4,3 miliardi di record. I valori sono ottenuti da 24 giorni di dati sui clic resi disponibili da [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Per semplificare il lavoro dei data scientist, sono disponibili dati non compressi con cui eseguire l'esperimento.

Ogni record nel set di dati contiene 40 colonne:

- la prima colonna è una colonna di etichetta che indica se un utente fa clic su un **annuncio** (valore 1) o non fa clic (valore 0)
- le successive 13 colonne sono di tipo numerico
- le ultime 26 colonne sono di tipo categorico

Le colonne sono rese anonime e usano una serie di nomi enumerati: da "Col1" (per la colonna di etichetta) a "Col40" (per l'ultima colonna categorica).

Di seguito è riportato un estratto delle prime 20 colonne di due osservazioni (righe) di questo set di dati:

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Il set di dati presenta valori mancanti sia nelle colonne numeriche sia in quelle categoriche. Di seguito viene descritto un semplice metodo per la gestione dei valori mancanti. Altri dettagli relativi ai dati sono illustrati quando vengono archiviati nelle tabelle Hive.

**Definizione:** *percentuale di click-through (CTR, Clickthrough Rate):* indica la percentuale di clic nei dati. In questo set di dati Criteo, il valore corrisponde circa al 3,3% o 0,033.

## <a name="mltasks"></a>Esempi di attività di stima
Questa procedura dettagliata illustra due problemi di stima di esempio:

1. **Classificazione binaria**: stima se un utente ha fatto clic su un annuncio:
	- Classe 0: nessun clic
	- Classe 1: clic

2. **Regressione**: stima la probabilità di un clic su un annuncio in base alle caratteristiche dell'utente.


## <a name="setup"></a>Configurare un cluster Hadoop di HDInsight per l'analisi scientifica

**Nota:** in genere, questa attività viene svolta da un **amministratore**.

Per configurare l'ambiente di analisi scientifica dei dati di Azure per la creazione di soluzioni di analisi predittiva con i cluster HDInsight, sono necessari tre passaggi:

1. [Creare un account di archiviazione](../storage/storage-create-storage-account.md): l'account di archiviazione viene usato per archiviare i dati nell'archivio BLOB di Azure. I dati usati nei cluster HDInsight vengono archiviati in questa posizione.

2. [Personalizzare i cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati](machine-learning-data-science-customize-hadoop-cluster.md): questo passaggio consente di creare un cluster Hadoop di Azure HDInsight con la versione a 64 bit di Anaconda Python 2.7 installata in tutti i nodi. Quando si personalizza il cluster HDInsight, occorre completare due importanti passaggi descritti in questo argomento.

	* È necessario collegare l'account di archiviazione creato nel passaggio 1 al cluster HDInsight al momento della creazione. Questo account di archiviazione viene usato per accedere ai dati che possono essere elaborati all'interno del cluster.

	* È necessario abilitare l'accesso remoto al nodo head del cluster dopo la creazione. Ricordare le credenziali di accesso remoto specificate qui, che sono diverse da quelle specificate durante la creazione del cluster, perché sono necessarie per completare le procedure seguenti.

3. [Creare un'area di lavoro di Azure ML](machine-learning-create-workspace.md): l'area di lavoro di Azure Machine Learning viene usata per la creazione di modelli di Machine Learning dopo l'esplorazione iniziale dei dati e il loro sottocampionamento nel cluster HDInsight.

## <a name="getdata"></a>Ottenere e utilizzare i dati da un'origine pubblica

È possibile accedere al set di dati [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) facendo clic sul collegamento, accettando le condizioni per l'utilizzo e specificando un nome. Ecco uno snapshot di come appare:

![Accettazione delle condizioni Criteo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/hLxfI2E.png)  

Fare clic su **Continue to Download** (Continuare per il download) per leggere altre informazioni sul set di dati e sulla relativa disponibilità.

I dati si trovano in un [archivio BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md) pubblico: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. La sintassi "wasb" indica la posizione di archiviazione BLOB di Azure.

1. I dati in questo archivio BLOB pubblico sono costituiti da tre sottocartelle di dati non compressi.

	1. La sottocartella *raw/count/* contiene i primi 21 giorni di dati, da day\_00 a day\_20
	2. La sottocartella *raw/train/* è costituita da un singolo giorno di dati, day\_21
	3. La sottocartella *raw/test/* è costituita da due giorni di dati, day\_22 e day\_23

2. Se si vuole iniziare usando i dati in formato gzip non elaborati, questi sono disponibili nella cartella principale *raw/* e indicati come day\_NN.gz, dove NN va da 00 a 23.

Un approccio alternativo per accedere ai dati, esplorarli e modellarli senza eseguire alcun download locale è descritto più avanti in questa procedura dettagliata, al momento della creazione delle tabelle Hive.

## <a name="login"></a>Accedere al nodo head del cluster

Per accedere al nodo head del cluster, usare il [portale di Azure](https://ms.portal.azure.com) per individuare il cluster. Fare clic sull'icona a forma di elefante di HDInsight a sinistra e quindi fare doppio clic sul nome del cluster. Passare alla scheda **Configurazione**, fare doppio clic sull'icona CONNETTI nella parte inferiore della pagina e immettere le credenziali di accesso remoto quando richiesto. In questo modo, verrà visualizzato il nodo head del cluster.

Ecco la tipica finestra visualizzata al primo accesso al nodo head del cluster:

![Accesso al cluster](./media/machine-learning-data-science-process-hive-criteo-walkthrough/Yys9Vvm.png)  


A sinistra è presente la "riga di comando di Hadoop", che viene usata per l'esplorazione dei dati. Sono inoltre disponibili due utili URL, "Hadoop Yarn Status" e "Hadoop Name Node". Il primo URL mostra lo stato del processo, mentre il secondo fornisce informazioni dettagliate sulla configurazione del cluster.

Ora che la configurazione è stata completata, è possibile iniziare la prima parte della procedura dettagliata, ovvero l'esplorazione dei dati tramite Hive e la loro preparazione per Azure Machine Learning.

## <a name="hive-db-tables"></a> Creare il database e le tabelle Hive

Per creare le tabelle Hive per il set di dati Criteo, aprire la ***riga di comando di Hadoop*** sul desktop del nodo head e specificare la directory Hive immettendo il comando

    cd %hive_home%\bin

>[AZURE.NOTE] Al prompt della directory bin/ Hive eseguire tutti i comandi di Hive riportati in questa procedura dettagliata. In questo modo, eventuali problemi di percorso vengono risolti automaticamente. I termini "prompt della directory Hive", "prompt della directory bin/ Hive" e "riga di comando di Hadoop" vengono usati in modo intercambiabile.

>[AZURE.NOTE]  Per eseguire qualsiasi query Hive, è sempre possibile usare i comandi seguenti:

		cd %hive_home%\bin
		hive

Dopo che viene visualizzata la shell REPL Hive con l'indicazione "hive >", è sufficiente tagliare e incollare la query per eseguirla.

Il codice seguente crea un database "criteo" e quindi genera 4 tabelle:


* una *tabella per la generazione di conteggi* compilata nei giorni compresi tra day\_00 e day\_20
* una *tabella da usare come set di dati di training* compilata il giorno day\_21
* due *tabelle da usare come set di dati di test* compilate rispettivamente nei giorni day\_22 e day\_23

Il set di dati di test è suddiviso in due diverse tabelle perché uno dei giorni è festivo e si vuole determinare se il modello è in grado di rilevare le differenze tra un giorno festivo e uno non festivo in base alla percentuale di click-through.

Lo script [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) è visualizzato qui per praticità:

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

1. **Usando la riga di comando REPL Hive**: il primo modo consiste nell'eseguire un comando "hive" e copiare e incollare la query nella riga di comando REPL Hive. A tale scopo, eseguire l'operazione seguente:

		cd %hive_home%\bin
		hive

 	A questo punto, tagliando e incollando la query nella riga di comando REPL questa viene eseguita.

2. **Salvando le query in un file ed eseguendo il comando**: il secondo modo consiste nel salvare le query in un file con estensione hql ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e quindi eseguire il comando seguente per eseguire la query:

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### Verificare la creazione del database e della tabella

Viene quindi verificata la creazione del database con il comando seguente al prompt della directory bin/ Hive:

		hive -e "show databases;"

Il risultato è il seguente:

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

Ciò conferma la creazione del nuovo database, "criteo".

Per vedere quali tabelle sono state create, è sufficiente eseguire il comando seguente al prompt della directory bin/ Hive:

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

Il contenuto di [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) è visualizzato qui:

		SELECT COUNT(*) FROM criteo.criteo_train;

Il risultato è il seguente:

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

In alternativa, è anche possibile eseguire questo comando al prompt della directory bin/ Hive:

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### Numero di esempi di test nei due set di dati di test

Viene ora contato il numero di esempi di test nei due set di dati di test. Il contenuto di [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) è visualizzato qui:

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Il risultato è il seguente:

		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

Come di consueto, è anche possibile chiamare lo script al prompt della directory bin/ Hive eseguendo questo comando:

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Infine, viene esaminato il numero di esempi di test nel set di dati di test basato sul giorno 23 (day\_23).

Il comando per eseguire questa operazione è simile a quello illustrato in precedenza. Vedere [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql):

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

È possibile usare la funzione "histogram\_numeric" nativa di Hive per visualizzare la distribuzione delle variabili numeriche. Ecco il contenuto di [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

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

La combinazione LATERAL VIEW - explode in Hive serve a produrre un output simile a quello di SQL anziché il solito elenco. Si noti che in questa tabella la prima colonna corrisponde al valore di bin\_center e la seconda alla relativa frequenza.

### Percentili approssimativi di alcune variabili numeriche nel set di dati di training

Un altro aspetto interessante in relazione alle variabili numeriche è il calcolo dei percentili approssimativi. A tale scopo, è disponibile la funzione "percentile\_approx" nativa di Hive. Il contenuto di [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) è il seguente:

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

Lo script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) esegue la stessa operazione per i dati di test del giorno 22 (day\_22):

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


Infine, lo script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) esegue l'operazione per i dati di test del giorno 23 (day\_23):

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
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

**Nota:** in questa procedura dettagliata viene esaminato l'uso di tabelle di conteggio per produrre rappresentazioni compatte di caratteristiche categorie a elevata dimensionalità. Questa tecnica non è l'unico modo disponibile per codificare le funzionalità categoriche. Per altre informazioni su tecniche diverse, è possibile vedere gli argomenti relativi a [codifica one-hot](http://en.wikipedia.org/wiki/One-hot) e [hashing delle funzionalità](http://en.wikipedia.org/wiki/Feature_hashing).

Per creare tabelle di conteggio sui dati di conteggio, vengono usati i dati nella cartella raw/count. Nella sezione relativa alla modellazione viene illustrato come creare da zero queste tabelle di conteggio per le caratteristiche categoriche oppure, in alternativa, come usare una tabella di conteggio preesistente per le analisi. Con "tabelle di conteggio preesistenti" si intendono le tabelle fornite da Microsoft. Istruzioni dettagliate su come accedere a queste tabelle sono disponibili nella sezione successiva.

## <a name="aml"></a> Creare un modello con Azure Machine Learning

Il processo di creazione di un modello in Azure Machine Learning prevede i passaggi seguenti:

1. [Ottenere i dati dalle tabelle Hive in Azure Machine Learning](#step1)
2. [Creare l'esperimento: pulire i dati, scegliere uno strumento di apprendimento e definire le caratteristiche con le tabelle di conteggio](#step2)
3. [Eseguire il training del modello](#step3)
4. [Assegnare un punteggio al modello in base ai dati di test](#step4)
5. [Valutare il modello](#step5)
6. [Pubblicare il modello come servizio Web per consentirne l'utilizzo](#step6)

È ora possibile creare modelli in Azure Machine Learning Studio. I dati sottocampionati vengono salvati come tabelle Hive nel cluster. Per leggere i dati viene usato il modulo **Import Data** (Importa dati) di Azure Machine Learning. Le credenziali per accedere all'account di archiviazione del cluster sono indicate di seguito.

### <a name="step1"></a> Passaggio 1: Ottenere i dati dalle tabelle Hive in Azure Machine Learning usando il modulo Import Data e selezionarli per un esperimento di Machine Learning

Per iniziare, selezionare **+NEW** -> **EXPERIMENT** -> **Blank Experiment**. Quindi, nella casella **Search** (Ricerca) in alto a sinistra cercare "Import Data". Trascinare il modulo **Import Data** sull'area di disegno degli esperimenti (la parte centrale della schermata) per usare il modulo per l'accesso ai dati.

Ecco l'aspetto del modulo **Import Data** durante il recupero dei dati dalla tabella Hive:

![Acquisizione di dati con Import Data](./media/machine-learning-data-science-process-hive-criteo-walkthrough/i3zRaoj.png)  

Per il modulo **Import Data** i valori dei parametri forniti nel grafico sono solo esempi del tipo di valori che è necessario specificare. Di seguito sono illustrate alcune indicazioni generali su come compilare il set di parametri per il modulo **Import Data**.

1. In **Data Source** scegliere "Hive query".
2. Nella casella **Hive database query**, è sufficiente specificare l'istruzione SELECT * FROM <nome\_database.nome\_tabella>.
3. **Hcatalog server URI**: se il cluster è "abc", specificare semplicemente https://abc.azurehdinsight.net
4. **Hadoop user account name**: nome utente scelto al momento dell'autorizzazione del cluster (NON il nome utente di accesso remoto).
5. **Hadoop user account password** (Password dell'account utente Hadoop): password per il nome utente scelta al momento dell'autorizzazione del cluster (NON la password di accesso remoto).
6. **Location of output data**: scegliere "Azure".
7. **Azure storage account name**: account di archiviazione associato al cluster.
8. **Azure storage account key**: chiave dell'account di archiviazione associato al cluster.
9. **Azure container name**: se il nome del cluster è "abc", in genere questo valore è semplicemente "abc".


Quando il modulo **Import Data** termina il recupero dei dati, il completamento è indicato da un segno di spunta verde nel modulo, salvarli come set di dati con un nome a propria scelta. L'aspetto è il seguente:

![Salvataggio di dati con Import Data](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oxM73Np.png)  

Fare clic con il pulsante destro del mouse sulla porta di output del modulo **Import Data**. Verranno visualizzate le opzioni **Save as dataset** e **Visualize**. Facendo clic sull'opzione **Visualize** vengono visualizzati 100 righe di dati e un pannello, sul lato destro, utile per le statistiche di riepilogo. Per salvare i dati, è sufficiente selezionare **Save as dataset** e seguire le istruzioni.

Per selezionare il set di dati salvato per l'uso in un esperimento di Machine Learning, individuare il set di dati usando la casella **Search** (Ricerca) illustrata nella figura seguente. Digitare quindi una parte del nome assegnato al set di dati per accedervi e trascinare il set di dati nel pannello principale. Rilasciando il set di dati sul pannello principale, questo viene selezionato per la modellazione in Machine Learning.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/cl5tpGw.png)  

>[AZURE.NOTE] Eseguire questa operazione per entrambi i set di dati, di training e di test. Ricordare anche di usare il nome database e i nomi delle tabelle assegnati a questo scopo. I valori usati nella figura hanno puramente scopo illustrativo.**

### <a name="step2"></a>Passaggio 2: Creare un semplice esperimento in Azure Machine Learning per stimare i clic o l'assenza di clic

L'esperimento di Azure ML è simile al seguente:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xRpVfrY.png)  

Ora si esamineranno i componenti chiave di questo esperimento. Si ricordi che prima è necessario trascinare i set di dati di training e di test salvati sull'area di disegno degli esperimenti.

#### Pulire i dati mancanti

Il modulo **Clean Missing Data** pulisce i dati mancanti in un modo che può essere specificato dall'utente. Analizzando il modulo, è possibile vedere quanto segue:

![Pulizia dei dati mancanti](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0ycXod6.png)

In questo caso, si sceglie di sostituire tutti i valori mancanti con 0. Sono disponibili anche altre opzioni, che è possibile visualizzare aprendo gli elenchi a discesa nel modulo.

#### Progettazione di funzionalità per i dati

Per alcune funzioni categoriche di set di dati di grandi dimensioni possono esistere milioni di valori univoci. Non è possibile usare metodi di base come la codifica one-hot per rappresentare funzioni categoriche con dimensionalità così elevata. In questa procedura dettagliata, viene illustrato come usare le funzioni di conteggio con i moduli predefiniti di Azure Machine Learning per generare rappresentazioni compatte di queste funzioni categoriche con dimensionalità così elevata. Come risultato finale si ottengono dimensioni minori per il modello, tempi di training più rapidi e metriche delle prestazioni abbastanza simili all'uso di altre tecniche.

##### Compilazione di trasformazioni conteggio

Per compilare funzioni di conteggio, si usa il modulo **Build Counting Transform** disponibile in Azure Machine Learning. Il modulo è simile al seguente:


![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/e0eqKtZ.png) ![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OdDN0vw.png)


**Nota importante**: nella casella **Count columns**, si immettono le colonne su cui eseguire i conteggi. Come indicato in precedenza, si tratta in genere di colonne categoriche con dimensionalità elevata. All'inizio si è detto che il set di dati Criteo contiene 26 colonne categoriche: da Col15 a Col40. Ora vengono tenute tutte in considerazione e si assegnano gli indici (da 15 a 40 separati da virgole, come nella figura).

Per usare il modulo in modalità MapReduce, appropriata per i set di dati di grandi dimensioni, è necessario accedere a un cluster Hadoop HDInsight (quello usato per esplorare le funzionalità può essere usato anche a questo scopo) e alle relative credenziali. Le figure precedenti illustrano i valori inseriti. Sostituire i valori forniti a scopo illustrativo con quelli pertinenti al proprio caso d'uso.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/05IqySf.png)  

Nella figura precedente, viene mostrato come immettere il percorso BLOB di input. Questo percorso include i dati riservati per la compilazione delle tabelle di conteggio.


Dopo l'esecuzione di questo modulo, è possibile salvare la trasformazione per un uso successivo facendo clic con il pulsante destro del mouse sul modulo e scegliendo l'opzione **Save as Transform** (Salva come trasformazione):

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IcVgvHR.png)  

Nell'architettura dell'esperimento precedente, il set di dati "ytransform2" corrisponde esattamente a una trasformazione conteggio salvata. Nella parte restante di questo esperimento, si presume che il lettore abbia usato un modulo **Build Counting Transform** su alcuni dati per generare i conteggi e possa quindi usare tali conteggi per generare le funzioni di conteggio nei set di dati di training e di test.

##### Scelta delle funzioni di conteggio da includere nei set di dati di training e di test

Una volta disponibile una trasformazione conteggio, l'utente può scegliere quali funzioni includere nei set di dati di training e di test usando il modulo **Modify Count Table Parameters**. Questo modulo viene illustrato qui per completezza, ma per semplicità non viene effettivamente usato nell'esperimento.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/PfCHkVg.png)  

In questo caso, come si può osservare, si è scelto di usare solo i log-odds e di ignorare la colonna backoff. Si possono anche impostare parametri come la soglia cestino, il numero di pseudo esempi precedenti da aggiungere per lo smoothing (attenuazione) e se usare o meno la scala laplaciana del rumore. Sono tutte funzioni avanzate e si deve osservare che i valori predefiniti sono un valido punto di partenza per gli utenti con poca familiarità con questo tipo di generazione di funzioni.

##### Trasformazione dei data prima di generare le funzioni conteggio

Ora verrà illustrata una fase importante della trasformazione dei dati di training e di test prima della generazione effettiva delle funzioni conteggio. Si noti che vengono usati due moduli **Execute R Script** prima di applicare la trasformazione conteggio ai dati.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/aF59wbc.png)

Ecco il primo script R:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/3hkIoMx.png)


In questo script R, si rinominano le colonne con nomi da "Col1" a "Col40". Infatti i nomi devono avere questo formato nella trasformazione conteggio.

Nel secondo script R, si bilancia la distribuzione tra classi positive e negative, rispettivamente le classi 1 e 0, sottocampionando la classe negativa. Lo script R seguente mostra come eseguire questa operazione:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/91wvcwN.png)  

In questo semplice script R, si usa "pos\_neg\_ratio" per impostare la quantità di bilanciamento tra le classi positiva e negativa. Questo è importante perché, riducendo lo sbilanciamento delle classi, si ottengono di solito vantaggi a livello delle prestazioni per i problemi di classificazione in cui la distribuzione delle classi è asimmetrica. Si ricordi che in questo caso la classe positiva è pari al 3,3% e la classe negativa al 96,7%.

##### Applicazione della trasformazione conteggio ai dati

Infine, si può usare il modulo **Apply Transformation** per applicare le trasformazioni conteggio ai set di dati di training e di test. Questo modulo accetta la trasformazione conteggio salvata come input e i set di dati di training o di test come secondo input e restituisce i dati con le funzioni conteggio, come illustrato qui:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xnQvsYf.png)  

##### Estratto delle funzioni conteggio

È interessante osservare come appaiono le funzioni conteggio in questo caso. Eccone un estratto:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/FO1nNfw.png)  

In questo estratto, si nota che, per le colonne usate per i conteggi, si ottengono i conteggi e i log-odds oltre ai backoff pertinenti.

Ora si può compilare un modello di Azure Machine Learning usando questi set di dati trasformati. Nella sezione successiva viene illustrato come effettuare questa operazione.

#### Compilazione di modelli in Azure Machine Learning

##### Scelta dello strumento di apprendimento

Prima di tutto è necessario scegliere uno strumento di apprendimento. Verrà usato un albero delle decisioni con boosting a due classi. Ecco le opzioni predefinite per questo strumento di apprendimento:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/bH3ST2z.png)  

Per l'esperimento verranno scelti i valori predefiniti. I valori predefiniti sono in genere significativi e consentono di ottenere previsioni rapide sulle prestazioni. È possibile migliorare le prestazioni con lo sweep dei parametri, una volta che si dispone di una previsione.

#### Eseguire il training del modello

Per il training, è sufficiente richiamare un modulo **Train Model**. I due input sono lo strumento di apprendimento Two-Class Boosted Decision Tree e il set di dati di training, come illustrato qui:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/2bZDZTy.png)  


#### Assegnare un punteggio al modello

Una volta disponibile un modello con training, è possibile assegnare un punteggio al set di dati di test e valutarne le prestazioni. A questo scopo, usare il modulo **Score Model** (Modello di punteggio) illustrato nella figura seguente, insieme a un modulo **Evaluate Model** (Modello di valutazione):

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/fydcv6u.png)  


### <a name="step5"></a> Passaggio 5: Valutare il modello

Infine, si vogliono analizzare le prestazioni del modello. In genere, per i problemi di classificazione a due classi (binaria), un buon metodo di misurazione è costituito dall'area sottesa alla curva (AUC). Per visualizzare quest'area, è necessario collegare il modulo **Score Model** a un modulo **Evaluate Model**. Fare clic su **Visualize** (Visualizza) nel modulo **Evaluate Model** per visualizzare un grafico simile al seguente:

![Modulo di valutazione del modello per l'albero delle decisioni con boosting](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0Tl0cdg.png)  

Per i problemi di classificazione binaria (o a due classi), un buon metodo di misurazione dell'accuratezza della stima è costituito dall'area sottesa alla curva (AUC). Di seguito sono illustrati i risultati dell'uso del modello sul set di dati di test. Per ottenere i risultati, fare clic con il pulsante destro del mouse sulla porta di output del modulo **Evaluate Model** e quindi fare clic su **Visualize**.

![Visualizzazione del modulo Evaluate Model](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IRfc7fH.png)  

### <a name="step6"></a> Passaggio 6: Pubblicare il modello come servizio Web
Per rendere disponibile su larga scala un modello di Azure Machine Learning, è possibile pubblicarlo come servizio Web in modo molto semplice. Una volta fatto, chiunque può eseguire chiamate al servizio Web con i dati di input per cui è necessario ottenere delle stime e il servizio Web usa il modello per restituire tali stime.

A tale scopo, è innanzitutto necessario salvare il modello sottoposto a training come oggetto Trained Model. Fare clic con il pulsante destro del mouse su **Train Model** (Modello di training) e scegliere **Save as Trained Model** (Salva come modello con training).

Successivamente, è necessario creare le porte di input e di output per il servizio Web:

* una porta di input accetta i dati nello stesso formato dei dati per cui sono necessarie le stime
* una porta di output restituisce le etichette con punteggi e le probabilità associate.

#### Selezionare alcune righe di dati per la porta di input

Per praticità, è possibile usare un modulo **Apply SQL Transformation** per selezionare solo 10 righe come dati della porta di input. Selezionare solo queste righe di dati per la porta di input usando la query SQL visualizzata qui:

![Dati porta di input](./media/machine-learning-data-science-process-hive-criteo-walkthrough/XqVtSxu.png)  

#### Servizio Web
È ora possibile eseguire un piccolo esperimento che può essere usato per pubblicare il servizio Web.

#### Generare i dati di input per il servizio Web

Come passaggio iniziale, poiché la tabella di conteggio è grande, vengono prese poche righe di dati di test e da esse vengono generati i dati di output con le caratteristiche di conteggio. Questo può essere il formato dei dati di input per il servizio Web, come illustrato qui:

![Creazione dati di input per l'albero delle decisioni con boosting](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OEJMmst.png)  

>[AZURE.NOTE] Per il formato dati di input, viene ora usato l'OUTPUT del modulo **Count Featurizer** (Creazione funzionalità da conteggi). Una volta terminata l'esecuzione dell'esperimento, salvare l'output del modulo **Count Featurizer** come set di dati. Il set di dati viene usato per i dati di input nel servizio Web.

#### Esperimento di assegnazione dei punteggi per la pubblicazione del servizio Web

Innanzitutto, viene illustrato l'aspetto. La struttura essenziale è un modulo **Score Model** che accetta l'oggetto modello sottoposto a training e poche righe di dati di input generate nei passaggi precedenti usando il modulo **Count Featurizer**. Viene usato il modulo "Select Columns in Dataset" per ottenere le etichette con i punteggi e le probabilità di stima.

![Select Columns in Dataset](./media/machine-learning-data-science-process-hive-criteo-walkthrough/kRHrIbe.png)

Si noti che modo è possibile utilizzare il modulo **Select Columns in Dataset** per filtrare i dati da escludere da un set di dati. Il contenuto è illustrato qui:

![Applicazione di filtri con il modulo Select Columns in Dataset](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oVUJC9K.png)  

Per ottenere le porte di input e di output indicate in blu, è sufficiente fare clic su **prepare webservice** in basso a destra. L'esecuzione di questo esperimento consente anche di pubblicare il servizio Web facendo clic sull'icona **PUBLISH WEB SERVICE** (PUBBLICA SERVIZIO WEB) in basso a destra, illustrata qui:

![Pubblicazione del servizio Web](./media/machine-learning-data-science-process-hive-criteo-walkthrough/WO0nens.png)  


Dopo la pubblicazione del servizio Web, si viene reindirizzati a una pagina simile alla seguente:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/YKzxAA5.png)

A sinistra sono presenti due collegamenti per i servizi Web:

* Il collegamento **REQUEST/RESPONSE** (o RRS) serve per le stime singole e viene usato in questo workshop.
* Il collegamento **BATCH EXECUTION** (BES) viene usato per le stime in batch e richiede che i dati di input usati per effettuare le stime si trovino in un archivio BLOB di Azure.

Facendo clic sul collegamento **REQUEST/RESPONSE** viene visualizzata una pagina che fornisce codice predefinito in C#, Python ed R. Questo codice può essere usato in modo semplice per effettuare chiamate al servizio Web. Si noti che la chiave API in questa pagina deve essere usata per l'autenticazione.

È consigliabile copiare questo codice Python in una nuova cella di IPython Notebook.

Ecco un segmento di codice Python con la chiave API corretta.

![Codice Python](./media/machine-learning-data-science-process-hive-criteo-walkthrough/f8N4L4g.png)  


Si noti che la chiave API predefinita è stata sostituita con la chiave API del servizio Web. Facendo clic su **Run** in questa cella di IPython Notebook, viene generata la risposta seguente:

![Risposta IPython](./media/machine-learning-data-science-process-hive-criteo-walkthrough/KSxmia2.png)

Si noti che per i due esempi di test chiesti (nel framework JSON dello script Python) si ottengono le risposte nel formato "Scored Labels, Scored Probabilities". In questo caso, sono stati scelti i valori predefiniti forniti dal codice predefinito (0 per tutte le colonne numeriche e la stringa "value" per tutte le colonne categoriche).

Con questa osservazione si conclude la procedura dettagliata end-to-end che mostra come gestire set di dati di grandi dimensioni con Azure Machine Learning. Partendo da un terabyte di dati, è stato creato un modello di previsione che è stato quindi distribuito come servizio Web nel cloud.

<!---HONumber=AcomDC_0914_2016-->