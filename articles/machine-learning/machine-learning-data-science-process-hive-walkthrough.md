<properties
	pageTitle="ADAPT (Advanced Analytics Process and Technology): uso dei cluster Hadoop | Microsoft Azure"
	description="Uso di ADAPT (Advanced Analytics Process and Technology) per uno scenario end-to-end in cui un cluster Hadoop di HDInsight viene usato per creare e distribuire un modello con un set di dati di grandi dimensioni disponibile pubblicamente."
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
	ms.date="09/28/2015"
	ms.author="hangzh;bradsev" />


# ADAPT (Advanced Analytics Process and Technology): uso di cluster Hadoop di HDInsight

In questa procedura dettagliata si userà Advanced Analytics Process and Technology (ADAPT) in uno scenario end-to-end usufruendo di un [cluster Hadoop di Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) per archiviare, esplorare e acquisire dati di progettazione del set di dati relativo alle [corse dei taxi di New York](http://www.andresmh.com/nyctaxitrips/) disponibile pubblicamente, nonché sottocampionarli. I modelli dei dati sono creati con Azure Machine Learning in modo da gestire la classificazione binaria e multiclasse e attività predittive di regressione.

Per una procedura dettagliata su come gestire un set di dati più grande (1 terabyte) in uno scenario simile in cui si usano i cluster Hadoop di HDInsight per l'elaborazione dei dati, vedere [ADAPT (Advanced Analytics Process and Technology): uso di cluster Hadoop di Azure HDInsight su un set di dati da 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md).

Per eseguire le attività presentate in questa procedura con un set di dati da 1 TB, è anche possibile usare IPython Notebook. Se si vuole provare questo approccio, vedere l'argomento relativo alla [procedura dettagliata Criteo con una connessione Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).


## <a name="dataset"></a>Descrizione del set di dati relativo alle corse dei taxi di New York

I dati relativi alle corse dei taxi di NYC sono rappresentati come file csv compressi e di dimensione pari a 20 GB (48 GB non compressi); questi comprendono informazioni su oltre 173 milioni di corse singole e sulle tariffe pagate. Il record di ogni corsa include la località di partenza e di arrivo, il numero di patente anonimo (del tassista) e il numero di licenza (ID univoco del taxi). I dati sono relativi a tutte le corse per l'anno 2013 e vengono forniti nei due set di dati seguenti per ciascun mese:

1. I file con estensione csv "trip\_data" contengono i dettagli delle corse, ad esempio il numero dei passeggeri, i punti di partenza e arrivo, la durata e la lunghezza della corsa. Di seguito vengono forniti alcuni record di esempio:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. I file con estensione csv "trip\_fare" contengono i dettagli della tariffa pagata per ciascuna corsa, ad esempio tipo di pagamento, importo, soprattassa e tasse, mance e pedaggi e l'importo totale pagato. Di seguito vengono forniti alcuni record di esempio:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La chiave univoca che consente di unire trip\_data e trip\_fare è composta dai campi: medallion, hack\_licence e pickup\_datetime.

Per ottenere tutti i dettagli relativi a una corsa specifica, è sufficiente creare un join con tre chiavi: "medallion", "hack\_license" e "pickup\_datetime".

Altri dettagli relativi ai dati verranno forniti a breve, quando se ne eseguirà l'archiviazione nelle tabelle Hive.

## <a name="mltasks"></a>Esempi di attività di stima
Prima di usare i dati, è opportuno stabilire il tipo di stima che si desidera ottenere dall'analisi, in modo da comprendere meglio le attività che devono essere incluse nel processo. Di seguito sono riportati tre esempi di problemi di previsione che verranno affrontati in questa procedura dettagliata e la cui formulazione si basa sul valore *tip\_amount*:

1. **Classificazione binaria**: consente di stabilire se sia stata pagata o meno una mancia per la corsa. In questo caso, un valore di *tip\_amount* superiore a $ 0 rappresenta un esempio positivo, mentre un valore di *tip\_amount* pari a $ 0 rappresenta un esempio negativo.

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0

2. **Classificazione multiclasse**: consente di prevedere l'intervallo in cui si inserisce l'importo della mancia pagata per la corsa. Il valore *tip\_amount* viene suddiviso in cinque contenitori o classi:

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Attività di regressione**: consente di prevedere l'importo della mancia pagata per una corsa.


## <a name="setup"></a>Configurare un cluster Hadoop di HDInsight per l'analisi avanzata

>[AZURE.NOTE]In genere, questa attività viene svolta da un **amministratore**.

Per impostare un ambiente Azure per l'analisi avanzata basato su un cluster HDInsight è necessario seguire questa procedura composta da tre passaggi:

1. [Creare un account di archiviazione](../storage-whatis-account.md): l'account di archiviazione viene usato per archiviare i dati nell'archivio BLOB di Azure. Anche i dati usati nei cluster HDInsight vengono archiviati in questa posizione.

2. [Personalizzare i cluster Hadoop di Azure HDInsight per Advanced Analytics Process and Technology](machine-learning-data-science-customize-hadoop-cluster.md): questo passaggio consente di creare un cluster Hadoop di Azure HDInsight con la versione a 64 bit di Anaconda Python 2.7 installata in tutti i nodi. Quando si personalizza un cluster HDInsight, è importante non dimenticare due passaggi importanti.

	* Collegare l'account di archiviazione creato nel passaggio 1 al cluster HDInsight al momento della creazione. Questo account di archiviazione, infatti, viene usato per accedere ai dati elaborati all'interno del cluster.

	* Dopo aver creato il cluster, abilitare l'accesso remoto al relativo nodo head. Passare alla scheda **Configurazione** e fare clic su **Abilita modalità remota**. Questo passaggio consente di specificare le credenziali utente da usare per l'accesso remoto.

3. [Creare un'area di lavoro di Azure Machine Learning](machine-learning-create-workspace.md): questa area di lavoro di Azure Machine Learning viene usata per creare modelli di apprendimento automatico. Questa attività viene eseguita dopo aver completato un'analisi iniziale dei dati e aver eseguito un'operazione di sottocampionamento con il cluster HDInsight.

## <a name="getdata"></a>Acquisire i dati da un'origine pubblica

>[AZURE.NOTE]In genere, questa attività viene svolta da un **amministratore**.

Per acquisire il set di dati [Corse dei taxi di New York](http://www.andresmh.com/nyctaxitrips/) dal relativo percorso pubblico, è possibile usare uno dei metodi descritti in [Spostamento dei dati da e verso l'archiviazione BLOB di Azure](machine-learning-data-science-move-azure-blob.md) e copiare i dati nella nuova macchina virtuale.

Si descrive ora come usare AzCopy per trasferire i file contenenti i dati. Per scaricare e installare AzCopy, seguire le istruzioni fornite in [Introduzione all'utilità della riga di comando AzCopy](../storage-use-azcopy.md).

1. Da una finestra del prompt dei comandi, eseguire i seguenti comandi AzCopy sostituendo *<path_to_data_folder>* con la destinazione desiderata:


		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. Al termine dell'operazione di copia, nella cartella dati scelta sarà presente un totale di 24 file compressi. Decomprimere i file scaricati nella stessa directory del computer locale. Prendere nota della cartella in cui si trovano i dati non compressi. Si farà riferimento a questa cartella come *<path\_to\_unzipped\_data\_files>*.


## <a name="upload"></a>Caricare i dati nel contenitore predefinito del cluster Hadoop di Azure HDInsight

>[AZURE.NOTE]In genere, questa attività viene svolta da un **amministratore**.

Nei seguenti comandi AzCopy, sostituire i parametri seguenti con i valori effettivi specificati durante la creazione del cluster Hadoop e decomprimere i file di dati.

* ***&#60;path\_to\_data\_folder>***: directory (insieme al percorso) sul computer contenente i file di dati non compressi  
* ***&#60;storage account name of Hadoop cluster>***: account di archiviazione associato al cluster HDInsight
* ***&#60;default container of Hadoop cluster>***: contenitore predefinito usato dal cluster. Il nome del contenitore predefinito corrisponde in genere al nome del cluster stesso. Ad esempio, se il nome del cluster è "abc123.azurehdinsight.net", quello del contenitore predefinito sarà abc123.
* ***&#60;storage account key>***: chiave dell'account di archiviazione usata dal cluster

Da un prompt dei comandi o una finestra di Windows PowerShell nel computer, eseguire i due comandi AzCopy seguenti.

Questo comando consente di caricare i dati delle corse nella directory ***nyctaxitripraw*** all'interno del contenitore predefinito del cluster Hadoop.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Questo comando consente di caricare i dati delle tariffe nella directory ***nyctaxitripraw*** all'interno del contenitore predefinito del cluster Hadoop.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

I dati si trovano ora nell'archiviazione BLOB di Azure e sono pronti per essere usati all'interno del cluster HDInsight.

## <a name="#download-hql-files"></a>Accedere al nodo head del cluster Hadoop e preparare l'analisi esplorativa dei dati

>[AZURE.NOTE]In genere, questa attività viene svolta da un **amministratore**.

Per accedere al nodo head del cluster per l'analisi esplorativa e il sottocampionamento dei dati, seguire la procedura descritta in [Accedere al nodo head del cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

In questa procedura dettagliata si useranno essenzialmente query scritte in [Hive](https://hive.apache.org/), un linguaggio di query di tipo SQL per l'esecuzione di analisi preliminari dei dati. Le query Hive vengono archiviate in file con estensione hql. Si effettuerà quindi il sottocampionamento dei dati da usare in Azure Machine Learning per la creazione dei modelli.

Per preparare il cluster per l'analisi esplorativa dei dati, è possibile scaricare i file con estensione hql con i relativi script Hive da [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) in una directory locale (C:\\temp) del nodo head. A questo scopo, aprire il **prompt dei comandi** dal nodo head del cluster ed eseguire i due comandi seguenti:

	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Questi due comandi consentiranno di scaricare tutti i file con estensione hql necessari in questa procedura nella directory locale ***C:\\temp&#92;*** del nodo head.

## <a name="#hive-db-tables"></a>Creare database e tabelle Hive partizionati in base al mese

>[AZURE.NOTE]In genere, questa attività viene svolta da un **amministratore**.

È ora possibile creare tabelle Hive per il set di dati sui taxi di New York. Nel nodo head del cluster Hadoop, aprire la ***riga di comando di Hadoop*** sul desktop del nodo head e specificare la directory Hive immettendo il comando

    cd %hive_home%\bin

>[AZURE.NOTE]**Eseguire tutti i comandi di Hive in questa procedura dettagliata dal prompt della directory bin/ Hive sopra indicato. In questo modo, eventuali problemi di percorso verranno risolti automaticamente. I termini "prompt della directory Hive", "prompt della directory bin/ Hive" e "riga di comando di Hadoop" verranno usati in modo intercambiabile in questo documento.**

Dal prompt della directory Hive, immettere il comando seguente nella riga di comando di Hadoop del nodo head per inviare query Hive per la creazione di database e tabelle Hive:

	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Di seguito è riportato il contenuto del file ***C:\\temp\\sample\_hive\_create\_db\_and\_tables.hql*** che consente di creare il database ***nyctaxidb*** e le tabelle ***trip*** e ***fare***.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count int,
	    trip_time_in_secs double,
	    trip_distance double,
	    pickup_longitude double,
	    pickup_latitude double,
	    dropoff_longitude double,
	    dropoff_latitude double)  
	PARTITIONED BY (month int)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    pickup_datetime string,
	    payment_type string,
	    fare_amount double,
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Questo script Hive crea due tabelle:

* la tabella "trip" che contiene i dettagli relativi alle corse (informazioni sull'autista, ora di prelievo, distanza e tempo della corsa) e
* la tabella "fare" che contiene dettagli relativi alle tariffe (importo della tariffa, importo della mancia, pedaggio ed eventuali supplementi).

Per ricevere maggiore assistenza per lo svolgimento di queste procedure o per conoscere procedure alternative, vedere la sezione [Inviare query Hive direttamente dalla riga di comando di Hadoop](machine-learning-data-science-process-hive-tables.md#submit).

## <a name="#load-data"></a>Caricare i dati nelle tabelle Hive in base alle partizioni

>[AZURE.NOTE]In genere, questa attività viene svolta da un **amministratore**.

Il set di dati sui taxi di New York presenta un partizionamento naturale per mese, utile per velocizzare i tempi di elaborazione e ridurre la durata delle query. I comandi di PowerShell seguenti (specificati dalla directory Hive tramite la **riga di comando di Hadoop**) caricano i dati nelle tabelle Hive "trip" e "fare" partizionate per mese.

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Il file *sample\_hive\_load\_data\_by\_partitions.hql* contiene i comandi **LOAD** seguenti.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Alcune query Hive a cui si ricorrerà più avanti nel corso del processo di esplorazione prevedono l'uso di un'unica o al massimo due partizioni. Queste query, tuttavia, possono essere eseguite sull'intero set di dati.

### <a name="#show-db"></a>Visualizzare i database nel cluster Hadoop di HDInsight

Per visualizzare i database creati nel cluster Hadoop di HDInsight nella finestra della riga di comando di Hadoop, eseguire il comando seguente nella riga di comando:

	hive -e "show databases;"

### <a name="#show-tables"></a>Visualizzare le tabelle Hive nel database nyctaxidb

Per visualizzare le tabelle nel database nyctaxidb, eseguire il seguente comando nella riga di comando di Hadoop:

	hive -e "show tables in nyctaxidb;"

È possibile verificare che le tabelle siano partizionate eseguendo il comando seguente:

	hive -e "show partitions nyctaxidb.trip;"

Di seguito è riportato l'output previsto:

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 2.075 seconds, Fetched: 12 row(s)

Analogamente, è possibile verificare che la tabella "fare" sia partizionata eseguendo il comando seguente:

	hive -e "show partitions nyctaxidb.fare;"

Di seguito è riportato l'output previsto:

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Esplorare i dati e progettare le funzionalità in Hive

>[AZURE.NOTE]In genere, questa attività viene svolta da un **data scientist**.

Le attività di esplorazione dei dati e di progettazione delle funzionalità relative ai dati caricati nelle tabelle Hive possono essere eseguite usando le query Hive. Di seguito sono riportati alcuni esempi delle attività che si affronteranno in questa sezione:

- Visualizzazione dei i 10 record principali in entrambe le tabelle.
- Esplorazione delle distribuzioni di dati di un numero ridotto di campi in diverse finestre temporali.
- Indagine sulla qualità dei dati dei campi di longitudine e latitudine.
- Generazione di etichette di classificazione binaria e multiclasse basata su **tip\_amount**.
- Creazione di funzionalità calcolando le distanze dirette delle corse.

### Esplorazione: visualizzare i 10 record principali nella tabella delle corse

>[AZURE.NOTE]In genere, questa attività viene svolta da un **data scientist**.

Per visualizzare la natura dei dati, verranno esaminati 10 record di ogni tabella. Eseguire separatamente le due query seguenti dal prompt della directory Hive nella console della riga di comando di Hadoop per analizzare i record.

Per ottenere i primi 10 record della tabella "trip" dal primo mese:

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Per ottenere i primi 10 record della tabella "fare" dal primo mese:

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Per una maggiore comodità di visualizzazione, è spesso utile salvare i record in un file. Una piccola modifica alla query precedente esegue questa operazione:

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### Esplorazione: visualizzare il numero di record in ognuna delle 12 partizioni

>[AZURE.NOTE]In genere, questa attività viene svolta da un **data scientist**.

È interessante osservare come il numero delle corse subisca sensibili variazioni nel corso dell'anno. Il raggruppamento in base al mese consente di vedere come sono distribuite le corse.

	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Viene prodotto l'output seguente:

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 283.406 seconds, Fetched: 12 row(s)

In questa tabella, la prima colonna è il mese e la seconda il numero di corse effettuate nel mese.

È inoltre possibile contare il numero totale di record nel set di dati delle corse eseguendo il comando seguente al prompt della directory Hive.

	hive -e "select count(*) from nyctaxidb.trip;"

Il risultato è il seguente:

	173179759
	Time taken: 284.017 seconds, Fetched: 1 row(s)

Usando comandi simili a quelli illustrati per il set di dati delle corse, è possibile eseguire query Hive dal prompt della directory Hive per il set di dati delle tariffe in modo da convalidare il numero di record.

	hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Viene prodotto l'output seguente:

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 253.955 seconds, Fetched: 12 row(s)

Osservare come per entrambi i set di dati venga restituito lo stesso numero di corse mensili. Questo costituisce la prima conferma che i dati sono stati caricati correttamente.

Per contare il numero totale di record nel set di dati delle tariffe è possibile usare il comando seguente dal prompt della directory Hive:

	hive -e "select count(*) from nyctaxidb.fare;"

Il risultato è il seguente:

	173179759
	Time taken: 186.683 seconds, Fetched: 1 row(s)

Anche il numero totale di record coincide in entrambe le tabelle. Questo costituisce la seconda conferma che i dati sono stati caricati correttamente.

### Esplorazione: distribuzione delle corse per licenza

>[AZURE.NOTE]In genere, questa attività viene svolta da un **data scientist**.

In questo esempio viene identificata la licenza (numero del taxi) che ha eseguito più di 100 corse in un determinato periodo. Per la query verrà usata la tabella partizionata poiché è condizionata dalla variabile di partizione **month**. I risultati della query vengono scritti nel file locale queryoutput.tsv presente nella directory `C:\temp` del nodo head.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Di seguito è riportato il contenuto del file *sample\_hive\_trip\_count\_by\_medallion.hql* per l'analisi.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100
	ORDER BY med_count desc;

Nel set di dati relativo ai taxi di New York, la licenza identifica un taxi univoco. È possibile identificare i taxi "occupati" chiedendo quali di essi hanno effettuato più di un determinato numero di corse in un intervallo di tempo specifico. L'esempio seguente identifica i taxi che hanno effettuato oltre cento corse nei primi tre mesi e salva i risultati della query in un file locale: C:\\temp\\queryoutput.tsv.

Di seguito è riportato il contenuto del file *sample\_hive\_trip\_count\_by\_medallion.hql* per l'analisi.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100
	ORDER BY med_count desc;

Dal prompt della directory Hive eseguire il comando seguente:

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### Esplorazione: distribuzione delle corse per licenza e hack\_license

>[AZURE.NOTE]In genere, questa attività viene svolta da un **data scientist**.

Durante l'esplorazione di un set di dati, spesso si desidera esaminare il numero di co-occorrenze di gruppi di valori. L'esempio fornito in questa sezione illustra come eseguire questa operazione per i taxi e gli autisti.

Il file *sample\_hive\_trip\_count\_by\_medallion\_license.hql* raggruppa il set di dati delle tariffe in "medallion" e "hack\_license" e restituisce la quantità di ogni combinazione. Di seguito è riportato il contenuto del file.

    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

Questa query restituisce le combinazioni dei taxi e degli autisti, visualizzate in ordine decrescente in base al numero di corse.

Dal prompt della directory Hive eseguire:

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

I risultati della query vengono scritti nel file locale C:\\temp\\queryoutput.tsv.

### Esplorazione: valutazione della qualità dei dati mediante il controllo del record di longitudine/latitudine non validi

>[AZURE.NOTE]In genere, questa attività viene svolta da un **data scientist**.

Un obiettivo comune di analisi esplorativa dei dati è quello di eliminare i record non validi. L'esempio riportato in questa sezione determina se i campi di longitudine o latitudine contengono un valore esterno all'area di New York. Poiché è probabile che in questi record siano presenti valori di longitudine/latitudine errati, è opportuno eliminarli da tutti i dati che dovranno essere usati per la modellazione.

Di seguito è riportato il contenuto del file *sample\_hive\_quality\_assessment.hql* per l'analisi.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Dal prompt della directory Hive eseguire:

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

L'argomento *-S* incluso in questo comando sostituisce l'immagine della schermata di stato relativa ai processi Map/Reduce di Hive. Ciò è utile poiché rende più leggibile l'immagine relativa alla schermata dell'output della query Hive.

### Esplorazione: distribuzioni in classi binarie delle mance delle corse

**Nota:** in genere, questa attività viene svolta da un **data scientist**.

Per il problema di classificazione binaria descritto nella sezione [Esempi di attività di stima](machine-learning-data-science-process-hive-walkthrough.md#mltasks), è utile sapere se è stata offerta una mancia. Questa distribuzione delle mance è di tipo binario:

* mancia offerta(classe 1, tip\_amount > $ 0)  
* nessuna mancia (classe 0, tip\_amount = $ 0).

Il file *sample\_hive\_tipped\_frequencies.hql* consente di sapere se è stata offerta una mancia.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Al prompt della directory Hive eseguire:

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### Esplorazione: distribuzioni in classi nell'impostazione multiclasse

**Nota:** in genere, questa attività viene svolta da un **data scientist**.

Per il problema di classificazione multiclasse descritto nella sezione [Esempi di attività di stima](machine-learning-data-science-process-hive-walkthrough.md#mltasks), questo set di dati si presta a una classificazione naturale in cui si desidera stimare l'importo delle mance offerte. Per definire gli intervalli di mance nella query, è possibile usare i contenitori. Per ottenere le distribuzioni in classi per i vari intervalli di mance, si userà il file *sample\_hive\_tip\_range\_frequencies.hql*. Di seguito è riportato il contenuto del file.

	SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Eseguire il seguente comando dalla console della riga di comando di Hadoop:

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### Esplorazione: calcolare la distanza diretta tra due posizioni di latitudine e longitudine

**Nota:** in genere, questa attività viene svolta da un **data scientist**.

La disponibilità di una misura della distanza diretta consente di rilevare eventuali discrepanze con la distanza effettiva della corsa. Questa funzionalità è stata motivata sottolineando come un passeggero sarebbe probabilmente meno incline a offrire una mancia se intuisse che l'autista ha intenzionalmente compiuto un percorso più lungo.

Per visualizzare il confronto tra la distanza effettiva della corsa e la [distanza Haversine](http://en.wikipedia.org/wiki/Haversine_formula) tra due punti di latitudine-longitudine (distanza "grande-cerchio"), si useranno le funzioni trigonometriche disponibili in Hive., pertanto:

    set R=3959;
    set pi=radians(180);

	insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

Nella query sopra riportata, R è il raggio della terra in miglia e il pi greco viene convertito in radianti. I punti di latitudine/longitudine, inoltre, sono stati "filtrati" in modo da rimuovere i valori esterni all'area di New York.

In questo caso, i risultati vengono scritti in una directory denominata "queryoutputdir". La sequenza di comandi seguente crea questa directory di output, quindi esegue il comando Hive.

Al prompt della directory Hive eseguire:

	hdfs dfs -mkdir wasb:///queryoutputdir

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


I risultati della query vengono scritti in nove BLOB di Azure (da ***queryoutputdir/000000\_0*** a ***queryoutputdir/000008\_0***), all'interno del contenitore predefinito del cluster Hadoop.

Per visualizzare le dimensioni dei singoli BLOB, è possibile eseguire il comando seguente dal prompt della directory Hive:

	hdfs dfs -ls wasb:///queryoutputdir

Per visualizzare il contenuto di un file specifico, ad esempio 000000\_0, si userà invece il comando `copyToLocal` di Hadoop.

	hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

**Avviso:** in presenza di file di grandi dimensioni, l'operazione `copyToLocal` può essere molto lenta e non è quindi consigliabile.

Uno dei vantaggi derivanti dalla disponibilità dei dati in un BLOB di Azure è la possibilità di esplorarli in Azure Machine Learning usando il modulo [Reader][reader].


## <a name="#downsample"></a>Sottocampionare i dati e creare modelli in Azure Machine Learning

**Nota:** in genere, questa attività viene svolta da un **data scientist**.

Dopo la fase di analisi esplorativa, si passerà ora a eseguire il sottocampionamento dei dati per la creazione di modelli in Azure Machine Learning. Questa sezione illustra come usare una query Hive per eseguire il sottocampionamento dei dati, a cui sarà possibile accedere dal lettore [Reader][reader] di Azure Machine Learning.

### Sottocampionare i dati

Questa procedura si articola in due passaggi. Innanzitutto è necessario aggiungere le tabelle **nyctaxidb.trip** e **nyctaxidb.fare** a tre chiavi presenti in tutti i record: "medallion", "hack\_license" e "pickup\_datetime". È quindi possibile generare un'etichetta di classificazione binaria **tipped** e un'etichetta di classificazione multiclasse **tip\_class**.

Per poter usare il sottocampionamento dei dati direttamente dal modulo [Reader][reader] in Azure Machine Learning, è necessario archiviare i risultati della query precedente in una tabella interna di Hive. Di seguito viene creata una tabella interna di Hive e ne viene popolato il contenuto con i dati sottocampionati e sottoposti a join.

La query applica funzioni standard di Hive direttamente per generare l'ora del giorno, la settimana dell'anno, il giorno della settimana (1 indica lunedì e 7 indica domenica) dal campo "pickup\_datetime" e la distanza diretta tra i percorsi di partenza e arrivo. Per un elenco completo di queste funzionalità, gli utenti possono fare riferimento alla [funzionalità definita dall'utente LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

La query consente quindi di eseguire il sottocampionamento dei dati; in questo modo, i risultati della query possono adattarsi ad Azure Machine Learning Studio. Solo l'1% del set di dati originale viene importato in Studio.

Di seguito sono riportati i contenuti del file *sample\_hive\_prepare\_for\_aml\_full.hql* che prepara i dati per la creazione del modello in Azure Machine Learning.

		set R = 3959;
	    set pi=radians(180);

		create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
		)
		row format delimited fields terminated by ','
		lines terminated by '\n'
		stored as textfile;

		--- now insert contents of the join into the above internal table

    	insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
    	select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
		t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

    	from
    	(
        select
		medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
		${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Per eseguire questa query, dal prompt della directory di Hive eseguire:

	hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

È ora disponibile una tabella interna "nyctaxidb.nyctaxi\_downsampled\_dataset" alla quale è possibile accedere tramite il modulo [Reader][reader] di Azure Machine Learning. Inoltre, sarà possibile usare questo set di dati per la creazione di modelli di Machine Learning.

### Usare il modulo Reader in Azure Machine Learning per accedere ai dati sottocampionati

I prerequisiti necessari per l'inoltro di query Hive nel modulo [Reader][reader] di Azure Machine Learning sono l'accesso a un'area di lavoro di Azure Machine Learning e l'accesso alle credenziali del cluster e al relativo account di archiviazione.

Di seguito sono elencate informazioni dettagliate sul modulo [Reader][reader] e sui parametri di input:

**URI del server HCatalog**: se il nome del cluster è abc123, l'URI è semplicemente: https://abc123.azurehdinsight.net

**Nome dell'account utente di Hadoop**: nome utente scelto per il cluster (**non** il nome utente di accesso remoto).

**Password dell'account utente di Hadoop**: password scelta per il cluster (**non** la password di accesso remoto).

**Percorso dei dati di output**: ossia Azure.

**Nome dell'account di archiviazione di Azure**: nome dell'account di archiviazione predefinito associato al cluster.

**Nome del contenitore di Azure**: nome del contenitore predefinito per il cluster, in genere corrisponde al nome del cluster. Per un cluster denominato "abc123", il nome del contenitore è semplicemente abc123.

**Nota importante:** **qualsiasi tabella su cui si desidera eseguire una query mediante il modulo [Reader][reader] di Azure Machine Learning deve essere una tabella interna.** Di seguito è riportato un suggerimento per determinare se una tabella T in un database D.db è una tabella interna.

Al prompt della directory Hive eseguire il comando seguente:

	hdfs dfs -ls wasb:///D.db/T

Se la tabella è una tabella interna e viene popolata, il relativo contenuto deve essere visualizzato in questa posizione. Un altro modo per determinare se una tabella è una tabella interna consiste nell'usare Esplora archivi Azure. Questo strumento consente di passare al nome del contenitore del cluster predefinito e quindi filtrare in base al nome della tabella. Se la tabella e il relativo contenuto vengono visualizzati, allora si tratta di una tabella interna.

Di seguito è riportato uno snapshot della query Hive e del modulo [Reader][reader]\:

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

Si noti che poiché i dati sottocampionati risiedono nel contenitore predefinito, la query Hive risultante da Azure Machine Learning è molto semplice ed è di tipo "SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data".

È ora possibile usare il set di dati come punto di partenza per la creazione di modelli di Machine Learning.

### <a name="mlmodel"></a>Creare modelli in Azure Machine Learning

A questo punto è possibile procedere con la creazione e la distribuzione di modelli in [Azure Machine Learning](https://studio.azureml.net). I dati possono essere ora usati per risolvere i problemi relativi alle stime indicati in precedenza:

**1. Classificazione binaria**: consente di prevedere se per la corsa è stata pagata o meno una mancia.

**Strumento di apprendimento usato:** regressione logistica a due classi

a. Per questo problema, l'etichetta (o classe) di destinazione è "tipped". Il set di dati sottocampionati originale dispone di alcune colonne che indicano le perdite di destinazione per questo esperimento di classificazione. In particolare: tip\_class, tip\_amount e total\_amount contengono informazioni sull'etichetta di destinazione non disponibile in fase di test. È possibile rimuovere queste colonne dalla valutazione tramite il modulo [Project Columns][project-columns].

Lo snapshot seguente illustra un esperimento in cui si prevede se per una corsa è stata pagata o meno una mancia.

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. Per questo esperimento, le distribuzioni dell'etichetta di destinazione sono approssimativamente 1:1.

Lo snapshot seguente illustra la distribuzione delle etichette della classe relativa alla mancia per il problema di classificazione binaria.

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

Di conseguenza, si ottengono un valore di AUC di 0,987 come illustrato nella figura seguente.

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. Classificazione multiclasse**: consente di stimare l'intervallo di mance pagate per una corsa in base alle classi definite in precedenza.

**Strumento di apprendimento usato:** regressione logistica multiclasse

a. Per questo problema, l'etichetta (o classe) di destinazione è "tip\_class", che può assumere uno dei cinque valori (0,1,2,3,4). Come nel caso della classificazione binaria, sono presenti alcune colonne che indicano le perdite di destinazione per questo esperimento. In particolare: tipped, tip\_amount e total\_amount contengono informazioni sull'etichetta di destinazione non disponibile in fase di test. È possibile rimuovere queste colonne tramite il modulo [Project Columns][project-columns].

Lo snapshot seguente illustra l'esperimento che stima la possibile collocazione di una mancia (Classe 0: mancia = $ 0, classe 1: mancia > $ 0 e <= $ 5, classe 2: mancia > $ 5 e <= $ 10, classe 3: mancia > $ 10 e <= $ 20, classe 4: mancia > $ 20)

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

Di seguito è illustrata la distribuzione della classe di test effettiva. Si noti che, mentre la classe 0 e 1 sono prevalenti, le altre classi sono meno frequenti.

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. Per questo esperimento, si usa una matrice di confusione per esaminare l'accuratezza della stima. come illustrato di seguito.

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

Si noti che mentre la precisione è abbastanza efficace in relazione alle classi più diffuse, il modello non ottiene un buon risultato di "apprendimento" sulle classi meno frequenti.


**3. Attività di regressione**: consente di prevedere l'importo della mancia pagata per una corsa.

**Strumento di apprendimento usato:** albero delle decisioni con boosting

a. Per questo problema, l'etichetta (o classe) di destinazione è "tip\_amount". In questo caso, le perdite di destinazione sono tipped, tip\_class, total\_amount e tutte queste variabili contengono informazioni sull'importo della mancia che non è in genere disponibile in fase di test. È possibile rimuovere queste colonne tramite il modulo [Project Columns][project-columns].

Lo snapshot seguente illustra l'esperimento che stima l'importo della mancia pagata.

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. Per problemi di regressione, l'accuratezza della stima viene misurata esaminando l'errore quadratico nelle stime, il coefficiente di determinazione e altri parametri simili, come illustrato di seguito.

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

Si noti che il coefficiente di determinazione è 0,709, che implica che circa il 71% della varianza è rappresentato dai coefficienti del modello.

**Nota importante:** per altre informazioni su Azure Machine Learning e su come accedere e usare il servizio, consultare [Cos'è Azure Machine Learning?](machine-learning-what-is-machine-learning.md). Una risorsa molto utile per la riproduzione di una serie di esperimenti di apprendimento automatico su Azure Machine Learning è la [raccolta di Cortana Analytics](https://gallery.azureml.net/). La raccolta include una vasta gamma di esperimenti e fornisce un'introduzione approfondita alle varie funzionalità di Azure Machine Learning.

## Informazioni sulla licenza

Questa procedura di esempio e gli script contenuti sono forniti da Microsoft con licenza MIT. Selezionare il file LICENSE.txt nella directory del codice di esempio in GitHub per ulteriori informazioni.

## Riferimenti

• [Pagina di Andrés Monroy per scaricare i dati sulle corse dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/) • [Complemento ai dati sulle corse dei taxi di NYC di Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/) • [Ricerche e statistiche su NYC Taxi and Limousine Commission](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=Oct15_HO3-->