---
title: 'Esercitazione: Introduzione ad Azure Synapse Analytics'
description: Procedura per comprendere rapidamente i concetti di base in Azure Synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 00f93086fec62c08c5241d868fc5104a1197cff3
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84605409"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Introduzione ad Azure Synapse Analytics

Questo documento descrive tutti i passaggi di base necessari per configurare e usare Azure Synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Preparare un account di archiviazione da usare nell'area di lavoro Synapse

* Aprire il [portale di Azure](https://portal.azure.com)
* Creare un nuovo account di archiviazione con le impostazioni seguenti:

    |Scheda|Impostazione | Valore consigliato | Descrizione |
    |---|---|---|---|
    |Nozioni di base|**Nome account di archiviazione**| È possibile assegnare un nome qualsiasi.|In questo documento viene fatto riferimento al nome `contosolake`.|
    |Nozioni di base|**Tipo di account**|Deve essere impostato su `StorageV2`||
    |Nozioni di base|**Posizione**|È possibile scegliere una posizione qualsiasi| È consigliabile che l'area di lavoro di Synapse e l'account Azure Data Lake Storage (ADLS) Gen2 si trovino nella stessa area.|
    |Avanzate|**Data Lake Storage Gen2**|`Enabled`| Azure Synapse funziona solo con gli account di archiviazione in cui questa impostazione è abilitata.|

1. Dopo aver creato l'account di archiviazione, selezionare **Controllo di accesso (IAM)** nel riquadro di spostamento a sinistra. Assegnare quindi i ruoli seguenti o verificare che siano già assegnati. 

    a. * Assegnare a se stessi il ruolo di **Proprietario** nell'account di archiviazione b. * Assegnare a se stessi il ruolo di **Proprietario dei dati dei BLOB di archiviazione** nell'account di archiviazione

1. Nel riquadro di spostamento a sinistra selezionare **Contenitori** e creare un contenitore. È possibile assegnare un nome qualsiasi. Accettare l'impostazione predefinita **Livello di accesso pubblico**. In questo documento il contenitore viene chiamato `users`. Selezionare **Crea**. 

Nel passaggio seguente verrà configurata l'area di lavoro di Synapse per usare questo account di archiviazione come account di archiviazione "primario" e il contenitore per archiviare i dati dell'area di lavoro. L'area di lavoro archivia i dati nelle tabelle Apache Spark e nei registri applicazioni Spark di questo account in una cartella denominata `/synapse/workspacename`.

## <a name="create-a-synapse-workspace"></a>Creare un'area di lavoro di Synapse

* Aprire il [portale di Azure](https://portal.azure.com) e nella parte superiore cercare `Synapse`.
* Nei risultati della ricerca, in **Servizi** selezionare **Azure Synapse Analytics (anteprima delle aree di lavoro)**
* Selezionare **+ Aggiungi** per creare un'area di lavoro usando queste impostazioni

    |Scheda|Impostazione | Valore consigliato | Descrizione |
    |---|---|---|---|
    |Nozioni di base|**Nome area di lavoro**|È possibile assegnare un nome qualsiasi.| In questo documento viene usato `myworkspace`|
    |Nozioni di base|**Area**|Trovare la corrispondenza con l'area dell'account di archiviazione|

1. In **Selezionare Data Lake Storage Gen 2** selezionare l'account e il contenitore creati in precedenza.

1. Selezionare **Rivedi e crea**. Selezionare **Crea**. L'area di lavoro è pronta in pochi minuti.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Verificare che l'identità del servizio gestito dell'area di lavoro di Synapse abbia accesso all'account di archiviazione

Anche se questa operazione potrebbe essere già stata eseguita per conto dell'utente, in ogni caso è necessario verificare.

1. Aprire il [portale di Azure](https://portal.azure.com) e aprire l'account di archiviazione primario scelto per l'area di lavoro.
1. Selezionare **Controllo di accesso (IAM)** nel riquadro di spostamento a sinistra. Assegnare quindi i ruoli seguenti o verificare che siano già assegnati. 
    a. Assegnare l'identità dell'area di lavoro al ruolo **Collaboratore ai dati dei BLOB di archiviazione** nell'account di archiviazione. L'identità dell'area di lavoro ha lo stesso nome di quest'ultima. In questo documento il nome dell'area di lavoro è `myworkspace` e pertanto l'identità dell'ara di lavoro è `myworkspaced`
1. Selezionare **Salva**.
    
## <a name="launch-synapse-studio"></a>Avviare Synapse Studio

Dopo aver creato l'area di lavoro di Synapse, è possibile aprire Synapse Studio in due modi diversi:
* Aprire l'area di lavoro di Synapse nel [portale di Azure](https://portal.azure.com) e nella parte superiore della sezione **Panoramica** selezionare **Avvia Synapse Studio**
* Passare direttamente a https://web.azuresynapse.net e accedere all'area di lavoro.

## <a name="create-a-sql-pool"></a>Creare un pool SQL

1. Nel riquadro di spostamento a sinistra di Synapse Studio selezionare **Gestisci > Pool SQL**
1. Selezionare **+Nuovo** e immettere le impostazioni seguenti:

    |Impostazione | Valore consigliato | 
    |---|---|
    |**Nome del pool SQL**| `SQLDB1`|
    |**Livello di prestazioni**|`DW100C`|

1. Selezionare **Rivedi e crea** e quindi seleziona **Crea**.
1. Il pool SQL è pronto in pochi minuti. Quando viene creato, il pool SQL viene associato a un database del pool SQL denominato anche **SQLDB1**.

Un pool SQL usa risorse fatturabili finché è attivo. È possibile sospendere il pool in un secondo momento per ridurre i costi.

## <a name="create-an-apache-spark-pool"></a>Creare un pool di Apache Spark

1. Nel riquadro a sinistra di Synapse Studio selezionare **Gestisci > Pool di Apache Spark**
1. Selezionare **+Nuovo** e immettere le impostazioni seguenti:

    |Impostazione | Valore consigliato | 
    |---|---|
    |**Nome del pool di Apache Spark**|`Spark1`
    |**Dimensioni nodo**| `Small`|
    |**Numero di nodi**| Impostare il valore minimo e il valore massimo su 3|

1. Selezionare **Rivedi e crea** e quindi seleziona **Crea**.
1. Il pool di Apache Spark è pronto in pochi secondi.

> [!NOTE]
> Nonostante il nome, un pool di Apache Spark non è simile a un pool SQL. Si tratta solo di alcuni metadati di base usati per indicare all'area di lavoro di Synapse come interagire con Spark. 

Poiché si tratta di metadati, i pool di Spark non possono essere avviati o arrestati. 

Quando si esegue un'attività Spark in Synapse, si specifica un pool di Spark da usare. Il pool indica a Synapse il numero di risorse Spark da usare. Si paga solo per le risorse che vengono usate. Quando si interrompe attivamente l'utilizzo del pool, le risorse vengono automaticamente sottoposte a timeout e riciclate.

> [!NOTE]
> I database Spark vengono creati in modo indipendente dai pool di Spark. Un'area di lavoro include sempre un database Spark **predefinito** ed è possibile creare database Spark aggiuntivi.

## <a name="the-sql-on-demand-pool"></a>Pool SQL su richiesta

Ogni area di lavoro è associata a un pool predefinito e non eliminabile denominato **SQL su richiesta**. Il pool SQL su richiesta consente di usare SQL senza la necessità di creare o gestire un pool Synapse SQL. Diversamente dagli altri tipi di pool, la fatturazione per SQL su richiesta è basata sulla quantità di dati analizzati per eseguire la query e non sul numero di risorse usate per eseguirla.

* SQL su richiesta dispone anche di database SQL su richiesta indipendenti da qualsiasi pool SQL su richiesta.
* A un'area di lavoro, attualmente, è sempre associato esattamente un pool SQL su richiesta denominato **SQL su richiesta**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Caricare i dati dell'esempio NYC Taxi nel database SQLDB1

1. Nel menu blu più in alto di Synapse Studio selezionare l'icona **?** .
1. Selezionare **Getting started > Getting started hub** (Introduzione > Hub di introduzione)
1. Nella scheda etichettata **Query sample data** (Dati di esempio query) selezionare il pool SQL denominato `SQLDB1`
1. Selezionare **Query data** (Dati query). Viene visualizzata una notifica che indica che il caricamento dei dati di esempio è in corso e che in seguito scompare.
1. Nella parte superiore di Synapse Studio viene visualizzata una barra di notifica blu chiaro che indica che è in corso il caricamento dei dati in SQLDB1. Attendere che diventi verde, quindi ignorare il messaggio.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Esplorare i dati dell'esempio NYC Taxi nel pool SQL

1. In Synapse Studio passare all'hub **Data** (Dati)
1. Passare a **SQLDB1 > Tables** (Tabelle). Si nota che sono state caricate diverse tabelle.
1. Fare clic con il pulsante destro del mouse sulla tabella **dbo.Trip** e selezionare **New SQL Script > Select TOP 100 Rows** (Nuovo script SQL > Seleziona le prime 100 righe)
1. Viene creato un nuovo script SQL che verrà eseguito automaticamente.
1. Si noti che nella parte superiore dello script SQL l'opzione **Connetti a** è impostata automaticamente sul pool SQL denominato `SQLDB1`.
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

1. Questa query mostra come le distanze totali e la distanza media delle corse sono correlate al numero di passeggeri
1. Nella finestra dei risultati dello script SQL modificare **Vista** in **Grafico** per visualizzare i risultati come un grafico a linee

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>Caricare i dati di esempio di NYC Taxi nel database nyctaxi di Spark

I dati sono disponibili in una tabella in `SQLDB1`. A questo punto è possibile caricarli in un database Spark denominato `nyctaxi`.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo)
1. Selezionare **+** e quindi selezionare **Notebook**
1. Nella parte superiore del notebook impostare il valore **Attach to** (Collega a) su `Spark1`
1. Selezionare **Add code** (Aggiungi codice) per aggiungere una cella di codice del notebook e incollare il testo seguente:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Passare all'hub **Dati**, fare clic con il pulsante destro del mouse su **Database** e scegliere **Aggiorna**.
1. A questo punto vengono visualizzati i database seguenti:
    - SQLDB1 (pool SQL)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizzare i dati dell'esempio NYC Taxi con Spark e notebook

1. Tornare al notebook
1. Creare una nuova cella di codice, immettere il testo seguente ed eseguire la cella per avviare l'esempio dei dati di NYC Taxi caricati nel database Spark `nyctaxi`.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Eseguire il codice seguente per avviare la stessa analisi effettuata in precedenza con il pool SQL `SQLDB1`. Questo codice salva inoltre i risultati dell'analisi in una tabella denominata `nyctaxi.passengercountstats` e consente di visualizzarli.

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

1. Nei risultati della cella selezionare **Grafico** per visualizzare i dati
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Personalizzare la visualizzazione dei dati con Spark e notebook

Con i notebook è possibile controllare la modalità di rendering dei grafici. Il codice seguente mostra un semplice esempio che usa le librerie più diffuse `matplotlib` e `seaborn`. Viene eseguito il rendering dello stesso tipo di grafico a linee visualizzato durante l'esecuzione delle query SQL precedenti.

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

In precedenza i dati sono stati copiati da una tabella `SQLDB1.dbo.Trip` del pool SQL in una tabella `nyctaxi.trip` di Spark. In seguito, tramite Spark i dati sono stati aggregati nella tabella `nyctaxi.passengercountstats` di Spark. A questo punto, i dati vengono copiati da `nyctaxi.passengercountstats` in una tabella del pool SQL denominata `SQLDB1.dbo.PassengerCountStats`. 

Eseguire la cella seguente nel notebook. La tabella Spark aggregata viene copiata nuovamente nella tabella del pool SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analizzare i dati dell'esempio NYC Taxi nei database Spark tramite SQL su richiesta 

Le tabelle nei database Spark sono automaticamente visibili e utilizzabili per l'esecuzione di query da SQL su richiesta.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo) e creare un nuovo script SQL
1. Impostare **Connect to** (Collega a) su **SQL su richiesta** 
1. Incollare il testo seguente nello script ed eseguire lo script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > La prima volta che si esegue una query che usa SQL su richiesta, la raccolta di risorse SQL necessarie per l'esecuzione delle query richiederà circa 10 secondi. Le query successive non richiederanno questo tempo e saranno molto più veloci.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orchestrare le attività con le pipeline

È possibile orchestrare un'ampia gamma di attività in Azure Synapse. In questa sezione viene illustrato quanto sia semplice.

1. In Synapse Studio passare all'hub **Orchestrate** (Orchestrazione).
1. Selezionare **+** e quindi selezionare **Pipeline**. Viene creata una nuova pipeline.
1. Passare all'hub Develop (Sviluppo) e trovare il notebook creato in precedenza.
1. Trascinare il notebook nella pipeline.
1. Nella pipeline selezionare **Add trigger > New/edit** (Aggiungi trigger > Nuovo/modifica).
1. In **Choose trigger** (Scegli trigger) selezionare **New** (Nuovo) e nell'opzione per la ricorrenza impostare il trigger per essere eseguito ogni ora.
1. Selezionare **OK**.
1. Selezionare **Publish All** (Pubblica tutto) e la pipeline verrà eseguita ogni ora.
1. Per fare in modo che la pipeline venga eseguita senza attendere l'ora successiva, selezionare **Add trigger > New/edit** (Aggiungi trigger > Nuovo/modifica).

## <a name="working-with-data-in-a-storage-account"></a>Uso dei dati in un account di archiviazione

Fino a questo momento sono stati illustrati gli scenari in cui i dati risiedono nei database nell'area di lavoro. Verrà ora illustrato come usare i file negli account di archiviazione. In questo scenario viene usato l'account di archiviazione primario dell'area di lavoro e il contenitore specificato durante la creazione dell'area di lavoro stessa.

* Nome dell'account di archiviazione: `contosolake`
* Nome del contenitore nell'account di archiviazione: `users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Creazione di file CSV e Parquet nell'account di archiviazione

Eseguire il codice seguente in un notebook per creare un file CSV e un file Parquet nell'account di archiviazione

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analisi dei dati in un account di archiviazione

1. In Synapse Studio passare all'hub **Data** (Dati)
1. Selezionare **Collegato**
1. Passare a **Storage accounts > myworkspace (Primary - contosolake)** (Account di archiviazione > miaareadilavoro - Primario - contosolake)
1. Selezionare **users (Primary)** (utenti - Primario)
1. Viene visualizzata una cartella denominata `NYCTaxi`. All'interno vengono visualizzate due cartelle, ovvero `PassengerCountStats.csv` e `PassengerCountStats.parquet`.
1. Passare alla cartella `PassengerCountStats.parquet`.
1. Fare clic con il pulsante destro del mouse sul file `.parquet` all'interno e selezionare **new notebook** (nuovo notebook). Viene creato un notebook con una cella simile alla seguente:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Eseguire la cella.
1. Fare clic con il pulsante destro del mouse sul file Parquet all'interno e selezionare **New SQL script > SELECT TOP 100 rows** (Nuovo script SQL > Seleziona le prime 100 righe). Viene creato uno script SQL simile al seguente:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. Nello script il campo **Attach to** (Collega a) viene impostato su **SQL on-demand** (SQL su richiesta).
1. Eseguire lo script.

## <a name="visualize-data-with-power-bi"></a>Visualizzare i dati con Power BI

A partire dai dati dell'esempio NYX Taxi, sono stati creati set di dati aggregati in due tabelle:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

È possibile collegare un'area di lavoro di Power BI all'area di lavoro di Synapse. In questo modo è possibile ottenere facilmente i dati nell'area di lavoro di Power BI, nonché modificare i report di Power BI direttamente nell'area di lavoro di Synapse.

### <a name="create-a-power-bi-workspace"></a>Creare un'area di lavoro di Power BI

1. Accedere a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Creare una nuova area di lavoro di Power BI denominata `NYCTaxiWorkspace1`.

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Collegare l'area di lavoro di Synapse alla nuova area di lavoro di Power BI

1. In Synapse Studio passare a **Gestisci > Servizi collegati**.
1. Selezionare **+ Nuovo**, **Connetti a Power BI** e impostare questi campi:

    |Impostazione | Valore consigliato | 
    |---|---|
    |**Nome**|`NYCTaxiWorkspace1`|
    |**Nome area di lavoro**|`NYCTaxiWorkspace1`|
        
1. Selezionare **Crea**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Creare un set di dati di Power BI che usa i dati dell'area di lavoro di Synapse

1. In Synapse Studio passare all'hub **Develop > Power BI** (Sviluppo > Power BI)
1. Passare a **NYCTaxiWorkspace1 > Set di dati Power BI** e selezionare **Nuovo set di dati Power BI**.
1. Passare il puntatore del mouse sul database `SQLDB1` e selezionare **Scarica file .pbids**.
1. Aprire il file `.pbids` scaricato. 
1. Questa operazione avvierà Power BI Desktop e lo connetterà automaticamente a `SQLDB1` nell'area di lavoro di Synapse.
1. Se viene visualizzata una finestra di dialogo denominata **Database di SQL Server**: a. Selezionare l'**account Microsoft**. 
    b. Selezionare **Accedi** ed eseguire l'accesso.
    c. Selezionare **Connetti**.
1. Viene visualizzata la finestra di dialogo **Navigator**. Selezionare la tabella **PassengerCountStats** e quindi selezionare **Carica**.
1. Viene visualizzata una finestra di dialogo **Impostazioni di connessione**. Selezionare **DirectQuery** e quindi **OK**
1. Selezionare il pulsante **Report** a sinistra.
1. Aggiungere un **grafico a linee** al report.
    a. Trascinare la colonna **PasssengerCount** in **Visualizzazioni > Asse** b. Trascinare le colonne **SumTripDistance** e **AvgTripDistance** in **Visualizzazioni > Valori**.
1. Nella scheda **Home** selezionare **Pubblica**.
1. Viene chiesto se si desidera salvare le modifiche. Selezionare **Salva**.
1. Viene chiesto di selezionare un nome file. Scegliere `PassengerAnalysis.pbix` e selezionare **Salva**.
1. Viene visualizzata la richiesta **Selezionare una destinazione**. Selezionare `NYCTaxiWorkspace1` e quindi fare clic su **Seleziona**.
1. Attendere il completamento della pubblicazione.

### <a name="configure-authentication-for-your-dataset"></a>Configurare l'autenticazione per il set di dati

1. Aprire [powerbi.microsoft.com](https://powerbi.microsoft.com/) e fare clic su **Accedi**
1. In **Aree di lavoro** a sinistra selezionare l'area di lavoro `NYCTaxiWorkspace1`.
1. All'interno di tale area di lavoro viene visualizzato un set di dati denominato `Passenger Analysis` e un report denominato `Passenger Analysis`.
1. Passare il puntatore del mouse sul set di dati `PassengerAnalysis`, selezionare l'icona con i tre punti e quindi selezionare **Impostazioni**.
1. In **Credenziali origine dati** impostare il campo **Metodo di autenticazione** su **OAuth2** e selezionare **Accedi**.

### <a name="edit-a-report-in-synapse-studio"></a>Modificare un report in Synapse Studio

1. Tornare a Synapse Studio e selezionare **Close and refresh** (Chiudi e aggiorna) 
1. Passare all'hub **Develop** (Sviluppo) 
1. Passare il puntatore del mouse su **Power BI** e fare clic per aggiornare il nodo **Report di Power BI**.
1. In **Power BI** sono ora visibili gli elementi seguenti: a. * In **NYCTaxiWorkspace1 > Set di dati Power BI**, un nuovo set di dati denominato **PassengerAnalysis**.
    b. * In **NYCTaxiWorkspace1 > Report di Power BI**, un nuovo report denominato **PassengerAnalysis**.
1. Selezionare il report **PassengerAnalysis**. 
1. Il report viene aperto e ora è possibile modificarlo direttamente in Synapse Studio.

## <a name="monitor-activities"></a>Monitorare le attività

1. In Synapse Studio passare all'hub di monitoraggio.
1. In questa posizione è possibile visualizzare una cronologia di tutte le attività che si verificano nell'area di lavoro e quelle attualmente attive.
1. Esaminare i campi **Esecuzioni della pipeline**, **Apache Spark applications** (Applicazioni Apache Spark) e **SQL requests** (Richieste SQL) per visualizzare le attività eseguite nell'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Azure Synapse Analytics (anteprima)](overview-what-is.md)

