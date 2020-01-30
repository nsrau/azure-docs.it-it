---
title: Inserire dati in formato JSON in Azure Esplora dati
description: Informazioni su come inserire dati in formato JSON in Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772339"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Inserire dati di esempio in formato JSON in Azure Esplora dati

Questo articolo illustra come inserire dati in formato JSON in un database di Esplora dati di Azure. Si inizierà con semplici esempi di JSON non elaborati e mappati, si continuerà a usare JSON con più righe e quindi si affronteranno schemi JSON più complessi contenenti matrici e dizionari.  Gli esempi illustrano in dettaglio il processo di inserimento dei dati in formato JSON usando kusto Query Language C#(KQL), o Python. Il linguaggio di query kusto `ingest` i comandi di controllo vengono eseguiti direttamente nell'endpoint del motore. Negli scenari di produzione, l'inserimento viene eseguito al servizio Gestione dati usando le librerie client o le connessioni dati. Leggi i dati di inserimento [usando la libreria Python di azure Esplora dati](/azure/data-explorer/python-ingest-data) e Inserisci [dati usando Azure Esplora dati .NET standard SDK](/azure/data-explorer/net-standard-ingest-data) per una procedura dettagliata sull'inserimento di dati con queste librerie client.

## <a name="prerequisites"></a>Prerequisiti

