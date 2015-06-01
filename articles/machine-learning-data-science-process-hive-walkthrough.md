<properties 
	pageTitle="Analisi scientifica dei dati di Azure in esecuzione: uso dei cluster Hadoop di HDInsight | Azure" 
	description="Processo di analisi scientifica dei dati end-to-end tramite un cluster Hadoop di HDInsight, al fine di creare e distribuire un modello che usa il set di dati disponibile pubblicamente." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# Analisi scientifica dei dati di Azure in esecuzione: uso dei cluster Hadoop di HDInsight

In questa esercitazione, viene descritto il mapping end-to-end del processo di analisi scientifica dei dati di Azure tramite un cluster Hadoop di HDInsight. In questo modo, sarà possibile creare e distribuire un modello tramite un set di dati disponibile pubblicamente, [Corse dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/). 


## <a name="dataset"></a>Descrizione del set di dati Corse dei taxi di NYC

I dati relativi alle corse dei taxi di NYC sono rappresentati come file csv compressi e di dimensione pari a 20 GB (48 GB non compressi); questi comprendono informazioni su oltre 173 milioni di corse singole e sulle tariffe pagate. Il record di ogni corsa include informazioni sull'ora e sul luogo di partenza e di arrivo, il numero di patente anonimo (del tassista) e il numero della licenza (ID univoco del taxi). I dati comprendono tutte le corse effettuate nel 2013 e vengono forniti nei due set di dati seguenti (per ogni mese):

1. I file csv 'trip_data' contengono dettagli sulle corse, ad esempio, il numero di passeggeri, il luogo di partenza e di arrivo, la durata e la lunghezza della corsa. Di seguito, sono riportati alcuni record di esempio:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. I file csv 'trip_fare' includono dettagli sulle tariffe corrisposte per ogni corsa, ad esempio, il tipo di pagamento, l'importo, tasse e soprattasse, mance, pedaggi e l'importo totale. Di seguito, sono riportati alcuni record di esempio:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La chiave univoca che consente di unire trip_data e trip_fare è composta dai campi: medallion, hack_licence e pickup_datetime.

Sono presenti rispettivamente 12 file per 'trip_data' e per  'trip_fare'. Nei nomi dei file, i numeri 1-12 rappresentano i mesi in cui sono state effettuate le corse. 

## <a name="mltasks"></a>Esempi di attività predittive
Prima di usare i dati, è opportuno stabilire il tipo di stima che si desidera ottenere dall'analisi, in modo da comprendere meglio le attività che devono essere incluse nel processo.
Di seguito, sono riportati esempi di problemi relativi alle stime che verranno risolti da questa procedura dettagliata. La loro formulazione si basa su  *tip_amount*:

1. **Classificazione binaria**: consente di stabilire se sia stata lasciata o meno una mancia per la corsa, vale a dire che un valore *tip_amount* superiore a $ 0 rappresenta un esempio positivo, mentre un valore *tip_amount* pari a $ 0 rappresenta un esempio negativo.

2. **Classificazione multiclasse**: consente di prevedere il range relativo all'importo della mancia lasciata per una corsa. Il valore *tip_amount* viene diviso in 5 contenitori o classi:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Attività di regressione**: consente di prevedere l'importo della mancia lasciata per una corsa.  


## <a name="setup"></a>Impostare l'ambiente di analisi scientifica dei dati di Azure

