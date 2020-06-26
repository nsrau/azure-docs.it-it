---
title: 'Esercitazione: Introduzione ad Azure Synapse Analytics'
description: Questa esercitazione illustra i passaggi di base per la configurazione e l'uso di Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791861"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Introduzione ad Azure Synapse Analytics

Questa esercitazione descrive tutti i passaggi di base per configurare e usare Azure Synapse Analytics.

## <a name="prepare-a-storage-account"></a>Preparare un account di archiviazione

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Creare un nuovo account di archiviazione con le impostazioni seguenti:

    |Scheda|Impostazione | Valore consigliato | Descrizione |
    |---|---|---|---|
    |Nozioni di base|**Nome account di archiviazione**| È possibile assegnare un nome qualsiasi.|In questo documento verrà denominato **contosolake**.|
    |Nozioni di base|**Tipo di account**|Il valore deve essere impostato su **Archiviazione V2**.||
    |Nozioni di base|**Posizione**|Scegliere la località desiderata.| È consigliabile che l'area di lavoro di Azure Synapse Analytics e l'account Azure Data Lake Storage Gen2 risiedano nella stessa area.|
    |Avanzate|**Data Lake Storage Gen2**|**Enabled**| Azure Synapse funziona solo con gli account di archiviazione in cui questa impostazione è abilitata.|
    |||||

1. Dopo aver creato l'account di archiviazione, selezionare **Controllo di accesso (IAM)** nel riquadro a sinistra. Assegnare quindi i ruoli seguenti o verificare che siano già assegnati:
    1. Assegnare a se stessi al ruolo **Proprietario**.
    1. Assegnare a se stessi il ruolo **Proprietario dei dati dei BLOB di archiviazione**.
1. Nel riquadro a sinistra selezionare **Contenitori** e creare un contenitore.
1. È possibile assegnare al contenitore il nome desiderato. In questo documento verrà assegnato il nome **users**.
1. Accettare l'impostazione predefinita **Livello di accesso pubblico**, quindi selezionare **Crea**.

Nel passaggio seguente verranno configurati l'area di lavoro di Azure Synapse per usare questo account di archiviazione come account di archiviazione "primario" e il contenitore per archiviare i dati dell'area di lavoro. L'area di lavoro archivia i dati in tabelle Apache Spark. Archivia i log delle applicazioni Spark in una cartella denominata **/synapse/nome_area_di_lavoro**.

## <a name="create-a-synapse-workspace"></a>Creare un'area di lavoro di Synapse

1. Aprire il [portale di Azure](https://portal.azure.com) e nella parte superiore cercare **Synapse**.
1. Nei risultati della ricerca, in **Servizi** selezionare **Azure Synapse Analytics (anteprima delle aree di lavoro)** .
1. Selezionare **Aggiungi** per creare un'area di lavoro usando queste impostazioni:

    |Scheda|Impostazione | Valore consigliato | Descrizione |
    |---|---|---|---|
    |Nozioni di base|**Nome area di lavoro**|È possibile assegnare un nome qualsiasi.| In questo documento verrà usato il nome **myworkspace**.|
    |Nozioni di base|**Area**|Usare la stessa area dell'account di archiviazione.|

1. In **Selezionare Data Lake Storage Gen 2** selezionare l'account e il contenitore creati in precedenza.
1. Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

## <a name="verify-access-to-the-storage-account"></a>Verificare l'accesso all'account di archiviazione

Le identità gestite per l'area di lavoro di Azure Synapse potrebbero già avere accesso all'account di archiviazione. Per verificare, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com) e aprire l'account di archiviazione primario scelto per l'area di lavoro.
1. Selezionare **Controllo di accesso (IAM)** dal riquadro a sinistra.
1. Assegnare i ruoli seguenti o verificare che siano già assegnati. Per l'identità dell'area di lavoro e il nome dell'area di lavoro verrà usato lo stesso nome.
    1. Per il ruolo **Collaboratore ai dati dei BLOB di archiviazione** nell'account di archiviazione, assegnare **myworkspace** come identità dell'area di lavoro.
    1. Assegnare **myworkspace** come nome dell'area di lavoro.

