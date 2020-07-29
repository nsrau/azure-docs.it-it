---
title: Creare un processo di streaming T-SQL in Azure SQL Edge (anteprima)
description: Informazioni sulla creazione di processi di analisi di flusso in Azure SQL Edge (anteprima).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 346a59f085e766fef09d73b9e7baa03dad510148
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321718"
---
# <a name="create-an-azure-stream-analytics-job-in-azure-sql-edge-preview"></a>Creare un processo di analisi di flusso di Azure in Azure SQL Edge (anteprima) 

Questo articolo illustra come creare un processo di streaming T-SQL in Azure SQL Edge (anteprima). Si creano gli oggetti di input e output del flusso esterno, quindi si definisce la query del processo di streaming nell'ambito della creazione del processo di streaming.

> [!NOTE]
> Per abilitare la funzionalità di streaming T-SQL in Azure SQL Edge, abilitare TF 11515 come opzione di avvio oppure usare il comando [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) . Per altre informazioni su come abilitare i flag di traccia usando un file MSSQL. conf, vedere [configurare usando un file MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

## <a name="configure-the-external-stream-input-and-output-objects"></a>Configurare gli oggetti di input e output del flusso esterno

Il flusso T-SQL usa la funzionalità di origine dati esterna di SQL Server per definire le origini dati associate agli input e agli output del flusso esterno del processo di streaming. Usare i comandi T-SQL seguenti per creare un oggetto di input o output del flusso esterno:

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Se, inoltre, Azure SQL Edge, SQL Server o database SQL di Azure viene usato come flusso di output, è necessario [creare le credenziali con ambito database (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Questo comando T-SQL definisce le credenziali per l'accesso al database.

### <a name="supported-input-and-output-stream-data-sources"></a>Origini dati dei flussi di input e di output supportati

SQL Edge di Azure attualmente supporta solo le origini dati seguenti come output e input di flusso.

| Tipo di origine dati | Input | Output | Descrizione |
|------------------|-------|--------|------------------|
| Hub Azure IoT Edge | S | S | Origine dati per la lettura e la scrittura dei dati di streaming in un hub Azure IoT Edge. Per altre informazioni, vedere [Hub IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).|
| Database SQL | N | S | Connessione all'origine dati per scrivere i dati di streaming nel database SQL. Il database può essere un database locale in Azure SQL Edge o un database remoto in SQL Server o nel database SQL di Azure.|
| Kafka | S | N | Origine dati per la lettura dei dati in streaming da un argomento Kafka. Questa scheda è attualmente disponibile solo per le versioni Intel o AMD di Azure SQL Edge. Non è disponibile per la versione ARM64 di Azure SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Esempio: creare un oggetto di input/output del flusso esterno per Azure IoT Edge Hub

Nell'esempio seguente viene creato un oggetto flusso esterno per Azure IoT Edge Hub. Per creare un'origine dati di input/output del flusso esterno per Azure IoT Edge Hub, è prima di tutto necessario creare un formato di file esterno per il layout dei dati letti o scritti.

1. Creare un formato di file esterno di tipo JSON.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Creare un'origine dati esterna per l'hub Azure IoT Edge. Lo script T-SQL seguente crea una connessione all'origine dati a un hub IoT Edge eseguito sullo stesso host Docker di Azure SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Creare l'oggetto flusso esterno per Azure IoT Edge Hub. Lo script T-SQL seguente crea un oggetto flusso per l'hub IoT Edge. Nel caso di un oggetto flusso di IoT Edge Hub, il parametro LOCATION è il nome dell'argomento dell'hub IoT Edge o del canale in lettura o scrittura.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Esempio: creare un oggetto flusso esterno nel database SQL di Azure

Nell'esempio seguente viene creato un oggetto flusso esterno nel database locale in Azure SQL Edge. 

1. Creare una chiave master nel database. Questo passaggio è necessario per crittografare il segreto delle credenziali.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Creare una credenziale con ambito database per accedere all'origine SQL Server. Nell'esempio seguente viene creata una credenziale per l'origine dati esterna, con IDENTITY = username e SECRET = password.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Creare un'origine dati esterna con CREATE EXTERNAL DATA SOURCE. L'esempio seguente:

    * Crea un'origine dati esterna denominata *LocalSQLOutput*.
    * Identifica l'origine dati esterna (LOCATION = '<vendor>://<server>[:<port>]'). Nell'esempio punta a un'istanza locale di Azure SQL Edge.
    * Usa le credenziali create in precedenza.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. Creare l'oggetto flusso esterno. Nell'esempio seguente viene creato un oggetto flusso esterno che punta a una tabella *dbo. TemperatureMeasurements*, nel database *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>Esempio: creare un oggetto flusso esterno per Kafka

Nell'esempio seguente viene creato un oggetto flusso esterno nel database locale in Azure SQL Edge. Questo esempio presuppone che il server Kafka sia configurato per l'accesso anonimo. 

1. Creare un'origine dati esterna con CREATE EXTERNAL DATA SOURCE. L'esempio seguente:

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Creare un formato di file esterno per l'input Kafka. Nell'esempio seguente è stato creato un formato di file JSON con compressione gzippato. 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```
    
3. Creare l'oggetto flusso esterno. Nell'esempio seguente viene creato un oggetto flusso esterno che punta a un argomento Kafka `*TemperatureMeasurement*` .

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',     
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Creare il processo di streaming e le query di streaming

Usare il `sys.sp_create_streaming_job` stored procedure di sistema per definire le query di streaming e creare il processo di streaming. Il `sp_create_streaming_job` stored procedure accetta i parametri seguenti:

- `job_name`: Nome del processo di streaming. I nomi dei processi di streaming sono univoci nell'istanza.
- `statement`: Istruzioni di query di streaming basate sul [linguaggio di query di analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

L'esempio seguente crea un processo di streaming semplice con una query di streaming. Questa query legge gli input dall'hub IoT Edge e scrive `dbo.TemperatureMeasurements` nel database.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Nell'esempio seguente viene creato un processo di streaming più complesso con più query diverse. Queste query includono una che usa la `AnomalyDetection_ChangePoint` funzione predefinita per identificare le anomalie nei dati di temperatura.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Avviare, arrestare, eliminare e monitorare i processi di streaming

Per avviare un processo di streaming in Azure SQL Edge, eseguire il `sys.sp_start_streaming_job` stored procedure. Il stored procedure richiede il nome del processo di streaming da avviare come input.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Per arrestare un processo di streaming, eseguire il `sys.sp_stop_streaming_job` stored procedure. Il stored procedure richiede che il nome del processo di streaming venga arrestato come input.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Per eliminare o eliminare un processo di streaming, eseguire il `sys.sp_drop_streaming_job` stored procedure. Il stored procedure richiede il nome del processo di streaming da eliminare, come input.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Per ottenere lo stato corrente di un processo di streaming, eseguire il `sys.sp_get_streaming_job` stored procedure. Il stored procedure richiede il nome del processo di streaming da eliminare, come input. Restituisce il nome e lo stato corrente del processo di streaming.

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

Il processo di streaming può avere uno degli Stati seguenti:

| Stato | Descrizione |
|--------| ------------|
| Data di creazione | Il processo di streaming è stato creato, ma non è ancora stato avviato. |
| Avvio in corso | Il processo di streaming si trova nella fase di avvio. |
| Idle | Il processo di streaming è in esecuzione, ma non è presente alcun input da elaborare. |
| Elaborazione in corso | Il processo di streaming è in esecuzione ed è in corso l'elaborazione degli input. Questo stato indica uno stato integro per il processo di streaming. |
| Degraded | Il processo di streaming è in esecuzione, ma si sono verificati alcuni errori non irreversibili durante l'elaborazione dell'input. L'esecuzione del processo di input continuerà, ma verranno eliminati gli input per cui si verificano errori. |
| Arrestato | Il processo di streaming è stato arrestato. |
| Non riuscito | Il processo di streaming non è riuscito. Si tratta in genere di un'indicazione di un errore irreversibile durante l'elaborazione. |

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare i metadati associati ai processi di streaming in SQL Edge di Azure (anteprima)](streaming-catalog-views.md) 
- [Creare un flusso esterno](create-external-stream-transact-sql.md)
