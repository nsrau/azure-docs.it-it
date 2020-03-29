---
title: Inserire i dati in formato JSON in Esplora dati di AzureIngest JSON formatted data into Azure Data Explorer
description: Informazioni su come inserire dati in formato JSON in Azure Data Explorer.Learn about how to ing json formatted data into Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772339"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Inserire i dati di esempio in formato JSON in Esplora dati di AzureIngest JSON formatted sample data into Azure Data Explorer

Questo articolo illustra come inserire dati in formato JSON in un database di Azure Data Explorer.This article shows you how to ingest JSON formatted data into an Azure Data Explorer database. Inizierai con semplici esempi di JSON non elaborato e mappato, continuerai con JSON multilineato e quindi affronterai schemi JSON più complessi contenenti matrici e dizionari.  Negli esempi viene descritto in dettaglio il processo di inserimento di dati in formato JSON utilizzando Kusto query Language (KQL), C' o Python. I comandi del `ingest` controllo del linguaggio di query Kusto vengono eseguiti direttamente all'endpoint del motore. Negli scenari di produzione, l'inserimento viene eseguito nel servizio di gestione dati tramite librerie client o connessioni dati. Leggere [Ingest data using the Azure Data Explorer Python library](/azure/data-explorer/python-ingest-data) and [Ingest data using the Azure Data Explorer .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) for a walk-through regarding ingesting data with these client libraries.

## <a name="prerequisites"></a>Prerequisiti