[Un cluster e un database di test](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Formato JSON

Azure Esplora dati supporta due formati di file JSON:
* `json`: codice JSON separato da righe. Ogni riga nei dati di input include esattamente un record JSON.
* `multijson`: JSON con più righe. Il parser ignora i separatori di riga e legge un record dalla posizione precedente fino alla fine di un file JSON valido.

### <a name="ingest-and-map-json-formatted-data"></a>Inserire e mappare i dati in formato JSON

Per l'inserimento di dati in formato JSON è necessario specificare il *formato* usando la [Proprietà](/azure/kusto/management/data-ingestion/index#ingestion-properties)di inserimento. L'inserimento di dati JSON richiede il [mapping](/azure/kusto/management/mappings), che esegue il mapping di una voce di origine JSON alla relativa colonna di destinazione. Quando si inseriscono dati, utilizzare la proprietà di inserimento `jsonMappingReference` predefinita o specificare la proprietà di inserimento `jsonMapping`. In questo articolo verrà utilizzata la proprietà di inserimento `jsonMappingReference`, che è predefinita nella tabella utilizzata per l'inserimento. Negli esempi seguenti si inizierà inserendo i record JSON come dati non elaborati in una tabella a colonna singola. Si userà quindi il mapping per inserire ogni proprietà nella relativa colonna mappata. 

### <a name="simple-json-example"></a>Esempio JSON semplice

L'esempio seguente è un semplice JSON, con una struttura flat. I dati hanno informazioni sulla temperatura e sull'umidità, raccolti da diversi dispositivi. Ogni record è contrassegnato con un ID e un timestamp.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Inserire record JSON non elaborati 

In questo esempio si inseriscono i record JSON come dati non elaborati in una tabella a colonna singola. La manipolazione dei dati, l'utilizzo di query e i criteri di aggiornamento vengono eseguiti dopo l'inserimento dei dati.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Usare il linguaggio di query kusto per inserire i dati in un formato JSON non elaborato.

1. Accedere a [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Selezionare **Add cluster**  (Aggiungi cluster).

1. Nella finestra di dialogo **Add cluster** (Aggiungi cluster) immettere l'URL del cluster nel modulo`https://<ClusterName>.<Region>.kusto.windows.net/`, quindi selezionare **Aggiungi**.

1. Incollare il comando seguente e selezionare Run ( **Esegui** ) per creare la tabella.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Questa query crea una tabella con una singola colonna `Event` di un tipo di dati [dinamico](/azure/kusto/query/scalar-data-types/dynamic) .

1. Creare il mapping JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Questo comando crea un mapping ed esegue il mapping del percorso radice JSON `$` alla colonna `Event`.

1. Inserire i dati nella tabella `RawEvents`.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Usare C# per inserire dati in formato JSON non elaborato.

1. Creare la tabella `RawEvents`.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Creare il mapping JSON.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Questo comando crea un mapping ed esegue il mapping del percorso radice JSON `$` alla colonna `Event`.

1. Inserire i dati nella tabella `RawEvents`.

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> I dati vengono aggregati in base ai criteri di invio in [batch](/azure/kusto/concepts/batchingpolicy), con conseguente latenza di pochi minuti.

# <a name="pythontabpython"></a>[Python](#tab/python)

Usare Python per inserire dati in formato JSON non elaborato.

1. Creare la tabella `RawEvents`.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Creare il mapping JSON.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inserire i dati nella tabella `RawEvents`.

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > I dati vengono aggregati in base ai criteri di invio in [batch](/azure/kusto/concepts/batchingpolicy), con conseguente latenza di pochi minuti.

---

## <a name="ingest-mapped-json-records"></a>Inserire record JSON mappati

In questo esempio si inseriscono i dati dei record JSON. Ogni proprietà JSON è mappata a una singola colonna nella tabella. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Creare una nuova tabella con uno schema simile ai dati di input JSON. Questa tabella verrà usata per tutti gli esempi e i comandi di inserimento seguenti. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Creare il mapping JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    In questo mapping, in base a quanto definito dallo schema della tabella, le voci `timestamp` verranno inserite nella colonna `Time` come tipi di dati `datetime`.

1. Inserire i dati nella tabella `Events`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Il file ' Simple. JSON ' contiene alcuni record JSON delimitati da righe. Il formato è `json`e il mapping usato nel comando di inserimento è il `FlatEventMapping` creato.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Creare una nuova tabella con uno schema simile ai dati di input JSON. Questa tabella verrà usata per tutti gli esempi e i comandi di inserimento seguenti. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Creare il mapping JSON.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    In questo mapping, in base a quanto definito dallo schema della tabella, le voci `timestamp` verranno inserite nella colonna `Time` come tipi di dati `datetime`.    

1. Inserire i dati nella tabella `Events`.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    Il file ' Simple. JSON ' contiene alcuni record JSON delimitati da righe. Il formato è `json`e il mapping usato nel comando di inserimento è il `FlatEventMapping` creato.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Creare una nuova tabella con uno schema simile ai dati di input JSON. Questa tabella verrà usata per tutti gli esempi e i comandi di inserimento seguenti. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Creare il mapping JSON.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inserire i dati nella tabella `Events`.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Il file ' Simple. JSON ' contiene alcuni record JSON delimitati da righe. Il formato è `json`e il mapping usato nel comando di inserimento è il `FlatEventMapping` creato.    
---

## <a name="ingest-multi-lined-json-records"></a>Inserire record JSON con più righe

In questo esempio si inseriscono record JSON a più righe. Ogni proprietà JSON è mappata a una singola colonna nella tabella. Il file ' multilined. JSON ' contiene alcuni record JSON rientrati. Il formato `multijson` indica al motore di leggere i record dalla struttura JSON.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Inserire i dati nella tabella `Events`.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Inserire i dati nella tabella `Events`.

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Inserire i dati nella tabella `Events`.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Inserire record JSON contenenti matrici

I tipi di dati matrice sono una raccolta ordinata di valori. L'inserimento di una matrice JSON viene eseguito da un [criterio di aggiornamento](/azure/kusto/management/update-policy). Il codice JSON viene inserito così com'è in una tabella intermedia. Un criterio di aggiornamento esegue una funzione predefinita nella tabella `RawEvents`, inserendo nuovamente i risultati nella tabella di destinazione. I dati vengono inseriti con la struttura seguente:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Creare una funzione `update policy` che espande la raccolta di `records` in modo che ogni valore nella raccolta riceva una riga distinta, usando l'operatore di `mv-expand`. Si userà Table `RawEvents` come tabella di origine e `Events` come tabella di destinazione.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. Lo schema ricevuto dalla funzione deve corrispondere allo schema della tabella di destinazione. Utilizzare `getschema` operatore per esaminare lo schema.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Aggiungere i criteri di aggiornamento alla tabella di destinazione. Questi criteri eseguiranno automaticamente la query su tutti i dati appena inseriti nella `RawEvents` tabella intermedia e inseriranno i risultati nella tabella `Events`. Definire un criterio di conservazione zero per evitare che la tabella intermedia venga mantenuta.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Inserire i dati nella tabella `RawEvents`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Esaminare i dati nella tabella `Events`.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Creare una funzione di aggiornamento che espande la raccolta di `records` in modo che ogni valore nella raccolta riceva una riga distinta, usando l'operatore `mv-expand`. Si userà Table `RawEvents` come tabella di origine e `Events` come tabella di destinazione.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > Lo schema ricevuto dalla funzione deve corrispondere allo schema della tabella di destinazione.

1. Aggiungere i criteri di aggiornamento alla tabella di destinazione. Questo criterio eseguirà automaticamente la query su tutti i dati appena inseriti nella `RawEvents` tabella intermedia e li inserirà nella tabella `Events`. Definire un criterio di conservazione zero per evitare che la tabella intermedia venga mantenuta.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Inserire i dati nella tabella `RawEvents`.

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Esaminare i dati nella tabella `Events`.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Creare una funzione di aggiornamento che espande la raccolta di `records` in modo che ogni valore nella raccolta riceva una riga distinta, usando l'operatore `mv-expand`. Si userà Table `RawEvents` come tabella di origine e `Events` come tabella di destinazione.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > Lo schema ricevuto dalla funzione deve corrispondere allo schema della tabella di destinazione.

1. Aggiungere i criteri di aggiornamento alla tabella di destinazione. Questo criterio eseguirà automaticamente la query su tutti i dati appena inseriti nella `RawEvents` tabella intermedia e li inserirà nella tabella `Events`. Definire un criterio di conservazione zero per evitare che la tabella intermedia venga mantenuta.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inserire i dati nella tabella `RawEvents`.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Esaminare i dati nella tabella `Events`.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Inserire record JSON contenenti dizionari

Il JSON strutturato del dizionario contiene coppie chiave-valore. I record JSON subiscono il mapping di inserimento usando un'espressione logica nel `JsonPath`. È possibile inserire dati con la struttura seguente:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Creare un mapping JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Inserire i dati nella tabella `Events`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Creare un mapping JSON.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Inserire i dati nella tabella `Events`.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Creare un mapping JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inserire i dati nella tabella `Events`.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dell'inserimento dei dati](ingest-data-overview.md)
* [Scrivere query](write-queries.md)