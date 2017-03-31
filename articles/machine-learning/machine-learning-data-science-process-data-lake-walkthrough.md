---
title: 'Analisi scientifica dei dati scalabile in Azure Data Lake: procedura dettagliata end-to-end | Documentazione Microsoft'
description: "Come usare Azure Data Lake per eseguire attività di esplorazione di dati e di classificazione binaria su un set di dati."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 91a8207f-1e57-4570-b7fc-7c5fa858ffeb
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: bradsev;weig
translationtype: Human Translation
ms.sourcegitcommit: 29c718d0c34d1e2f9d17b285a7270541a9ff15cf
ms.openlocfilehash: c7444d457592538a26834091c77f49a3c1ef8591
ms.lasthandoff: 02/24/2017


---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Analisi scientifica dei dati scalabile in Azure Data Lake: procedura dettagliata end-to-end
Questa procedura dettagliata end-to-end illustra come usare Azure Data Lake per eseguire attività di esplorazione dei dati e di classificazione binaria su un campione del set di dati relativo alle corse e alle tariffe dei taxi di NYC, in modo da prevedere se un passeggero pagherà la mancia. Vengono esaminati i passaggi del [processo di analisi scientifica dei dati del team](http://aka.ms/datascienceprocess), end-to-end, dall'acquisizione dei dati al training modello e quindi alla distribuzione di un servizio Web che pubblica il modello.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) include tutte le funzionalità che consentono ai data scientist di archiviare con facilità dati di qualsiasi dimensione, forma e velocità e di eseguire attività di elaborazione di dati, analisi avanzate e modellazione di Machine Learning con scalabilità elevata e costi contenuti.   Il pagamento viene effettuato per i singoli processi, solo quando i dati vengono effettivamente elaborati. Analisi Azure Data Lake include U-SQL, un linguaggio che unisce la natura dichiarativa di SQL all'efficacia espressiva di C# per offrire funzionalità di query distribuite e scalabili. Consente di elaborare dati non strutturati applicando lo schema in fase di lettura, nonché di inserire logica e funzioni UDF personalizzate e aggiungere estensibilità per permettere il controllo granulare sulle modalità di esecuzione in scala. Per altre informazioni sulla filosofia di progettazione alla base di U-SQL, vedere questo [post di blog su Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Analisi Data Lake è anche un componente chiave di Cortana Analytics Suite e si integra con Azure SQL Data Warehouse, Power BI e Data Factory, per offrire una piattaforma completa per Big Data sul cloud e per le analisi avanzate.

Questa procedura dettagliata descrive prima di tutto i prerequisiti e le risorse necessari per completare le attività con Analisi Data Lake che costituiscono i processi di analisi scientifica dei dati e illustra come installarli. Delinea quindi i passaggi di elaborazione dei dati da eseguire con U-SQL e conclude illustrando come usare Python e Hive con Azure Machine Learning Studio per creare e distribuire modelli predittivi. 

### <a name="u-sql-and-visual-studio"></a>U-SQL e Visual Studio
Questa procedura dettagliata consiglia l'uso di Visual Studio per modificare gli script U-SQL ed elaborare il set di dati. Gli script U-SQL sono illustrati in questo articolo e sono disponibili in un file separato. Il processo include l'inserimento, l'esplorazione e il campionamento dei dati. Viene quindi illustrato come eseguire un processo U-SQL con script dal portale di Azure. Le tabelle Hive vengono create per i dati in un cluster HDInsight associato per semplificare la compilazione e la distribuzione di un modello di classificazione binario in Azure Machine Learning Studio.  

### <a name="python"></a>Python
Questa procedura dettagliata contiene anche una sezione in cui si descrive come creare e distribuire un modello predittivo usando Python con Azure Machine Learning Studio.  Per questa parte del processo viene fornito un notebook di Jupyter con gli script Python. Il notebook include codice per alcuni passaggi di progettazione di funzionalità aggiuntive e per la creazione di modelli come la classificazione multiclasse o la creazione di modelli di regressione, oltre al modello di classificazione binaria illustrato in questo articolo. L'attività di regressione consente di prevedere l'importo della mancia in base ad altre funzionalità relative alle mance. 

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning Studio consente di creare e distribuire modelli predittivi. Queste operazioni possono essere eseguite adottando un duplice approccio: prima con gli script Python e quindi con le tabelle Hive in un cluster HDInsight (Hadoop).

### <a name="scripts"></a>Script
In questa procedura dettagliata sono illustrati solo i passaggi principali. È possibile scaricare lo **script U-SQL** completo e il **notebook di Jupyter** da [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* Una sottoscrizione di Azure. Se non è già disponibile, vedere l'articolo che illustra [come ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Consigliato] Visual Studio 2013 o versione successiva. Se non è già installata una di queste versioni, è possibile scaricare una versione Community gratuita da [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Invece di Visual Studio, è possibile usare anche il portale di Azure per inviare query di Azure Data Lake. Le istruzioni per eseguire queste operazioni con Visual Studio e con il portale sono disponibili nella sezione intitolata **Elaborare i dati con U-SQL**. 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Preparare un ambiente di analisi scientifica dei dati per Azure Data Lake
Per preparare l'ambiente di analisi scientifica dei dati per questa procedura guidata, creare le risorse seguenti:

* Archivio Azure Data Lake (ADLS) 
* Analisi Azure Data Lake (ADLA)
* Account di archiviazione BLOB di Azure
* Account di Azure Machine Learning Studio
* Azure Data Lake Tools per Visual Studio (consigliato)

Questa sezione fornisce istruzioni per la creazione di tutte queste risorse. Se si sceglie di usare tabelle Hive con Azure Machine Learning, anziché Python, per creare un modello è necessario anche effettuare il provisioning di un cluster HDInsight (Hadoop). Questa procedura alternativa viene descritta in un'apposita sezione riportata di seguito.


> [!NOTE]
> **Azure Data Lake Store** può essere creato separatamente oppure quando si crea **Azure Data Lake Analytics** come archiviazione predefinita. Le istruzioni per la creazione di ognuna delle risorse sono fornite separatamente più avanti, ma l'account di archiviazione di Data Lake non deve essere creato in modo separato.
>
> 

### <a name="create-an-azure-data-lake-store"></a>Creare un Archivio Azure Data Lake


Creare un Archivio Azure Data Lake dal [portale di Azure](http://portal.azure.com). Per informazioni dettagliate, vedere [Creare un cluster HDInsight con Archivio Data Lake tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di configurare l'identità di AAD del cluster nel pannello **Origine dati** del pannello **Configurazione facoltativa** come illustrato in questo articolo. 

 ![3](./media/machine-learning-data-science-process-data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Creare un account di Analisi Azure Data Lake
Creare un account di Analisi Azure Data Lake dal [portale di Azure](http://portal.azure.com). Per informazioni dettagliate, vedere [Esercitazione: Introduzione ad Analisi Azure Data Lake con il portale di Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/machine-learning-data-science-process-data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Creare un account di archiviazione BLOB di Azure
Creare un account di archiviazione BLOB di Azure dal [portale di Azure](http://portal.azure.com). Per informazioni dettagliate, vedere la sezione Creare un account di archiviazione in [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

 ![5](./media/machine-learning-data-science-process-data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Configurare un account di Azure Machine Learning Studio
Iscriversi o accedere ad Azure Machine Learning Studio dalla pagina [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) . Fare clic sul pulsante **Per iniziare** e quindi scegliere l'opzione per "area di lavoro gratuita" o "area di lavoro standard". Dopo questa operazione sarà possibile creare esperimenti in Azure Machine Learning Studio.  

### <a name="install-azure-data-lake-tools-recommended"></a>Installare Azure Data Lake Tools [consigliato]
Installare Azure Data Lake Tools per la versione di Visual Studio in uso da [Azure Data Lake Tools per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/machine-learning-data-science-process-data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Al termine dell'installazione, aprire Visual Studio. L'opzione Data Lake dovrebbe essere disponibile nel menu in alto. Le risorse di Azure dovrebbero essere visualizzate nel pannello sinistro quando si accede all'account di Azure.

 ![7](./media/machine-learning-data-science-process-data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Set di dati NYC Taxi Trip
Il set di dati usato in questo articolo è disponibile pubblicamente, ovvero il [set di dati NYC Taxi Trip](http://www.andresmh.com/nyctaxitrips/). I dati di NYC Taxi Trip sono costituiti da circa 20 GB di file CSV compressi (circa 48 GB non compressi) e registrano oltre 173 milioni di corse singole nonché le tariffe pagate per ogni corsa. Il record di ogni corsa include le località e gli orari di partenza e di arrivo, il numero di patente anonimo (del tassista) e il numero di licenza (ID univoco del taxi). I dati sono relativi a tutte le corse per l'anno 2013 e vengono forniti nei due set di dati seguenti per ciascun mese:

* Il file CSV 'trip_data' contiene i dettagli delle corse, ad esempio il numero dei passeggeri, i punti partenza e arrivo, la durata e la lunghezza della corsa. Di seguito vengono forniti alcuni record di esempio:
  
       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
* Il file CSV 'trip_fare' contiene i dettagli della tariffa pagata per ciascuna corsa, ad esempio tipo di pagamento, importo, soprattassa e tasse, mance e pedaggi e l'importo totale pagato. Di seguito vengono forniti alcuni record di esempio:
  
       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La chiave univoca che consente di unire trip\_data e trip\_fare è composta da tre campi: medallion, hack\_licence e pickup\_datetime. È possibile accedere ai file CSV non elaborati da un BLOB di archiviazione di Azure pubblico. Lo script U-SQL per questo join è disponibile nella sezione [Unire le tabelle relative a corse e tariffe](#join) .

## <a name="process-data-with-u-sql"></a>Elaborare i dati con U-SQL
Le attività di elaborazione dei dati illustrate in questa sezione includono l'inserimento, il controllo della qualità, l'esplorazione e il campionamento dei dati. Viene illustrato anche come unire le tabelle relative a corse e tariffe. La sezione finale illustra l'esecuzione di un processo U-SQL con script dal portale di Azure. Ecco i collegamenti per ogni sottosezione:

* [Inserimento di dati: leggere dati dal BLOB pubblico](#ingest)
* [Controlli della qualità dei dati](#quality)
* [Esplorazione dei dati](#explore)
* [Unire le tabelle relative a corse e tariffe](#join)
* [Campionamento dei dati](#sample)
* [Eseguire processi U-SQL](#run)

Gli script U-SQL sono illustrati in questo articolo e sono disponibili in un file separato. È possibile scaricare gli **script U-SQL** completi da [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Per eseguire U-SQL, aprire Visual Studio, fare clic su **File --> Nuovo --> Progetto**, scegliere **Progetto U-SQL**, specificare un nome e salvare il progetto in una cartella.

![8](./media/machine-learning-data-science-process-data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Per eseguire U-SQL è possibile usare il portale di Azure anziché Visual Studio. In questo caso, è possibile passare alla risorsa Analisi Azure Data Lake nel portale e inviare direttamente le query come illustrato nella figura seguente.
> 
> 

![9](./media/machine-learning-data-science-process-data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Inserimento di dati: leggere dati dal BLOB pubblico
La posizione dei dati nel BLOB di Azure viene indicata come **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** e può essere estratta tramite **Extractors.Csv()**. Sostituire il nome del contenitore e il nome dell'account di archiviazione personali negli script seguenti per container_name@blob_storage_account_name nell'indirizzo wasb. Poiché i nomi di file hanno lo stesso formato, è possibile usare **trip\_data_{\*\}.csv** per leggere tutti i 12 file delle corse. 

    ///Read in Trip data
    @trip0 =
        EXTRACT 
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Poiché la prima riga include intestazioni, è necessario rimuovere le intestazioni e cambiare i tipi di colonna specificando i tipi appropriati. È possibile salvare i dati elaborati nell'archiviazione di Azure Data Lake usando **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/nome_cartella/nome_file**_ o nell'account di archiviazione BLOB di Azure usando **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

    // change data types
    @trip =
        SELECT 
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv(); 

    ////Output data to blob
    OUTPUT @trip   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();  

Analogamente, è possibile leggere nei set di dati relativi alle tariffe. Fare clic con il pulsante destro del mouse su Azure Data Lake Store. È possibile scegliere di esaminare i dati in **Portale di Azure --> Esplora dati** o **Esplora file** in Visual Studio. 

 ![10](./media/machine-learning-data-science-process-data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/machine-learning-data-science-process-data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Controlli della qualità dei dati
Dopo la lettura delle tabelle relative a corse e tariffe, è possibile eseguire controlli della qualità dei dati nel modo seguente. I file CSV risultati possono essere restituiti all'archivio BLOB di Azure o all'Archivio Azure Data Lake. 

È possibile trovare il numero di licenze e il numero univoco delle licenze:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month, 
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv(); 

È possibile trovare le licenze associate a più di 100 corse:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv(); 

È possibile trovare i record non validi a livello di valore pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

È possibile trovare i valori mancanti per alcune variabili:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT 
            vendor_id,
        SUM(missing_medallion) AS medallion_empty, 
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique  
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="explore"></a>Esplorazione dei dati
È possibile esplorare i dati per ottenere una migliore comprensione dei dati stessi.

È possibile trovare la distribuzione di corse associate o non associate alla mancia:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv(); 

È possibile trovare la distribuzione dell'importo della mancia con valori limite compresi tra 0, 5, 10 e 20 dollari.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv(); 

È possibile trovare le statistiche di base relative alla distanza della corsa:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT 
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance 
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

È possibile trovare i valori percentile relativi alla distanza della corsa:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv(); 


### <a name="join"></a>Unire le tabelle relative a corse e tariffe
Le tabelle relative alle corse e alle tariffe possono essere unite in base ai valori medallion, hack_license e pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 

    ////output data to ADL
    OUTPUT @model_data_full   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 


Per ogni livello di conteggio di passeggeri, è possibile calcolare il numero di record, l'importo medio della mancia, la varianza dell'importo della mancia e la percentuale di corse con mancia.

    // contigency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="sample"></a>Campionamento dei dati
Selezionare in modo casuale lo 0,1% dei dati dalla tabella unita:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv(); 

Eseguire quindi un campionamento stratificato in base alla variabile binaria tip_class:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 


### <a name="run"></a>Eseguire processi U-SQL
Al termine della modifica degli script U-SQL, è possibile inviarli al server usando il proprio account Analisi Azure Data Lake. Fare clic su **Data Lake**, **Invia processo**, selezionare il proprio **account Analisi**, scegliere **Parallelismo** e fare clic sul pulsante **Invia**.  

 ![12](./media/machine-learning-data-science-process-data-lake-walkthrough/12-submit-USQL.PNG)

Al termine del processo, lo stato del processo verrà visualizzato in Visual Studio per il monitoraggio. Al termine dell'esecuzione del processo, è anche possibile ripetere il processo di esecuzione del processo e individuare i passaggi collo di bottiglia per migliorare l'efficienza del processo. È anche possibile passare al portale di Azure per verificare lo stato dei processi U-SQL.

 ![13](./media/machine-learning-data-science-process-data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/machine-learning-data-science-process-data-lake-walkthrough/14-USQL-jobs-portal.PNG)

È ora possibile controllare i file di output nell'archivio BLOB di Azure o nel portale di Azure. Nel passaggio successivo verranno usati i dati del campione stratificato per la modellazione.

 ![15](./media/machine-learning-data-science-process-data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/machine-learning-data-science-process-data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Compilare e distribuire modelli in Azure Machine Learning
In questa sezione vengono illustrate le due opzioni disponibili per eseguire il pull dei dati in Azure Machine Learning. 

* Nella prima opzione vengono usati i dati campionati scritti in un BLOB di Azure (nel passaggio **Campionamento dei dati** precedente), quindi viene usato Python per creare e distribuire modelli in Azure Machine Learning. 
* Nella seconda opzione è possibile eseguire query sui dati direttamente in Azure Data Lake mediante una query Hive. Questa opzione richiede la creazione di un nuovo cluster HDInsight o l'uso di un cluster HDInsight esistente in cui le tabelle Hive facciano riferimento ai dati relativi alle corse dei taxi di New York in Archivio Azure Data Lake.  Entrambe le opzioni sono illustrate di seguito. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opzione 1: Usare Python per compilare e distribuire modelli di Machine Learning
Per creare e distribuire modelli di Machine Learning tramite Python, creare un notebook di Jupyter sul computer locale o in Azure Machine Learning Studio. Il notebook di Jupyter disponibile in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) include il codice completo per esplorare e visualizzare i dati, progettare funzionalità, creare modelli ed eseguire la distribuzione. Questo articolo illustra solo i passaggi relativi alla modellazione e alla distribuzione. 

### <a name="import-python-libraries"></a>Importare librerie Python
Per eseguire il notebook di Jupyter di esempio o il file di script Python, sono necessari i pacchetti di Python seguenti. Se si usa il servizio Notebook di Azure Machine Learning, questi pacchetti sono stati preinstallati.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Leggere i dati dal BLOB
* Connection String   
  
        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Leggere come testo
  
        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  
  ![17](./media/machine-learning-data-science-process-data-lake-walkthrough/17-python_readin_csv.PNG)    
* Aggiungere i nomi di colonna e separare le colonne
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Impostare alcune colonne su numeriche
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Compilare modelli di Machine Learning
In questo passaggio viene creato un modello di classificazione binaria per prevedere se una corsa è associata a una mancia o no. Nel notebook di Jupyter è possibile trovare altri due modelli, ovvero la classificazione multiclasse e i modelli di regressione.

* È prima di tutto necessario creare variabili fittizie che possono essere usate in modelli scikit-learn
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Creare il frame di dati per la modellazione
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* Training e test della suddivisione 60-40
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Regressione logistica nel set di training
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/machine-learning-data-science-process-data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Assegnare un punteggio al set di dati di test
  
        Y_test_pred = logit_fit.predict(X_test)
* Calcolare le metriche di valutazione
  
        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train
  
        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
        print fpr_test, tpr_test, thresholds_test
  
        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)
  
        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)
  
       ![c2](./media/machine-learning-data-science-process-data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Compilare l'API del servizio Web e utilizzarla in Python
Si vuole rendere operativo il modello di Machine Learning dopo la compilazione. In questa sezione viene usato come esempio il modello logistico binario. Assicurarsi che la versione di scikit-learn nel computer locale sia 0.15.1. Non è necessario preoccuparsi della versione se si usa il servizio Azure Machine Learning Studio.

* Trovare le credenziali dell'area di lavoro dalle impostazioni di Azure Machine Learning Studio. In Azure Machine Learning Studio fare clic su **Impostazioni** --> **Nome** --> **Token di autorizzazione**. 
  
    ![c3](./media/machine-learning-data-science-process-data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Creare un servizio Web
  
        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Ottenere le credenziali del servizio Web
  
        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
  
        print url
        print api_key
  
        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Chiamare un'API del servizio Web. È necessario attendere 5-10 secondi dopo il passaggio precedente.
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/machine-learning-data-science-process-data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opzione 2: Creare e distribuire modelli direttamente in Azure Machine Learning
Azure Machine Learning Studio può leggere i dati direttamente da Archivio Azure Data Lake e usarli quindi per creare e distribuire modelli. Questo approccio usa una tabella Hive che fa riferimento ad Archivio Azure Data Lake. In questo caso, tuttavia, è necessario eseguire il provisioning di un cluster Azure HDInsight separato in cui verrà creata la tabella Hive. Le sezioni seguenti mostrano come eseguire questa operazione. 

### <a name="create-an-hdinsight-linux-cluster"></a>Creare un cluster HDInsight Linux
Creare un cluster HDInsight (Linux) dal [portale di Azure](http://portal.azure.com). Per informazioni dettagliate, vedere la sezione **Creare un cluster Azure HDInsight con accesso a Azure Data Lake Store** in [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/machine-learning-data-science-process-data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Creare una tabella Hive in HDInsight
È ora possibile creare in un cluster HDInsight le tabelle Hive da usare in Azure Machine Learning Studio usando i dati archiviati in Archivio Azure Data Lake nel passaggio precedente. Passare al cluster HDInsight appena creato. Fare clic su **Impostazioni** --> **Proprietà** --> **Identità AAD del cluster** --> **Accesso a ADLS** e assicurarsi che l'account Azure Data Lake Store sia aggiunto all'elenco con diritti di lettura, scrittura ed esecuzione. 

 ![19](./media/machine-learning-data-science-process-data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Fare quindi clic su **Dashboard** accanto al pulsante **Impostazioni**. Verrà visualizzata una finestra. Fare clic su **Vista Hive** nell'angolo superiore destro della pagina. Verrà visualizzato l'**Editor di query**.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/machine-learning-data-science-process-data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Incollare gli script Hive seguenti per creare una tabella. Il percorso dell'origine dati è nella Guida di riferimento di Azure Data Lake Store: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Al termine dell'esecuzione della query, i risultati verranno visualizzati in modo analogo al seguente:

 ![22](./media/machine-learning-data-science-process-data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Compilare e distribuire modelli in Azure Machine Learning Studio
È ora possibile creare e distribuire un modello in grado di stabilire se sia stata lasciata o meno una mancia mediante Azure Machine Learning. I dati di esempio stratificati sono pronti per essere usati in questo problema di classificazione binaria (mancia o no). Con Azure Machine Learning Studio è possibile anche creare e distribuire modelli predittivi che usano la classificazione multiclasse (tip_class) e la regressione (tip_amount), ma in questo caso ci si limiterà a illustrare come gestire il caso usando il modello di classificazione binaria.

1. Inserire i dati in Azure ML tramite il modulo **Import Data** (Importa dati), disponibile nella sezione **Input e output dei dati**. Per altre informazioni, vedere la pagina di riferimento sul [modulo Import Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) (Importa dati).
2. Selezionare **Query Hive** come **origine dati** nel pannello delle **proprietà**.
3. Incollare lo script Hive seguente nell'editor **Hive database query** (Query di database Hive)
   
        select * from nyc_stratified_sample;
4. Immettere l'URI del cluster HDInsight (disponibile nel portale di Azure), le credenziali di Hadoop, la posizione dei dati di output e il nome dell'account di archiviazione di Azure/la chiave/il nome di contenitore.
   
   ![23](./media/machine-learning-data-science-process-data-lake-walkthrough/23-reader-module-v3.PNG)  

Un esempio relativo a un esperimento di classificazione binaria per la lettura di dati dalla tabella Hive è disponibile nella figura seguente.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthrough/24-AML-exp.PNG)

Dopo la creazione dell'esperimento, fare clic su **Set Up Web Service** --> **Predictive Web Service** (Imposta servizio Web - Servizio Web predittivo)

 ![25](./media/machine-learning-data-science-process-data-lake-walkthrough/25-AML-exp-deploy.PNG)

Eseguire l'esperimento di assegnazione dei punteggi creato automaticamente e al termine, fare clic su **Deploy Web Service** (Distribuisci servizio Web)

 ![26](./media/machine-learning-data-science-process-data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Il dashboard del servizio Web verrà visualizzato a breve:

 ![27](./media/machine-learning-data-science-process-data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Riepilogo
Seguendo questa procedura dettagliata è stato creato un ambiente di analisi scientifica dei dati per la creazione di soluzioni end-to-end scalabili in Azure Data Lake. Questo ambiente è stato quindi usato per analizzare un set di dati pubblico di grandi dimensioni, sottoposto ai passaggi del processo di analisi scientifica dei dati: dall'acquisizione dei dati al training del modello, fino alla distribuzione del modello come servizio Web. Per elaborare, esplorare e campionare i dati è stato usato U-SQL, mentre per creare e distribuire i modelli predittivi sono stati usati Python e Hive con Azure Machine Learning Studio.

## <a name="whats-next"></a>Passaggi successivi
Nel percorso di apprendimento relativo al [Processo di analisi scientifica dei dati per i team (TDSP)](http://aka.ms/datascienceprocess) sono inclusi alcuni collegamenti ad argomenti che descrivono ogni passaggio del processo di analisi avanzata. È disponibile una serie di procedure dettagliate collegate alla pagina [Processo di analisi scientifica dei dati per i team](data-science-process-walkthroughs.md) che illustrano come usare risorse e servizi nei vari scenari di analisi predittiva:

* [Processo di analisi scientifica dei dati per i team in azione: uso di SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md)
* [Processo di analisi scientifica dei dati per i team in azione: uso dei cluster Hadoop di HDInsight](machine-learning-data-science-process-hive-walkthrough.md)
* [Processo di analisi scientifica dei dati per i team: uso di SQL Sever](machine-learning-data-science-process-sql-walkthrough.md)
* [Panoramica del Processo di analisi scientifica dei dati con Spark in Azure HDInsight](machine-learning-data-science-spark-overview.md)