[Un cluster e un database di test](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Il formato JSON

Azure Data Explorer supporta due formati di file JSON:Azure Data Explorer supports two JSON file formats:
* `json`: JSON separato da riga. Ogni riga nei dati di input ha esattamente un record JSON.
* `multijson`: JSON multilineato. Il parser ignora i separatori di riga e legge un record dalla posizione precedente alla fine di un JSON valido.

### <a name="ingest-and-map-json-formatted-data"></a>Inserimento e mapping dei dati in formato JSON

L'inserimento di dati in formato JSON richiede di specificare il *formato* utilizzando la [proprietà di inserimento.](/azure/kusto/management/data-ingestion/index#ingestion-properties) L'inserimento di dati JSON richiede [il mapping](/azure/kusto/management/mappings), che esegue il mapping di una voce di origine JSON alla relativa colonna di destinazione. Quando si ingeriscono dati, usare la proprietà di inserimento predefinita `jsonMappingReference` o specificare la `jsonMapping`proprietà di inserimento. Questo articolo userà la `jsonMappingReference` proprietà di inserimento, che è predefinita nella tabella usata per l'inserimento. Negli esempi seguenti, inizieremo inserendo i record JSON come dati non elaborati in una singola tabella di colonna. Quindi useremo il mapping per ingerire ogni proprietà alla relativa colonna mappata. 

### <a name="simple-json-example"></a>Esempio JSON semplice

L'esempio seguente è un JSON semplice, con una struttura piatta. I dati confondono informazioni su temperatura e umidità, raccolte da diversi dispositivi. Ogni record è contrassegnato con un ID e un timestamp.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Inserimento di record JSON non elaborati 

In questo esempio, si inserino i record JSON come dati non elaborati in una tabella a colonna singola. La modifica dei dati, l'utilizzo di query e i criteri di aggiornamento vengono eseguiti dopo l'inserimento dei dati.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Usare il linguaggio di query Kusto per l'inserimento di dati in formato JSON non elaborato.

1. Accedere a [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Selezionare **Add cluster ** (Aggiungi cluster).

1. Nella finestra di dialogo **Add cluster** (Aggiungi cluster) immettere l'URL del cluster nel modulo`https://<ClusterName>.<Region>.kusto.windows.net/`, quindi selezionare **Aggiungi**.

1. Incollare il comando seguente e selezionare **Esegui** per creare la tabella.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Questa query crea una `Event` tabella con una singola colonna di un tipo di dati [dinamici.](/azure/kusto/query/scalar-data-types/dynamic)

1. Creare il mapping JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Questo comando crea un mapping ed `$` esegue `Event` il mapping del percorso radice JSON alla colonna.

1. Inserire i dati `RawEvents` nella tabella.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

Usare c'è per l'inserimento di dati in formato JSON non elaborato.

1. Creare `RawEvents` la tabella.

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
    Questo comando crea un mapping ed `$` esegue `Event` il mapping del percorso radice JSON alla colonna.

1. Inserire i dati `RawEvents` nella tabella.

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
> I dati vengono aggregati in base ai criteri di [invio in batch](/azure/kusto/concepts/batchingpolicy), con una latenza di alcuni minuti.

# <a name="python"></a>[Python](#tab/python)

Usare Python per l'inserimento di dati in formato JSON non elaborato.

1. Creare `RawEvents` la tabella.

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

1. Inserire i dati `RawEvents` nella tabella.

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
    > I dati vengono aggregati in base ai criteri di [invio in batch](/azure/kusto/concepts/batchingpolicy), con una latenza di alcuni minuti.

---

## <a name="ingest-mapped-json-records"></a>Eseguire l'inserimento di record JSON mappati

In questo esempio vengono ingeriti i dati dei record JSON. Ogni proprietà JSON viene mappata a una singola colonna nella tabella. 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Creare una nuova tabella, con uno schema simile ai dati di input JSON. Questa tabella verrà usata per tutti gli esempi seguenti e per i comandi di inserimento. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Creare il mapping JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    In questo mapping, come definito dallo `timestamp` schema della tabella, le `Time` voci `datetime` verranno rese in genere nella colonna come tipi di dati.

1. Inserire i dati `Events` nella tabella.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Il file 'simple.json' contiene alcuni record JSON separati da riga. Il formato `json`è e il mapping utilizzato nel `FlatEventMapping` comando di inserimento è quello creato dall'utente.

# <a name="c"></a>[C #](#tab/c-sharp)

1. Creare una nuova tabella, con uno schema simile ai dati di input JSON. Questa tabella verrà usata per tutti gli esempi seguenti e per i comandi di inserimento. 

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

    In questo mapping, come definito dallo `timestamp` schema della tabella, le `Time` voci `datetime` verranno rese in genere nella colonna come tipi di dati.    

1. Inserire i dati `Events` nella tabella.

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

    Il file 'simple.json' contiene alcuni record JSON separati da riga. Il formato `json`è e il mapping utilizzato nel `FlatEventMapping` comando di inserimento è quello creato dall'utente.

# <a name="python"></a>[Python](#tab/python)

1. Creare una nuova tabella, con uno schema simile ai dati di input JSON. Questa tabella verrà usata per tutti gli esempi seguenti e per i comandi di inserimento. 

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

1. Inserire i dati `Events` nella tabella.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Il file 'simple.json' contiene alcuni record JSON separati da righe. Il formato `json`è e il mapping utilizzato nel `FlatEventMapping` comando di inserimento è quello creato dall'utente.    
---

## <a name="ingest-multi-lined-json-records"></a>Inserimento di record JSON su più righe

In questo esempio vengono ingeriti record JSON con più righe. Ogni proprietà JSON viene mappata a una singola colonna nella tabella. Il file 'multilined.json' contiene alcuni record JSON rientrati. Il `multijson` formato indica al motore di leggere i record in base alla struttura JSON.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Inserire i dati `Events` nella tabella.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C #](#tab/c-sharp)

Inserire i dati `Events` nella tabella.

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

# <a name="python"></a>[Python](#tab/python)

Inserire i dati `Events` nella tabella.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Inserimento di record JSON contenenti matriciIngest JSON records containing arrays

I tipi di dati matrice sono una raccolta ordinata di valori. L'inserimento di una matrice JSON viene eseguito da un criterio di [aggiornamento.](/azure/kusto/management/update-policy) Il codice JSON viene ingerito così com'è in una tabella intermedia. Un criterio di aggiornamento esegue una `RawEvents` funzione predefinita nella tabella, rimuovendo i risultati alla tabella di destinazione. Insereremo i dati con la seguente struttura:

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

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Creare `update policy` una funzione che espande `records` la raccolta di in modo che ogni `mv-expand` valore nella raccolta riceva una riga separata, utilizzando l'operatore . Useremo la `RawEvents` tabella come tabella `Events` di origine e come tabella di destinazione.

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

1. Aggiungere il criterio di aggiornamento nella tabella di destinazione. Questo criterio eseguirà automaticamente la query su tutti `RawEvents` i dati appena inseriti `Events` nella tabella intermedia e inserirà i risultati nella tabella. Definire un criterio di conservazione zero per evitare di rendere persistente la tabella intermedia.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Inserire i dati `RawEvents` nella tabella.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Esaminare i `Events` dati nella tabella.

    ```Kusto
    Events
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

1. Creare una funzione di aggiornamento `records` che espande la raccolta di in modo che `mv-expand` ogni valore nella raccolta riceva una riga separata, utilizzando l'operatore . Useremo la `RawEvents` tabella come tabella `Events` di origine e come tabella di destinazione.   

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

1. Aggiungere il criterio di aggiornamento nella tabella di destinazione. Questo criterio eseguirà automaticamente la query su tutti `RawEvents` i dati appena inseriti `Events` nella tabella intermedia e inserirà i risultati nella tabella. Definire un criterio di conservazione zero per evitare di rendere persistente la tabella intermedia.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Inserire i dati `RawEvents` nella tabella.

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
    
1. Esaminare i `Events` dati nella tabella.

# <a name="python"></a>[Python](#tab/python)

1. Creare una funzione di aggiornamento `records` che espande la raccolta di in modo che `mv-expand` ogni valore nella raccolta riceva una riga separata, utilizzando l'operatore . Useremo la `RawEvents` tabella come tabella `Events` di origine e come tabella di destinazione.   

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

1. Aggiungere il criterio di aggiornamento nella tabella di destinazione. Questo criterio eseguirà automaticamente la query su tutti `RawEvents` i dati appena inseriti `Events` nella tabella intermedia e inserirà i risultati nella tabella. Definire un criterio di conservazione zero per evitare di rendere persistente la tabella intermedia.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inserire i dati `RawEvents` nella tabella.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Esaminare i `Events` dati nella tabella.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Inserimento di record JSON contenenti dizionari

Il dizionario strutturato JSON contiene coppie chiave-valore. I record Json vengono sottoposti al `JsonPath`mapping di inserimento utilizzando l'espressione logica nell'oggetto . È possibile inserire dati con la struttura seguente:You can ingest data with the following structure:

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

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Creare un mapping JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Inserire i dati `Events` nella tabella.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

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

1. Inserire i dati `Events` nella tabella.

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

# <a name="python"></a>[Python](#tab/python)

1. Creare un mapping JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Inserire i dati `Events` nella tabella.

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

* [Panoramica dell'inserimento dati](ingest-data-overview.md)
* [Scrivere query](write-queries.md)