1. Selezionare **Salva**.

## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro di Synapse nel [portale di Azure](https://portal.azure.com). Nella parte superiore della sezione **Panoramica** selezionare **Avvia Synapse Studio**.
* Passare a https://web.azuresynapse.net e accedere all'area di lavoro.

## <a name="create-a-sql-pool"></a>Creare un pool SQL

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool SQL**.
1. Selezionare **Nuovo** e immettere le impostazioni seguenti:

    |Impostazione | Valore consigliato | 
    |---|---|---|
    |**Nome del pool SQL**| **SQLDB1**|
    |**Livello di prestazioni**|**DW100C**|
    |||

1. Selezionare **Rivedi e crea** > **Crea**. Il pool SQL è pronto in pochi minuti. Il pool SQL viene associato a un database del pool SQL anch'esso denominato **SQLDB1**.

Un pool SQL utilizza risorse fatturabili finché è attivo. È possibile sospendere il pool in un secondo momento per ridurre i costi.

## <a name="create-an-apache-spark-pool"></a>Creare un pool di Apache Spark

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool di Apache Spark**.
1. Selezionare **Nuovo** e immettere le impostazioni seguenti:

    |Impostazione | Valore consigliato | 
    |---|---|---|
    |**Nome del pool di Apache Spark**|**Spark1**
    |**Dimensioni nodo**| **Small**|
    |**Numero di nodi**| Impostare il valore minimo e il valore massimo su 3|

1. Selezionare **Rivedi e crea** > **Crea**. Il pool di Apache Spark è pronto in pochi secondi.

> [!NOTE]
> Nonostante il nome, un pool di Apache Spark non è simile a un pool SQL. Si tratta solo di alcuni metadati di base usati per indicare all'area di lavoro di Azure Synapse come interagire con Spark.

Poiché sono metadati, i pool di Spark non possono essere avviati o arrestati.

Quando si esegue un'attività Spark in Azure Synapse, si specifica un pool di Spark da usare. Il pool indica ad Azure Synapse il numero di risorse Spark da usare. Verranno addebitati i costi solo per le risorse usate. Quando si interrompe attivamente l'uso del pool, le risorse raggiungono automaticamente il timeout e vengono riciclate.

> [!NOTE]
> I database Spark vengono creati in modo indipendente dai pool di Spark. In un'area di lavoro è sempre presente un database Spark **predefinito**. È possibile creare ulteriori database Spark.

## <a name="the-sql-on-demand-pool"></a>Pool SQL su richiesta

Ogni area di lavoro include un pool predefinito denominato **SQL su richiesta**. Questo pool non può essere eliminato. Il pool SQL su richiesta consente di usare SQL senza la necessità di creare o gestire un pool SQL in Azure Synapse.

Diversamente dagli altri tipi di pool, la fatturazione per SQL su richiesta è basata sulla quantità di dati analizzati per eseguire la query e non sul numero di risorse usate per eseguirla.

* SQL su richiesta dispone di database SQL su richiesta indipendenti da qualsiasi pool SQL su richiesta.
* A un'area di lavoro è sempre associato esattamente un pool SQL su richiesta denominato **SQL su richiesta**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Caricare i dati di esempio di NYC Taxi nel database SQLDB1

1. Nel menu blu più in alto di Synapse Studio selezionare l'icona **?** .
1. Selezionare **Getting started** > **Getting started hub** (Attività iniziali > Hub attività iniziali).
1. Nella scheda etichettata **Query sample data** (Dati di esempio query) selezionare il pool SQL denominato **SQLDB1**.
1. Selezionare **Query data** (Dati query). Verrà visualizzata brevemente una notifica di caricamento dei dati di esempio. Nella parte superiore di Synapse Studio viene visualizzata una barra di stato azzurra che indica che è in corso il caricamento dei dati in SQLDB1.
1. Quando la barra di stato diventa verde, eliminarla.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Esplorare i dati di esempio di NYC Taxi nel pool SQL

