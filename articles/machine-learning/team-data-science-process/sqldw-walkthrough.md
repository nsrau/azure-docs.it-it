---
title: Creare e distribuire un modello usando Analisi synapse di Azure - Team Data Science Process
description: Creare e distribuire un modello di apprendimento automatico usando Analisi synapse di Azure con un set di dati disponibile pubblicamente.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 96d0a5b2fb59e4612107d8ccbf7285fff7576585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128380"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Processo di analisi scientifica dei dati del team in azione: usando Azure Synapse AnalyticsThe Team Data Science Process in action: using Azure Synapse Analytics
In questa esercitazione viene descritto come creare e distribuire un modello di apprendimento automatico usando Analisi synapse di Azure per un set di dati disponibile pubblicamente, ovvero il set di dati [NYC Taxi Trips.In](https://www.andresmh.com/nyctaxitrips/) this tutorial, we walk you through building and deploying a machine learning model using Azure Synapse Analytics for a publicly available dataset -- the NYC Taxi Trips dataset. Il modello di classificazione binaria costruito stima se una mancia viene pagata o meno per un viaggio.  I modelli includono la classificazione multiclasse (indipendentemente dal fatto che vi sia o meno una mancia) e la regressione (la distribuzione per gli importi di mancia pagati).

La procedura segue il flusso di lavoro del [Processo di analisi scientifica dei dati per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) . Viene illustrato come configurare un ambiente di analisi scientifica dei dati, come caricare i dati in Azure Synapse Analytics e come usare Azure Synapse Analytics o un blocco appunti IPython per esplorare i dati e progettare le funzionalità per la modellazione. Viene quindi illustrato come compilare e distribuire un modello con Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>Set di dati NYC Taxi Trips
I dati di NYC Taxi Trip sono costituiti da circa 20 GB di file compressi con estensione CSV (circa 48 GB non compressi) e registrano oltre 173 milioni di corse singole, nonché le tariffe pagate per ogni corsa. Il record di ogni corsa include le posizioni e l'ora di partenza e arrivo, il numero di patente (del tassista) in modalità anonima e il numero di licenza (ID univoco del taxi). I dati sono relativi a tutte le corse per l'anno 2013 e vengono forniti nei due set di dati seguenti per ciascun mese:

1. Il file **trip_data.csv** contiene i dettagli delle corse, ad esempio il numero dei passeggeri, i punti di partenza e arrivo, la durata e la lunghezza della corsa. Di seguito vengono forniti alcuni record di esempio:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Il file **trip_fare.csv** contiene i dettagli della tariffa pagata per ciascuna corsa, ad esempio tipo di pagamento, importo, soprattassa e tasse, mance e pedaggi e l'importo totale pagato. Di seguito vengono forniti alcuni record di esempio:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La **chiave univoca** usata per unire trip\_data e trip\_fare è costituita dai tre campi seguenti:

* medallion,
* hack\_license e
* pickup\_datetime.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Risolvere tre tipi di attività di stima
Sono stati formulati tre problemi di stima basati sul valore di *tip\_amount* per illustrare tre tipi di attività di modellazione:

1. **Classificazione binaria**: Per prevedere se una mancia è stata pagata per un viaggio, vale a dire, un importo di *suggerimento\_* che è maggiore di 0 è un esempio positivo, mentre un importo di *suggerimento\_* di 0 dollari è un esempio negativo.
2. **Classificazione multiclasse**: consente di prevedere l'intervallo in cui rientra la mancia lasciata per la corsa. Il valore *tip\_amount* viene suddiviso in cinque bin o classi:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Attività di **regressione**: Per prevedere l'importo della mancia pagata per un viaggio.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Configurare l'ambiente di scienza dei dati di Azure per l'analisi avanzata
Per configurare l'ambiente di analisi scientifica dei dati di Azure, seguire questi passaggi.

**Creare l'account di archiviazione BLOB di Azure**

* Quando si effettua il provisioning dell'archivio BLOB di Azure, scegliere una località geografica per l'archivio BLOB di Azure il più vicino possibile agli **Stati Uniti centro-meridionali**, dove vengono archiviati i dati relativi alle corse dei taxi di New York. I dati verranno copiati tramite AzCopy dal contenitore di archiviazione BLOB pubblico a un contenitore nell'account di archiviazione. Quanto più l'archivio BLOB di Azure è vicino agli Stati Uniti centro-meridionali tanto più rapidamente verrà completata questa attività (passaggio 4).
* Per creare il proprio account di archiviazione di Azure, seguire i passaggi descritti in [Informazioni sugli account di archiviazione](../../storage/common/storage-create-storage-account.md)di Azure.To create your own Azure Storage account, follow the steps outlined to About Azure Storage accounts . Assicurarsi di prendere nota dei valori delle credenziali dell'account di archiviazione seguenti perché saranno necessarie più avanti nella procedura dettagliata.

  * **Nome account di archiviazione**
  * **Chiave dell'account di archiviazioneStorage Account Key**
  * **Nome contenitore** (in cui si vogliono salvare i dati nell'archivio BLOB di Azure)

**Effettuare il provisioning dell'istanza di Azure Synapse Analytics.Provision your Azure Synapse Analytics instance.**
Seguire la documentazione in Creare ed eseguire query in Azure SQL Data Warehouse nel portale di Azure per eseguire il provisioning di un'istanza di Azure Synapse Analytics.Follow the documentation at Create and [query an Azure SQL Data Warehouse in the Azure portal](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) to provision a Azure Synapse Analytics instance. Assicurarsi di eseguire notazioni sulle credenziali di Azure Synapse Analytics seguenti che verranno usate nei passaggi successivi.

* **Nome**server \<: Nome server>.database.windows.net
* **Nome SQLDW (database)**
* **Nome utente**
* **Password**

**Installare Visual Studio e SQL Server Data Tools.** Per istruzioni, vedere [Introduzione a Visual Studio 2019 per SQL Data Warehouse.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)

**Connettersi ad Azure Synapse Analytics con Visual Studio.Connect to your Azure Synapse Analytics with Visual Studio.** Per istruzioni, vedere i passaggi 1 & 2 in [Connect to Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Eseguire la query SQL seguente sul database creato in Azure Synapse Analytics (anziché la query fornita nel passaggio 3 dell'argomento di connessione) per **creare una chiave master.**
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Creare un'area di lavoro di Azure Machine Learning nella sottoscrizione di Azure.** Per istruzioni, vedere [Creare un'area di lavoro di Machine Learning di Azure](../studio/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Caricare i dati in Azure Synapse AnalyticsLoad the data into Azure Synapse Analytics
Aprire una console dei comandi di Windows PowerShell. Eseguire i comandi di PowerShell seguenti per scaricare i file di script SQL di esempio condivisi con l'utente in GitHub in una directory locale specificata con il parametro *-DestDir*. È possibile sostituire il valore del parametro *-DestDir* con quello di qualsiasi directory locale. Se *-DestDir* non esiste, verrà creata dallo script di PowerShell.

> [!NOTE]
> Potrebbe essere necessario fare clic su **Esegui come amministratore** quando si esegue lo script di PowerShell seguente se sono richiesti privilegi di amministratore per creare o scrivere nella directory *DestDir* .
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Al termine dell'esecuzione, la directory di lavoro corrente diventa *-DestDir*. Verrà visualizzata una schermata simile alla seguente:

![Modifiche alla directory di lavoro corrente][19]

In *-DestDir*eseguire lo script di PowerShell seguente in modalità amministratore:

    ./SQLDW_Data_Import.ps1

Quando lo script di PowerShell viene eseguito per la prima volta, verrà richiesto di immettere le informazioni dall'analisi Synapse di Azure e dall'account di archiviazione BLOB di Azure.When the PowerShell script runs for the first time, you will be asked to input the information from your Azure Synapse Analytics and your Azure blob storage account. Quando l'esecuzione di questo script di PowerShell viene completata per la prima volta, le credenziali inserite risulteranno scritte in un file di configurazione SQLDW.conf nella directory di lavoro presente. L'esecuzione futura di questo file di script di PowerShell può leggere tutti i parametri necessari da questo file di configurazione. Se è necessario modificare alcuni parametri, è possibile scegliere di inserirli nella schermata al prompt eliminando questo file di configurazione e inserendo i valori dei parametri come richiesto oppure di cambiare i valori dei parametri modificando il file SQLDW.conf nella directory *-DestDir* .

> [!NOTE]
> Per evitare conflitti di nomi di schema con quelli già presenti in Azure Synapse Analytics, durante la lettura dei parametri direttamente dal file SQLDW.conf, viene aggiunto un numero casuale a 3 cifre al nome dello schema dal file SQLDW.conf come schema predefinito nome per ogni corsa. Lo script di PowerShell può richiedere un nome schema: il nome può essere specificato a discrezione dell'utente.
>
>

Questo file di **script di PowerShell** completa le attività seguenti:

* **Esegue il download e l'installazione di AzCopy**, se non è già installato

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Copia i dati dal BLOB pubblico all'account di archiviazione BLOB privato** con AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* Carica i **dati usando Polybase (eseguindo LoadDataToSQLDW.sql)** nell'analisi Synapse di Azure dall'account di archiviazione BLOB privato con i comandi seguenti.

  * Creare uno schema

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Creare una credenziale con ambito di database

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Creare un'origine dati esterna per un BLOB di Archiviazione di AzureCreate an external data source for an Azure Storage blob

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Creare un formato di file esterno da un file con estensione csv. I dati non sono compressi e i campi sono separati dal carattere barra verticale.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Creare tabelle esterne relative a tariffe e corse per il set di dati relativo alle corse dei taxi di New York nell'archivio BLOB di Azure.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Caricare dati da tabelle esterne nell'archiviazione BLOB di Azure in Azure Synapse AnalyticsLoad data from external tables in Azure blob storage to Azure Synapse Analytics

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Creazione di una tabella dati di esempio (NYCTaxi_Sample) con inserimento di dati dalla selezione di query SQL sulle tabelle delle corse e delle tariffe. (Alcuni passaggi di questa procedura dettagliata devono utilizzare questa tabella di esempio.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

La posizione geografica degli account di archiviazione influisce sui tempi di caricamento.

> [!NOTE]
> A seconda della posizione geografica dell'account di archiviazione BLOB privato, il processo di copia dei dati da un BLOB pubblico all'account di archiviazione privato può richiedere circa 15 minuti o anche più a lungo e il processo di caricamento dei dati dall'account di archiviazione in Azure Analisi synapse di Azure potrebbe richiedere 20 minuti o più.
>
>

È necessario decidere cosa fare se si dispone di file di origine e destinazione duplicati.

> [!NOTE]
> Se i file con estensione csv da copiare dall'archivio BLOB pubblico all'account di archiviazione BLOB privato esistono già nell'account di archiviazione BLOB privato, AzCopy chiederà se li si vuole sovrascrivere. Se non si vuole farlo, digitare **n** quando richiesto. Per sovrascriverli **tutti**, digitare **a** quando richiesto. È anche possibile digitare **y** per sovrascrivere i file con estensione csv singolarmente.
>
>

![Output di AzCopy][21]

È possibile usare i propri dati. Se i dati sono nel computer locale nell'applicazione reale, è tuttavia possibile usare AzCopy per caricare i dati locali nell'archiviazione BLOB di Azure privato. È sufficiente sostituire il percorso **Source**, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, nel comando di AzCopy del file di script di PowerShell con la directory locale contenente i dati.

> [!TIP]
> Se i dati si trova già nell'archiviazione BLOB di Azure privata nell'applicazione reale, è possibile ignorare il passaggio AzCopy nello script di PowerShell e caricare direttamente i dati in Azure Azure Synapse Analytics.If your data is already in your private Azure blob storage in your real life application, you can skip the AzCopy step in the PowerShell script and directly upload the data to Azure Azure Synapse Analytics. Saranno necessarie altre modifiche dello script per adattarlo al formato dei dati.
>
>

Questo script di PowerShell inserisce anche le informazioni di Azure Synapse Analytics nei file di esempio di esplorazione dei dati SQLDW_Explorations.sql, SQLDW_Explorations.ipynb e SQLDW_Explorations_Scripts.py in modo che questi tre file siano pronti per essere provati immediatamente dopo il completamento dello script di PowerShell.

Al termine dell'esecuzione, verrà visualizzata una schermata simile alla seguente:

![Output di un'esecuzione corretta dello script][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Esplorazione dei dati e progettazione delle funzionalità in Azure Synapse AnalyticsData exploration and feature engineering in Azure Synapse Analytics
In questa sezione viene eseguita l'esplorazione e la generazione di funzionalità dei dati eseguendo query SQL su Azure Synapse Analytics direttamente tramite **Visual Studio Data Tools.** Tutte le query SQL usate in questa sezione sono disponibili nello script di esempio *SQLDW_Explorations.sql*. Questo file è già stato scaricato nella directory locale dallo script di PowerShell. È anche possibile recuperarlo da [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ma il file in GitHub non dispone delle informazioni di Azure Synapse Analytics inserite.

Connettersi a Azure Synapse Analytics usando Visual Studio con il nome di accesso e la password di Azure Synapse Analytics e aprire **Esplora oggetti SQL** per confermare che il database e le tabelle sono state importate. Recuperare il file *SQLDW_Explorations.sql*.

> [!NOTE]
> Per aprire un editor di query Parallel Data Warehouse (PDW), usare il comando **Nuova query** mentre è selezionato PDW in **Esplora oggetti di SQL**. L'editor di query SQL standard non è supportato da PDW.
>
>

Di seguito sono riportati i tipi di attività di esplorazione dei dati e di generazione delle funzionalità eseguite in questa sezione:Here are the types of data exploration and feature generation tasks performed in this section:

* Esplorazione delle distribuzioni di dati di un numero ridotto di campi in diverse finestre temporali.
* Indagine sulla qualità dei dati dei campi di longitudine e latitudine.
* Generazione di etichette di classificazione binaria e multiclasse basata su **tip\_amount**.
* Generazione di funzionalità calcolo/confronto delle distanze delle corse.
* Unione di due tabelle ed estrazione di un campione casuale che verrà utilizzato per la creazione di modelli.

### <a name="data-import-verification"></a>Verifica dell'importazione dei dati
Queste query consentono una verifica rapida del numero di righe e di colonne nelle tabelle popolate in precedenza tramite l'importazione in blocco in parallelo di Polybase,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Output:** si dovrebbero ottenere 173.179.759 righe e 14 colonne.

### <a name="exploration-trip-distribution-by-medallion"></a>Esplorazione: distribuzione delle corse per licenza
Questa query di esempio identifica le licenze (numeri dei taxi) che hanno eseguito più di 100 corse in un periodo specificato. Per la query verrà usata la tabella partizionata poiché è condizionata dallo schema di partizione di **pickup\_datetime**. Per la query del set di dati completo verrà inoltre utilizzata la tabella partizionata e/o l'analisi dell'indice.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Uscita:** La query deve restituire una tabella con righe che specificano i 13.369 medaglioni (taxi) e il numero di viaggi completati nel 2013. L'ultima colonna contiene il totale delle corse eseguite.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Esplorazione: distribuzione delle corse per licenza e hack_license
Questo esempio identifica le licenze (numeri dei taxi) e i numeri di hack_license (tassisti) che hanno eseguito più di 100 corse in un periodo specificato.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Output:** la query dovrebbe restituire una tabella con 13.369 righe in cui vengono specificati quali dei 13.369 ID di automobile/autista hanno eseguito più di 100 corse nel 2013. L'ultima colonna contiene il totale delle corse eseguite.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Valutazione della qualità dei dati: verifica dei record con longitudine o latitudine errate
In questo esempio viene esaminato se uno dei campi relativi alla longitudine o alla latitudine contiene un valore non valido (i gradi radianti devono essere compresi tra -90 e 90) o presenta le coordinate (0, 0).

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Output:** la query restituisce 837.467 corse i cui campi longitudine e/o latitudine non sono validi.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Esplorazione: distribuzione delle corse per le quali è stata lasciata una mancia e di quelle per le quali non è stata lasciata una mancia
Questo esempio trova il numero di corse per cui è stata lasciata una mancia rispetto a quelle per cui non è stata lasciata una mancia in un periodo specificato oppure nel set di dati completo, se copre tutto l'anno (come in questo caso). Questa distribuzione riflette la distribuzione delle etichette binarie che dovranno essere utilizzate in seguito per la creazione di modelli di classificazione binaria.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Output:** la query dovrebbe restituire le frequenze di mance seguenti per l'anno 2013: 90.447.622 corse per cui è stata lasciata una mancia e 82.264.709 corse per cui non è stata lasciata una mancia.

### <a name="exploration-tip-classrange-distribution"></a>Esplorazione: distribuzione della classe o dell'intervallo delle mance
In questo esempio viene calcolata la distribuzione degli intervalli delle mance in un determinato periodo (o nel set di dati completo, in caso di un anno intero). Questa distribuzione delle classi di etichette verrà utilizzata in un secondo momento per la modellazione della classificazione multiclasse.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Output:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Esplorazione: calcolo e confronto della distanza delle corse
In questo esempio, la longitudine e la latitudine del prelievo e dello scarico vengono convertite in punti geografici SQL, viene calcolata la distanza della corsa tramite la differenza dei punti geografici di SQL e viene restituito un campione casuale dei risultati per il confronto. Nell'esempio i risultati vengono limitati unicamente alle coordinate valide tramite la query per la valutazione della qualità dei dati illustrata in precedenza.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Progettazione di funzionalità con le funzioni SQL
Le funzioni SQL a volte possono essere una valida opzione per progettare funzionalità. In questa procedura dettagliata viene definita una funzione SQL per calcolare la distanza diretta tra le località di partenza e di arrivo. È possibile eseguire gli script SQL seguenti in **Visual Studio Data Tools**.

Ecco lo script SQL che definisce la funzione della distanza.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Ecco un esempio per chiamare questa funzione per generare le funzionalità nella query SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Output:** questa query genera una tabella (con 2.803.538 righe) con la latitudine e la longitudine del prelievo e dello scarico e le corrispondenti distanze dirette in miglia. Ecco i risultati per le prime tre righe:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Preparazione dei dati per la creazione del modello
Le query riportate di seguito consentono di unire le tabelle **nyctaxi\_trip** e **nyctaxi\_fare**, generare un'etichetta di classificazione binaria **tipped**, un'etichetta di classificazione multiclasse **tip\_class** e di estrarre un campione dall'intero set di dati unito. Il campionamento viene eseguito recuperando un subset delle corse in base all'orario di partenza.  Questa query può essere copiata e incollata direttamente nel modulo di importazione dei dati di importazione di [Azure Machine Learning Studio (classico)](https://studio.azureml.net) per l'inserimento diretto dei dati dall'istanza del database SQL in Azure.This query can be copied then pasted directly in the Azure Machine Learning Studio (classic) [Import Data][import-data] module for direct data ingestion from the SQL Database instance in Azure. La query esclude i record con le coordinate errate (0, 0).

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Una volta pronti a proseguire con Azure Machine Learning, è possibile effettuare una delle seguenti operazioni:

1. Salvare la query SQL finale per estrarre e campionare i dati e copiare e incollare la query direttamente in un[modulo] importa dati Import Data] in Azure Machine Learning oppure
2. Rendere persistenti i dati campionati e progettati che si prevede di usare per la creazione di modelli in una nuova tabella di Analisi synapse di Azure e usare la nuova tabella nel modulo Importa dati importare dati in Azure Machine Learning.Persist the sampled and engineerd data you plan to use for model building in a new Azure Synapse Analytics table and use the new table in the [Import Data][import-data] module in Azure Machine Learning. Lo script di PowerShell nel passaggio precedente ha eseguito questa attività per l'utente. È possibile leggere direttamente in questa tabella nel modulo Import Data.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Esplorazione dei dati e progettazione di funzionalità in IPython Notebook
In questa sezione verrà eseguita l'esplorazione dei dati e la generazione di funzionalità usando query Python e SQL su Azure Synapse Analytics create in precedenza. Un IPython Notebook di esempio denominato **SQLDW_Explorations.ipynb** e un file di script di Python **SQLDW_Explorations_Scripts.py** sono stati scaricati nella directory locale. Sono disponibili anche in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Questi due file sono identici negli script di Python. Il file di script di Python viene fornito nel caso in cui non sia presente un server IPython Notebook. Questi due file di Python di esempio sono stati progettati in **Python 2.7**.

Le informazioni necessarie di Azure Synapse Analytics nell'esempio IPython Notebook e il file di script Python scaricato nel computer locale sono stati collegati in precedenza dallo script di PowerShell.The needed Azure Synapse Analytics information in the sample IPython Notebook and the Python script file downloaded to your local machine has been pluginto in precedent. Possono essere eseguiti senza alcuna modifica.

Se è già stata configurata un'area di lavoro di Azure Machine Learning, è possibile caricare direttamente il blocco appunti IPython di esempio nel servizio Blocco appunti IPython di AzureML e avviare l'esecuzione. Ecco i passaggi per caricare nel servizio AzureML IPython Notebook:Here are the steps to upload to the AzureML IPython Notebook service:

1. Accedere all'area di lavoro di Azure Machine Learning, fare clic su **Studio"** nella parte superiore e quindi su **NOTEBOOKS** sul lato sinistro della pagina Web.

    ![Fare clic su Studio quindi su NOTEBOOK][22]
2. Fare clic su **NUOVO** nell'angolo in basso a sinistra della pagina Web e selezionare **Python 2**. Specificare quindi un nome per il notebook e fare clic sul segno di spunta per creare il nuovo IPython Notebook vuoto.

    ![Fare clic su NUOVO, quindi selezionare Python 2][23]
3. Fare clic sul simbolo **Jupyter** nell'angolo in alto a sinistra del nuovo blocco appunti IPython.

    ![Fare clic sul simbolo di Jupyter][24]
4. Trascinare l'esempio di IPython Notebook nella pagina **albero** del servizio IPython Notebook di Azure Machine Learning e fare clic su **Upload** (Carica). L'esempio di IPython Notebook verrà quindi caricato nel servizio IPython Notebook di AzureML.

    ![Fare clic su Upload (Carica).][25]

Per eseguire l'esempio di IPython Notebook o il file di script di Python, sono necessari i pacchetti di Python seguenti. Se si usa il servizio IPython Notebook di AzureML, questi pacchetti sono stati preinstallati.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

Quando si creano soluzioni analitiche avanzate in Azure Machine Learning con dati di grandi dimensioni, ecco la sequenza consigliata:When building advanced analytical solutions on Azure Machine Learning with large data, here is the recommended sequence:

* Leggere un piccolo campione di dati in un frame di dati in memoria.
* Eseguire alcune visualizzazioni ed esplorazioni tramite i dati campionati.
* Sperimentare la progettazione di funzionalità utilizzando i dati campionati.
* Per l'esplorazione dei dati più ampia, la manipolazione dei dati e la progettazione delle funzionalità, usare Python per eseguire query SQL direttamente su Azure Synapse Analytics.For larger data exploration, data manipulation and feature engineering, use Python to issue SQL Queries directly against the Azure Synapse Analytics.
* Decidere la dimensione del campione adatta per la creazione di modelli di Azure Machine Learning.

Di seguito vengono forniti alcuni esempi di esplorazione dei dati, visualizzazione dei dati e progettazione di funzionalità. Altre esplorazioni dei dati sono disponibili nell'esempio di IPython Notebook e nel file di script di Python di esempio.

### <a name="initialize-database-credentials"></a>Inizializzare le credenziali di database
Inizializzare le impostazioni di connessione del database nelle seguenti variabili:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Creare la connessione al database
Ecco la stringa di connessione che crea la connessione al database.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Segnalare il numero di righe e di colonne nella tabella <nyctaxi_trip>
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Numero di righe totali = 173179759
* Numero di colonne totali = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Segnalare il numero di righe e di colonne nella tabella <nyctaxi_fare>
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Numero di righe totali = 173179759
* Numero di colonne totali = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Lettura di un piccolo esempio di dati dal database di analisi di Azure SynapseRead-in a small data sample from the Azure Synapse Analytics Database
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Il tempo per la lettura della tabella di esempio è di 14,096495 secondi.
Numero di righe e di colonne recuperate = (1000, 21)

### <a name="descriptive-statistics"></a>Statistiche descrittive
Ora è possibile esplorare i dati campionati. Si inizia da alcune statistiche descrittive per **trip\_distance** (o qualsiasi altro campo che si sceglie di specificare).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualizzazione: esempio di box plot
Successivamente si consulterà il box plot per la distanza delle corse, per visualizzare i quantili.

    df1.boxplot(column='trip_distance',return_type='dict')

![Output di box plot][1]

### <a name="visualization-distribution-plot-example"></a>Visualizzazione: esempio di tracciato di distribuzione
Tracciati che visualizzano la distribuzione e un istogramma per le distanze delle corse campionate.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Output del tracciato di distribuzione][2]

### <a name="visualization-bar-and-line-plots"></a>Visualizzazione: tracciati a barre e linee
In questo esempio, la distanza delle corse viene suddivisa in cinque contenitori e vengono visualizzati i risultati di questa suddivisione.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

La distribuzione binaria precedente può essere rappresentata in un tracciato a barre o linee con:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Output del tracciato a barre][3]

e

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Output del tracciato a linee][4]

### <a name="visualization-scatterplot-examples"></a>Visualizzazione: esempi di grafico a dispersione
Viene eseguito un grafico a dispersione tra **trip\_time\_in\_secs** e **trip\_distance** per verificare se esiste una correlazione.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Output di grafico a dispersione di relazione tra tempo e distanza][6]

Allo stesso modo, è possibile verificare la relazione tra **rate\_code** e **trip\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Output di grafico a dispersione di relazione tra codice e distanza][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Esplorazione nei dati campionati usando query SQL in IPython Notebook
In questa sezione vengono esaminate le distribuzioni dei dati usando i dati campionati che vengono salvati in modo permanente nella nuova tabella creata in precedenza. Esplorazioni simili possono essere eseguite utilizzando le tabelle originali.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Esplorazione: segnalare il numero di righe e di colonne nella tabella campionata
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Esplorazione: distribuzione delle corse per cui è stata lasciata una mancia e di quelle per cui non è stata lasciata una mancia
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Esplorazione: distribuzione della classe delle mance
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Esplorazione: tracciare la distribuzione delle mance per classe
    tip_class_dist['tip_freq'].plot(kind='bar')

![Grafico n. 26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Esplorazione: distribuzione giornaliera delle corse
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Esplorazione: distribuzione delle corse per licenza
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Esplorazione: distribuzione delle corse per licenza e hack_license
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Esplorazione: distribuzione degli orari delle corse
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Esplorazione: distribuzione delle distanze delle corse
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Esplorazione: distribuzione dei tipi di pagamento
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verificare l'aspetto finale della tabella con funzionalità
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Creare modelli in Azure Machine Learning
È ora possibile procedere alla creazione di modelli e alla distribuzione del modello in [Azure Machine Learning.](https://studio.azureml.net) I dati sono pronti per essere usati nei problemi di stima identificati in precedenza, in modo specifico:

1. **Classificazione binaria**: Per prevedere se una mancia è stata pagata o meno per un viaggio.
2. **Classificazione multiclasse**: Per prevedere l'intervallo di mancia pagata, in base alle classi definite in precedenza.
3. Attività di **regressione**: Per prevedere l'importo della mancia pagata per un viaggio.

Per iniziare l'esercizio di modellazione, accedere all'area di lavoro di **Azure Machine Learning (classica).** Se non è ancora stata creata un'area di lavoro di Machine Learning, vedere Creare un'area di lavoro di [Azure Machine Learning Studio (classica).](../studio/create-workspace.md)

1. Per iniziare a usare Azure Machine Learning, vedere [Che cos'è Azure Machine Learning Studio (classico)?](../studio/what-is-ml-studio.md)
2. Accedere ad [Azure Machine Learning Studio (classico).](https://studio.azureml.net)
3. La home page di Machine Learning Studio (classica) offre numerose informazioni, video, esercitazioni, collegamenti alla guida di riferimento per i moduli e altre risorse. Per altre informazioni su Azure Machine Learning, vedere il Centro documentazione di Azure Machine Learning.For more information about Azure Machine Learning, see the [Azure Machine Learning Documentation Center.](https://azure.microsoft.com/documentation/services/machine-learning/)

Un tipico esperimento di training comprende i passaggi seguenti:

1. Creare un esperimento **NUOVO.**
2. Ottenere i dati in Azure Machine Learning Studio (classico).
3. Pre-elaborare, trasformare e modificare i dati in base alle esigenze.
4. Generazione di funzionalità, se necessario.
5. Suddivisione dei dati in set di dati di training, convalida o test(o creazione di set di dati distinti per ciascuna tipologia).
6. Selezione di uno o più algoritmi Machine Learning, a seconda del problema di apprendimento da risolvere. Ad esempio, classificazione binaria, classificazione multiclasse, regressione.
7. Training di uno o più modelli tramite il set di dati di training.
8. Calcolo del punteggio del set di dati di convalida mediante i modelli sottoposti a training.
9. Valutazione dei modelli per calcolare la metrica rilevante per il problema di apprendimento.
10. Ottimizzare i modelli e selezionare il modello migliore da distribuire.

In questo esercizio sono già stati esplorati e progettati i dati in Analisi synapse di Azure e si è deciso di utilizzare le dimensioni del campione da eseguire in Azure Machine Learning Studio (classico). Ecco la procedura per compilare uno o più modelli di stima:

1. Ottenere i dati in Azure Machine Learning Studio (classico) usando il modulo [Importa dati]importare[dati,] disponibile nella sezione **Input e output dati.** Per altre informazioni, vedere la pagina di riferimento sul modulo [Importa dati][import-data].

    ![Import Data di Azure ML][17]
2. Selezione di **Database SQL di Azure** come **Origine dati** nel pannello **Proprietà**.
3. Immissione del nome DNS del database nel campo **Nome server database** . Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Immissione del **Nome database** nel campo corrispondente.
5. Immettere il *nome utente SQL* in **Server user account name** (Nome account utente server) e la *password* in **Server user account password** (Password account utente server).
7. Nell'area di testo Modifica **query database** incollare la query che estrae i campi di database necessari (inclusi eventuali campi calcolati, ad esempio le etichette) e i dati vengono campionati in base alle dimensioni di esempio desiderate.

Nella figura seguente è riportato un esempio di esperimento di classificazione binaria che legge i dati direttamente dal database di Azure Synapse Analytics (ricordarsi di sostituire i nomi delle tabelle nyctaxi_trip e nyctaxi_fare in base al nome dello schema e ai nomi di tabella usati nel database procedura dettagliata). È possibile creare esperimenti dello stesso tipo per i problemi di classificazione multiclasse e di regressione.

![Formazione su Azure ML][10]

> [!IMPORTANT]
> Negli esempi di estrazione dei dati di modellazione e di query di campionamento forniti nelle sezioni precedenti, **tutte le etichette per i tre esercizi sulla creazione dei modelli sono incluse nella query**. Un passaggio importante (richiesto) in ciascun esercizio sulla modellazione consiste nell'**escludere** le etichette non necessarie per gli altri due problemi ed eventuali **perdite di destinazione**. Ad esempio, con la classificazione binaria, usare l'etichetta **tipped** ed escludere i campi **tip\_class**, **tip\_amount** e **total\_amount**. Questi ultimi sono perdite di destinazione in quanto implicano la mancia pagata.
>
> Per escludere eventuali colonne non necessarie o le perdite di destinazione, è possibile usare il modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) o [Edit Metadata][edit-metadata] (Modifica metadati). Per altre informazioni, vedere le pagine di riferimento per [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) ed [Edit Metadata][edit-metadata] (Modifica metadati).
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Distribuire modelli in Azure Machine Learning
Quando il modello è pronto, è possibile distribuirlo in modo semplice come servizio Web direttamente dall'esperimento. Per ulteriori informazioni sulla distribuzione di servizi Web Azure ML, vedere [Distribuzione di un servizio Web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Per distribuire un nuovo servizio Web, è necessario effettuare le seguenti operazioni:

1. Creare un esperimento di assegnazione di punteggio.
2. Distribuire il servizio web.

Per creare un esperimento di assegnazione dei punteggi da un esperimento di training **completato**, fare clic su **CREATE SCORING EXPERIMENT** (CREA ESPERIMENTO DI ASSEGNAZIONE PUNTEGGI) nella barra delle azioni inferiore.

![Valutazione di Azure][18]

Azure Machine Learning tenterà di creare un esperimento di assegnazione di punteggio basato sui componenti dell'esperimento di training. In particolare, verranno effettuate le seguenti operazioni:

1. Salvataggio del modello sottoposto a training e rimozione dei moduli di training del modello.
2. Identificazione di una **porta di input** logica per rappresentare lo schema di dati di input previsto.
3. Identificazione di una **porta di output** logica per rappresentare lo schema di output del servizio Web previsto.

Quando viene creato l'esperimento di assegnazione del punteggio, esaminare i risultati e apportare le regole necessarie. Una regolazione tipica consiste nel sostituire il set di dati di input o la query con uno che esclude i campi etichetta, perché questi campi etichetta non verranno mappati allo schema quando si chiama il servizio. È inoltre consigliabile ridurre le dimensioni del set di dati di input e/o della query a pochi record, sufficienti a indicare lo schema di input. Per la porta di output, di solito vengono esclusi tutti i campi di input e inclusi soltanto **Scored Labels** (Etichette con punteggio) e **Scored Probabilities** (Probabilità con punteggio) nell'output, tramite il modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati).

Nella figura di seguito viene fornito un esperimento di assegnazione dei punteggi di esempio. Quando si è pronti per la distribuzione, fare clic sul pulsante **PUBBLICA SERVIZIO WEB** nella barra delle azioni inferiore.

![Pubblicazione di Azure ML][11]

## <a name="summary"></a>Riepilogo
Ricapitolando quanto è stato fatto, durante questa procedura è stato creato un ambiente di analisi scientifica dei dati di Azure da usare con set di dati pubblici di grandi dimensioni, seguendo l'intero Processo di analisi scientifica dei dati per i team, dall'acquisizione dei dati al training del modello e quindi alla distribuzione di un servizio Web Azure Machine Learning.

### <a name="license-information"></a>Informazioni sulla licenza
Questa procedura dettagliata di esempio e gli script e i blocchi di appunti IPython che la accompagnano sono condivisi da Microsoft nella licenza MIT. Controllare il file LICENSE.txt nella directory del codice di esempio su GitHub per ulteriori dettagli.

## <a name="references"></a>Riferimenti
- [Andrés Monroy NYC Taxi Trips Scarica Pagina](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC's Taxi Trip Dati di Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [Ricerca e statistica della Commissione per taxi e limousine a New York](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
