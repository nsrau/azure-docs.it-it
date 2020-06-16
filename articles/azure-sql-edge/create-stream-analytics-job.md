---
title: Creare un processo di Streaming T-SQL in SQL Edge di Azure (anteprima)
description: Informazioni sulla creazione di processi di analisi di flusso in SQL Edge di Azure (anteprima)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 323ec00667350917e6b16827f908ac1abeee77d6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233317"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Creare un processo di analisi di flusso in SQL Edge di Azure (anteprima) 

Questo articolo illustra come creare un processo di Streaming T-SQL in SQL Edge di Azure (anteprima). Per creare un processo di streaming in SQL Edge, è necessario eseguire i passaggi seguenti

1. Creare gli oggetti di input e output del flusso esterno
2. Definire la query del processo di streaming nell'ambito della creazione del processo di streaming.

> [!NOTE]
> Per abilitare la funzionalità di streaming T-SQL in SQL Edge di Azure, abilitare TF 11515 come opzione di avvio oppure usare il comando [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql). Per altre informazioni su come abilitare i flag di traccia usando il file mssql.conf, vedere [Configurazione tramite il file mssql.conf](configure.md#configure-using-mssqlconf-file). Questo requisito verrà rimosso negli aggiornamenti futuri di SQL Edge di Azure (anteprima).

## <a name="configure-an-external-stream-input-and-output-object"></a>Configurare un oggetto di input e output del flusso esterno

Streaming T-SQL usa la funzionalità dell'origine dati esterna di SQL Server per definire le origini dati associate agli input e agli output del flusso esterno del processo di streaming. Per creare un oggetto di input o di output del flusso esterno, sono necessari i comandi T-SQL seguenti.

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

Inoltre, nel caso di SQL Edge (o SQL Server, Azure SQL) usato come flusso di output, è necessario il comando T-SQL [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) per definire le credenziali per l'accesso al database SQL.

### <a name="supported-input-and-output-stream-data-sources"></a>Origini dati dei flussi di input e di output supportati

SQL Edge di Azure attualmente supporta solo le origini dati seguenti come output e input di flusso.

| Tipo di origine dati | Input | Output | Descrizione |
|------------------|-------|--------|------------------|
| Hub Azure IoT Edge | S | S | Origine dati per la lettura/scrittura dei dati di streaming in un hub Azure IoT Edge. Per altre informazioni sull'hub Azure IoT Edge, vedere [Hub di IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| Database SQL | N | S | Connessione all'origine dati per scrivere i dati di streaming nel database SQL. Il database SQL può essere un database SQL Edge locale o un database di SQL Server o SQL di Azure remoto|
| Archiviazione BLOB di Azure | N | S | Origine dati per la scrittura di dati in un BLOB in un account di archiviazione di Azure. |
| Kafka | S | N | Origine dati per la lettura dei dati in streaming da un argomento Kafka. Questa scheda è attualmente disponibile solo per la versione Intel/AMD di SQL Edge di Azure e non è disponibile per la versione ARM64 di SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Esempio: Creare un oggetto di input/output del flusso esterno per l'hub di Azure IoT Edge

Nell'esempio seguente viene creato un oggetto flusso esterno per l'hub di Edge. Per creare un'origine dati di input/output del flusso esterno per l'hub di Azure IoT Edge, è prima di tutto necessario creare un formato di file esterno per SQL per comprendere anche il layout dei dati letti/scritti.

1. Creare un formato di file esterno con il tipo di formato JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Creare un'origine dati esterna per l'hub di IoT Edge. Lo script T-SQL seguente crea una connessione all'origine dati a un hub di Edge in esecuzione nello stesso host Docker di SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Creare l'oggetto flusso esterno per l'hub di IoT Edge. Lo script T-SQL seguente crea un oggetto flusso per l'hub di Edge. Nel caso di un oggetto flusso dell'hub di Edge, il parametro LOCATION è il nome dell'argomento/canale dell'hub di Edge in lettura o scrittura.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Esempio: Creare un oggetto flusso esterno nel database SQL

Nell'esempio seguente viene creato un oggetto flusso esterno nel database SQL Edge locale. 

1. Creare una chiave master nel database. Questo passaggio è necessario per crittografare il segreto delle credenziali.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Creare le credenziali con ambito database per l'accesso all'origine SQL Server. Nell'esempio seguente viene creata una credenziale per l'origine dati esterna con IDENTITY = 'nomeutente' e SECRET = 'password'.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Creare un'origine dati esterna con CREATE EXTERNAL DATA SOURCE. L'esempio seguente:

    * Crea un'origine dati esterna denominata LocalSQLOutput
    * Identifica l'origine dati esterna (LOCATION = '<vendor>://<server>[:<port>]'). Nell'esempio punta a un'istanza locale di SQL Edge.
    * Infine, l'esempio usa le credenziali create in precedenza.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Creare l'oggetto flusso esterno. Nell'esempio seguente viene creato un oggetto flusso esterno che punta a una tabella *dbo.TemperatureMeasurements* nel database *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Creare il processo di streaming e le query di streaming

Usare la stored procedure di sistema **sys.sp_create_streaming_job** per definire le query di streaming e creare il processo di streaming. La stored procedure **sp_create_streaming_job** accetta due parametri

- job_name - Nome del processo di streaming. I nomi dei processi di streaming sono univoci nell'istanza.
- statement - Istruzioni di query di streaming basate sul [linguaggio di query di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

L'esempio seguente crea un processo di streaming semplice con una query di streaming. Questa query legge gli input dall'hub di Edge e scrive nella tabella *dbo.TemperatureMeasurements* nel database.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Nell'esempio seguente viene creato un processo di streaming più complesso con più query diverse, inclusa una query che usa la funzione AnomalyDetection_ChangePoint predefinita per identificare le anomalie nei dati di temperatura.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Avviare, arrestare, eliminare e monitorare i processi di streaming

Per avviare un processo di streaming in SQL Edge, eseguire la stored procedure **sys.sp_start_streaming_job**. La stored procedure richiede il nome del processo di streaming da avviare, come input.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Per arrestare un processo di streaming in SQL Edge, eseguire la stored procedure **sys.sp_stop_streaming_job**. La stored procedure richiede il nome del processo di streaming da arrestare, come input.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Per eliminare un processo di streaming in SQL Edge, eseguire la stored procedure **sys.sp_drop_streaming_job**. La stored procedure richiede il nome del processo di streaming da eliminare, come input.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Per ottenere lo stato corrente di un processo di streaming in SQL Edge, eseguire la stored procedure **sys.sp_get_streaming_job**. La stored procedure richiede il nome del processo di streaming da eliminare, come input, e restituisce il nome e lo stato corrente del processo di streaming.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

Il processo di streaming può trovarsi in uno degli stati seguenti

| Stato | Descrizione |
|--------| ------------|
| Data di creazione | Il processo di streaming è stato creato, ma non è ancora stato avviato |
| Avvio in corso | Il processo di streaming si trova nella fase di avvio |
| Idle | Il processo di streaming è in esecuzione, ma non è presente alcun input da elaborare |
| Elaborazione in corso | Il processo di streaming è in esecuzione ed è in corso l'elaborazione degli input. Questo stato indica che il processo di streaming è integro |
| Degraded | Il processo di streaming è in esecuzione, tuttavia si sono verificati alcuni errori di serializzazione/deserializzazione di input/output non irreversibili durante l'elaborazione dell'input. L'esecuzione del processo di input continuerà, ma verranno eliminati gli input per cui si verificano errori |
| Arrestato | Il processo di streaming è stato arrestato |
| Operazione non riuscita | Il processo di streaming non è riuscito. Si tratta in genere di un'indicazione di un errore irreversibile durante l'elaborazione |

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare i metadati associati ai processi di streaming in SQL Edge di Azure (anteprima)](streaming-catalog-views.md) 
- [Creare un flusso esterno](create-external-stream-transact-sql.md)
