---
title: Esplorare i dati in un cluster Hadoop e di creare modelli in Azure Machine Learning | Documentazione Microsoft
description: Uso di Team Data Science Process per uno scenario end-to-end, insieme a un cluster Hadoop di HDInsight per creare e distribuire un modello.
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev
ms.openlocfilehash: daf5168ba9a21a56d72fc14649c349b0fb63a167
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Processo di analisi scientifica dei dati per i team in azione: uso dei cluster Hadoop di HDInsight
In questa procedura dettagliata viene usato [Team Data Science Process (TDSP)](overview.md) in uno scenario end-to-end. Verrà usato un [cluster Hadoop di Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) per archiviazione, esplorazione e sviluppo delle funzionalità dei dati del set di dati delle [corse dei taxi di New York](http://www.andresmh.com/nyctaxitrips/), disponibile a livello pubblico, e per sottocampionare i dati. Per gestire attività predittive di regressione e classificazione binaria e multiclasse, verranno creati modelli dei dati con Azure Machine Learning. 

Per una procedura dettagliata che mostra come gestire set di dati di dimensioni più grandi, vedere [Processo di analisi scientifica dei dati per i team in azione: uso di un cluster Hadoop di Azure HDInsight su un set di dati da 1 TB](hive-criteo-walkthrough.md).

Per eseguire le attività presentate nella procedura dettagliata con un set di dati da 1 TB, è anche possibile usare IPython Notebook. Per altre informazioni, vedere [Criteo walkthrough using a Hive ODBC connection](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) (Procedura dettagliata per Criteo con una connessione Hive ODBC).

## <a name="dataset"></a>Descrizione del set di dati delle corse dei taxi di New York
I dati relativi alle corse dei taxi di New York sono costituiti da circa 20 GB di file con valori delimitati da virgole (CSV) compressi (circa 48 GB non compressi). Contengono più di 173 milioni di singole corse e includono le tariffe pagate per ogni corsa. Il record di ogni corsa include il luogo di partenza e di arrivo e l'ora, il numero di patente anonimo (del tassista) e il numero di licenza (ID univoco del taxi). I dati sono relativi a tutte le corse effettuate nell'anno 2013 e vengono forniti nei due set di dati seguenti per ogni mese:

- I file CSV trip_data contengono i dettagli sulle corse. Sono inclusi numero di passeggeri, punti di partenza e di arrivo, durata della corsa e lunghezza della corsa. Di seguito vengono forniti alcuni record di esempio:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- I file CSV trip_fare contengono i dettagli sulla tariffa pagata per ogni corsa. Sono inclusi metodo di pagamento, importo, supplemento e imposte, mance e pedaggi e l'importo totale pagato. Di seguito vengono forniti alcuni record di esempio:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La chiave univoca per creare un join di trip\_data e trip\_fare è costituita da questi campi: medallion, hack\_licence e pickup\_datetime. Per ottenere tutti i dettagli relativi a una corsa specifica, è sufficiente creare un join con queste tre sottochiavi.

## <a name="mltasks"></a>Esempi di attività di stima
Determinare il tipo di stime che si vuole eseguire in base all'analisi dei dati. In questo modo, si otterrà maggiore chiarezza sulle attività che è necessario includere nel processo. Ecco tre esempi di problemi relativi alle stime che possono essere risolti tramite questa procedura dettagliata. Gli esempi sono basati su *tip\_amount*:

- **Classificazione binaria**: permette di stimare se per la corsa è stata lasciata o meno una mancia. Se *tip\_amount* è maggiore di $ 0, si tratta di un esempio positivo, mentre se *tip\_amount* è uguale a $ 0, si tratta di un esempio negativo.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Classificazione multiclasse**: permette di stimare l'intervallo di importi delle mance lasciate per la corsa. Il valore di *tip\_amount* viene diviso in cinque classi:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Attività di regressione**: permette di stimare l'importo della mancia lasciata per una corsa.  

## <a name="setup"></a>Configurare un cluster Hadoop di HDInsight per l'analisi avanzata
> [!NOTE]
> Questa attività viene svolta in genere da un amministratore.
> 
> 

Per impostare un ambiente Azure per l'analisi avanzata basato su un cluster HDInsight è necessario seguire questa procedura composta da tre passaggi:

1. [Creare un account di archiviazione](../../storage/common/storage-create-storage-account.md): questo account di archiviazione viene usato per archiviare i dati nell'archiviazione BLOB di Azure. Anche i dati usati nei cluster HDInsight vengono archiviati in questa posizione.
2. [Personalizzare i cluster Hadoop di Azure HDInsight per Advanced Analytics Process and Technology](customize-hadoop-cluster.md). Questo passaggio crea un cluster Hadoop di Azure HDInsight con la versione a 64 bit di Anaconda Python 2.7 installata in tutti i nodi. Quando si personalizza un cluster HDInsight, è importante non dimenticare due passaggi importanti.
   
   * Ricordare di collegare l'account di archiviazione creato nel passaggio 1 al cluster HDInsight al momento della creazione. Questo account di archiviazione accede ai dati elaborati all'interno del cluster.
   * Dopo aver creato il cluster, abilitare l'accesso remoto al nodo head del cluster. Passare alla scheda **Configurazione** e selezionare **Abilita modalità remota**. Questo passaggio consente di specificare le credenziali utente da usare per l'accesso remoto.
3. [Creare un'area di lavoro di Azure Machine Learning](../studio/create-workspace.md): usare questa area di lavoro per creare modelli di apprendimento automatico. Questa attività viene eseguita dopo aver completato un'esplorazione iniziale e un sottocampionamento dei dati usando il cluster HDInsight.

## <a name="getdata"></a>Acquisire i dati da un'origine pubblica
> [!NOTE]
> Questa attività viene svolta in genere da un amministratore.
> 
> 

Per copiare il set di dati delle [corse dei taxi di New York](http://www.andresmh.com/nyctaxitrips/) nel computer dal percorso pubblico, usare uno dei metodi descritti in [Spostamento dei dati da e verso l'archivio BLOB di Azure](move-azure-blob.md).

Qui viene descritto come usare AzCopy per trasferire i file contenenti i dati. Per scaricare e installare AzCopy, seguire le istruzioni incluse in [Introduzione all'utilità della riga di comando AzCopy](../../storage/common/storage-use-azcopy.md).

1. Da una finestra del prompt dei comandi eseguire i comandi AzCopy seguenti, sostituendo *<path_to_data_folder>* con la destinazione desiderata:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Al termine dell'operazione di copia, nella cartella dati scelta sarà presente un totale di 24 file compressi. Decomprimere i file scaricati nella stessa directory del computer locale. Prendere nota della cartella in cui si trovano i dati non compressi. Nelle sezioni seguenti questa cartella sarà denominata *<path\_to\_unzipped_data\_files\>*.

## <a name="upload"></a>Caricare i dati nel contenitore predefinito del cluster Hadoop di HDInsight
> [!NOTE]
> Questa attività viene svolta in genere da un amministratore.
> 
> 

Nei seguenti comandi AzCopy, sostituire i parametri seguenti con i valori effettivi specificati durante la creazione del cluster Hadoop e decomprimere i file di dati.

* ***&#60;path_to_data_folder>***: directory (insieme al percorso) nel computer che contiene i file di dati non compressi.  
* ***&#60;storage account name of Hadoop cluster>***: account di archiviazione associato al cluster HDInsight.
* ***&#60;default container of Hadoop cluster>***: contenitore predefinito usato dal cluster. Il nome del contenitore predefinito corrisponde in genere al nome del cluster stesso. Ad esempio, se il nome del cluster è "abc123.azurehdinsight.net", quello del contenitore predefinito sarà abc123.
* ***&amp;#60;storage account key&gt;***: chiave dell'account di archiviazione usato dal cluster.

Da un prompt dei comandi o una finestra di Windows PowerShell eseguire i due comandi AzCopy seguenti.

Questo comando carica i dati delle corse nella directory ***nyctaxitripraw*** all'interno del contenitore predefinito del cluster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Questo comando carica i dati delle tariffe nella directory ***nyctaxifareraw*** all'interno del contenitore predefinito del cluster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

I dati si trovano ora nell'archiviazione BLOB e sono pronti per essere usati all'interno del cluster HDInsight.

## <a name="#download-hql-files"></a>Accedere al nodo head del cluster Hadoop e preparare l'analisi esplorativa dei dati
> [!NOTE]
> Questa attività viene svolta in genere da un amministratore.
> 
> 

Per accedere al nodo head del cluster per l'analisi esplorativa e il sottocampionamento dei dati, seguire la procedura descritta in [Accedere al nodo head del cluster Hadoop](customize-hadoop-cluster.md).

In questa procedura dettagliata si useranno essenzialmente query scritte in [Hive](https://hive.apache.org/), un linguaggio di query di tipo SQL per l'esecuzione di analisi preliminari dei dati. Le query Hive vengono archiviate in file con estensione hql. Verrà quindi eseguito il sottocampionamento dei dati da usare in Machine Learning per la creazione di modelli.

Per preparare il cluster per l'analisi esplorativa dei dati, scaricare i file HQL contenenti gli script Hive rilevanti da [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) in una directory locale (C:\temp) del nodo head. A questo scopo, aprire il prompt dei comandi dal nodo head del cluster ed eseguire i due comandi seguenti:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

I due comandi scaricano tutti i file HQL necessari in questa procedura dettagliata nella directory locale ***C:\temp&#92;*** del nodo head.

## <a name="#hive-db-tables"></a>Creare database e tabelle Hive partizionati in base al mese
> [!NOTE]
> Questa attività viene svolta in genere da un amministratore.
> 
> 

È ora possibile creare tabelle Hive per il set di dati dei taxi di New York.
Nel nodo head del cluster Hadoop aprire la riga di comando di Hadoop sul desktop del nodo head. Immettere la directory Hive eseguendo il comando seguente:

    cd %hive_home%\bin

> [!NOTE]
> Al prompt della directory bin/ Hive eseguire tutti i comandi di Hive riportati in questa procedura dettagliata. In questo modo, vengono risolti automaticamente tutti i problemi relativi al percorso. I termini "prompt della directory Hive", "prompt della directory/del bin Hive" e "riga di comando di Hadoop" verranno usati in modo intercambiabile in questa procedura dettagliata.
> 
> 

Dal prompt della directory Hive eseguire il comando seguente nella riga di comando di Hadoop del nodo head. Questo comando invia la query Hive per creare il database e le tabelle Hive:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Ecco il contenuto del file **C:\temp\sample\_hive\_create\_db\_and\_tables.hql**. Verranno creati il database Hive **nyctaxidb** e le tabelle **trip** e **fare**.

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

* La tabella **trip** contiene i dettagli sulle corse (informazioni sull'autista, ora di partenza, distanza della corsa e tempi).
* La tabella **fare** contiene i dettagli sulle tariffe (importo della tariffa, importo della mancia, pedaggi e supplementi).

Per ricevere maggiore assistenza per l'esecuzione di queste procedure o per informazioni su procedure alternative, vedere la sezione [Inviare query Hive direttamente dalla riga di comando di Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Caricare i dati nelle tabelle Hive in base alle partizioni
> [!NOTE]
> Questa attività viene svolta in genere da un amministratore.
> 
> 

Il set di dati sui taxi di New York presenta un partizionamento naturale per mese, utile per velocizzare i tempi di elaborazione e ridurre la durata delle query. I comandi di PowerShell seguenti (specificati dalla directory Hive tramite la riga di comando di Hadoop) caricano i dati nelle tabelle Hive trip e fare, partizionate per mese.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Il file **sample\_hive\_load\_data\_by\_partitions.hql** contiene i comandi **LOAD** seguenti:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Si noti che un certo numero di query Hive usate qui nel processo di esplorazione comporta la ricerca solo in una o due partizioni. Queste query, tuttavia, possono essere eseguite sull'intero set di dati.

### <a name="#show-db"></a>Visualizzare i database nel cluster Hadoop di HDInsight
Per visualizzare i database creati nel cluster Hadoop di HDInsight nella finestra della riga di comando di Hadoop, eseguire il comando seguente nella riga di comando di Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Visualizzare le tabelle Hive nel database **nyctaxidb**
Per visualizzare le tabelle nel database **nyctaxidb**, eseguire il comando seguente nella riga di comando di Hadoop:

    hive -e "show tables in nyctaxidb;"

È possibile verificare che le tabelle siano partizionate eseguendo il comando seguente:

    hive -e "show partitions nyctaxidb.trip;"

Ecco l'output previsto:

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

Analogamente, è possibile verificare che la tabella fare sia partizionata eseguendo il comando seguente:

    hive -e "show partitions nyctaxidb.fare;"

Ecco l'output previsto:

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
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

È possibile usare query Hive per svolgere le attività di esplorazione e progettazione delle funzionalità dei dati per i dati caricati nelle tabelle Hive. Ecco alcuni esempi di queste attività:

* Visualizzazione dei i 10 record principali in entrambe le tabelle.
* Esplorazione delle distribuzioni di dati di un numero ridotto di campi in diverse finestre temporali.
* Indagine sulla qualità dei dati dei campi di longitudine e latitudine.
* Generazione di etichette di classificazione binaria e multiclasse in base all'importo della mancia.
* Creazione di funzionalità calcolando le distanze dirette delle corse.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Esplorazione: visualizzare i 10 record principali nella tabella delle corse
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

Per determinare che aspetto avranno i dati, esaminare 10 record di ogni tabella. Per analizzare i record, eseguire le due query seguenti separatamente dal prompt della directory Hive nella console della riga di comando di Hadoop.

Per ottenere i primi 10 record della tabella trip dal primo mese:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Per ottenere i primi 10 record della tabella fare dal primo mese:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Per una visualizzazione più pratica, è possibile salvare i record in un file. Una piccola modifica alla query precedente esegue questa operazione:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Esplorazione: visualizzare il numero di record in ognuna delle 12 partizioni
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

È interessante osservare come il numero delle corse possa variare nel corso dell'anno. Il raggruppamento in base al mese mostra la distribuzione delle corse.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Viene generato l'output seguente:

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

In questa tabella la prima colonna corrisponde al mese, la seconda al numero di corse effettuate nel mese.

È anche possibile contare il numero totale di record nel set di dati delle corse eseguendo il comando seguente al prompt della directory Hive:

    hive -e "select count(*) from nyctaxidb.trip;"

Il risultato è il seguente:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Usando comandi simili a quelli mostrati per il set di dati delle corse, è possibile eseguire query Hive dal prompt della directory Hive per il set di dati delle tariffe in modo da convalidare il numero di record.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Viene generato l'output seguente:

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

Notare come per entrambi i set di dati venga restituito lo stesso numero di corse mensili. Questo costituisce la prima conferma che i dati sono stati caricati correttamente.

È possibile contare il numero totale di record nel set di dati delle tariffe eseguendo il comando seguente al prompt della directory Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

Il risultato è il seguente:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Anche il numero totale di record coincide in entrambe le tabelle. Questo costituisce la seconda conferma che i dati sono stati caricati correttamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Esplorazione: distribuzione delle corse per licenza
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

Questo esempio identifica le licenze (numeri di taxi) che hanno eseguito più di 100 corse in un determinato periodo. La query trae vantaggio dall'accesso alla tabella partizionata, perché è condizionata dalla variabile di partizione **month**. I risultati della query vengono scritti nel file locale **queryoutput.tsv**, all'interno della directory `C:\temp` del nodo head.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Ecco il contenuto del file **sample\_hive\_trip\_count\_by\_medallion.hql** per l'analisi.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Nel set di dati sui taxi di New York la licenza identifica un'auto univoca. È possibile identificare in modo comparativo i taxi più o meno occupati chiedendo quali hanno effettuato più di un certo numero di corse in un intervallo di tempo specifico. L'esempio seguente identifica i taxi che hanno effettuato più di cento corse nei primi tre mesi e salva i risultati della query nel file locale **C:\temp\queryoutput.tsv**.

Ecco il contenuto del file **sample\_hive\_trip\_count\_by\_medallion.hql** per l'analisi.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Dal prompt della directory Hive eseguire il comando seguente:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Esplorazione: distribuzione delle corse per licenza e hack_license
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

Durante l'esplorazione di un set di dati, spesso si desidera esaminare il numero di co-occorrenze di gruppi di valori. Questa sezione contiene un esempio di come eseguire questa operazione per i taxi e gli autisti.

Il file **sample\_hive\_trip\_count\_by\_medallion\_license.hql** raggruppa il set di dati delle tariffe in **medallion** e **hack_license** e restituisce la quantità di ogni combinazione. Ecco il contenuto del file:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Questa query restituisce le combinazioni di taxi e autisti, visualizzate in ordine decrescente in base al numero di corse.

Al prompt della directory Hive eseguire:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

I risultati della query vengono scritti nel file locale **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Esplorazione: valutazione della qualità dei dati mediante il controllo dei record di longitudine/latitudine non validi
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

Un obiettivo comune di analisi esplorativa dei dati è quello di eliminare i record non validi. L'esempio riportato in questa sezione determina se i campi di longitudine o latitudine contengono un valore esterno all'area di New York. Poiché è probabile che questi record contengano valori di longitudine/latitudine errati, è opportuno eliminarli da tutti i dati che dovranno essere usati per la modellazione.

Di seguito è riportato il contenuto del file **sample\_hive\_quality\_assessment.hql** per l'analisi.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Al prompt della directory Hive eseguire:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

L'argomento *-S* incluso in questo comando sostituisce l'immagine della schermata di stato relativa ai processi Map/Reduce di Hive. Ciò è utile poiché rende più leggibile l'immagine relativa alla schermata dell'output della query Hive.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Esplorazione: distribuzioni in classi binarie delle mance delle corse
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

Per il problema di classificazione binaria descritto nella sezione [Esempi di attività di stima](hive-walkthrough.md#mltasks) , è utile sapere se è stata offerta una mancia. Questa distribuzione delle mance è di tipo binario:

* Mancia lasciata (classe 1, tip\_amount > $ 0)  
* Nessuna mancia (classe 0, tip\_amount = $ 0)

Il file **sample\_hive\_tipped\_frequencies.hql** seguente esegue queste operazioni:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Al prompt della directory Hive eseguire:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Esplorazione: distribuzioni in classi nell'impostazione multiclasse
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

Per il problema di classificazione multiclasse descritto nella sezione [Esempi di attività di stima](hive-walkthrough.md#mltasks), questo set di dati si presta a una classificazione naturale per stimare l'importo delle mance. Per definire gli intervalli di mance nella query, è possibile usare i contenitori. Per ottenere le distribuzioni in classi per i diversi intervalli di mance, usare il file **sample\_hive\_tip\_range\_frequencies.hql**. Ecco il contenuto del file.

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

Eseguire il comando seguente dalla console della riga di comando di Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Esplorazione: calcolare la distanza diretta tra due posizioni di latitudine/longitudine
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

Potrebbe essere necessario determinare se esiste una differenza tra la distanza diretta tra due posizioni e la distanza effettiva della corsa del taxi. Un passeggero potrebbe essere meno propenso a lasciare la mancia se ritiene che l'autista abbia intenzionalmente scelto un itinerario più lungo.

Per visualizzare il confronto tra la distanza effettiva della corsa e la [distanza in base alla formula dell'emisenoverso](http://en.wikipedia.org/wiki/Haversine_formula) tra due punti di latitudine/longitudine (distanza di cerchio massimo), è possibile usare le funzioni trigonometriche disponibili in Hive:

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

Nella query precedente R è il raggio della terra in miglia e pi greco viene convertito in radianti. Notare che i punti di latitudine/longitudine vengono filtrati per rimuovere i valori esterni all'area di New York.

In questo caso, i risultati vengono scritti in una directory denominata **queryoutputdir**. La sequenza dei comandi seguenti crea prima di tutto questa directory di output e quindi esegue il comando Hive.

Al prompt della directory Hive eseguire:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


I risultati della query vengono scritti in nove BLOB di Azure (da **queryoutputdir/000000\_0** a **queryoutputdir/000008\_0**), all'interno del contenitore predefinito del cluster Hadoop.

Per visualizzare le dimensioni dei singoli BLOB, è possibile eseguire il comando seguente dal prompt della directory Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Per visualizzare il contenuto di un file specifico, ad esempio **000000\_0**, usare il comando `copyToLocal` di Hadoop.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> In presenza di file di grandi dimensioni, l'operazione `copyToLocal` può essere molto lenta e non è quindi consigliabile.  
> 
> 

Uno dei vantaggi principali del fatto che i dati si trovano in un BLOB di Azure è la possibilità di esplorarli in Machine Learning, usando il modulo [Import Data][import-data] (Importa dati).

## <a name="#downsample"></a>Sottocampionare i dati e creare modelli in Machine Learning
> [!NOTE]
> Questa attività viene in genere svolta da un data scientist.
> 
> 

Dopo la fase di analisi esplorativa dei dati, è ora possibile sottocampionare i dati per la creazione di modelli in Machine Learning. In questa sezione viene mostrato come usare una query Hive per sottocampionare i dati. Machine Learning accede quindi ai dati dal modulo [Import Data][import-data] (Importa dati).

### <a name="down-sampling-the-data"></a>Sottocampionamento dei dati
Questa procedura si articola in due passaggi. Prima di tutto, è necessario creare un join delle tabelle**nyctaxidb.trip** e **nyctaxidb.fare** con tre chiavi presenti in tutti i record: **medallion**, **hack\_license** e **pickup\_datetime**. Vengono quindi generate un'etichetta di classificazione binaria chiamata **tipped** e un'etichetta di classificazione multiclasse chiamata **tip\_class**.

Per poter usare i dati sottocampionati dal modulo [Import Data][import-data] (Importa dati) in Machine Learning, è necessario archiviare i risultati della query precedente in una tabella Hive interna. Nella sezione seguente verrà creata una tabella Hive interna, in cui verranno immessi i dati sottoposti a join e sottocampionati.

La query applica direttamente funzioni standard di Hive per generare le informazioni seguenti dal campo **pickup\_datetime**:
- Ora del giorno
- Settimana dell'anno
- Giorno della settimana (1 sta per lunedì e 7 sta per domenica)

La query restituisce anche la distanza diretta tra i punti di partenza e di arrivo. Per un elenco completo di queste funzioni, vedere [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Funzioni definite dall'utente LanguageManual).

La query esegue quindi il sottocampionamento dei dati in modo che i risultati possano essere aggiunti in Azure Machine Learning Studio. Solo l'1% del set di dati originale viene importato in Studio.

Ecco il contenuto del file **sample\_hive\_prepare\_for\_aml\_full.hql** che prepara i dati per la creazione del modello in Machine Learning:

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

Per eseguire questa query, dal prompt della directory Hive eseguire il comando seguente:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

È ora disponibile la tabella interna **nyctaxidb.nyctaxi_downsampled_dataset**, alla quale è possibile accedere tramite il modulo [Import Data][import-data] (Importa dati) da Machine Learning. Inoltre, è possibile usare questo set di dati per la creazione di modelli di Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Usare il modulo Import Data (Importa dati) in Machine Learning per accedere ai dati sottocampionati
Per inviare query Hive nel modulo [Import Data][import-data] (Importa dati) di Machine Learning, è necessario accedere a un'area di lavoro di Machine Learning. È anche necessario accedere alle credenziali del cluster e all'account di archiviazione associato.

Ecco alcuni dettagli sul modulo [Import Data][import-data] (Importa dati) e sui parametri da immettere:

**HCatalog server URI** (URI del server HCatalog): se il nome del cluster è **abc123**, l'URI sarà semplicemente: https://abc123.azurehdinsight.net.

**Hadoop user account name** (Nome dell'account utente Hadoop): nome utente scelto per il cluster (non il nome utente di accesso remoto).

**Hadoop user account password** (Password dell'account utente di Hadoop): password scelta per il cluster (non la password di accesso remoto).

**Location of output data** (Percorso dei dati di output): ossia Azure.

**Nome dell'account di archiviazione di Azure**: nome dell'account di archiviazione predefinito associato al cluster.

**Nome del contenitore di Azure**: nome del contenitore predefinito per il cluster, in genere corrisponde al nome del cluster. Per un cluster denominato **abc123**, il nome sarà semplicemente abc123.

> [!IMPORTANT]
> Qualsiasi tabella su cui si desidera eseguire una query tramite il modulo [Import Data][import-data] (Importa dati) di Machine Learning deve essere una tabella interna.
> 
> 

Ecco come determinare se una tabella **T** in un database **D.db** è una tabella interna. Dal prompt della directory Hive eseguire il comando seguente:

    hdfs dfs -ls wasb:///D.db/T

Se la tabella è una tabella interna e viene popolata, il relativo contenuto deve essere visualizzato in questa posizione.

Un altro modo per determinare se una tabella è una tabella interna consiste nell'usare Azure Storage Explorer. Questo strumento consente di passare al nome del contenitore del cluster predefinito e quindi filtrare in base al nome della tabella. Se la tabella e il relativo contenuto vengono visualizzati, allora si tratta di una tabella interna.

Ecco uno screenshot della query Hive e del modulo [Import Data][import-data] (Importa dati):

![Screenshot di una query Hive per il modulo Import Data (Importa dati)](./media/hive-walkthrough/1eTYf52.png)

Poiché i dati sottocampionati si trovano nel contenitore predefinito, la query Hive risultante da Machine Learning è molto semplice. È semplicemente **SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data**.

È ora possibile usare il set di dati come punto di partenza per la creazione di modelli di Machine Learning.

### <a name="mlmodel"></a>Creare modelli in Machine Learning
È ora possibile procedere alla creazione e alla distribuzione di modelli in [Machine Learning](https://studio.azureml.net). I dati sono pronti per essere usati per la risoluzione dei problemi relativi alle stime identificati in una delle sezioni precedenti:

- **Classificazione binaria**: consente di prevedere se per la corsa è stata lasciata o meno una mancia.

  **Strumento di apprendimento usato:** regressione logistica a due classi

  a. Per questo problema, l'etichetta (o classe) di destinazione è **tipped**. Il set di dati sottocampionato originale contiene alcune colonne che indicano le perdite di destinazione per questo esperimento di classificazione. In particolare, **tip\_class**, **tip\_amount** e **total\_amount** rivelano informazioni sull'etichetta di destinazione che non sono disponibili in fase di test. È possibile rimuovere queste colonne dalla valutazione tramite il modulo [Seleziona colonne in set di dati][select-columns].

  Il diagramma seguente mostra l'esperimento per stimare se per una corsa specifica è stata o meno lasciata una mancia:

  ![Diagramma dell'esperimento](./media/hive-walkthrough/QGxRz5A.png)

  b. Per questo esperimento, le distribuzioni dell'etichetta di destinazione sono approssimativamente 1:1.

   Il grafico seguente mostra la distribuzione delle etichette delle classi relative alla mancia per il problema di classificazione binaria:

  ![Grafico della distribuzione delle etichette delle classi relative alla mancia](./media/hive-walkthrough/9mM4jlD.png)

    Di conseguenza, si ottiene un'area sottesa alla curva (AUC) di 0,987, come mostrato nella figura seguente:

  ![Grafico del valore dell'area sottesa alla curva](./media/hive-walkthrough/8JDT0F8.png)

- **Classificazione multiclasse**: permette di stimare l'intervallo degli importi delle mance lasciate per la corsa, usando le classi definite in precedenza.

  **Strumento di apprendimento usato:** regressione logistica multiclasse

  a. Per questo problema, l'etichetta (o classe) di destinazione è **tip\_class**, che può accettare cinque valori diversi (0, 1, 2, 3, 4). Come nel caso della classificazione binaria, sono presenti alcune colonne che indicano le perdite di destinazione per questo esperimento. In particolare, **tipped**, **tip\_amount** e **total\_amount** rivelano informazioni sull'etichetta di destinazione che non sono disponibili in fase di test. È possibile rimuovere queste colonne tramite il modulo [Seleziona colonne in set di dati][select-columns].

  Il diagramma seguente mostra l'esperimento per stimare in quale bin è probabilmente inclusa una mancia. I bin sono: classe 0: tip = $ 0, classe 1: tip > $ 0 e tip <= $ 5, classe 2: tip > $ 5 e tip <= $ 10, classe 3: tip > $ 10 e tip <= $ 20 e classe 4: tip > $ 20.

  ![Diagramma dell'esperimento](./media/hive-walkthrough/5ztv0n0.png)

  Viene ora mostrata l'effettiva distribuzione delle classi di test. Le classi 0 e 1 sono quelle prevalenti, mentre le altre sono più rare.

  ![Grafico della distribuzione delle classi di test](./media/hive-walkthrough/Vy1FUKa.png)

  b. Per questo esperimento verrà usata una matrice di confusione per esaminare l'accuratezza delle stime. come illustrato qui:

  ![Matrice di confusione](./media/hive-walkthrough/cxFmErM.png)

  Si noti che mentre l'accuratezza delle classi è abbastanza soddisfacente per le classi prevalenti, il modello non è altrettanto efficace per l'"apprendimento" delle classi più rare.

- **Attività di regressione**: consente di prevedere l'importo della mancia lasciata per una corsa.

  **Strumento di apprendimento usato:** albero delle decisioni con boosting

  a. Per questo problema, l'etichetta (o classe) di destinazione è **tip\_amount**. In questo caso, le perdite di destinazione sono: **tipped**, **tip\_class** e **total\_amount**. Tutte queste variabili rivelano informazioni sull'importo delle mance che in genere non sono disponibili in fase di test. È possibile rimuovere queste colonne tramite il modulo [Seleziona colonne in set di dati][select-columns].

  Il diagramma seguente mostra l'esperimento per la stima dell'importo della mancia lasciata:

  ![Diagramma dell'esperimento](./media/hive-walkthrough/11TZWgV.png)

  b. Per problemi di regressione, l'accuratezza della stima viene misurata esaminando l'errore quadratico nelle stime e il coefficiente di determinazione:

  ![Screenshot delle statistiche delle stime](./media/hive-walkthrough/Jat9mrz.png)

  Qui il coefficiente di determinazione è 0,709, che implica che circa il 71% della varianza è rappresentato dai coefficienti del modello.

> [!IMPORTANT]
> Per altre informazioni su Azure Machine Learning e su come accedere a questo servizio e usarlo, vedere [Che cos'è Machine Learning?](../studio/what-is-machine-learning.md). Inoltre, il sito Web [Azure AI Gallery](https://gallery.cortanaintelligence.com/) contiene una vasta gamma di esperimenti e offre un'introduzione completa alle ricche funzionalità di Machine Learning.
> 
> 

## <a name="license-information"></a>Informazioni sulla licenza
Questa procedura di esempio e gli script contenuti sono forniti da Microsoft con licenza MIT. Per altre informazioni, vedere il file **LICENSE.txt** nella directory del codice di esempio in GitHub.

## <a name="references"></a>Riferimenti
•    [Pagina di Andrés Monroy per scaricare i dati sulle corse dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/)  
•    [Complemento dai dati sulle corse dei taxi di NYC di Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [Ricerche e statistiche su NYC Taxi and Limousine Commission](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