1. In Synapse Studio passare all'hub **Data** (Dati).
1. Passare a **SQLDB1** > **Tables**. Saranno disponibili numerose tabelle.
1. Fare clic con il pulsante destro del mouse sulla tabella **dbo.Trip** e selezionare **New SQL Script** > **Select TOP 100 Rows** (Nuovo script SQL > Seleziona prime 100 righe).
1. Attendere mentre viene creato ed eseguito un nuovo script SQL.
1. Si noti che nella parte superiore dello script SQL l'opzione **Connetti a** è impostata automaticamente sul pool SQL denominato **SQLDB1**.
1. Sostituire il testo dello script SQL con il codice ed eseguirlo.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Questa query mostra come le distanze totali e la distanza media delle corse sono correlate al numero di passeggeri.
1. Nella finestra dei risultati dello script SQL modificare la **Vista** impostandola su **Grafico** per visualizzare i risultati come un grafico a linee.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Caricare i dati di NYC Taxi nel database nyctaxi di Spark

I dati sono disponibili in una tabella in **SQLDB1**. Caricarli in un database Spark denominato **nyctaxi**.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo).
1. Selezionare **+**  > **Notebook**.
1. Nella parte superiore del notebook impostare il valore per **Collega a** su **Spark1**.
1. Selezionare **Aggiungi codice** per aggiungere una cella di codice del notebook e incollare il testo seguente:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Passare all'hub **Dati**, fare clic con il pulsante destro del mouse su **Database** e scegliere **Aggiorna**. Verranno visualizzati i database seguenti:

    - **SQLDB1** (pool SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizzare i dati dell'esempio NYC Taxi con Spark e notebook

1. Tornare al notebook.
1. Creare una nuova cella di codice e immettere il testo seguente. Eseguire quindi la cella per visualizzare i dati di NYC Taxi caricati nel database Spark **nyctaxi**.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Eseguire il codice seguente per avviare la stessa analisi effettuata in precedenza con il pool SQL **SQLDB1**. Questo codice salva i risultati dell'analisi in una tabella denominata **nyctaxi.passengercountstats** e consente di visualizzarli.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nei risultati della cella selezionare **Grafico** per visualizzare i dati.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Personalizzare la visualizzazione dei dati con Spark e notebook

È possibile controllare il rendering dei grafici tramite i notebook. Il codice seguente mostra un semplice esempio. Usa le note librerie **matplotlib** e **seaborn**. Il rendering del codice verrà eseguito nella stessa tipologia di grafico a linee delle query SQL eseguite in precedenza.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```

## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Caricare dati da una tabella Spark in una tabella del pool SQL

In precedenza sono stati copiati i dati dalla tabella del pool SQL **SQLDB1.dbo.Trip** nella tabella Spark **nyctaxi.trip**. In seguito, tramite Spark i dati sono stati aggregati nella tabella Spark **nyctaxi.passengercountstats**. A questo punto, i dati verranno copiati da **nyctaxi.passengercountstats** in una tabella del pool SQL denominata **SQLDB1.dbo.PassengerCountStats**.

Eseguire la cella seguente nel notebook. La tabella Spark aggregata verrà copiata nuovamente nella tabella del pool SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analizzare i dati di NYC Taxi nei database Spark tramite SQL su richiesta

Le tabelle nei database Spark sono automaticamente visibili e possono essere usate da SQL su richiesta per l'esecuzione di query.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo) e creare un nuovo script SQL.
1. Impostare **Connetti a** su **SQL su richiesta**.
1. Incollare il testo seguente nello script ed eseguire lo script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > La prima volta che si esegue una query che usa SQL su richiesta, la raccolta delle risorse SQL necessarie per l'esecuzione delle query richiederà circa 10 secondi. Le query successive saranno molto più veloci.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orchestrare le attività con le pipeline

È possibile orchestrare un'ampia gamma di attività in Azure Synapse.

1. In Synapse Studio passare all'hub **Orchestrate** (Orchestrazione).
1. Selezionare **+**  > **Pipeline** per creare una nuova pipeline.
1. Passare all'hub **Develop** (Sviluppo) e trovare il notebook creato in precedenza.
1. Trascinare il notebook nella pipeline.
1. Nella pipeline selezionare **Aggiungi trigger** > **Nuovo/Modifica**.
1. In **Scegliere un trigger** selezionare **Nuovo** e in **ricorrenza** impostare il trigger per essere eseguito ogni ora.
1. Selezionare **OK**.
1. Selezionare **Pubblica tutti**. La pipeline verrà eseguita ogni ora.
1. Per eseguire la pipeline adesso senza attendere l'ora successiva, selezionare **Aggiungi trigger** > **Nuovo/Modifica**.

## <a name="work-with-data-in-a-storage-account"></a>Usare i dati in un account di archiviazione

Fino a questo momento sono stati illustrati gli scenari in cui i dati risiedono nei database nell'area di lavoro. Verrà ora illustrato come usare i file negli account di archiviazione. In questo scenario viene usato l'account di archiviazione primario dell'area di lavoro e il contenitore specificato durante la creazione dell'area di lavoro stessa.

* Nome dell'account di archiviazione: **contosolake**
* Nome del contenitore nell'account di archiviazione: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Creare file CSV e Parquet nell'account di archiviazione

Eseguire il codice seguente in un notebook per creare un file CSV e un file Parquet nell'account di archiviazione.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analizzare i dati in un account di archiviazione

1. In Synapse Studio passare all'hub **Data** (Dati) e quindi selezionare **Linked** (Collegati).
1. Passare a **Account di archiviazione** > **myworkspace (Primario - contosolake)** .
1. Selezionare **users (primario)** . Verrà visualizzata la cartella **NYCTaxi**. Al suo interno saranno presenti due cartelle denominate **PassengerCountStats.csv** e **PassengerCountStats.parquet**.
1. Aprire la cartella **PassengerCountStats.parquet**. Al suo interno sarà presente un file parquet con un nome simile a *part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet*.
1. Fare clic con il pulsante destro del mouse su **.parquet**, quindi scegliere **Nuovo notebook**. Verrà creato un notebook con una cella simile alla seguente:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Eseguire la cella.
1. Fare clic con il pulsante destro del mouse sul file parquet al suo interno, quindi scegliere **New SQL script** > **SELECT TOP 100 rows** (Nuovo script SQL > Seleziona prime 100 righe). Verrà creato uno script SQL simile al seguente:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     Nello script il campo **Collega a** è impostato su **SQL su richiesta**.

1. Eseguire lo script.

## <a name="visualize-data-with-power-bi"></a>Visualizzare i dati con Power BI

A partire dai dati di NYC Taxi, sono stati creati set di dati aggregati in due tabelle:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

È possibile collegare un'area di lavoro di Power BI all'area di lavoro di Azure Synapse, per visualizzare facilmente i dati nell'area di lavoro di Power BI. È possibile modificare i report di Power BI direttamente nell'area di lavoro di Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Creare un'area di lavoro di Power BI

1. Accedere a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Creare una nuova area di lavoro di Power BI denominata **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Collegare l'area di lavoro di Azure Synapse alla nuova area di lavoro di Power BI

1. In Synapse Studio passare a **Gestisci** > **Servizi collegati**.
1. Selezionare **Nuovo** > **Connetti a Power BI** e impostare questi campi:

    |Impostazione | Valore consigliato | 
    |---|---|
    |**Nome**|**NYCTaxiWorkspace1**|
    |**Nome area di lavoro**|**NYCTaxiWorkspace1**|

1. Selezionare **Crea**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Creare un set di dati di Power BI che usa i dati dell'area di lavoro di Azure Synapse

1. In Synapse Studio passare a **Develop** > **Power BI** (Sviluppo > Power BI).
1. Passare a **NYCTaxiWorkspace1** > **Set di dati di Power BI** e selezionare **New Power BI dataset** (Nuovo set di dati di Power BI).
1. Passare il puntatore del mouse sul database **SQLDB1** e selezionare **Download .pbids file** (Scarica file .pbids).
1. Aprire il file **PBIDS** scaricato. Verrà aperto Power BI Desktop che si connetterà automaticamente a **SQLDB1** nell'area di lavoro di Azure Synapse.
1. Se viene visualizzata una finestra di dialogo denominata **Database SQL Server**:
    1. Selezionare l'**account Microsoft**.
    1. Selezionare **Accedi** e accedere al proprio account.
    1. Selezionare **Connetti**.
1. All'apertura della finestra di dialogo **Navigator**, selezionare la tabella **PassengerCountStats** e quindi **Carica**.
1. Nella finestra di dialogo **Impostazioni di connessione** selezionare **DirectQuery** > **OK**.
1. Fare clic sul pulsante **Report** a sinistra.
1. Aggiungere un **grafico a linee** al report.
    1. Trascinare la colonna **PassengerCount** in **Visualizzazioni** > **Asse**.
    1. Trascinare le colonne **SumTripDistance** e **AvgTripDistance** in **Visualizzazioni** > **Valori**.
1. Nella scheda **Home** selezionare **Pubblica**.
1. Fare clic su **Salva** per salvare le modifiche.
1. Scegliere il nome file **PassengerAnalysis.pbix**, quindi selezionare **Salva**.
1. In **Selezionare una destinazione** scegliere **NYCTaxiWorkspace1** e quindi fare clic su **Seleziona**.
1. Attendere il completamento della pubblicazione.

### <a name="configure-authentication-for-your-dataset"></a>Configurare l'autenticazione per il set di dati

1. Aprire [powerbi.microsoft.com](https://powerbi.microsoft.com/) e fare clic su **Accedi**.
1. Sul lato sinistro, in **Aree di lavoro** selezionare l'area di lavoro **NYCTaxiWorkspace1**.
1. All'interno dell'area di lavoro individuare un set di dati denominato **Passenger Analysis** e un report denominato **Passenger Analysis**.
1. Passare il puntatore del mouse sul set di dati **PassengerAnalysis**, selezionare il pulsante con i puntini di sospensione (...) e quindi selezionare **Impostazioni**.
1. In **Credenziali origine dati** impostare il campo **Metodo di autenticazione** su **OAuth2** e selezionare **Accedi**.

### <a name="edit-a-report-in-synapse-studio"></a>Modificare un report in Synapse Studio

1. Tornare a Synapse Studio e selezionare **Close and refresh** (Chiudi e aggiorna).
1. Passare all'hub **Develop** (Sviluppo).
1. Passare il puntatore del mouse su **Power BI** e fare clic per aggiornare il nodo **Report di Power BI**.
1. In **Power BI** sono ora visibili gli elementi seguenti:
    1. In **NYCTaxiWorkspace1** > **Set di dati di Power BI**, un nuovo set di dati denominato **PassengerAnalysis**.
    1. In **NYCTaxiWorkspace1** > **Report di Power BI**, un nuovo report denominato **PassengerAnalysis**.
1. Selezionare il report **PassengerAnalysis**. Il report verrà aperto e sarà possibile modificarlo direttamente all'interno di Synapse Studio.

## <a name="monitor-activities"></a>Monitorare le attività

1. In Synapse Studio passare all'hub **Monitor** (Monitoraggio).
1. In questa posizione è possibile visualizzare una cronologia di tutte le attività che si verificano nell'area di lavoro e quelle attualmente attive.
1. Esaminare **Esecuzioni pipeline**, **Applicazioni Apache Spark** e **Richieste SQL** per visualizzare le attività eseguite nell'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Azure Synapse Analytics (anteprima delle aree di lavoro)](overview-what-is.md).