Come si può notare dalla guida [Organizzazione dell'ambiente](machine-learning-data-science-plan-your-environment.md), è possibile adottare vari approcci quando si usa il set di dati relativo alle corse dei taxi di NYC in Azure:

- Utilizzare i dati nei BLOB di Azure, quindi creare un modello in Azure Machine Learning
- Caricare i dati in un database SQL Server, quindi creare un modello in Azure Machine Learning
- Caricare i dati nelle tabelle Hive di HDInsight e modellarli in Azure Machine Learning

A seconda della dimensione del set di dati, del percorso relativo all'origine dei dati e all'ambiente di destinazione di Azure selezionato, questo scenario è analogo a [Scenario \n. 7: Set di dati di grandi dimensioni in file locali, destinazione Hive nei cluster Hadoop di Azure HDInsight](machine-learning-data-science-plan-sample-scenarios.md#largedbtohive).

Per impostare l'ambiente relativo all'analisi scientifica dei dati di Azure per questo approccio che usa i cluster Hadoop di Azure HDInsight e, nello specifico, le tabelle e le query Hive per memorizzare ed elaborare i dati, effettuare la seguente procedura.

1. [Creare un account di archiviazione](storage-whatis-account.md)

2. [Creare un'area di lavoro Azure ML](machine-learning-create-workspace.md)

3. [Eseguire il provisioning di una macchina virtuale **Windows** per l'analisi scientifica dei dati](machine-learning-data-science-setup-virtual-machine.md).

	> [AZURE.NOTE] Gli script di esempio verranno scaricati nella macchina virtuale per l'analisi scientifica dei dati durante la procedura di configurazione della stessa. Quando viene completato lo script di post-installazione della macchina virtuale, gli esempi saranno visualizzati nella libreria di documenti della macchina virtuale, nel percorso  *C:\Users<user_name>\Documents\Data Science Scripts*  Questa cartella di esempio, viene denominata come **Script di esempio**. Le query Hive di esempio sono incluse in file con estensione hql nella cartella **Script di esempio**. Tenere presente che il valore *<user_name>* indicato nel percorso della cartella corrisponde al nome di accesso a Windows della macchina virtuale e non al nome utente di Azure.

4. [Personalizzare i cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati](machine-learning-data-science-customize-hadoop-cluster.md). Questa procedura consente di creare un cluster Hadoop di Azure HDInsight con una versione a 64 bit di Anaconda Python 2.7 installata su tutti i nodi. Dopo aver creato il cluster Hadoop personalizzato, consentire al nodo head del cluster Hadoop di accedere in remoto al portale di Azure, eseguendo la procedura descritta in questo argomento sulla personalizzazione.


## <a name="getdata"></a>Visualizzare i dati da un'origine pubblica

Per visualizzare il set di dati [Corse dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/) dal relativo percorso pubblico, è possibile usare uno qualsiasi dei metodi descritti in [Spostamento dei dati da e verso l'archivio BLOB di Azure](machine-learning-data-science-move-azure-blob.md) e copiare i dati nella nuova macchina virtuale.

Per copiare i dati usando AzCopy:

1. Accedere alla macchina virtuale (VM)

2. Creare una nuova directory nel disco dati della macchina virtuale (nota: non utilizzare come disco dati il disco temporaneo fornito con la macchina virtuale).

3. Da una finestra del prompt dei comandi, eseguire i seguenti comandi AzCopy, sostituendo <path_to_data_folder> con il percorso della cartella di dati creata al passaggio (2):

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	Al termine dell'esecuzione di AzCopy, nella cartella dovrebbero essere presenti 24 file CSV compressi (12 per trip_data e 12 per trip_fare).

	>[AZURE.NOTE] *Source:https://getgoing.blob.core.windows.net/nyctaxitrip* e *Source:https://getgoing.blob.core.windows.net/nyctaxifare* indicano due contenitori pubblici di Azure che vengono usati per condividere con gli utenti i file decompressi relativi ai taxi di NYC. Per leggere questi due contenitori pubblici di Azure non è necessario disporre di una chiave di accesso. 

## <a name="upload"></a>Caricare i dati nel contenitore predefinito del cluster Hadoop di Azure HDInsight

Da una finestra del prompt di comandi o di Windows PowerShell della propria macchina virtuale, eseguire il comando AzCopy sostituendo i seguenti parametri con i valori indicati durante la creazione del cluster Hadoop. Pertanto, sostituire:

* ***&#60;path_to_data_folder>*** con la cartella di dati creata nella sezione precedente 
* ***&#60;storage account name of Hadoop cluster>*** con l'account di archiviazione usato dal cluster
* ***&#60;default container of Hadoop cluster>*** con il contenitore predefinito usato dal cluster
* ***&#60;storage account key>*** con la chiave dell'account di archiviazione usata dal cluster

Tenere presente che se il valore ***nyctaxitripraw*** o ***nyctaxifareraw*** indicato nel comando non esiste, verrà creato nel contenitore. 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>Creare database e tabelle Hive partizionati in base al mese

Per accedere ed eseguire le query Hive usate in questa procedura, accedere al nodo head del cluster Hadoop, aprire la riga di comando di Hadoop presente sul desktop del nodo head, quindi inserire la directory Hive tramite il comando

    cd %hive_home%\bin

Se si necessita di ulteriore assistenza in merito a queste procedure o ad alcune alternative, vedere la sezione [Inviare query Hive direttamente dalla riga di comando di Hive ](machine-learning-data-science-process-hive-tables.md#submit). 

Nel caso in cui sia stata creata una macchina virtuale di Azure seguendo le istruzioni della sezione [Configurazione dell'ambiente di analisi scientifica dei dati di Azure](#setup), la query Hive di esempio dovrebbe essere già stata scaricata nella macchina virtuale, all'interno della cartella **Script di esempio**. In alternativa, gli utenti possono controllare i file hql da [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). 

Per inviare le query Hive nei file hql è necessario che questi ultimi vengano trasferiti in una directory locale nel nodo head del cluster Hadoop. Negli esempi seguenti, si presuppone che i file hql siano stati trasferiti nella directory ***C:\temp&#92;*** del nodo head.

Di seguito, è riportato il contenuto del file ***C:\temp\sample_hive_create_db_and_tables.hql*** che consente di creare il database ***nyctaxidb*** e le tabelle ***corsa*** e ***tariffa*** Hive.

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

Inserire il comando nella riga di comando Hadoop del nodo head al fine di inviare queste query Hive:
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>Caricare i dati nelle tabelle in base alle partizioni
Eseguire i seguenti comandi PowerShell nella riga di comando Hadoop al fine di caricare dati nelle tabelle Hive in base alle partizioni mensili:

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Di seguito, è riportato il contenuto del file *sample_hive_load_data_by_partitions.hql* da esaminare.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>Visualizzazione dei database nel cluster Hadoop di HDInsight

Per visualizzare i database creati nel cluster Hadoop di HDInsight nella finestra della riga di comando di Hadoop, eseguire il comando seguente nella riga di comando:

	hive -e "show databases;"

### <a name="#show-tables"></a>Visualizzazone delle tabelle nel database nyctaxidb 
	
Per visualizzare le tabelle nel database nyctaxidb, eseguire il seguente comando nella riga di comando di Hadoop:
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Esplorazione dati e progettazione di funzionalità in Hive
Le attività di esplorazione dei dati e di progettazione delle funzionalità relative ai dati caricati nelle tabelle Hive possono essere eseguite usando le query Hive. Nella sezione seguente, sono riportati gli esempi di tali attività:

- Visualizzazione dei i 10 record principali in entrambe le tabelle.
- Esplorazione delle distribuzioni di dati di un numero ridotto di campi in diverse finestre temporali.
- Indagine sulla qualità dei dati dei campi di longitudine e latitudine.
- Generazione di etichette di classificazione binaria e multiclasse basata su **tip_amount**.
- Creazione di funzionalità calcolando le distanze dirette delle corse.
- Unione di due tabelle ed estrazione di un campione casuale che verrà utilizzato per la creazione di modelli.

Dopo aver completato l'esplorazione dei dati e la progettazione delle funzionalità in Hive, è possibile proseguire con Azure Machine Learning. È possibile salvare la query Hive finale al fine di estrarre e campionare i dati, quindi copiare e incollare la query direttamente nel modulo Lettore in Azure Mobile Learning. A questo punto, è possibile creare un modello per questi dati.

### Esplorazione: visualizzare i 10 record principali nella tabella delle corse

Per comprendere la natura dello schema dati e l'aspetto dei dati, gli utenti possono estrarre i 10 record principali da ogni tabella. Eseguire le due query separatamente dalla console della riga di comando di Hadoop per analizzare i record.

Per visualizzare i 10 record principali in  *table _trip_*:

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
Per visualizzare i 10 record principali in  *table _fare_*:
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### Esplorazione: visualizzare il numero di record in ognuna delle 12 partizioni

Eseguire il seguente comando nella console della riga di comando di Hadoop per visualizzare il numero di record di ogni partizione di 12 mesi.
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### Esplorazione: distribuzione delle corse per licenza

Questo esempio consente di identificare la licenza (numeri dei taxi) che ha effettuato più di 100 corse in un determinato periodo. La query si avvale dell'accesso alla tabella partizionata, dal momento che è condizionata dalla variabile di partizione denominata **month**. 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

Di seguito, è riportato il contenuto del file *sample_hive_trip_count_by_medallion.hql* da esaminare.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### Esplorazione: distribuzione delle corse per licenza e hack_license

Di seguito, è riportato un esempio relativo a un raggruppamento a più colonne, in questo caso, in base alle colonne sulla licenza e hack_license. Eseguire il seguente comando dalla console della riga di comando di Hadoop:

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

Di seguito, è riportato il contenuto del file *sample_hive_trip_count_by_medallion_license.hql* da esaminare.
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### Valutazione relativa alla qualità dei dati: verificare i record con longitudini e latitudini errate

In questo esempio, viene descritto se i campi longitudine e/o latitudine contengono un valore non valido (i gradi del radiante devono essere compresi tra -90 e 90) o disporre di coordinate (0,0).

Eseguire il seguente comando dalla console della riga di comando di Hadoop:

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

L'argomento *-S* incluso nel comando sostituisce l'immagine della schermata di stato relativa ai processi mapping/riduzione. Ciò è utile poiché rende più leggibile l'immagine relativa alla schermata dell'output della query Hive. 

Di seguito, è riportato il contenuto del file *sample_hive_quality_assessment.hql* da esaminare.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### Esplorazione: frequenza delle corse per cui è stata lasciata una mancia e di quelle per cui non è stata lasciata una mancia

In questo esempio, viene descritto il numero di corse per cui è stata lasciata una mancia rispetto a quelle per cui non è stata lasciata una mancia in un determinato periodo oppure nel set di dati completo, se copre tutto l'anno. Questa distribuzione riflette quella a etichetta binaria da usare in seguito per eseguire la modellazione della classificazione binaria.

Eseguire il seguente comando dalla console della riga di comando di Hadoop:

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

Di seguito, è riportato il contenuto del file *sample_hive_tipped_frequencies.hql* da esaminare.

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### Esplorazione: frequenza relativa al range di mancia

In questo esempio, viene calcolata la distribuzione relativa al range di mancia per un determinato periodo o per il set di dati completo, se quest'ultimo copre tutto l'anno. Si tratta della distribuzione relativa alle classi delle etichette che verranno usate in un secondo momento per eseguire la modellazione della classificazione multiclasse.

Eseguire il seguente comando dalla console della riga di comando di Hadoop:

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

Di seguito, è riportato il contenuto del file *sample_hive_tip_range_frequencies.hql* da esaminare.

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

### Esplorazione: calcolare la distanza diretta e confrontarla con quella della corsa

In questo esempio, viene calcolata la distanza diretta della corsa(in miglia). Nell'esempio, i risultati si limitano alle coordinate valide, usando soltanto la query di valutazione della qualità descritta in precedenza.

Eseguire il seguente comando dalla console della riga di comando di Hadoop:

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

Di seguito, è riportato il contenuto del file *sample_hive_trip_direct_distance.hql* da esaminare.

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

Una volta completata l'esecuzione della query, i risultati vengono scritti in un BLOB ***queryoutputdir/000000_0*** di Azure, all'interno del contenitore del cluster Hadoop. Se si usa Esplora archivi di Azure, si dovrebbe visualizzare il BLOB nel contenitore predefinito del cluster Hadoop, come mostrato nell'immagine seguente.

![Create workspace][2]

Tenere presente che è possibile applicare il filtro (evidenziato dalla casella rossa) per recuperare soltanto il BLOB che contiene lettere nel nome. 

Al termine dell'esecuzione della query, è possibile usare l'SDK di Azure per leggere l'output della query dal BLOB di Azure in un frame di dati Pandas ed eseguire ulteriori esplorazioni ed elaborazioni. Fare riferimento a [Elaborazione dei dati BLOB di Azure nell'ambiente di analisi scientifica dei dati](machine-learning-data-science-process-data-blob.md) per istruzioni su come leggere i BLOB di Azure all'interno di frame di dati Pandas. 
	
### Preparazione dei dati per la creazione del modello

La query fornita nell'esempio consente di unire le tabelle **nyctaxidb.trip** e **nyctaxidb.fare**, creare un'etichetta di classificazione binaria **tipped** e una di classificazione multiclasse **tip_class**. È possibile copiare e incollare questa query direttamente nel modulo Lettore di [Azure Machine Learning Studio](https://studio.azureml.net) per inserire i dati dalla **query Hive** in Azure. La query esclude i record con coordinate di longitudine e latitudine errate.

La query consente di applicare direttamente le funzionalità incorporate Hive definite dall'utente al fine di creare alcune funzionalità dai record Hive, ad esempio, quelle di ora, settimana dell'anno e giorno della settimana (1 = lunedì, 7 = domenica) per il campo _pickup_datetime_. Gli utenti possono riferirsi alla [funzionalità definita dall'utente LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) per visualizzare un elenco completo di funzionalità definite dall'utente incorporate.

Inoltre, questa query consente di eseguire il sottocampionamento dei dati; in questo modo, i risultati della query possono adattarsi ad Azure Machine Learning Studio. In Studio, viene importato soltanto l'1%.

Inviare la query eseguendo il comando dalla console della riga di comando di Hadoop:

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

Di seguito, è riportato il contenuto del file *sample_hive_prepare_for_aml.hql* da esaminare.
	
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
        select medallion, 
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
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
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

## <a name="mlmodel"></a>Creare modelli in Azure Machine Learning

A questo punto, è possibile passare alla creazione e alla distribuzione del modello in [Azure Machine Learning](https://studio.azureml.net). I dati possono essere ora usati per per risolvere i problemi relativi alle stime indicati in precedenza:

1. **Classificazione binaria**: consente di prevedere se per la corsa è stata lasciata o meno una mancia.

2. **Classificazione multiclasse**: consente di stimare il range della mancia lasciata per una corsa. 

3. **Attività di regressione**: consente di prevedere l'importo della mancia lasciata per una corsa.  

Per iniziare l'esercizio relativo alla creazione di modelli, accedere all'area di lavoro Azure Machine Learning. Se non è stata ancora creata un'area di lavoro per Machine Learning, vedere [Creazione di un'area di lavoro Azure ML](machine-learning-create-workspace.md).

1. Per iniziare a utilizzare Azure Machine Learning, vedere [Informazioni su Azure Machine Learning Studio](machine-learning-what-is-ml-studio.md)

2. Effettuare l'accesso ad [Azure Machine Learning Studio](https://studio.azureml.net).

3. Nella home page di Studio vengono fornite molte informazioni, video, esercitazioni, link a riferimenti sui moduli e altre risorse. Per ulteriori informazioni su Azure Machine Learning, consultare il [Centro di documentazione di Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/).

Un tipico esperimento training consiste nelle seguenti operazioni:

1. Creazione di un esperimento **+NEW**.
2. Inserimento dei dati in Azure ML.
3. Pre-elaborazione, trasformazione e manipolazione dei dati secondo le esigenze.
4. Generazione di funzionalità, se necessario.
5. Suddivisione dei dati in set di dati di training, convalida o test(o creazione di set di dati distinti per ciascuna tipologia).
6. Selezione di uno o più algoritmi Machine Learning, a seconda del problema di apprendimento da risolvere. Ad esempio, classificazione binaria, classificazione multiclasse, regressione.
7. Training di uno o più modelli tramite il set di dati di training.
8. Calcolo del punteggio del set di dati di convalida mediante i modelli sottoposti a training.
9. Valutazione dei modelli per calcolare la metrica rilevante per il problema di apprendimento.
10. Ottimizzazione dei modelli e selezione del modello migliore per la distribuzione.

Nell'esercizio, sono stati già esplorati e progettati i dati in Hive (passaggi da 1 a 4) ed è stata stabilita la dimensione del campione da inserire in Azure ML. Per creare uno o più modelli di previsione, effettuare le seguenti operazioni:

1. Inserimento dei dati in Azure ML tramite il modulo **Lettore**, disponibile nella sezione **Input e output dei dati**. Per ulteriori informazioni, vedere la pagina di riferimento sul modulo [Lettore](http://msdn.microsoft.com/library/dn784775).

	![Create workspace][15]

2. Selezione della **query Hive** come **Origine dati** nel riquadro **Proprietà**.

3. Immissione delle informazioni relative al cluster Hadoop di HDInsight, come mostrato di seguito. Il **nome account di archiviazione di Azure** deve corrispondere all'account di archiviazione utilizzato per creare il cluster Hadoop di HDInsight, mentre il **nome del contenitore di Azure** deve corrispondere al contenitore predefinito del cluster Hadoop 

	![Create workspace][11]

4. Nell'area di testo relativa **query Hive del database**, incollare la query che consente di estrarre i campi del database necessari (compresi quelli calcolati, ad esempio, le etichette) ed eseguire il sottocampionamento dei dati nella dimensione di campione desiderata.

Nella figura in basso, è riportato un esempio relativo a un esperimento di classificazione che legge i dati direttamente dalla query Hive. Per la classificazioni multiclasse e per i problemi di regressione, è possibile creare esperimenti analoghi al precedente.

![Create workspace][12]

> [AZURE.IMPORTANT] Negli esempi di estrazione dei dati di modellazione e di query di campionamento forniti nelle sezioni precedenti, **tutte le etichette per i tre esercizi sulla creazione dei modelli sono incluse nella query**. Un passaggio importante (richiesto) in ciascun esercizio sulla modellazione consiste nell'**escludere** le etichette non necessarie per gli altri due problemi ed eventuali **perdite di destinazione**. Ad esempio, nell'utilizzo della classificazione binaria, utilizzare l'etichetta **tipped** ed escludere i campi **tip_class**, **tip_amount** e **total_amount**. Questi ultimi sono perdite di destinazione in quanto implicano la mancia pagata.

> In questo esperimento, il primo modulo **Editor metadati** consente di aggiungere i nomi di colonna nei dati di output del modulo Lettore. Il modulo è necessario dal momento che il modulo Lettore, che legge i dati della query Hive, non è in grado di creare nomi di colonna per i dati di output. 

> Per escludere le colonne non necessarie e/o le perdite di destinazione, è possibile utilizzare il modulo **Colonne progetto** o l'**Editor metadati**. Per ulteriori informazioni, vedere le pagine di riferimento [Colonne progetto](http://msdn.microsoft.com/library/dn784740) ed [Editor metadati](http://msdn.microsoft.com/library/dn784761).

## <a name="mldeploy"></a>Distribuzione di modelli in Azure Machine Learning

Una volta creato il modello, distribuirlo come servizio Web direttamente dall'esperimento. Per ulteriori informazioni sulla pubblicazione dei servizi Web di Azure ML, vedere [Operazioni del servizio API di Azure Machine Learning](machine-learning-overview-of-azure-ml-process.md).

Per distribuire un nuovo servizio Web, è necessario effettuare le seguenti operazioni:

1. Creare un esperimento di assegnazione di punteggio.
2. Pubblicare il servizio Web.

Per creare un esperimento di assegnazione di punteggio da un esperimento di training **Completato**, fare clic su **CREA ESPERIMENTO DI ASSEGNAZIONE PUNTEGGIO** nella barra delle azioni inferiore.

![Azure Scoring][13]

Azure Machine Learning tenterà di creare un esperimento di assegnazione di punteggio basato sui componenti dell'esperimento di training. In particolare, verranno effettuate le seguenti operazioni:

1. Salvataggio del modello sottoposto a training e rimozione dei moduli di training del modello.
2. Identificazione di una **porta di input** logica per rappresentare lo schema di dati di input previsto.
3. Identificazione di una **porta di output** logica per rappresentare lo schema di output del servizio Web previsto.

Una volta creato l'esperimento di punteggio, esaminarlo e apportare le dovute modifiche. Un esempio tipico di modifica consiste nel sostituire il set di dati e/o la query di input con una che escluda i campi di etichetta, dal momento che questi ultimi non sono disponibili quando si chiama il servizio. Inoltre, si consiglia di ridurre il set di dati e/o la query di input a pochi record, una dimensione sufficiente a indicare lo schema dati. Per la porta di output, è opportuno escludere tutti i campi di input e includere soltanto quelli **Etichette con punteggio** e **Probabilità con punteggio** nell'output, usando il modulo **Colonne progetto**.

Nella figura in basso, viene riportato un esperimento di punteggio campione. Per pubblicare, fare clic sul pulsante **PUBBLICA SERVIZIO WEB** nella barra delle azioni inferiore.

![Create workspace][14]

Ricapitolando, durante questa procedura è stato creato un ambiente di analisi scientifica dei dati di Azure da usare con set di dati pubblici di grandi dimensioni. A partire dall'acquisizione dei dati, proseguendo con le attività di esplorazione e progettazione delle funzionalità nel processo di analisi scientifica dei dati, fino al training modello e alla pubblicazione di un servizio Web di Azure.

## Informazioni sulla licenza

Questa procedura di esempio e gli script contenuti sono forniti da Microsoft con licenza MIT. Per ulteriori dettagli, controllare il file LICENSE.txt presente nella directory del codice di esempio su GitHub.

## Riferimenti

*	[Pagina di Andrés Monroy per scaricare i dati sulle corse dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/)  
*	[Complemento ai dati sulle corse dei taxi di NYC di Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[Ricerche e statistiche su NYC Taxi and Limousine Commission](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49-->